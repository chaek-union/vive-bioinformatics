# 11장. MCP와 Claude for Life Sciences

## 11.1 MCP란?

**MCP(Model Context Protocol)**는 AI 에이전트가 외부 도구와 데이터 소스에 접근할 수 있게 해주는 표준 프로토콜이다. Claude Code에 MCP 서버를 연결하면, Claude가 데이터베이스 조회, API 호출, 파일 시스템 접근 등 다양한 작업을 직접 수행할 수 있다.

쉽게 말해, MCP는 **Claude Code에 새로운 능력을 플러그인처럼 추가하는 방법**이다.

### MCP 이전 vs 이후

| | MCP 없이 | MCP 사용 |
|---|---|---|
| **논문 검색** | 사용자가 PubMed에서 검색 → 결과를 복사 → Claude에 붙여넣기 | Claude가 직접 PubMed 검색 → 결과 분석 |
| **단백질 정보** | UniProt 웹사이트에서 조회 → 복사 → 붙여넣기 | Claude가 UniProt API로 직접 조회 → 해석 |
| **프리프린트** | bioRxiv 사이트에서 검색 → PDF 다운로드 → Claude에 전달 | Claude가 bioRxiv 검색 → 초록 분석 → 요약 |

MCP를 사용하면 **사람이 중간 다리 역할을 할 필요 없이**, Claude가 데이터를 직접 가져와서 분석할 수 있다.

![MCP 개념도 — Claude Code가 여러 MCP 서버를 통해 외부 서비스에 접근하는 구조](../assets/ch11-01-mcp-concept.png)

## 11.2 Claude Code에서 MCP 서버 설정하기

### 설정 파일 위치

Claude Code의 MCP 서버 설정은 프로젝트 디렉토리의 `.claude/settings.json` 또는 전역 설정 `~/.claude/settings.json`에 추가한다.

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

### bioRxiv MCP 서버 추가

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

이 설정을 추가한 후 Claude Code를 재시작하면, Claude가 bioRxiv 프리프린트를 직접 검색하고 분석할 수 있게 된다.

### MCP 서버 설치 방법

대부분의 MCP 서버는 **npm** 또는 **pip(uvx)**로 설치할 수 있다:

```bash
# npm으로 설치하는 MCP 서버
npx -y @anthropic/mcp-server-fetch

# uvx(pip)로 설치하는 MCP 서버
uvx biorxiv-mcp
```

설정 파일에 등록만 하면, Claude Code가 시작할 때 자동으로 MCP 서버를 실행해 준다.

### 여러 MCP 서버 동시 사용

MCP 서버는 여러 개를 동시에 등록할 수 있다. 생명정보학 작업을 위해 다음과 같이 여러 서버를 조합할 수 있다:

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

![Claude Code 설정에서 MCP 서버를 추가하는 화면](../assets/ch11-02-mcp-settings.png)

## 11.3 MCP 마켓플레이스

### mcpmarket.com

[mcpmarket.com](https://mcpmarket.com/)은 MCP 서버를 찾아 설치할 수 있는 마켓플레이스다. 카테고리별로 정리되어 있어 필요한 도구를 쉽게 찾을 수 있다.

주요 카테고리:
- **Data Science & ML**: 데이터 분석, 머신러닝 관련 도구
- **Developer Tools**: 개발 생산성 도구
- **Database Management**: 데이터베이스 연동
- **Web Scraping & Data Collection**: 웹 데이터 수집

그 외에도 [mcp.so](https://mcp.so/), [pulsemcp.com](https://www.pulsemcp.com/servers) 등 다양한 MCP 서버 디렉토리가 있다. "bioinformatics", "genomics", "protein" 등으로 검색하면 생명정보학 관련 MCP 서버를 찾을 수 있다.

![mcpmarket.com에서 생명정보학 관련 MCP 서버를 검색하는 화면](../assets/ch11-03-mcpmarket.png)

## 11.4 생명정보학 MCP 서버

### bioRxiv / medRxiv

생명과학(bioRxiv)과 의학(medRxiv) 프리프린트를 검색하고 분석할 수 있다.

```text
> 최근 30일간 단일세포 RNA-seq 관련 bioRxiv 프리프린트 찾아줘
> 이 프리프린트의 초록을 요약하고, 사용된 분석 방법을 정리해줘
```

### UniProt

UniProt 단백질 데이터베이스에서 직접 정보를 조회할 수 있다. 서열 검색, 기능 도메인 분석, 비교 유전체학 등을 수행할 수 있다.

```text
> TP53 단백질의 기능 도메인과 알려진 변이를 조회해줘
> 이 단백질 서열의 UniProt 정보를 검색해줘
```

### PubMed

의생명 문헌 데이터베이스 PubMed를 검색할 수 있다.

```text
> CRISPR base editing 관련 최신 논문 10편 찾아줘
> 이 논문들의 주요 발견을 비교 정리해줘
```

### fetch (범용 웹 접근)

특정 MCP 서버가 없는 서비스도 범용 fetch 서버로 접근할 수 있다. NCBI, Ensembl 등의 REST API를 직접 호출할 수 있다.

```text
> NCBI Gene에서 BRCA1 유전자 정보를 가져와줘
> Ensembl REST API로 이 유전자의 오솔로그를 조회해줘
```

### MCPmed

학술지 *Briefings in Bioinformatics*에 발표된 MCPmed는 기존 생명정보학 웹 서비스(GEO, STRING, UCSC Cell Browser 등)에 MCP 계층을 추가하여 LLM이 직접 활용할 수 있게 만든 시스템이다.

```text
> GEO에서 pancreatic cancer 관련 scRNA-seq 데이터셋을 검색해줘
> STRING에서 TP53과 상호작용하는 단백질 네트워크를 조회해줘
```

이런 방식이 널리 퍼지면, 대부분의 생명정보학 데이터베이스를 Claude Code에서 자연어로 조회하고 분석할 수 있게 될 것이다.

## 11.5 Claude for Life Sciences

Anthropic은 2025년 10월 **Claude for Life Sciences**를 발표하며 생명과학 분야에 특화된 기능을 제공하기 시작했다.

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

### Agent Skills

Claude for Life Sciences에는 생명정보학 전용 **에이전트 스킬**도 포함되어 있다. 예를 들어 `single-cell-rna-qc` 스킬은 scverse 모범 사례에 따라 RNA 시퀀싱 데이터의 품질 관리와 필터링을 도와준다.

### 주요 활용 분야

- **문헌 리뷰와 가설 생성**: 수천 편의 논문을 빠르게 검토하고 연구 방향 제안
- **프로토콜 작성**: 실험 프로토콜, SOP(표준 운영 절차), 동의서 초안 작성
- **생명정보학 분석**: 유전체 데이터 분석 코드 작성 및 결과 해석
- **규제 문서**: 임상시험 관련 규제 문서 초안 작성

> **참고**: Claude for Life Sciences의 일부 기능은 기업용(Enterprise) 플랜에서만 쓸 수 있다. 개인 사용자도 MCP 서버를 조합하면 비슷한 워크플로우를 만들 수 있다.

## 11.6 정리

- **MCP(Model Context Protocol)**: Claude Code에 외부 도구와 데이터 소스를 연결하는 표준 프로토콜
- **설정 방법**: `.claude/settings.json`의 `mcpServers`에 서버 정보를 등록
- **MCP 마켓플레이스**: mcpmarket.com 등에서 필요한 MCP 서버를 검색하고 설치
- **생명정보학 MCP 서버**: bioRxiv, PubMed, UniProt 등 주요 데이터베이스에 Claude가 직접 접근
- **Claude for Life Sciences**: Scientific Connectors와 Agent Skills로 생명과학 연구 지원
- **핵심**: MCP 서버를 조합하면 Claude Code를 **생명정보학 연구 도우미**로 확장할 수 있다
