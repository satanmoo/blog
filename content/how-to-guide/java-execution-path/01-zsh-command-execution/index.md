---
aliases:
  - 01-zsh-command-execution
tags:
  - how-to-guide
---
# 01. zsh에서 `java -version` 실행 경로 확인하기

터미널에서 아래처럼 입력한다고 하자

```zsh
% java -version
```

zsh의 simple command 기준으로 이 입력에는 두 개의 word가 있음

- `java`
- `-version`

첫 번째 word인 `java`는 command name이고, `-version`은 `java`에 전달되는 argument임

일반적인 zsh 명령 실행 순서는 [[topics/zsh/explanation/how-zsh-execute-command/index|zsh가 명령어를 실행하는 과정]] 참고

## `java`가 무엇으로 해석되는지 확인하기

`java`라는 command name이 실제로 무엇을 가리키는지는 `command -V`로 확인할 수 있음

```zsh
% command -V java
java is /usr/bin/java
```

또는 asdf를 사용하고 있다면 아래처럼 shim 경로가 나올 수 있음

```zsh
% command -V java
java is <home>/.asdf/shims/java
```

## `$path`와 asdf shim

`java`에는 `/`가 없으므로 zsh 입장에서 external command

 zsh는 external command를 찾을 때 `$path`의 디렉터리를 검색함
 - [[topics/zsh/reference/parameters/questions#`path`|Parameters/path]] 참고

```zsh
% typeset -p1 path
typeset -aT PATH path=(
   <home>/.asdf/shims
   /opt/homebrew/bin
   /usr/bin
   /bin
)
```

위처럼 `<home>/.asdf/shims`가 `/usr/bin`보다 앞에 있으면 `java`는 `/usr/bin/java`보다 asdf shim인 `<home>/.asdf/shims/java`로 먼저 해석될 수 있음

> [!TODO] [[topics/zsh/explanation/how-zsh-execute-command/index|how zsh command execution]] 참고해서 연결

이 선택은 `$path` 검색과 zsh의 command hash table을 통해 이루어질 수 있음

현재 command hash table에 기록된 entry는 `hash -L`로 확인할 수 있음

```zsh
% hash -L | grep java
hash java=<home>/.asdf/shims/java
```

즉 `java -version` 실행은 대략 다음처럼 볼 수 있음

1. `java -version`을 simple command로 해석함
2. `java`를 command name으로, `-version`을 argument로 구분함
3. `java`에 `/`가 없으므로 function, builtin, external command 순서로 해석함
4. external command 검색이 필요하면 `$path`와 command hash table을 통해 실행 파일 경로를 찾음
5. 찾은 실행 파일에 `-version` argument를 전달해서 실행함
