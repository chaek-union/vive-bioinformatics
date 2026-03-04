# 8장. 랜딩 페이지 디자인

## 8.1 랜딩 페이지란?

랜딩 페이지(Landing Page)는 웹 사이트의 **가장 첫 페이지**이다. 방문자가 URL에 접속했을 때 가장 먼저 보게 되는 화면으로, 웹 사이트의 첫인상을 결정한다. 따라서 방문자의 이목을 끌 수 있도록 화려하고 매력적인 디자인이 중요하다.

![잘 디자인된 랜딩 페이지 예시](../assets/ch8-01-landing-page-example.png)

## 8.2 랜딩 페이지의 구조

### Header, Body, Footer

웹 페이지는 크게 세 영역으로 구성된다:

![웹 페이지 구조](../assets/ch8-02-web-page-structure.png)

| 영역 | 포함 요소 |
|------|-----------|
| **Header** | 로고, 메인 메뉴(Navigation Bar), Hero 섹션 |
| **Body** | 주 콘텐츠 (기능 소개, 특징, 사용 방법 등) |
| **Footer** | 로고(주로 흑백), 하단 메뉴, 연락처, 주소, 법적 정보, 저작권 정보 등 |

실제 구현 시 각각을 별도의 컴포넌트 파일로 나누어서 구현한다.

### Navigation Bar (Navbar)

Navigation Bar는 웹 사이트의 상단에 위치하는 메뉴이다. 로고와 주요 페이지 링크를 포함하며, 사용자가 사이트 내를 이동할 수 있게 한다.

![Navbar 디자인 예시](../assets/ch8-03-navbar-examples.png)

### Hero Section

Hero Section은 랜딩 페이지의 가장 **눈에 띄는 첫 번째 영역**이다. 화면의 좌우 전체 너비를 사용하며, 다음 요소들을 포함한다:

- **헤드라인**: 사이트의 핵심 가치를 한 문장으로 전달
- **서브 헤드라인**: 헤드라인을 보충하는 설명
- **CTA(Call to Action) 버튼**: 사용자가 취해야 할 행동 (예: "시작하기", "도구 사용하기")
- **배경 이미지 또는 동영상**: 시각적 임팩트

> 참고: Hero Section 디자인 패턴에 대한 자세한 내용은 https://www.awebco.com/blog/hero-section/ 에서 확인할 수 있다.

![Hero Section 예시](../assets/ch8-04-hero-section-example.png)

### Carousel

Carousel(캐러셀)은 여러 이미지나 콘텐츠를 **슬라이드 형태**로 보여주는 요소이다. 여러 기능이나 특징을 순차적으로 소개할 때 유용하다.

![Carousel 컴포넌트 예시](../assets/ch8-05-carousel-example.png)

### Features Section

웹 도구의 주요 기능을 소개하는 영역이다. 보통 **카드(Card)** 형태로 3~4개의 핵심 기능을 나열한다.

![Features Section 예시](../assets/ch8-06-features-section.png)

## 8.3 UI 컴포넌트

자주 사용되는 웹 구성 요소를 패턴화한 것을 **UI 컴포넌트**라 한다. 2011년 등장한 트위터 부트스트랩 프레임워크(https://getbootstrap.com/docs/4.0/components/)에서 유래된 것들이 많다.

랜딩 페이지에서 자주 사용되는 컴포넌트:

| 컴포넌트 | 설명 |
|----------|------|
| **Navbar** | 상단 네비게이션 바 |
| **Hero** | 첫 화면의 대형 배너 영역 |
| **Card** | 카드형 콘텐츠 블록 |
| **Carousel** | 슬라이드 형태의 콘텐츠 |
| **Button** | 클릭 가능한 버튼 (Primary, Secondary, Ghost 등) |
| **Badge** | 작은 라벨/태그 |
| **Footer** | 하단 정보 영역 |

## 8.4 AI를 활용한 디자인 목업 생성

### 나노바나나(Nanobanana)를 활용한 디자인

Google Gemini 등의 AI를 통해 디자인 목업을 생성할 수 있다. 앞서 배운 Header, Body, Footer 및 컴포넌트 개념을 활용하여 프롬프트를 구체적으로 작성하면 더 정확한 결과를 얻을 수 있다.

**기본 프롬프트 예시:**

```
생명정보학 웹 도구의 랜딩 페이지를 디자인해줘.
상단에는 로고와 Navbar, 큰 Hero Section에는 DNA 관련 이미지와
"Sequence Analysis Made Simple" 헤드라인, "시작하기" 버튼이 있고,
아래에는 3개의 기능 카드가 있는 디자인.
```

여러 번 생성해보고 마음에 드는 것을 선택한다.

![Gemini 디자인 목업](../assets/ch8-07-gemini-mockups.png)

**더 구체적인 프롬프트 예시:**

```
Header: 좌측에 로고, 우측에 Home/Tools/About/Contact 메뉴가 있는 Navbar.
Hero Section: 배경은 그라데이션, 좌측에 헤드라인 "Bioinformatics Tools for Everyone",
서브라인 "AI-powered sequence analysis", CTA 버튼 "Get Started".
우측에는 DNA 구조 일러스트.
Features: 3개의 Card — Reverse Complement, Sequence Alignment, BLAST Search.
Footer: 로고(흑백), 링크 목록, 저작권 정보.
```

![상세 디자인 목업](../assets/ch8-08-detailed-mockup.png)

### Claude Code의 design 스킬

Claude Code에는 **design 스킬**이 내장되어 있어, 디자인 목업 이미지를 기반으로 실제 코드를 생성할 수 있다. 사용 방법은 다음과 같다:

1. AI로 생성한 디자인 목업 이미지를 프로젝트 폴더에 저장
2. Claude Code에서 디자인 이미지를 참조하며 구현을 요청

```text
> 이 디자인 목업을 참고하여 랜딩 페이지를 SvelteKit + Tailwind CSS로 구현해줘.
> (이미지를 드래그하여 붙여넣기)
```

Claude Code는 이미지를 분석하여 레이아웃, 색상, 컴포넌트 구조를 파악하고 코드를 생성한다.

![Claude 디자인 요청](../assets/ch8-09-claude-design-request.png)

## 8.5 SvelteKit에서 랜딩 페이지 구현

### 레이아웃 구성

SvelteKit에서는 `src/routes/+layout.svelte` 파일이 모든 페이지에 공통으로 적용되는 레이아웃을 정의한다. Header와 Footer를 여기에 배치한다.

```svelte
<!-- src/routes/+layout.svelte -->
<script>
  import Header from '$lib/components/Header.svelte';
  import Footer from '$lib/components/Footer.svelte';
  import '../app.css';
</script>

<Header />
<main>
  <slot />
</main>
<Footer />
```

### 컴포넌트 분리

각 UI 요소를 별도의 Svelte 컴포넌트로 분리하여 `src/lib/components/` 디렉토리에 저장한다:

```
src/lib/components/
├── Header.svelte       # Navbar 포함
├── Footer.svelte       # 하단 정보
├── Hero.svelte         # Hero 섹션
├── FeatureCard.svelte  # 기능 카드
└── Carousel.svelte     # 캐러셀 (필요 시)
```

### 랜딩 페이지 조립

`src/routes/+page.svelte`에서 컴포넌트들을 조립하여 랜딩 페이지를 완성한다:

```svelte
<!-- src/routes/+page.svelte -->
<script>
  import Hero from '$lib/components/Hero.svelte';
  import FeatureCard from '$lib/components/FeatureCard.svelte';
</script>

<Hero />

<section class="py-16 px-4 max-w-6xl mx-auto">
  <h2 class="text-3xl font-bold text-center mb-12">주요 기능</h2>
  <div class="grid grid-cols-1 md:grid-cols-3 gap-8">
    <FeatureCard
      title="Reverse Complement"
      description="DNA 시퀀스의 역상보 서열을 생성합니다."
    />
    <FeatureCard
      title="Sequence Alignment"
      description="두 시퀀스 간의 정렬을 수행합니다."
    />
    <FeatureCard
      title="BLAST Search"
      description="데이터베이스에서 유사 서열을 검색합니다."
    />
  </div>
</section>
```

## 8.6 정리

- **랜딩 페이지는 웹 사이트의 첫인상을 결정하는 중요한 페이지**
  - Header(Navbar) + Hero Section + Features + Footer 구조
- **UI 컴포넌트의 명칭과 역할을 이해하면 AI에게 더 정확한 지시가 가능**
  - Navbar, Hero, Card, Carousel, Button, Badge, Footer
- **나노바나나로 디자인 목업을 생성하고, Claude Code로 구현**
  - 프롬프트에 컴포넌트 명칭을 사용하여 구체적으로 요청
  - Claude Code의 design 스킬로 이미지 기반 코드 생성 가능
- **SvelteKit에서는 컴포넌트를 분리하여 레이아웃에 조립하는 방식으로 구현**
