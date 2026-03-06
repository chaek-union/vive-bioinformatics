# 11장. MCP와 Claude for Life Sciences

## 11.1 MCP란?

**MCP(Model Context Protocol)**는 AI 에이전트가 외부 도구와 데이터 소스에 접근할 수 있게 해주는 표준 프로토콜이다. Claude Code에 MCP 서버를 연결하면, Claude가 데이터베이스 조회, API 호출, 파일 시스템 접근 등 다양한 작업을 직접 수행할 수 있다.

쉽게 말해, MCP는 **Claude Code에 새로운 능력을 플러그인처럼 추가하는 방법**이다. 스마트폰에 앱을 설치하면 새로운 기능이 생기는 것처럼, Claude Code에 MCP 서버를 추가하면 새로운 데이터 소스에 접근할 수 있게 된다.

### MCP가 필요한 이유

Claude Code는 기본적으로 로컬 파일을 읽고 쓰고, 터미널 명령을 실행하는 능력을 가지고 있다. 하지만 외부 웹 서비스나 데이터베이스에 직접 접근하는 능력은 제한적이다. 예를 들어 "최신 TP53 관련 논문을 찾아줘"라고 요청하면, Claude는 자신의 학습 데이터에 있는 정보만 활용할 수 있다. 학습 이후에 발표된 논문은 알 수 없다.

MCP 서버를 연결하면 이 한계가 해소된다. PubMed MCP 서버를 추가하면 Claude가 실시간으로 PubMed를 검색할 수 있고, bioRxiv MCP 서버를 추가하면 최신 프리프린트를 직접 조회할 수 있다.

### MCP 이전 vs 이후

| | MCP 없이 | MCP 사용 |
|---|---|---|
| **논문 검색** | 사용자가 PubMed에서 검색 → 결과를 복사 → Claude에 붙여넣기 | Claude가 직접 PubMed 검색 → 결과 분석 |
| **단백질 정보** | UniProt 웹사이트에서 조회 → 복사 → 붙여넣기 | Claude가 UniProt API로 직접 조회 → 해석 |
| **프리프린트** | bioRxiv 사이트에서 검색 → PDF 다운로드 → Claude에 전달 | Claude가 bioRxiv 검색 → 초록 분석 → 요약 |

MCP를 사용하면 **사람이 중간 다리 역할을 할 필요 없이**, Claude가 데이터를 직접 가져와서 분석할 수 있다. 이것이 12~13장에서 다루는 "도메인 지식 조사 자동화"의 핵심 기술이다.

### MCP의 구조

MCP는 클라이언트-서버 구조로 동작한다. Claude Code가 **MCP 클라이언트** 역할을 하고, 각 외부 서비스에 대한 **MCP 서버**가 데이터를 제공한다.

```
Claude Code (MCP 클라이언트)
    ├── bioRxiv MCP 서버  →  bioRxiv API
    ├── PubMed MCP 서버   →  PubMed API
    ├── UniProt MCP 서버  →  UniProt API
    └── fetch MCP 서버    →  임의의 웹 URL
```

MCP 서버는 Claude Code가 시작할 때 함께 실행되고, Claude가 필요할 때 호출한다. 사용자가 "bioRxiv에서 논문 찾아줘"라고 말하면, Claude는 bioRxiv MCP 서버를 통해 검색을 수행하고 결과를 사용자에게 보여준다.

![MCP 개념도 — Claude Code가 여러 MCP 서버를 통해 외부 서비스에 접근하는 구조](../assets/ch11-01-mcp-concept.png)

## 11.2 Claude Code에서 MCP 서버 설정하기

### 설정 파일 위치

Claude Code의 MCP 서버 설정은 두 곳에 추가할 수 있다:

- **프로젝트 설정**: `.claude/settings.json` — 해당 프로젝트에서만 사용
- **전역 설정**: `~/.claude/settings.json` — 모든 프로젝트에서 사용

생명정보학 MCP 서버처럼 여러 프로젝트에서 공통으로 사용할 서버는 전역 설정에, 특정 프로젝트에만 필요한 서버는 프로젝트 설정에 추가하는 것이 좋다.

설정 형식은 다음과 같다:

```json
{
  "mcpServers": {
    "서버이름": {
      "command": "실행할 명령",
      "args": ["인자1", "인자2"],
      "env": {
        "환경변수": "값"
      }
    }
  }
}
```

`command`는 MCP 서버를 실행하는 명령, `args`는 명령에 전달할 인자, `env`는 API 키 등의 환경 변수이다. 대부분의 MCP 서버는 `npx`(Node.js) 또는 `uvx`(Python)로 실행한다.

### bioRxiv MCP 서버 추가

bioRxiv 프리프린트를 검색하고 분석하는 MCP 서버를 추가하는 예시이다:

```json
{
  "mcpServers": {
    "biorxiv": {
      "command": "uvx",
      "args": ["biorxiv-mcp"]
    }
  }
}
```

이 설정을 추가한 후 Claude Code를 재시작하면, Claude가 bioRxiv 프리프린트를 직접 검색하고 분석할 수 있게 된다. "최근 단일세포 RNA-seq 논문 찾아줘"라고 말하면 Claude가 실시간으로 bioRxiv를 검색한다.

### MCP 서버 설치 방법

대부분의 MCP 서버는 **npm** 또는 **pip(uvx)**로 설치할 수 있다:

```bash
# npm으로 설치하는 MCP 서버
npx -y @anthropic/mcp-server-fetch

# uvx(pip)로 설치하는 MCP 서버
uvx biorxiv-mcp
```

`npx`와 `uvx`는 패키지를 임시로 다운로드하고 실행하는 명령이다. 별도의 설치 과정 없이 설정 파일에 등록만 하면, Claude Code가 시작할 때 자동으로 MCP 서버를 실행해 준다.

### 여러 MCP 서버 동시 사용

MCP 서버는 여러 개를 동시에 등록할 수 있다. 생명정보학 연구를 위해 다음과 같이 여러 서버를 조합하면, Claude Code가 논문 검색, 단백질 정보 조회, 웹 데이터 수집을 모두 직접 수행할 수 있다.

```json
{
  "mcpServers": {
    "biorxiv": {
      "command": "uvx",
      "args": ["biorxiv-mcp"]
    },
    "pubmed": {
      "command": "uvx",
      "args": ["pubmed-mcp"]
    },
    "fetch": {
      "command": "npx",
      "args": ["-y", "@anthropic/mcp-server-fetch"]
    }
  }
}
```

`fetch` 서버는 범용 웹 접근 서버로, 특정 MCP 서버가 없는 서비스에도 REST API를 통해 접근할 수 있게 해준다. NCBI Gene, Ensembl, KEGG 등 MCP 서버가 아직 만들어지지 않은 서비스에 유용하다.

![Claude Code 설정에서 MCP 서버를 추가하는 화면](../assets/ch11-02-mcp-settings.png)

## 11.3 MCP 마켓플레이스

### mcpmarket.com

[mcpmarket.com](https://mcpmarket.com/)은 MCP 서버를 찾아 설치할 수 있는 마켓플레이스다. 스마트폰의 앱 스토어처럼, 필요한 기능을 검색하고 설정 방법을 확인할 수 있다.

주요 카테고리:
- **Data Science & ML**: 데이터 분석, 머신러닝 관련 도구
- **Developer Tools**: 개발 생산성 도구
- **Database Management**: 데이터베이스 연동
- **Web Scraping & Data Collection**: 웹 데이터 수집

그 외에도 [mcp.so](https://mcp.so/), [pulsemcp.com](https://www.pulsemcp.com/servers) 등 다양한 MCP 서버 디렉토리가 있다. "bioinformatics", "genomics", "protein" 등으로 검색하면 생명정보학 관련 MCP 서버를 찾을 수 있다.

MCP 생태계는 빠르게 성장하고 있다. 2024년 말 MCP가 공개된 이후 수천 개의 서버가 만들어졌으며, 생명정보학 분야도 예외가 아니다. 자주 사용하는 데이터베이스에 대한 MCP 서버가 아직 없다면, AI에게 "UniProt MCP 서버를 만들어줘"라고 요청하여 직접 만들 수도 있다.

![mcpmarket.com에서 생명정보학 관련 MCP 서버를 검색하는 화면](../assets/ch11-03-mcpmarket.png)

## 11.4 생명정보학 MCP 서버

### bioRxiv / medRxiv

생명과학(bioRxiv)과 의학(medRxiv) 프리프린트를 검색하고 분석할 수 있다. 프리프린트는 동료 심사(peer review)를 거치기 전에 공개된 논문으로, 최신 연구 동향을 가장 빠르게 파악할 수 있는 소스이다.

```text
> 최근 30일간 단일세포 RNA-seq 관련 bioRxiv 프리프린트 찾아줘
> 이 프리프린트의 초록을 요약하고, 사용된 분석 방법을 정리해줘
```

Claude가 bioRxiv API를 통해 최신 프리프린트를 검색하고, 초록을 읽어 분석 방법론을 정리해 준다. 문헌 조사 초기 단계에서 시간을 크게 절약할 수 있다.

### UniProt

UniProt 단백질 데이터베이스에서 직접 정보를 조회할 수 있다. 서열 검색, 기능 도메인 분석, 비교 유전체학 등을 수행할 수 있다.

```text
> TP53 단백질의 기능 도메인과 알려진 변이를 조회해줘
> 이 단백질 서열의 UniProt 정보를 검색해줘
```

10장에서 만든 BLAST 도구와 조합하면 강력한 워크플로우가 만들어진다. BLAST로 유사 서열을 찾고, UniProt MCP로 해당 서열의 기능 정보를 조회하는 과정을 Claude가 연속으로 수행할 수 있다.

### PubMed

의생명 문헌 데이터베이스 PubMed를 검색할 수 있다. 3,600만 건 이상의 논문 메타데이터에 접근할 수 있다.

```text
> CRISPR base editing 관련 최신 논문 10편 찾아줘
> 이 논문들의 주요 발견을 비교 정리해줘
```

PubMed MCP 서버를 사용하면, Claude가 논문을 검색하고 초록을 읽어 핵심 내용을 정리해 준다. "이 논문들 중에서 in vivo 실험 결과가 있는 것만 골라줘" 같은 후속 질문도 가능하다.

### fetch (범용 웹 접근)

특정 MCP 서버가 없는 서비스도 범용 fetch 서버로 접근할 수 있다. NCBI, Ensembl 등의 REST API를 직접 호출할 수 있어, MCP 서버가 아직 만들어지지 않은 서비스에도 접근이 가능하다.

```text
> NCBI Gene에서 BRCA1 유전자 정보를 가져와줘
> Ensembl REST API로 이 유전자의 오솔로그를 조회해줘
```

fetch 서버는 만능 도구이지만, 전용 MCP 서버보다는 사용이 불편할 수 있다. 전용 서버는 해당 서비스에 최적화된 인터페이스를 제공하지만, fetch 서버는 사용자가 API 엔드포인트를 어느 정도 알고 있어야 한다. 그래도 "NCBI Gene에서 BRCA1 정보를 가져와줘"라는 자연어 요청만으로 Claude가 적절한 API를 찾아 호출하는 경우가 많다.

### MCPmed

학술지 *Briefings in Bioinformatics*에 발표된 MCPmed는 기존 생명정보학 웹 서비스(GEO, STRING, UCSC Cell Browser 등)에 MCP 계층을 추가하여 LLM이 직접 활용할 수 있게 만든 시스템이다.

```text
> GEO에서 pancreatic cancer 관련 scRNA-seq 데이터셋을 검색해줘
> STRING에서 TP53과 상호작용하는 단백질 네트워크를 조회해줘
```

MCPmed는 학술 연구로서 생명정보학과 AI의 접점을 보여주는 좋은 사례이다. 이런 방식이 널리 퍼지면, 대부분의 생명정보학 데이터베이스를 Claude Code에서 자연어로 조회하고 분석하는 것이 일상이 될 수 있다.

## 11.5 Claude for Life Sciences

Anthropic은 2025년 10월 **Claude for Life Sciences**를 발표하며 생명과학 분야에 특화된 기능을 제공하기 시작했다. 이는 생명과학이 AI 활용 가능성이 높은 분야라는 인식을 반영한 것이다.

### Scientific Connectors

생명과학 분야의 주요 플랫폼과 연동할 수 있는 커넥터가 있다:

| 커넥터 | 용도 |
|--------|------|
| **Benchling** | 실험 데이터 관리, 전자 실험 노트북 |
| **BioRender** | 과학 일러스트 및 논문 그림 제작 |
| **PubMed** | 의생명 문헌 검색 |
| **10x Genomics** | 단일세포/공간 전사체 분석 |
| **Synapse.org** | 협업 기반 데이터 분석 플랫폼 |
| **ClinicalTrials.gov** | 임상시험 데이터 |
| **ChEMBL** | 약물-표적 상호작용 데이터베이스 |
| **bioRxiv / medRxiv** | 프리프린트 서버 |

이 커넥터들은 MCP 서버와 유사한 역할을 하지만, Anthropic이 공식적으로 관리하고 최적화한 것이라 안정성과 성능이 더 높을 수 있다.

### Agent Skills

Claude for Life Sciences에는 생명정보학 전용 **에이전트 스킬**도 포함되어 있다. 예를 들어 `single-cell-rna-qc` 스킬은 scverse 모범 사례에 따라 RNA 시퀀싱 데이터의 품질 관리와 필터링을 도와준다. 5장에서 배운 QC 과정을 Claude가 더 정확하게 수행할 수 있도록 전문 지식이 내장되어 있다.

### 주요 활용 분야

- **문헌 리뷰와 가설 생성**: 수천 편의 논문을 빠르게 검토하고 연구 방향 제안
- **프로토콜 작성**: 실험 프로토콜, SOP(표준 운영 절차), 동의서 초안 작성
- **생명정보학 분석**: 유전체 데이터 분석 코드 작성 및 결과 해석
- **규제 문서**: 임상시험 관련 규제 문서 초안 작성

> **참고**: Claude for Life Sciences의 일부 기능은 기업용(Enterprise) 플랜에서만 쓸 수 있다. 개인 사용자도 MCP 서버를 조합하면 비슷한 워크플로우를 구현할 수 있다. 이 책에서 다루는 MCP 설정 방법이 그 기반이 된다.

## 11.6 MCP와 바이브 코딩

MCP는 바이브 코딩의 가능성을 크게 확장한다. 10장까지는 "사람이 도메인 지식을 알아야 AI에게 정확한 지시를 내릴 수 있다"고 했다. MCP를 사용하면 여기서 한 발 더 나아간다.

예를 들어, BLAST 도구를 만들 때 "E-value가 뭔지, FASTA 형식이 어떻게 생겼는지" 같은 도메인 지식이 필요했다. MCP가 연결된 Claude Code에게는 이렇게 요청할 수 있다:

```text
> bioRxiv에서 최신 BLAST 웹 도구 관련 논문을 찾아보고,
> 다른 연구자들이 BLAST 웹 인터페이스를 어떻게 구현했는지 조사해줘.
> 그 내용을 바탕으로 우리 BLAST 도구의 UI를 개선해줘.
```

Claude가 논문을 검색하고, 분석하고, 그 결과를 코드에 반영하는 과정을 하나의 대화에서 처리할 수 있다. 12~13장에서는 이 패턴을 본격적으로 활용하여, 도메인 지식 조사부터 구현까지 AI에게 맡기는 방법을 다룬다.

## 11.7 정리

- **MCP(Model Context Protocol)**: Claude Code에 외부 도구와 데이터 소스를 연결하는 표준 프로토콜
  - 사람이 중간 다리 역할을 하지 않아도 Claude가 직접 데이터에 접근
- **설정 방법**: `.claude/settings.json`의 `mcpServers`에 서버 정보를 등록
  - 프로젝트별 또는 전역으로 설정 가능
  - `npx` 또는 `uvx`로 실행
- **MCP 마켓플레이스**: mcpmarket.com 등에서 필요한 MCP 서버를 검색하고 설치
  - 생태계가 빠르게 성장 중
- **생명정보학 MCP 서버**: bioRxiv, PubMed, UniProt 등 주요 데이터베이스에 Claude가 직접 접근
  - 범용 fetch 서버로 MCP가 없는 서비스에도 접근 가능
- **Claude for Life Sciences**: Scientific Connectors와 Agent Skills로 생명과학 연구 지원
- **핵심**: MCP 서버를 조합하면 Claude Code를 **생명정보학 연구 도우미**로 확장할 수 있다. 12~13장에서는 이를 활용해 도메인 지식 조사까지 자동화한다
