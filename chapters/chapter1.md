# 1장. 개발 환경 구성

## 1.1 WSL (Windows Subsystem for Linux)

Windows 사용자는 가장 먼저 **WSL(Windows Subsystem for Linux)**을 설치해야 한다. 웹 개발 및 생명정보학 도구 대부분이 리눅스 환경을 기반으로 하기 때문이다. macOS나 Linux 사용자는 이 절을 건너뛰어도 된다.

### WSL이란?

WSL은 Windows 위에서 리눅스 배포판을 직접 실행할 수 있게 해주는 기능이다. 별도의 가상 머신 없이도 리눅스 터미널과 명령어를 사용할 수 있으며, Windows의 파일 시스템과도 자연스럽게 연동된다.

생명정보학에서 WSL이 중요한 이유가 있다. samtools, STAR, BLAST+ 같은 핵심 도구들은 리눅스용으로 개발되어 Windows에서 직접 실행하기 어렵다. Docker도 리눅스 위에서 동작하는 것이 가장 자연스럽다. WSL을 사용하면 Windows의 편의성을 유지하면서 리눅스 생태계의 모든 도구를 활용할 수 있다.

### WSL 설치

**Microsoft Store**를 열고 "Ubuntu"를 검색하여 설치한다. 터미널에서 명령어를 입력할 필요 없이, 평소 앱을 설치하듯 **받기** 버튼을 클릭하면 된다.

![Microsoft Store에서 Ubuntu를 검색하여 설치하는 화면 스크린샷](../assets/ch1-04-wsl-install.png)

설치가 완료되면 **컴퓨터를 재부팅해야 한다**. 재부팅하지 않으면 WSL이 정상적으로 동작하지 않으므로 반드시 재부팅한다.

재부팅 후 Ubuntu를 처음 실행하면, 리눅스 사용자 이름과 비밀번호를 설정하라는 메시지가 나타난다. 이때 설정하는 비밀번호는 `sudo` 명령(관리자 권한 실행)을 사용할 때 필요하므로 잊지 않도록 한다.

![WSL Ubuntu 초기 설정 — 사용자 이름과 비밀번호 입력 화면 스크린샷](../assets/ch1-05-wsl-setup.png)

## 1.2 Visual Studio Code 설치

Visual Studio Code(VS Code)는 Microsoft에서 개발한 무료 코드 편집기다. 다양한 프로그래밍 언어를 지원하며 확장 프로그램을 통해 기능을 추가할 수 있다. 이 책에서는 VS Code를 기본 개발 환경으로 사용한다.

VS Code가 개발자들 사이에서 가장 인기 있는 편집기가 된 데는 몇 가지 이유가 있다. 먼저, 무료이면서도 IntelliSense(코드 자동 완성), 디버거, Git 통합 등 유료 IDE 못지않은 기능을 제공한다. 또한 Windows, macOS, Linux를 모두 지원하므로 운영체제에 관계없이 동일한 환경에서 작업할 수 있다. 무엇보다, 이 책에서 사용할 Claude Code가 VS Code 확장 프로그램으로 제공되기 때문에 VS Code를 선택했다.

### 설치 방법

1. https://code.visualstudio.com 에 접속한다
2. 운영체제에 맞는 설치 파일을 다운로드한다 (Windows, macOS, Linux)
3. 다운로드한 파일을 실행하여 설치를 완료한다

![VS Code 공식 웹사이트에서 다운로드 버튼이 보이는 메인 페이지 스크린샷](../assets/ch1-01-vscode-website.png)

### 설치 확인

설치가 완료되면 VS Code를 실행하여 정상적으로 동작하는지 확인한다. 처음 실행하면 Welcome 탭이 나타나며, 여기서 색상 테마를 선택하거나 기본 설정을 조정할 수 있다.

![VS Code를 처음 실행했을 때의 Welcome 탭 화면 스크린샷](../assets/ch1-02-vscode-welcome.png)

### 유용한 확장 프로그램

VS Code의 강력한 장점 중 하나는 확장 프로그램(Extension)이다. 왼쪽 사이드바의 확장 프로그램 아이콘을 클릭하거나 `Cmd+Shift+X` (Mac) / `Ctrl+Shift+X` (Windows/Linux)를 눌러 확장 프로그램 마켓플레이스를 열 수 있다.

확장 프로그램 마켓플레이스에는 수만 개의 확장 프로그램이 등록되어 있다. Python 개발을 위한 Python 확장, 코드 포매팅을 위한 Prettier, Git 히스토리를 시각화하는 GitLens 등이 대표적이다. 앞으로 필요한 확장 프로그램은 이곳에서 검색하여 설치하면 된다. 이 책에서는 WSL 확장과 Claude Code 확장을 주로 사용하게 된다.

![VS Code 확장 프로그램 마켓플레이스 화면 스크린샷](../assets/ch1-03-vscode-extensions.png)

### VS Code에서 WSL 연동

Windows 사용자는 VS Code에서 WSL 환경을 연결해야 한다. WSL을 설치한 뒤 재부팅했다면, 별도로 확장 프로그램을 검색하여 설치할 필요 없이 바로 연결할 수 있다.

1. VS Code 좌측 하단의 파란색 `><` 아이콘을 클릭
2. 메뉴에서 **"WSL"**을 선택하면 WSL 확장 프로그램이 자동으로 설치된다

![VS Code 좌측 하단 >< 아이콘을 클릭하여 WSL 메뉴를 선택하는 화면 스크린샷](../assets/ch1-06-vscode-wsl-connect.png)

3. 설치가 완료되면 `><` 아이콘을 다시 클릭
4. **"Connect to WSL"**을 선택하여 WSL 환경에 연결한다

연결이 완료되면 VS Code 좌측 하단에 **"WSL: Ubuntu"**라고 표시되며, 터미널을 열면 리눅스 셸이 실행된다. 이후 이 책의 모든 터미널 명령은 WSL 환경에서 실행하면 된다.

이 연결이 되어 있는 상태에서는 VS Code의 파일 탐색기, 터미널, 확장 프로그램이 모두 WSL 환경에서 동작한다. Windows 파일 시스템이 아닌 리눅스 파일 시스템(`/home/사용자명/`)에서 프로젝트를 생성하고 관리하는 것이 성능 면에서 유리하다.

![VS Code가 WSL에 연결된 상태 — 좌측 하단에 "WSL: Ubuntu" 표시와 리눅스 터미널이 열린 모습](../assets/ch1-07-vscode-wsl-active.png)

### 터미널이란?

**터미널(Terminal)**은 텍스트 명령으로 컴퓨터와 소통하는 창이다. 마우스로 아이콘을 클릭하는 대신, 명령어를 직접 입력하여 파일을 관리하거나 프로그램을 실행할 수 있다. 이 책에서 "터미널에서 실행한다"라고 하면, 이 텍스트 입력 창에 명령어를 입력한다는 뜻이다.

터미널을 여는 방법은 두 가지가 있다.

**방법 1: Ubuntu 앱으로 직접 열기**

Windows 시작 메뉴에서 **"Ubuntu"**를 검색하여 실행하면, 검은 배경의 텍스트 입력 창이 나타난다. 이것이 리눅스 터미널이다. WSL 설치 시 설정한 사용자 이름이 표시되며, 여기에 명령어를 입력할 수 있다.

**방법 2: VS Code 내장 터미널 사용 (권장)**

VS Code 안에서도 터미널을 열 수 있다. 상단 메뉴에서 **터미널 → 새 터미널**을 선택하거나, 단축키 `` Ctrl+` ``(백틱)을 누르면 편집기 아래쪽에 터미널 패널이 나타난다. WSL에 연결된 상태라면 자동으로 리눅스 터미널이 열린다.

VS Code 내장 터미널을 권장하는 이유는 **코드 편집과 터미널 명령을 한 화면에서 모두 처리할 수 있기 때문**이다. 파일을 수정하면서 바로 아래 터미널에서 실행 결과를 확인할 수 있어 효율적이다.

> macOS나 Linux 사용자는 시스템에 기본 설치된 터미널 앱을 사용하거나, 마찬가지로 VS Code 내장 터미널을 사용하면 된다.

## 1.3 VS Code에서 Claude Code 사용하기

Claude Code는 VS Code에 직접 통합되는 AI 코딩 도구다. Github Copilot과 마찬가지로 VS Code 확장 프로그램으로 설치하여 사용할 수 있으며, 코드 작성, 디버깅, 리팩토링 등 다양한 작업을 AI의 도움을 받아 수행할 수 있다.

Claude Code가 Github Copilot과 다른 점은 **에이전트 방식**으로 동작한다는 것이다. Copilot이 주로 코드 자동 완성에 초점을 맞추는 반면, Claude Code는 파일을 읽고, 수정하고, 터미널 명령을 실행하고, Git 작업까지 수행할 수 있다. 마치 옆에 앉아서 함께 코딩하는 동료처럼 동작한다. 이 책 전체에서 Claude Code를 활용하여 생명정보학 도구를 만들게 된다.

### 필수 조건

- VS Code 1.98.0 이상
- Anthropic 계정 (확장 프로그램을 처음 열 때 로그인)

### 설치 방법

VS Code에서 `Cmd+Shift+X` (Mac) 또는 `Ctrl+Shift+X` (Windows/Linux)를 눌러 확장 프로그램 보기를 열고, "Claude Code"를 검색한 후 **설치**를 클릭한다.

![VS Code 확장 프로그램 마켓플레이스에서 Claude Code를 검색하여 설치하는 화면 스크린샷](../assets/ch1-08-vscode-claude-install.png)

### 시작하기

#### Claude Code 패널 열기

편집기 오른쪽 위 모서리의 **Spark 아이콘**을 클릭하여 Claude Code 패널을 연다. 또는 다음 방법으로 열 수 있다:

- **명령 팔레트**: `Cmd+Shift+P` (Mac) 또는 `Ctrl+Shift+P` (Windows/Linux)를 누르고 "Claude Code"를 입력
- **상태 표시줄**: 창 오른쪽 아래의 **Claude Code**를 클릭

![VS Code 편집기에서 Spark 아이콘 위치와 Claude Code 패널이 열린 모습 스크린샷](../assets/ch1-09-vscode-claude-panel.png)

#### 프롬프트 보내기

Claude에게 코드에 대해 질문하거나, 디버깅을 요청하거나, 변경 사항 작성을 요청한다. 편집기에서 텍스트를 선택하면 Claude가 해당 코드를 자동으로 인식한다.

- `@` 뒤에 파일명을 입력하면 특정 파일을 참조할 수 있다 (예: `@auth.js`)
- `Option+K` (Mac) / `Alt+K` (Windows/Linux)로 현재 파일과 선택 영역의 참조를 삽입할 수 있다

`@-멘션` 기능은 특히 유용하다. 프로젝트에 파일이 수십 개 있을 때, Claude가 어떤 파일을 참조해야 하는지 명확히 알려줄 수 있기 때문이다. 예를 들어 `@src/lib/server/db.ts 이 파일의 데이터베이스 연결 코드를 참고해서 새 API를 만들어줘`라고 요청하면, Claude는 해당 파일의 패턴을 따라 일관된 코드를 작성해 준다.

![Claude Code 패널에 프롬프트를 입력하고 @-멘션으로 파일을 참조하는 모습 스크린샷](../assets/ch1-10-claude-prompt.png)

#### 변경 사항 검토

Claude가 파일을 편집하려고 하면, 원본과 제안된 변경 사항을 나란히 비교하는 diff 화면이 나타난다. 수락하거나 거부하거나, Claude에게 다른 방법으로 수정하도록 요청할 수 있다.

이 diff 화면은 바이브 코딩에서 매우 중요한 역할을 한다. AI가 제안한 코드를 무조건 수락하는 것이 아니라, 변경 사항을 확인하고 이해한 뒤 수락 여부를 결정할 수 있기 때문이다. 특히 데이터베이스 스키마 변경이나 보안 관련 코드는 반드시 diff를 확인하는 습관을 들이는 것이 좋다.

![Claude가 제안한 코드 변경 사항의 diff 화면과 수락/거부 버튼이 있는 스크린샷](../assets/ch1-11-claude-diff.png)

### 주요 기능

#### 권한 모드

프롬프트 상자 하단의 모드 표시기를 클릭하여 전환한다:

| 모드 | 설명 |
|------|------|
| **일반 모드** | 각 작업 전에 권한을 요청 |
| **Plan Mode** | 수행할 작업을 설명하고 승인을 기다림 |
| **자동 수락 모드** | 요청하지 않고 바로 편집 |

처음에는 **일반 모드**로 시작하여 Claude가 어떤 작업을 수행하는지 하나씩 확인하면서 익숙해지는 것을 권장한다. 작업 흐름에 익숙해지면 자동 수락 모드로 전환하여 더 빠르게 작업할 수 있다. Plan Mode는 복잡한 리팩토링처럼 큰 변경을 수행하기 전에 계획을 먼저 확인하고 싶을 때 유용하다.

#### 파일 및 폴더 참조

`@-멘션`을 사용하여 특정 파일이나 폴더에 대한 컨텍스트를 Claude에게 제공한다:

> @auth.js 이 파일의 로직을 설명해줘

> @src/components/ 이 폴더의 구조를 분석해줘

#### 여러 대화 실행

명령 팔레트에서 **새 탭에서 열기** 또는 **새 창에서 열기**를 사용하여 여러 대화를 동시에 실행할 수 있다. 각 대화는 독립적인 기록과 컨텍스트를 유지한다. 예를 들어 한 탭에서는 프론트엔드 UI를 수정하고, 다른 탭에서는 백엔드 API를 개발하는 식으로 병렬 작업이 가능하다.

#### Git 통합

Claude Code는 Git과 통합되어 커밋, 브랜치 관리, PR 생성 등을 자연어로 요청할 수 있다. Git에 대한 자세한 내용은 2장에서 다룬다.

### 주요 단축키

| 명령 | 단축키 (Mac / Windows·Linux) | 설명 |
|------|------|------|
| 포커스 전환 | `Cmd+Esc` / `Ctrl+Esc` | 편집기와 Claude 사이 전환 |
| 새 탭에서 열기 | `Cmd+Shift+Esc` / `Ctrl+Shift+Esc` | 새 대화를 탭으로 열기 |
| 새 대화 | `Cmd+N` / `Ctrl+N` | 새 대화 시작 (Claude 포커스 시) |
| @-멘션 삽입 | `Option+K` / `Alt+K` | 현재 파일 및 선택 영역 참조 삽입 |

## 1.4 uv와 micromamba

생명정보학 작업에는 Python 패키지 관리 도구가 필요하다. 이 책에서는 **uv**(Python 패키지 매니저)와 **micromamba**(Conda 호환 환경 매니저)를 사용한다. 이 도구들의 설치도 Claude Code에게 맡길 수 있다.

### uv

uv는 Rust로 작성된 초고속 Python 패키지 매니저다. 기존의 pip보다 10~100배 빠르며, 가상 환경 생성과 패키지 설치를 한 번에 처리할 수 있다. 2024년 Astral사에서 공개한 이후 빠르게 Python 커뮤니티의 표준으로 자리잡고 있다.

Claude Code에게 설치를 요청한다:

> uv를 설치해줘

Claude가 `curl -LsSf https://astral.sh/uv/install.sh | sh` 명령을 실행하고, 셸 설정까지 자동으로 처리해 준다.

uv 설치 후에는 **글로벌 CLAUDE.md**에 uv 사용 지침을 추가하는 것이 좋다. 글로벌 CLAUDE.md(`~/.claude/CLAUDE.md`)는 모든 프로젝트에서 Claude Code가 참조하는 설정 파일이다. 여기에 "패키지 설치에 uv를 사용할 것"이라고 명시해 두면, 어떤 프로젝트에서든 Claude Code가 pip 대신 uv를 사용한다.

> 글로벌 CLAUDE.md에 다음 규칙을 추가해줘: Python 패키지 설치 시 pip 대신 uv를 사용할 것, 가상 환경 생성 시 uv venv를 사용할 것

### micromamba

micromamba는 Conda의 경량화 버전으로, Conda와 동일한 패키지 저장소(conda-forge, bioconda)를 사용하지만 훨씬 빠르고 가볍다. 원래 Conda는 Anaconda 배포판에 포함된 패키지 매니저인데, 설치 용량이 크고 속도가 느리다는 단점이 있었다. micromamba는 C++로 작성되어 이런 문제를 해결했다.

생명정보학 도구 중 상당수가 Conda/Bioconda 채널을 통해 배포되므로 micromamba가 필요하다. PyPI에 없는 도구 — 예를 들어 samtools(시퀀스 데이터 처리), STAR(RNA-seq 정렬), bedtools(게놈 구간 연산) 등 — 는 Bioconda 채널에서만 설치할 수 있다.

Claude Code에게 설치를 요청한다:

> micromamba를 설치하고, conda 명령으로도 사용할 수 있게 alias 설정해줘

Claude가 micromamba 설치와 `alias conda="micromamba"` 설정을 자동으로 처리해 준다. 이 alias가 있으면 AI가 `conda install` 명령을 생성해도 micromamba가 실행된다.

### uv vs micromamba

| | uv | micromamba (conda) |
|---|---|---|
| **용도** | Python 패키지 설치 | Python + 비Python 도구 설치 |
| **속도** | 매우 빠름 | 빠름 (conda보다 훨씬 빠름) |
| **사용 상황** | pandas, matplotlib 등 순수 Python 패키지 | samtools, STAR, snakemake 등 바이너리 도구 |
| **채널** | PyPI | conda-forge, bioconda |

> **팁**: 일반적인 Python 패키지는 uv로 설치하고, 생명정보학 전용 도구(samtools, STAR, bedtools 등)는 micromamba로 설치하는 것이 좋다. 두 도구는 서로 충돌하지 않으므로 함께 사용해도 문제없다.

## 1.5 정리

- **Windows 사용자는 WSL을 가장 먼저 설치**
  - Microsoft Store에서 Ubuntu를 검색하여 설치 후 재부팅
- **VS Code 설치 및 WSL 연동**
  - 좌측 하단 `><` 아이콘에서 WSL 연결 (확장 프로그램 자동 설치)
  - 이후 모든 작업은 WSL 환경에서 진행
- **Claude Code 설치 및 기본 사용법 숙지**
  - VS Code 확장 프로그램으로 설치
  - @-멘션, 권한 모드, diff 검토 등 주요 기능 익히기
  - 이 책 전체에서 Claude Code를 활용하여 생명정보학 도구를 개발
- **uv와 micromamba는 Claude Code에게 설치를 맡기기**
  - uv: 빠른 Python 패키지 설치 (PyPI)
  - micromamba: Conda 호환 환경 매니저 (bioconda 채널 활용)
  - `alias conda="micromamba"`로 편의성 확보
