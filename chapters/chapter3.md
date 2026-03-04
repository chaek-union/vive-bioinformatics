# 3장. Docker

## 3.1 Docker란?

Docker는 애플리케이션을 **컨테이너(Container)**라는 격리된 환경에서 실행할 수 있게 해주는 도구이다. 컨테이너는 운영체제, 라이브러리, 설정 파일 등을 모두 포함하고 있어, 어떤 컴퓨터에서든 동일한 환경으로 프로그램을 실행할 수 있다.

### 왜 Docker가 필요한가?

생명정보학 도구를 개발하다 보면 다음과 같은 문제를 자주 겪게 된다:

- "내 컴퓨터에서는 되는데 다른 컴퓨터에서는 안 돼요"
- 파이썬 버전, 라이브러리 버전 충돌
- 운영체제마다 설치 방법이 다름
- 데이터베이스, 웹 서버 등 여러 서비스를 동시에 관리해야 함

Docker를 사용하면 이러한 문제를 해결할 수 있다. 개발 환경을 코드로 정의하여 누구나 동일한 환경을 재현할 수 있다.

![Docker 없이 개발할 때의 환경 차이 문제 vs Docker를 사용할 때의 일관된 환경을 비교하는 다이어그램](../assets/ch3-01-docker-consistency.png)

### 가상 머신과의 차이

Docker 컨테이너는 가상 머신(VM)과 비슷해 보이지만, 중요한 차이가 있다. 가상 머신은 운영체제 전체를 포함하므로 무겁고 느린 반면, 컨테이너는 호스트 운영체제의 커널을 공유하므로 가볍고 빠르다.

![가상 머신 vs Docker 컨테이너 아키텍처 비교 다이어그램 — VM은 Guest OS 포함, Container는 커널 공유](../assets/ch3-02-vm-vs-docker.png)

## 3.2 Docker 설치

이 책에서는 모든 개발을 WSL(Windows) 또는 네이티브 리눅스/macOS 환경에서 진행한다. Docker도 WSL 내에서 직접 설치한다. Windows 사용자는 1장에서 설치한 WSL Ubuntu 터미널을 열고 진행한다.

터미널에서 다음 명령을 순서대로 실행한다:

```bash
# Docker 공식 GPG 키 추가
sudo apt-get update
sudo apt-get install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Docker 저장소 추가
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

# Docker 설치
sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

# 현재 사용자를 docker 그룹에 추가 (sudo 없이 docker 사용 가능)
sudo usermod -aG docker $USER
```

설치 후 WSL 터미널을 재시작한다.

### 설치 확인

터미널에서 다음 명령을 실행하여 Docker가 정상적으로 설치되었는지 확인한다:

```bash
docker --version
docker run hello-world
```

![docker run hello-world 실행 시 "Hello from Docker!" 메시지가 출력되는 터미널 스크린샷](../assets/ch3-03-terminal-hello-world.png)

## 3.3 Docker 기본 개념

### 이미지 (Image)

Docker 이미지는 컨테이너를 만들기 위한 **설계도**이다. 운영체제, 프로그램, 설정 파일 등이 모두 포함되어 있다. Docker Hub(https://hub.docker.com/)에서 다양한 공식 이미지를 다운로드할 수 있다.

### 컨테이너 (Container)

컨테이너는 이미지를 기반으로 **실제로 실행되는 인스턴스**이다. 하나의 이미지로 여러 개의 컨테이너를 만들 수 있다.

### Dockerfile

Dockerfile은 Docker 이미지를 만들기 위한 **레시피 파일**이다. 어떤 기반 이미지를 사용하고, 어떤 파일을 복사하고, 어떤 명령을 실행할지 순서대로 기술한다.

```dockerfile
FROM node:20-alpine
WORKDIR /app
COPY package.json .
RUN npm install
COPY . .
CMD ["npm", "run", "dev"]
```

### Docker Compose

여러 개의 컨테이너를 함께 관리해야 할 때 사용하는 도구이다. 예를 들어 웹 서버와 데이터베이스를 동시에 실행해야 하는 경우, `compose.yml` 파일 하나로 모든 서비스를 정의하고 한 번에 실행할 수 있다.

```yaml
services:
  web:
    build: .
    ports:
      - "3000:3000"
  db:
    image: postgres:16
    environment:
      POSTGRES_PASSWORD: mysecret
```

실행은 다음 명령 하나로 가능하다:

```bash
docker compose up
```

![docker compose up 실행 시 웹 서버와 데이터베이스가 동시에 시작되는 터미널 출력 스크린샷](../assets/ch3-04-terminal-compose-up.png)

## 3.4 자주 사용하는 Docker 명령어

| 명령어 | 설명 |
|--------|------|
| `docker compose up` | compose.yml에 정의된 모든 서비스 시작 |
| `docker compose up -d` | 백그라운드에서 서비스 시작 |
| `docker compose down` | 모든 서비스 종료 |
| `docker compose logs` | 서비스 로그 확인 |
| `docker ps` | 실행 중인 컨테이너 목록 |
| `docker exec -it <컨테이너> bash` | 실행 중인 컨테이너에 접속 |

## 3.5 정리

- **Docker는 개발 환경을 코드로 정의하여 일관된 환경을 재현하는 도구**
  - "내 컴퓨터에서는 되는데" 문제를 해결
- **Dockerfile로 이미지를 정의하고, Docker Compose로 여러 서비스를 관리**
  - 웹 서버 + 데이터베이스 등을 한 번에 실행 가능
- **Windows 사용자는 WSL 위에 Docker를 설치하여 사용**
