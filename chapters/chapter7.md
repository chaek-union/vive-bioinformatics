# 7장. 생명정보 웹 툴 제작 기초

## 7.1 웹 도구, 아직 만들어야 하는가?

생명정보학에서 웹 도구는 오랫동안 **접근성의 관문** 역할을 해왔다. NCBI BLAST, Ensembl Genome Browser, Galaxy, UCSC Genome Browser — 이 도구들은 명령줄에 익숙하지 않은 실험 생물학자도 시퀀스 검색, 게놈 탐색, 분석 파이프라인 실행을 할 수 있게 만들었다. 웹 도구가 없었다면, 생명정보학은 소수의 전문 프로그래머만 접근할 수 있는 분야로 남았을 것이다.

그러나 이 장에서 웹 도구를 만들기 전에, 한 가지 질문을 던져야 한다. **AI 시대에도 웹 도구를 만드는 것이 여전히 의미가 있는가?**

웹(World Wide Web)은 30년 넘게 정보 공유의 기본 플랫폼이었다. 그러나 최근 **Web 4.0** — AI가 주도하는 지능형 웹 — 에 대한 논의가 활발해지면서, 전통적인 웹 인터페이스의 미래에 의문이 제기되고 있다. 이미 인터넷 트래픽의 상당 부분이 사람이 아닌 봇에 의해 발생하고 있으며, 이 비중은 계속 증가하고 있다. MCP와 같은 프로토콜이 보편화되면, AI 에이전트가 웹 브라우저를 거치지 않고 데이터와 서비스에 직접 접근하는 시대가 올 것이다. 그때 화려한 UI와 반응형 디자인은 더 이상 중요하지 않을 수 있다.

그럼에도 불구하고, 웹 도구를 배우는 것에는 몇 가지 현실적인 이유가 있다:

- **시각화와 인터랙션**: BLAST 검색 결과를 표로 보여주고, 시퀀스 정렬을 시각화하고, 파라미터를 조정하며 분석을 반복하는 작업은 여전히 GUI가 효과적이다. 터미널에서 텍스트로만 작업하는 것보다 직관적이다.
- **공유와 접근성**: 웹 도구가 생명정보학의 접근성을 높여온 것처럼, 직접 만든 분석 도구도 "이 URL을 열어보세요" 한마디로 동료와 공유할 수 있다.
- **풀스택 이해**: 프론트엔드, 백엔드, 데이터베이스, API를 아우르는 웹 개발 경험은 MCP 서버를 만들거나 AI 에이전트의 도구를 설계할 때도 직접적으로 활용된다.
- **바이브 코딩 실습**: 웹 개발은 AI 에이전트와 협업하는 훈련장으로 적합하다. 결과물이 눈에 보이므로 AI가 생성한 코드의 품질을 즉시 판단할 수 있다.

결론적으로, 웹 인터페이스 자체의 중요성은 점차 줄어들겠지만, **웹 도구를 만드는 과정에서 익히는 기술과 사고방식**은 AI 시대에도 유효하다. 이 장에서는 생명정보학 웹 도구의 기반이 되는 프로젝트를 구성한다.

## 7.2 이 책에서 사용하는 기술 스택

이 책에서는 다음과 같은 기술 스택을 사용하여 생명정보학 웹 도구를 개발한다.

| 기술 | 역할 | 설명 |
|------|------|------|
| **SvelteKit** | 프론트엔드 프레임워크 | 빠르고 가벼운 웹 앱 개발 프레임워크 |
| **Tailwind CSS** | CSS 프레임워크 | 유틸리티 클래스 기반의 스타일링 도구 |
| **PostgreSQL** | 데이터베이스 | 오픈소스 관계형 데이터베이스 |
| **Docker** | 컨테이너 | 개발 환경 통합 관리 |

이 조합을 선택한 이유는 바이브 코딩에 적합하기 때문이다. SvelteKit은 코드량이 적어 AI가 생성하고 수정하기 쉽고, Tailwind CSS는 스타일 정보가 HTML과 같은 파일에 있어 AI가 맥락을 파악하기 용이하다. PostgreSQL은 안정적이고 널리 사용되는 데이터베이스이므로 AI가 정확한 SQL을 생성할 확률이 높다.

### SvelteKit

SvelteKit은 Svelte를 기반으로 한 풀스택 웹 프레임워크이다. "풀스택"이란 프론트엔드(사용자가 보는 화면)와 백엔드(서버 로직, 데이터베이스 연동)를 하나의 프레임워크에서 모두 처리할 수 있다는 뜻이다.

React, Vue 등 다른 프레임워크와 비교했을 때 다음과 같은 장점이 있다:

- **컴파일 타임 최적화**: React는 브라우저에서 가상 DOM을 비교하며 화면을 갱신하지만, Svelte는 빌드 시점에 최적화된 JavaScript로 변환된다. 덕분에 런타임 오버헤드가 없어 매우 빠르다.
- **적은 코드량**: 동일한 기능을 React보다 30~40% 적은 코드로 구현할 수 있다. 이는 AI가 생성하는 코드량이 줄어든다는 뜻이기도 하다. 코드가 짧으면 에러가 날 가능성도 줄어든다.
- **서버 사이드 렌더링(SSR)** 기본 지원: 페이지를 서버에서 미리 렌더링하여 브라우저에 전달하므로, 첫 화면 로딩이 빠르다.
- **파일 기반 라우팅**: 파일 구조가 곧 URL 구조이다. `src/routes/tools/blast/+page.svelte` 파일을 만들면 자동으로 `/tools/blast` URL이 생긴다. URL 설정을 위한 별도 파일이 필요 없다.

![SvelteKit 공식 웹사이트](../assets/ch7-01-sveltekit-website.png)

SvelteKit에서 페이지는 크게 세 가지 파일로 구성된다:

| 파일 | 역할 | 실행 위치 |
|------|------|-----------|
| `+page.svelte` | 사용자에게 보이는 화면 (HTML + CSS + JS) | 브라우저 |
| `+page.server.ts` | 데이터 로딩, 폼 처리 등 서버 로직 | 서버 |
| `+server.ts` | REST API 엔드포인트 | 서버 |

예를 들어 BLAST 검색 도구를 만든다면, `+page.svelte`에는 시퀀스 입력 폼과 결과 테이블을, `+page.server.ts`에는 BLAST+ 명령 실행과 결과 파싱 로직을 작성한다. 이 구분을 이해하고 있으면, AI에게 "서버 로직에 BLAST 검색 기능 추가해줘"라고 구체적으로 요청할 수 있다.

### Tailwind CSS

Tailwind CSS는 유틸리티 클래스 기반의 CSS 프레임워크이다. 전통적인 CSS에서는 별도의 스타일시트 파일을 만들고 클래스 이름을 정의한 후 스타일을 작성한다. Tailwind는 이 과정을 생략하고, 미리 정의된 유틸리티 클래스를 HTML 요소에 직접 적용한다.

```html
<!-- 전통적인 CSS 방식 -->
<div class="card">제목</div>

<!-- Tailwind CSS 방식 -->
<div class="bg-white rounded-lg shadow-md p-6">제목</div>
```

Tailwind 방식에서 `bg-white`는 흰색 배경, `rounded-lg`는 둥근 모서리, `shadow-md`는 중간 크기 그림자, `p-6`은 패딩 1.5rem을 의미한다. 클래스 이름만 보면 어떤 스타일이 적용되는지 알 수 있다.

AI 에이전트와 함께 사용하기에 특히 적합한데, 스타일이 HTML과 같은 파일에 있어 컨텍스트를 파악하기 쉽기 때문이다. 전통적인 CSS에서는 AI가 HTML 파일과 CSS 파일을 모두 읽어야 전체 디자인을 이해할 수 있지만, Tailwind에서는 HTML 파일 하나만 읽으면 된다.

![Tailwind CSS 공식 웹사이트](../assets/ch7-02-tailwindcss-website.png)

### PostgreSQL

PostgreSQL은 세계에서 가장 많이 사용되는 오픈소스 관계형 데이터베이스이다. "관계형"이란 데이터를 표(테이블) 형태로 저장하고, 테이블 간의 관계를 정의할 수 있다는 뜻이다.

이 책에서 PostgreSQL은 다음과 같은 용도로 사용된다:
- **분석 결과 저장**: BLAST 검색 결과, 차등 발현 분석 결과 등
- **사용자 데이터 관리**: 사용자가 업로드한 시퀀스 정보
- **작업 이력 추적**: 누가 언제 어떤 분석을 실행했는지

SQLite 같은 가벼운 데이터베이스도 있지만, PostgreSQL을 선택한 이유는 실무 환경과 동일한 경험을 제공하기 위해서이다. Docker를 통해 실행하므로 설치가 번거롭지도 않다.

## 7.3 프로젝트 생성

### Node.js 설치

SvelteKit은 JavaScript/TypeScript 기반이므로 Node.js가 필요하다. Node.js는 브라우저 밖에서 JavaScript를 실행할 수 있게 해주는 런타임이다. 다음 웹사이트에서 설치한다:

https://nodejs.org/en/download

설치 시 운영체제를 선택하는 화면이 나오는데, **Windows 사용자도 WSL 환경에서 개발하므로 Linux를 선택**해야 한다. 그다음 **nvm**(Node Version Manager)과 **pnpm**(패키지 매니저)을 선택한 후 터미널에 표시되는 명령을 입력한다. nvm은 여러 버전의 Node.js를 설치하고 전환할 수 있게 해주는 도구이다. 프로젝트마다 다른 Node.js 버전이 필요할 수 있으므로, 직접 설치보다 nvm을 통한 설치가 권장된다.

pnpm은 npm의 대안으로, 디스크 공간을 절약하고 설치 속도가 빠르다. 여러 프로젝트에서 같은 패키지를 사용할 때, pnpm은 패키지를 한 번만 다운로드하고 심볼릭 링크로 연결한다.

![Node.js 다운로드 페이지](../assets/ch7-03-nodejs-nvm-pnpm.png)

설치 확인:

```bash
node --version
pnpm --version
```

### SvelteKit 프로젝트 초기화

터미널에서 다음 명령을 실행하여 SvelteKit 프로젝트를 생성한다:

```bash
npx sv create my-bioinfo-app
```

CLI가 대화형으로 옵션을 물어본다. 다음과 같이 선택한다:

| 질문 | 선택 |
|------|------|
| Which template would you like? | **SvelteKit minimal** |
| Add type checking with TypeScript? | **Yes, using TypeScript syntax** |
| What would you like to add to your project? | **tailwindcss, sveltekit-adapter, paraglide** |
| tailwindcss: Which plugins? | **typography, forms** |
| sveltekit-adapter: Which adapter? | **node** |
| paraglide: Which languages? | **en, ko** |
| paraglide: Include a demo? | **No** |
| Which package manager? | **pnpm** |

프로젝트 생성이 완료되면, VS Code에서 **파일 → 폴더 열기**로 `my-bioinfo-app` 디렉토리를 연다.

각 옵션의 역할을 간단히 정리하면:

- **TypeScript**: JavaScript에 타입 시스템을 추가한 언어. AI가 생성하는 코드에서도 타입이 있으면 잘못된 데이터 사용을 컴파일 시점에 잡아낼 수 있다.
- **Tailwind CSS**: 유틸리티 클래스 기반 CSS 프레임워크. Typography 플러그인은 마크다운 렌더링 시 보기 좋은 기본 스타일을, Forms 플러그인은 폼 요소에 기본 스타일을 적용한다.
- **sveltekit-adapter (node)**: SvelteKit 앱을 Node.js 서버로 배포할 수 있게 해주는 어댑터.
- **Paraglide**: 다국어(i18n) 지원 라이브러리. 영어(en)와 한국어(ko)를 지원하도록 설정한다.

> **참고**: 프로젝트 초기 생성은 AI 에이전트에 맡기기보다 직접 수행하는 것이 좋다. AI는 초기화 명령을 사용하기보다 기존 코드를 직접 작성하려는 경향이 있어, 최신 버전이 아닌 코드를 생성할 수 있다. 프로젝트 뼈대를 공식 CLI로 먼저 만들고, 그 위에서 AI에게 기능 추가를 요청하는 것이 안전하다.

### Tailwind CSS

Tailwind CSS는 SvelteKit 프로젝트 생성 시 옵션으로 함께 설치할 수 있다. 별도로 수동 설치할 필요 없이, 프로젝트 초기화 과정에서 Tailwind CSS를 선택하면 자동으로 설정된다. Typography 플러그인은 마크다운 렌더링 시 보기 좋은 기본 스타일을 제공하고, Forms 플러그인은 폼 요소(입력 필드, 버튼 등)에 기본 스타일을 적용해 준다.

## 7.4 Docker 환경 구성

### compose.yml과 Dockerfile 생성

3장에서 배운 Docker Compose를 실전에 적용한다. Claude Code에게 다음과 같이 요청한다:

> "SvelteKit 앱과 PostgreSQL 데이터베이스를 Docker Compose로 구성해줘. compose.yml과 compose.dev.yml을 만들어줘. compose.yml은 배포용이고, compose.dev.yml은 개발용으로 볼륨 마운트와 핫 리로드를 설정해줘. Dockerfile도 함께 만들어줘."

이때 **compose.yml**(배포용)과 **compose.dev.yml**(개발용)을 분리하는 것이 좋다. 개발 환경에서는 코드 변경이 즉시 반영되어야 하지만, 배포 환경에서는 최적화된 빌드 결과만 실행하면 된다. 두 파일을 분리해 두면, 개발할 때는 `docker compose -f compose.dev.yml up`으로, 배포할 때는 `docker compose up`으로 실행할 수 있다.

Claude Code가 생성하는 파일의 주요 설정을 이해해 두면, 결과를 검토하고 수정 요청을 내리기 쉽다:

| 설정 | 의미 |
|------|------|
| `volumes: - .:/app` | 로컬 코드를 컨테이너에 연결. 코드 수정 시 즉시 반영 |
| `/app/node_modules` | 컨테이너 자체의 node_modules 사용. 호스트와 충돌 방지 |
| `depends_on: - db` | db를 먼저 시작한 후 app 시작 |
| `pgdata:/var/lib/postgresql/data` | 컨테이너 삭제해도 데이터베이스 보존 |
| `DATABASE_URL` | 컨테이너 간 네트워크 주소. `db`는 서비스 이름 |

Dockerfile에서는 3장에서 배운 레이어 캐싱 원리가 적용된다. `package.json`을 먼저 복사하고 의존성을 설치한 뒤 소스 코드를 복사하면, 소스 코드만 변경되었을 때 의존성 설치 단계를 건너뛸 수 있다. `--host` 플래그는 Docker 컨테이너 내부 서버에 호스트에서 접속하기 위해 필요하다.

## 7.5 환경 변수 (.env)

프로젝트에서 데이터베이스 접속 정보, API 키 등 민감한 설정값은 **환경 변수**로 관리한다. 코드에 비밀번호를 직접 쓰면 Git에 커밋될 위험이 있다. 환경 변수를 사용하면 코드와 설정을 분리할 수 있다.

> **중요**: `.env` 파일은 **반드시 사용자가 직접 작성해야 한다**. `.env`에는 데이터베이스 비밀번호, API 키 등 민감한 정보가 포함되므로, **Claude Code가 이 파일을 읽거나 생성하게 해서는 안 된다**. 글로벌 `CLAUDE.md`(`~/.claude/CLAUDE.md`)에 다음과 같이 추가하여, AI 에이전트가 `.env` 파일에 접근하지 못하도록 설정하는 것을 권장한다:
>
> ```
> # 보안 규칙
> - .env 파일을 절대 읽거나 수정하지 말 것
> - .env.example만 참조할 것
> ```

프로젝트 루트에 `.env` 파일을 직접 생성한다:

```env
DATABASE_URL=postgresql://postgres:postgres@localhost:5432/bioinfo
PUBLIC_SITE_NAME=My Bioinfo App
```

SvelteKit에서 환경 변수는 두 가지로 나뉜다:

| 접두사 | 접근 범위 | 용도 |
|--------|-----------|------|
| `PUBLIC_` | 서버 + 클라이언트 | 사이트 이름 등 공개 정보 |
| (없음) | 서버만 | 데이터베이스 비밀번호, API 키 등 민감 정보 |

`PUBLIC_` 접두사가 없는 환경 변수는 서버 코드(`+page.server.ts`, `+server.ts`)에서만 접근할 수 있다. 브라우저에서 실행되는 코드에서는 읽을 수 없으므로, 데이터베이스 비밀번호나 API 키가 사용자에게 노출되지 않는다. 이 구분은 보안상 중요하다.

`.env` 파일은 `.gitignore`에 반드시 추가하여 Git에 커밋되지 않도록 한다. 대신 `.env.example` 파일을 만들어 어떤 환경 변수가 필요한지 안내한다. `.env.example`에는 실제 값 대신 템플릿을 넣는다. 이 파일은 Claude Code에게 생성을 요청해도 된다.

> "`.env.example` 파일을 만들어줘. DATABASE_URL과 PUBLIC_SITE_NAME이 필요해."

## 7.6 프로젝트 디렉토리 구조

최종적으로 프로젝트 디렉토리는 다음과 같은 구조를 가진다:

```
my-bioinfo-app/
├── compose.yml      # Docker 서비스 정의
├── Dockerfile              # Docker 이미지 정의
├── .env                    # 환경 변수 (Git에 포함하지 않음)
├── .env.example            # 환경 변수 템플릿
├── .gitignore
├── package.json
├── pnpm-lock.yaml
├── svelte.config.js        # SvelteKit 설정
├── vite.config.ts          # Vite 빌드 도구 설정
├── tailwind.config.ts      # Tailwind CSS 설정
├── CLAUDE.md               # AI 에이전트 참조용 프로젝트 명세
├── README.md
├── src/
│   ├── app.css             # 글로벌 스타일 (Tailwind 임포트)
│   ├── app.html            # HTML 템플릿
│   ├── lib/                # 공유 라이브러리
│   │   ├── components/     # 재사용 가능한 컴포넌트
│   │   └── server/         # 서버 전용 코드 (DB 연결 등)
│   └── routes/             # 페이지 라우트
│       ├── +layout.svelte  # 공통 레이아웃
│       ├── +page.svelte    # 랜딩 페이지
│       └── tools/
│           └── +page.svelte  # 도구 페이지
└── static/                 # 정적 파일 (이미지, 폰트 등)
```

이 구조에서 가장 중요한 폴더는 `src/routes/`이다. SvelteKit의 파일 기반 라우팅에 의해, 이 폴더의 구조가 곧 웹사이트의 URL 구조가 된다. `src/routes/tools/blast/+page.svelte`를 만들면 `/tools/blast`라는 URL이 자동으로 생긴다. AI에게 새 페이지를 추가해 달라고 할 때, 이 규칙을 알면 "tools 폴더 아래에 blast 페이지를 만들어줘"라고 구체적으로 지시할 수 있다.

`src/lib/` 폴더는 여러 페이지에서 공유하는 코드를 넣는 곳이다. 예를 들어 네비게이션 바 컴포넌트는 모든 페이지에서 사용하므로 `src/lib/components/NavBar.svelte`에 둔다. `src/lib/server/`에는 데이터베이스 연결 코드처럼 서버에서만 사용하는 코드를 둔다.

### CLAUDE.md 작성

`CLAUDE.md`는 AI 에이전트가 참조하는 프로젝트 명세 파일이다. Claude Code는 작업을 시작할 때 이 파일을 가장 먼저 읽고, 프로젝트의 구조와 규칙을 파악한다.

**핵심 원칙: CLAUDE.md에 정보를 많이 넣을수록 AI는 더 똑똑해진다.**

AI 코딩 에이전트는 사람과 달리 프로젝트의 맥락을 스스로 알지 못한다. "Navbar에 로고를 추가해줘"라고 요청했을 때, AI가 올바른 위치에 올바른 방식으로 코드를 작성하려면 다음을 알아야 한다:

- 이 프로젝트가 SvelteKit을 사용하는지, React를 사용하는지
- Navbar 컴포넌트가 어디에 있는지
- 스타일링에 Tailwind CSS를 쓰는지, 일반 CSS를 쓰는지
- 로고 이미지는 어디에 저장되어 있는지

이러한 정보가 CLAUDE.md에 없으면 AI는 매번 프로젝트 구조를 탐색하고 추측해야 한다. 반면, 이 정보가 잘 정리되어 있으면 AI는 곧바로 정확한 코드를 작성할 수 있다.

#### 왜 배경지식이 필요한가?

바이브 코딩은 "AI가 다 해주니까 개발을 몰라도 된다"는 뜻이 **아니다**. 오히려 그 반대이다. CLAUDE.md를 잘 작성하려면 **사람이 먼저 프로젝트를 이해하고 있어야 한다**.

예를 들어 이런 CLAUDE.md를 작성한다고 하자:

```markdown
# 프로젝트 개요
생명정보학 웹 도구 모음 사이트

# 기술 스택
- SvelteKit (프론트엔드 + 서버)
- Tailwind CSS (스타일링)
- PostgreSQL (데이터베이스)

# 디렉토리 구조
- src/routes/ : 페이지 라우트 (파일 기반 라우팅)
- src/lib/components/ : 재사용 가능한 UI 컴포넌트
- src/lib/server/ : 서버 전용 코드 (DB 연결 등)

# 코딩 컨벤션
- 컴포넌트 파일명은 PascalCase (예: NavBar.svelte)
- 서버 API는 +server.ts 파일에 작성
- 환경 변수는 $env/static/private 또는 $env/static/public 사용

# 디자인 가이드라인
- 색상: blue-600을 primary color로 사용
- 레이아웃: 최대 너비 max-w-7xl, 중앙 정렬
- 반응형: mobile-first 접근
```

이 파일을 작성하려면 SvelteKit의 파일 기반 라우팅이 무엇인지, Tailwind의 유틸리티 클래스가 어떻게 동작하는지, 컴포넌트와 레이아웃의 개념이 무엇인지 알아야 한다. **이 책에서 배우는 웹 개발 배경지식이 바로 이를 위한 것이다.**

> **핵심**: 바이브 코딩에서 사람의 역할은 코드를 직접 작성하는 것이 아니라, **AI가 올바른 코드를 작성할 수 있도록 정확한 지시를 내리는 것**이다. 정확한 지시를 내리려면 기본적인 개발 개념을 이해하고 있어야 한다.

#### CLAUDE.md에 포함할 내용

| 항목 | 설명 | 예시 |
|------|------|------|
| **프로젝트 개요** | 프로젝트의 목적과 대상 사용자 | "생명정보학 연구자를 위한 웹 기반 시퀀스 분석 도구" |
| **기술 스택** | 사용하는 프레임워크, 라이브러리 | "SvelteKit, Tailwind CSS, PostgreSQL" |
| **디렉토리 구조** | 주요 폴더의 역할 | "src/routes/는 페이지, src/lib/은 공유 코드" |
| **코딩 컨벤션** | 파일명 규칙, 코드 스타일 | "컴포넌트는 PascalCase, 변수는 camelCase" |
| **디자인 가이드라인** | 색상, 폰트, 레이아웃 규칙 | "primary color는 blue-600, 최대 너비 max-w-7xl" |
| **비즈니스 로직** | 도메인 특화 규칙 | "FASTA 형식은 >로 시작하는 헤더 + 시퀀스" |

CLAUDE.md는 한 번 작성하고 끝이 아니다. 프로젝트가 발전할수록 새로운 규칙과 패턴이 생기기 마련이다. 새로운 컴포넌트 규칙이 추가되거나, 디자인 가이드라인이 변경되면 CLAUDE.md도 함께 업데이트해야 한다. AI 에이전트가 항상 최신 상태를 파악할 수 있도록 유지하는 것이 바이브 코딩에서 사람의 중요한 역할이다.

> **팁**: Claude Code에게 "지금까지 작업한 내용을 바탕으로 CLAUDE.md를 업데이트해줘"라고 요청하면, AI가 현재 프로젝트 상태를 분석하여 CLAUDE.md를 자동으로 갱신해 주기도 한다.

## 7.7 개발 서버 실행

### Docker를 사용하는 경우

```bash
docker compose up
```

브라우저에서 `http://localhost:5173`으로 접속하면 SvelteKit 앱을 확인할 수 있다. 최초 실행 시 Docker 이미지 빌드와 의존성 설치에 몇 분이 걸릴 수 있지만, 두 번째 실행부터는 캐시 덕분에 수 초 만에 시작된다.

### Docker 없이 로컬에서 실행하는 경우

```bash
pnpm dev
```

Docker 없이도 개발 서버를 실행할 수 있지만, 이 경우 PostgreSQL을 별도로 설치하고 실행해야 한다. Docker를 사용하면 `docker compose up` 한 줄로 웹 서버와 데이터베이스가 모두 시작되므로 더 편리하다.

![SvelteKit 개발 서버 실행 화면](../assets/ch7-04-sveltekit-dev-server.png)

## 7.8 정리

- **SvelteKit + Tailwind CSS + PostgreSQL이 이 책의 기본 기술 스택**
  - SvelteKit: 코드량이 적고 파일 기반 라우팅을 지원하는 풀스택 프레임워크
  - Tailwind CSS: HTML과 스타일이 같은 파일에 있어 AI가 맥락을 파악하기 쉬운 CSS 프레임워크
  - PostgreSQL: Docker로 간편하게 실행하는 오픈소스 관계형 데이터베이스
- **프로젝트 초기화는 직접 수행하고, 이후 기능 추가는 AI에게 맡기기**
  - `pnpm create svelte@latest`로 뼈대 생성 후 AI와 협업
- **Docker Compose로 개발 환경을 통합 관리**
  - compose.yml(배포용)과 compose.dev.yml(개발용)을 분리
  - Dockerfile, compose.yml 생성은 Claude Code에게 요청
- **환경 변수(.env)는 반드시 사용자가 직접 작성**
  - Claude Code가 `.env`를 읽거나 생성하게 하지 않기
  - 글로벌 CLAUDE.md에 `.env` 접근 금지 규칙 추가
  - `PUBLIC_` 접두사로 공개/비공개 구분, `.gitignore`에 반드시 추가
- **CLAUDE.md에 프로젝트 명세를 작성하여 AI 에이전트 활용도를 높이기**
  - 정보가 많을수록 AI가 정확한 코드를 생성
  - 프로젝트 발전에 따라 지속적으로 업데이트
