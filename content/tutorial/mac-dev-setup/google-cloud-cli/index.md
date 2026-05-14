---
tags:
  - google-cloud-cli
  - mac
  - google-cloud-platform
references: https://docs.cloud.google.com/sdk/docs/install-sdk
---
## 1. 설치

설치 전 `Python 3.10 to 3.14` 설치 확인
- 보통 Xcode Command Line Tools와 함께 설치해서 있을 거임

```zsh
which python3
```

```zsh
brew update && brew install --cask gcloud-cli
```

### 1-1. 환경변수 등록

macOS 전용 설정이니 `~/.zshrc.local`에 추가:

```zsh
# Google Cloud SDK
export PATH="/opt/homebrew/share/google-cloud-sdk/bin:$PATH"
```

## 2. gcloud CLI 초기화 및 인가

### 2-1. 초기화

```zsh
gcloud init
```

실행하면 브라우저에서 인가 절차 진행

cli로 돌아와서 디폴트 프로젝트 등등 설정
