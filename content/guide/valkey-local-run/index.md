---
aliases:
  - 로컬에서 Valkey 실행하기
tags:
  - guide
  - valkey
references: https://valkey.io/
---
# 로컬에서 Valkey 실행하기

## 1. valkey 클론하기

https://github.com/valkey-io/valkey 에서 valkey 프로젝트를 클론


## 2. cmake 빌드

작업 디렉토리에서 다음을 실행

```zsh
% ninja -C cmake-build-debug
```

> [!Info] Clion 으로 실행하면 CMake 감지해서 빌드 해줌

## 3. valkey-server 실행

```zsh
% ./cmake-build-debug/bin/valkey-server
```

![[Pasted image 20260509213418.png]]

기본 포트 6379로 서버가 시작
- `Ready to accept connections tcp` 문구가 나오면 클라이언트를 연결하면 됨

> [!Info] 설정 파일과 함께 서버 실행하기
> 
> `./cmake-build-debug/bin/valkey-server valkey.conf`

## 4. valkey-cli 연결하기

프로젝트에 포함된 valkey-cli를 클라이언트로 valkey-server에 연결할 수 있음

![[Pasted image 20260509214103.png]]
