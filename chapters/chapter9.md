# 9장. 일반 페이지 디자인

## 9.1 일반 페이지란?

일반 페이지는 랜딩 페이지를 제외한 **나머지 모든 페이지**를 의미한다. 실제 웹 도구의 기능을 제공하는 핵심 페이지들이다. 랜딩 페이지가 "이 사이트는 이런 일을 합니다"를 보여주는 전시실이라면, 일반 페이지는 "여기서 실제로 작업하세요"라는 작업실이다.

랜딩 페이지의 화려한 Hero Section, Carousel 등의 디자인 요소를 축소하고, **모든 페이지에 공통적인 일관된 디자인**을 적용한다. 일관성이 중요한 이유는, 사용자가 어떤 도구 페이지를 열든 동일한 레이아웃과 동일한 조작 방식을 기대할 수 있어야 하기 때문이다. 매번 다른 레이아웃이라면 사용자는 페이지를 열 때마다 "입력 창이 어디 있지?"를 찾아야 한다.

![일반 페이지 예시](../assets/ch9-01-general-page-example.png)

## 9.2 일반 페이지의 구조

### 공통 레이아웃

일반 페이지는 랜딩 페이지와 동일한 Header(Navbar)와 Footer를 공유하되, Hero Section의 높이를 크게 줄여 간결한 형태로 사용한다. 랜딩 페이지의 Hero가 화면 좌우 전체 너비에 큰 높이를 차지하는 것과 달리, 일반 페이지의 Hero는 페이지 제목 정도만 표시하는 얇은 배너 형태이다.

이렇게 하면 공통 레이아웃(`+layout.svelte`)은 그대로 유지하면서, 각 페이지의 콘텐츠에 더 많은 화면 공간을 할애할 수 있다. 도구 페이지에서는 입력 폼과 결과 표시 영역이 넓어야 하므로, Hero Section이 화면의 절반을 차지하면 불편하다.

![레이아웃 비교](../assets/ch9-02-layout-comparison.png)

| 영역 | 랜딩 페이지 | 일반 페이지 |
|------|-------------|-------------|
| **Header** | Navbar + 큰 Hero Section | Navbar + 축소된 Hero (페이지 타이틀 배너) |
| **Body** | 기능 소개, 특징 카드 등 | 실제 도구 인터페이스, 콘텐츠 |
| **Footer** | 동일 | 동일 |

### Page Header (Breadcrumb 포함)

일반 페이지의 상단에는 현재 위치를 알려주는 **Breadcrumb**과 **페이지 제목(Heading)**을 배치한다. Breadcrumb은 "빵 부스러기"라는 뜻으로, 헨젤과 그레텔이 숲에서 길을 찾기 위해 빵 부스러기를 떨어뜨린 것에서 유래했다. 웹에서는 현재 페이지까지의 경로를 보여주는 네비게이션 요소이다.

```
Home > Tools > Reverse Complement
```

이 Breadcrumb을 보면 사용자는 "지금 Home 아래 Tools 아래 Reverse Complement 페이지에 있구나"를 즉시 알 수 있다. 각 항목은 클릭 가능한 링크로, "Tools"를 클릭하면 도구 목록 페이지로 이동한다.

![페이지 헤더와 Breadcrumb](../assets/ch9-03-page-header-breadcrumb.png)

### Sidebar

도구가 여러 개이거나 설정 옵션이 많은 경우, **Sidebar**를 활용하여 네비게이션을 제공한다. Sidebar는 화면의 좌측(또는 우측)에 고정된 세로형 메뉴이다. 도구 목록을 Sidebar에 배치하면, 한 페이지에서 다른 도구로 빠르게 전환할 수 있다.

Sidebar는 데스크톱 화면에서는 항상 표시하고, 모바일에서는 햄버거 메뉴로 숨겼다가 필요할 때 펼치는 방식이 일반적이다. AI에게 "반응형 Sidebar를 만들어줘"라고 요청하면 이 동작을 구현해 준다.

![사이드바 레이아웃](../assets/ch9-04-sidebar-layout.png)

## 9.3 도구 페이지에서 자주 사용되는 컴포넌트

생명정보학 웹 도구의 일반 페이지에서 자주 사용되는 UI 컴포넌트들이다. 8장에서 배운 컴포넌트 명칭의 중요성이 여기서도 동일하게 적용된다. AI에게 "시퀀스 입력 칸을 만들어줘"보다 "FASTA 시퀀스를 입력할 수 있는 Textarea를 만들어줘"가 더 정확한 결과를 낸다.

### 입력 폼 컴포넌트

사용자로부터 데이터를 받는 컴포넌트들이다.

| 컴포넌트 | 용도 | 예시 |
|----------|------|------|
| **Textarea** | 긴 텍스트 입력 | FASTA 시퀀스 입력. 여러 줄을 입력할 수 있는 넓은 텍스트 상자 |
| **Input** | 단일 값 입력 | 파라미터 값, 검색어. 한 줄짜리 텍스트 입력 |
| **Select / Dropdown** | 선택지 중 하나 선택 | 알고리즘 선택(blastn, blastp), 데이터베이스 선택(nr, swissprot) |
| **Checkbox** | 다중 선택 | 출력 옵션 선택 (정렬 포함, 통계 포함 등 여러 개 동시 선택 가능) |
| **Radio** | 단일 선택 | 분석 모드 선택 (Nucleotide 또는 Protein 중 하나만 선택) |
| **File Upload** | 파일 업로드 | FASTA 파일, VCF 파일 등을 직접 업로드 |
| **Button** | 실행/제출 | "분석 시작", "결과 다운로드" |
| **Label** | 입력 필드 설명 | 각 입력 필드의 이름과 안내 문구 |

Checkbox와 Radio의 차이를 이해하면 AI에게 정확한 요청을 할 수 있다. Checkbox는 여러 개를 동시에 선택할 수 있고(출력 옵션처럼), Radio는 하나만 선택할 수 있다(분석 모드처럼). AI에게 "분석 모드를 선택하는 Radio 버튼 3개 만들어줘"라고 요청하면 의도에 맞는 UI가 나온다.

### 출력/결과 컴포넌트

분석 결과를 사용자에게 보여주는 컴포넌트들이다.

| 컴포넌트 | 용도 | 예시 |
|----------|------|------|
| **Table** | 표 형태의 결과 표시 | BLAST 검색 결과(Hit 목록), 통계 요약. 행과 열로 정리된 데이터를 보여줄 때 사용 |
| **Code Block** | 코드/시퀀스 표시 | 결과 시퀀스, 명령어 예시. 고정폭 폰트로 표시되어 시퀀스 정렬이 깔끔하게 보인다 |
| **Tab** | 여러 결과 뷰 전환 | 텍스트 뷰 / 시각화 뷰. 같은 데이터를 다른 형태로 볼 수 있게 한다 |
| **Spinner** | 로딩 상태 표시 | 분석 진행 중 표시. 시간이 걸리는 작업에서 사용자가 "멈춘 건가?" 하고 불안해하지 않도록 |
| **Alert / Toast** | 알림 메시지 | 성공("분석 완료") / 오류("잘못된 시퀀스 형식") 메시지 |
| **Progress Bar** | 진행률 표시 | 대용량 파일 처리 진행률. "50% 완료" 같은 시각적 피드백 |
| **Pagination** | 결과 페이지 이동 | 검색 결과가 수백 개일 때, 한 페이지에 20개씩 나누어 표시 |

Spinner와 Progress Bar는 사용자 경험(UX)에서 중요한 역할을 한다. BLAST 검색처럼 몇 초에서 몇 분이 걸리는 작업에서 아무런 피드백 없이 화면이 멈춰 있으면, 사용자는 "오류가 난 건가?" 하고 페이지를 새로고침하거나 떠나버린다. Spinner 하나만 있어도 "처리 중이구나"라는 신호를 줄 수 있다.

### 시각화 컴포넌트

생명정보학 데이터를 그래프나 특수한 형태로 보여주는 컴포넌트들이다.

| 컴포넌트 | 용도 |
|----------|------|
| **Chart** | 데이터 시각화 (막대, 선, 파이 차트 등). Chart.js나 D3.js 라이브러리를 사용 |
| **Heatmap** | 유전자 발현 히트맵 등. 값의 크기를 색상 강도로 표현 |
| **Sequence Viewer** | 시퀀스 정렬 결과 시각화. 여러 시퀀스를 나란히 놓고 일치/불일치를 색상으로 표시 |

시각화 컴포넌트는 직접 구현하기보다 기존 라이브러리를 활용하는 것이 일반적이다. AI에게 "Chart.js를 사용해서 GC 함량을 막대 차트로 보여줘"라고 요청하면, 라이브러리 설치부터 차트 렌더링까지 처리해 준다.

## 9.4 일반 페이지 디자인 패턴

### 단일 도구 페이지

하나의 도구만 제공하는 간단한 페이지이다. 입력 → 실행 → 결과의 흐름을 하나의 페이지에서 처리한다. 가장 기본적이면서도 가장 많이 사용되는 패턴이다.

```
┌──────────────────────────────┐
│  Navbar                      │
├──────────────────────────────┤
│  Breadcrumb                  │
│  페이지 제목 (Heading)         │
├──────────────────────────────┤
│  입력 영역                    │
│  ┌────────────────────────┐  │
│  │  Textarea (시퀀스 입력)  │  │
│  └────────────────────────┘  │
│  [분석 시작] 버튼              │
├──────────────────────────────┤
│  결과 영역                    │
│  ┌────────────────────────┐  │
│  │  결과 출력 (Table/Code) │  │
│  └────────────────────────┘  │
│  [다운로드] 버튼               │
├──────────────────────────────┤
│  Footer                      │
└──────────────────────────────┘
```

이 구조의 핵심은 **위에서 아래로 흐르는 자연스러운 워크플로우**이다. 사용자는 위쪽에서 시퀀스를 입력하고, 중간의 버튼을 누르고, 아래쪽에서 결과를 확인한다. 이 흐름을 깨뜨리면 사용자가 혼란스러워진다.

![단일 도구 페이지 예시](../assets/ch9-05-single-tool-page.png)

### 다중 도구 페이지

여러 도구를 제공하는 경우, Sidebar로 도구를 선택하고 우측에서 사용하는 구조이다. 이 패턴은 도구가 5개 이상일 때 유용하다. 도구가 2~3개뿐이라면 Sidebar 없이 탭으로 전환하는 것이 더 간결하다.

```
┌──────────────────────────────────┐
│  Navbar                          │
├──────┬───────────────────────────┤
│      │  Breadcrumb               │
│ Side │  페이지 제목               │
│ bar  ├───────────────────────────┤
│      │                           │
│ 도구1 │  선택된 도구의              │
│ 도구2 │  입력/결과 영역             │
│ 도구3 │                           │
│      │                           │
├──────┴───────────────────────────┤
│  Footer                          │
└──────────────────────────────────┘
```

Sidebar에서 도구를 클릭하면 우측 영역만 바뀌고, 전체 페이지가 새로 로드되지는 않는다. 이를 **클라이언트 사이드 라우팅**이라 하며, SvelteKit이 기본으로 제공하는 기능이다.

![다중 도구 사이드바](../assets/ch9-06-multi-tool-sidebar.png)

### 탭 기반 결과 표시

분석 결과를 여러 형태로 보여줘야 할 때, Tab 컴포넌트를 활용한다. 예를 들어 BLAST 검색 결과를 표(Table)로도 보고, 시퀀스 정렬(Alignment)로도 보고, 통계 요약(Summary)으로도 볼 수 있게 한다.

탭은 같은 데이터를 다른 관점에서 보여줄 때 효과적이다. 각 탭의 데이터를 서버에서 다시 받아올 필요 없이, 이미 로드된 데이터를 다른 형태로 렌더링하므로 전환이 즉시 이루어진다.

![탭 기반 결과 표시](../assets/ch9-07-tabbed-results.png)

## 9.5 SvelteKit에서 일반 페이지 구현

### 파일 기반 라우팅

SvelteKit은 **파일 기반 라우팅**을 사용한다. `src/routes/` 디렉토리의 폴더 구조가 URL 구조가 된다. 새 페이지를 추가하고 싶으면 해당 경로에 폴더를 만들고 `+page.svelte` 파일을 생성하면 된다.

```
src/routes/
├── +page.svelte              → /         (랜딩 페이지)
├── +layout.svelte            → 공통 레이아웃
├── about/
│   └── +page.svelte          → /about
└── tools/
    ├── +page.svelte           → /tools   (도구 목록)
    ├── +layout.svelte         → tools 하위 공통 레이아웃
    ├── revcomp/
    │   └── +page.svelte       → /tools/revcomp
    └── alignment/
        └── +page.svelte       → /tools/alignment
```

`tools/+layout.svelte`를 만들면 tools 하위의 모든 페이지에 공통 레이아웃을 적용할 수 있다. 여기에 Sidebar를 배치하면, revcomp이든 alignment든 모든 도구 페이지에 같은 Sidebar가 표시된다. 루트의 `+layout.svelte`(Header/Footer)와 중첩되므로, Header + Sidebar + 콘텐츠 + Footer 구조가 자동으로 완성된다.

### Reverse Complement 예시

다음은 Reverse Complement 도구 페이지의 코드이다. 이 코드를 직접 작성하기보다, 이런 구조를 이해하고 AI에게 요청하는 것이 이 책의 접근법이다.

```svelte
<!-- src/routes/tools/revcomp/+page.svelte -->
<script>
  let inputSequence = '';
  let result = '';

  function reverseComplement(seq) {
    const complement = { A: 'T', T: 'A', G: 'C', C: 'G' };
    return seq
      .toUpperCase()
      .split('')
      .reverse()
      .map(base => complement[base] || base)
      .join('');
  }

  function handleSubmit() {
    // FASTA 헤더 제거 후 시퀀스만 추출
    const lines = inputSequence.split('\n');
    const seq = lines.filter(l => !l.startsWith('>')).join('');
    result = reverseComplement(seq);
  }
</script>

<div class="max-w-4xl mx-auto p-6">
  <nav class="text-sm text-gray-500 mb-4">
    <a href="/" class="hover:underline">Home</a> &gt;
    <a href="/tools" class="hover:underline">Tools</a> &gt;
    <span>Reverse Complement</span>
  </nav>

  <h1 class="text-3xl font-bold mb-6">Reverse Complement</h1>

  <div class="space-y-4">
    <label class="block">
      <span class="text-lg font-medium">Input Sequence (FASTA)</span>
      <textarea
        bind:value={inputSequence}
        class="mt-2 w-full h-40 p-3 border rounded-lg font-mono"
        placeholder=">sequence1&#10;ATCGATCG"
      ></textarea>
    </label>

    <button
      on:click={handleSubmit}
      class="bg-blue-600 text-white px-6 py-2 rounded-lg hover:bg-blue-700"
    >
      분석 시작
    </button>

    {#if result}
      <div class="mt-6">
        <h2 class="text-xl font-semibold mb-2">결과</h2>
        <pre class="bg-gray-100 p-4 rounded-lg font-mono overflow-x-auto">{result}</pre>
        <button
          on:click={() => navigator.clipboard.writeText(result)}
          class="mt-2 text-blue-600 hover:underline"
        >
          결과 복사
        </button>
      </div>
    {/if}
  </div>
</div>
```

이 코드에서 주목할 점은 다음과 같다:

- **`bind:value`**: Svelte의 양방향 바인딩이다. 사용자가 Textarea에 입력하면 `inputSequence` 변수가 자동으로 업데이트되고, 반대로 코드에서 변수를 바꾸면 화면도 자동으로 갱신된다.
- **`{#if result}`**: Svelte의 조건부 렌더링이다. `result` 변수에 값이 있을 때만 결과 영역을 표시한다. 분석 시작 버튼을 누르기 전에는 결과 영역이 보이지 않는다.
- **FASTA 파싱**: `>`로 시작하는 줄은 헤더이므로 제거하고, 나머지 줄을 합쳐서 순수 시퀀스만 추출한다. 이것은 생명정보학 도메인 지식이다.

이 코드를 처음부터 작성할 수는 없더라도, "Textarea에 FASTA를 입력받고, 헤더를 제거한 뒤 역상보 서열을 계산해서 Code Block으로 보여주는 페이지를 만들어줘"라고 요청할 수 있어야 한다. 그러려면 FASTA 형식, 역상보 서열, Textarea, Code Block의 개념을 알아야 한다.

## 9.6 AI를 활용한 일반 페이지 디자인

일반 페이지도 랜딩 페이지와 마찬가지로 AI를 활용하여 디자인할 수 있다. 프롬프트에 컴포넌트 명칭을 정확히 사용하면 더 좋은 결과를 얻을 수 있다.

**프롬프트 예시:**

```
Reverse Complement 도구 페이지를 디자인해줘.
상단에 Breadcrumb (Home > Tools > Reverse Complement)과 페이지 제목.
입력 영역에는 FASTA 시퀀스를 붙여넣을 수 있는 큰 Textarea와 "분석 시작" Button.
결과 영역에는 Code Block으로 결과 시퀀스를 표시하고, "복사" 버튼과 "다운로드" 버튼.
전체적으로 깔끔하고 과학적인 느낌의 디자인.
```

이 프롬프트에는 Breadcrumb, Textarea, Button, Code Block이라는 컴포넌트 이름이 명시되어 있다. AI는 이 이름들을 정확하게 해석하여 원하는 UI를 생성해 준다.

Claude Code에서는 디자인 목업 이미지를 참조하여 구현을 요청할 수 있다:

```text
> 이 디자인 목업을 참고하여 /tools/revcomp 페이지를 구현해줘.
> SvelteKit + Tailwind CSS를 사용하고, 입력/결과 영역을 Card 컴포넌트로 감싸줘.
```

![Claude 일반 페이지 디자인 요청](../assets/ch9-08-claude-general-request.png)

## 9.7 정리

- **일반 페이지는 랜딩 페이지와 동일한 Header/Footer를 공유하되, Hero Section을 축소**
  - Breadcrumb + Heading으로 현재 위치와 페이지 제목을 표시
  - 콘텐츠 영역을 최대한 넓게 활용
- **도구 페이지에서 자주 사용되는 컴포넌트를 숙지**
  - 입력: Textarea, Input, Select, File Upload, Checkbox, Radio, Button
  - 출력: Table, Code Block, Tab, Spinner, Alert, Progress Bar, Pagination
  - 시각화: Chart, Heatmap, Sequence Viewer
- **디자인 패턴을 상황에 맞게 선택**
  - 단일 도구: 위에서 아래로 흐르는 입력 → 실행 → 결과 흐름
  - 다중 도구: Sidebar로 도구 전환
  - 복합 결과: Tab으로 다양한 뷰 전환
- **SvelteKit의 파일 기반 라우팅으로 페이지를 구성**
  - 폴더 구조 = URL 구조
  - 중첩 레이아웃으로 공통 Sidebar 등을 관리
- **AI에게 컴포넌트 명칭을 정확히 사용하여 디자인 및 구현을 요청**
  - "입력 칸"보다 "Textarea", "로딩 표시"보다 "Spinner"가 정확한 결과를 낸다
