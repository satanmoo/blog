---
aliases:
  - 01-zsh-command-execution
tags:
  - tutorial
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

## `path`와 asdf shim

위의 `command -V` 의 결과를 이해하기 위해 다음을 먼저 이해해야 함
- zsh에서 `path`의 동작
- asdf의 `PATH` 설정의 원리
- asdf shim 의 동작

### asdf의 PATH 설정 탐구

[[tutorial/mac-dev-setup/asdf/index|asdf 설정 과정]]에서 다음 줄을 `~/.zshrc.local`에 넣었다고 하자

```zsh
export PATH="${ASDF_DATA_DIR:-$HOME/.asdf}/shims:$PATH"
```

이 줄은 asdf shim 디렉터리를 command search path 맨 앞에 두는 설정임

- `export`: `PATH`를 이후 실행되는 command의 environment로 export되도록 표시함
	- [[topics/zsh/reference/shell-builtin-commands/questions#`export`|export]] 참고
- `PATH=...`: `PATH` parameter에 새 값을 대입함
- `${ASDF_DATA_DIR:-$HOME/.asdf}`: `ASDF_DATA_DIR` 값이 있으면 그 값을 사용하고, 없거나 비어 있으면 `$HOME/.asdf`를 사용함
- `/shims`: asdf가 만든 shim executable들이 들어 있는 디렉터리
- `:$PATH`: 기존 `PATH` 값을 뒤에 이어 붙여 기존 검색 경로를 보존함

> [!NOTE] 기존 `PATH` (기존 검색 경로)가 뒤에 붙는 것이 중요함

따라서 위 명령어의 실행 결과는 `${ASDF_DATA_DIR:-$HOME/.asdf}/shims`를 기존 command search path 앞에 추가하는 것임

```zsh
% typeset -p1 PATH
export -T PATH path=(
  <home>/.asdf/shims
  ${PATH} # 기존 PATH 나열...
)
```

참고로 zsh에서는 `PATH`와 `path`가 tied parameter라서 `PATH`를 바꾸면 `path` 배열도 함께 바뀜
- [[topics/zsh/reference/parameters/questions#`PATH` & `path`|Parameters/PATH & path]]

### zsh에서 `path`의 동작

> [!NOTE] 일반적인 zsh 명령 실행 순서는 [[topics/zsh/explanation/how-zsh-execute-command/index|zsh가 명령어를 실행하는 과정]] 참고

`java`에는 `/`가 없으므로 zsh 입장에서 external command

zsh는 external command를 찾을 때 `$path`의 디렉터리를 검색함

```zsh
% typeset -p1 path
typeset -aT PATH path=(
   <home>/.asdf/shims
   ${PATH} # 기존 PATH 나열...
   /usr/bin
   ...
)
```

위처럼 `<home>/.asdf/shims`가 `/usr/bin`보다 앞에 있으면 `java`는 `/usr/bin/java`보다 asdf shim인 `<home>/.asdf/shims/java`로 **먼저** 해석됨

먼저 해석된 command는 command hash table에 기록됨

현재 command hash table에 기록된 entry는 `hash -L`로 확인할 수 있음

```zsh
% hash -L | grep -w java
hash java=<home>/.asdf/shims/java
```

따라서 현재 셸에서 `java` 명령은 해시된 `<home>/.asdf/shims/java`라는 것을 알 수 있음

### asdf shim

`<home>/.asdf/shims/java`는 실제 Java executable이 아니라 asdf shim임

이 shim이 실행되면 asdf는 `.tool-versions` 파일이나 환경 변수 기준으로 사용할 JDK version을 결정하고 특정 JDK의 `java` executable을 실행
- asdf가 version과 최종 executable path를 결정하는 흐름은 [[topics/asdf/explanation/how-asdf-work/index|how-asdf-work]] 참고

asdf 일반 개념과 대응하면 다음과 같음
- tool = java
- tool version = temurin-21.0.x 같은 특정 JDK version
- package = 특정 버젼의 JDK
- executable  = JDK 안의 bin/java

`java` executable에 대한 정보는 [[topics/java/reference/java-command/index|java command]] 참고
