# 7장. 프로젝트 디렉토리 설정

## 7.1 이 책에서 사용하는 기술 스택

이 책에서는 다음과 같은 기술 스택을 사용하여 생명정보학 웹 도구를 개발한다.

| 기술 | 역할 | 설명 |
|------|------|------|
| **SvelteKit** | 프론트엔드 프레임워크 | 빠르고 가벼운 웹 앱 개발 프레임워크 |
| **Tailwind CSS** | CSS 프레임워크 | 유틸리티 클래스 기반의 스타일링 도구 |
| **PostgreSQL** | 데이터베이스 | 오픈소스 관계형 데이터베이스 |
| **Docker** | 컨테이너 | 개발 환경 통합 관리 |

### SvelteKit

SvelteKit은 Svelte를 기반으로 한 풀스택 웹 프레임워크이다. React, Vue 등 다른 프레임워크와 비교했을 때 다음과 같은 장점이 있다:

- **컴파일 타임 최적화**: 런타임에 가상 DOM을 사용하지 않아 매우 빠름
- **적은 코드량**: 동일한 기능을 더 적은 코드로 구현 가능
- **서버 사이드 렌더링(SSR)** 기본 지원
- **파일 기반 라우팅**: 파일 구조가 곧 URL 구조

![SvelteKit 공식 웹사이트](../assets/ch7-01-sveltekit-website.png)

### Tailwind CSS

Tailwind CSS는 유틸리티 클래스 기반의 CSS 프레임워크이다. 별도의 CSS 파일을 작성하지 않고, HTML 요소에 직접 클래스를 적용하여 스타일링한다.

```html
<!-- 전통적인 CSS 방식 -->
<div class="card">제목</div>

<!-- Tailwind CSS 방식 -->
<div class="bg-white rounded-lg shadow-md p-6">제목</div>
```

AI 에이전트와 함께 사용하기에 특히 적합한데, 스타일이 HTML과 같은 파일에 있어 컨텍스트를 파악하기 쉽기 때문이다.

![Tailwind CSS 공식 웹사이트](../assets/ch7-02-tailwindcss-website.png)

### PostgreSQL

PostgreSQL은 세계에서 가장 많이 사용되는 오픈소스 관계형 데이터베이스이다. 사용자 데이터, 분석 결과 등을 저장하고 조회하는 데 사용한다. 이 책에서는 Docker를 통해 PostgreSQL을 실행한다.

## 7.2 프로젝트 생성

### Node.js 설치

SvelteKit을 사용하려면 Node.js가 필요하다. 다음 웹사이트에서 설치한다:

https://nodejs.org/en/download

설치 시 **nvm**(Node Version Manager)과 **pnpm**(패키지 매니저)을 선택한 후 터미널에 표시되는 명령을 입력한다.

![Node.js 다운로드 페이지](../assets/ch7-03-nodejs-nvm-pnpm.png)

설치 확인:

```bash
node --version
pnpm --version
```

### SvelteKit 프로젝트 초기화

터미널에서 다음 명령을 실행하여 SvelteKit 프로젝트를 생성한다:

```bash
pnpm create svelte@latest my-bioinfo-app
cd my-bioinfo-app
pnpm install
```

프로젝트 생성 시 다음 옵션을 선택한다:

- Template: **Skeleton project**
- Type checking: **Yes, using TypeScript syntax**
- Additional options: 방향키와 스페이스바로 선택 가능. **Tailwind CSS**, **Typography**, **Forms**를 선택할 것

> **참고**: 프로젝트 초기 생성은 AI 에이전트에 맡기기보다 직접 수행하는 것이 좋다. AI는 초기화 명령을 사용하기보다 기존 코드를 직접 작성하려는 경향이 있어, 최신 버전이 아닌 코드를 생성할 수 있다.

### Tailwind CSS

Tailwind CSS는 SvelteKit 프로젝트 생성 시 옵션으로 함께 설치할 수 있다. 별도로 수동 설치할 필요 없이, 프로젝트 초기화 과정에서 Tailwind CSS를 선택하면 자동으로 설정된다.

## 7.3 Docker 환경 구성

### compose.yml 작성

프로젝트 루트에 `compose.yml` 파일을 생성한다. 이 파일은 SvelteKit 개발 서버와 PostgreSQL 데이터베이스를 함께 관리한다.

```yaml
services:
  app:
    build: .
    ports:
      - "5173:5173"
    volumes:
      - .:/app
      - /app/node_modules
    environment:
      - DATABASE_URL=postgresql://postgres:postgres@db:5432/bioinfo
    depends_on:
      - db

  db:
    image: postgres:16-alpine
    ports:
      - "5432:5432"
    environment:
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: postgres
      POSTGRES_DB: bioinfo
    volumes:
      - pgdata:/var/lib/postgresql/data

volumes:
  pgdata:
```

### Dockerfile 작성

프로젝트 루트에 `Dockerfile`을 생성한다:

```dockerfile
FROM node:20-alpine

WORKDIR /app

# pnpm 설치
RUN corepack enable && corepack prepare pnpm@latest --activate

# 의존성 설치
COPY package.json pnpm-lock.yaml ./
RUN pnpm install

# 소스 코드 복사
COPY . .

# 개발 서버 실행
CMD ["pnpm", "dev", "--host"]
```

## 7.4 환경 변수 (.env)

프로젝트에서 데이터베이스 접속 정보, API 키 등 민감한 설정값은 **환경 변수**로 관리한다. 프로젝트 루트에 `.env` 파일을 생성한다:

```env
DATABASE_URL=postgresql://postgres:postgres@localhost:5432/bioinfo
PUBLIC_SITE_NAME=My Bioinfo App
```

SvelteKit에서 환경 변수는 두 가지로 나뉜다:

| 접두사 | 접근 범위 | 용도 |
|--------|-----------|------|
| `PUBLIC_` | 서버 + 클라이언트 | 사이트 이름 등 공개 정보 |
| (없음) | 서버만 | 데이터베이스 비밀번호, API 키 등 민감 정보 |

> **주의**: `.env` 파일은 `.gitignore`에 반드시 추가하여 Git에 커밋되지 않도록 한다. 대신 `.env.example` 파일을 만들어 어떤 환경 변수가 필요한지 안내한다.

```bash
# .gitignore에 추가
echo ".env" >> .gitignore
```

## 7.5 프로젝트 디렉토리 구조

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

> **팁**: CLAUDE.md는 한 번 작성하고 끝이 아니다. 프로젝트가 발전할수록 새로운 규칙과 패턴을 추가하여 AI 에이전트가 항상 최신 상태를 파악할 수 있도록 유지한다.

## 7.6 개발 서버 실행

### Docker를 사용하는 경우

```bash
docker compose up
```

브라우저에서 `http://localhost:5173`으로 접속하면 SvelteKit 앱을 확인할 수 있다.

### Docker 없이 로컬에서 실행하는 경우

```bash
pnpm dev
```

![SvelteKit 개발 서버 실행 화면](../assets/ch7-04-sveltekit-dev-server.png)

## 7.7 정리

- **SvelteKit + Tailwind CSS + PostgreSQL이 이 책의 기본 기술 스택**
  - SvelteKit: 빠르고 가벼운 풀스택 프레임워크
  - Tailwind CSS: AI와 함께 사용하기 좋은 유틸리티 CSS
  - PostgreSQL: 오픈소스 관계형 데이터베이스
- **Docker Compose로 개발 환경을 통합 관리**
  - 웹 앱과 데이터베이스를 한 번에 실행
- **환경 변수(.env)로 민감한 정보를 분리 관리**
  - `.gitignore`에 반드시 추가
- **CLAUDE.md에 프로젝트 명세를 작성하여 AI 에이전트 활용도를 높이기**
