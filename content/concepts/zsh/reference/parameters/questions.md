---
aliases:
  - Parameters
tags:
  - reference
  - questions
  - zsh
references: https://zsh.sourceforge.io/Doc/Release/Parameters.html#Parameters
---
# Parameters

## 15.1 Description

### type

어트리뷰트가 타입을 결정함

### scalar

타입에 속하는 개념

string, integer, floating point number
- 모두 단일 값
- 원소 하나짜리

### `-x` 속성 부여

> [!QUOTE]
> 
> Named scalar parameters may have the _exported_, -x, attribute, to copy them into the process environment, which is then passed from the shell to any new processes that it starts. Exported parameters are called _environment variables_.

export에서 따온 속성 이름

Named scalar paremeter
- 값이 scalar인 parameter
- https://unix.stackexchange.com/questions/805817/what-does-named-scalar-parameters-mean-in-the-zsh-manual

process environment
- OS 수준 개념
- 프로세스가 메모리 안에 가지는 영역
- KEY=VALUE 형태의 문자열 배열

```
PATH=/usr/local/bin:/usr/bin:/bin
HOME=/home/sigmastudio
LANG=ko_KR.UTF-8
SHELL=/bin/zsh
...
```

export 속성이 붙은 변수를 셸은 자기 process environment에 복사함

process environment에 추가된 값은 자식 프로세스가 볼 수 있음
- 자식 프로세스에 복사됨
- 자식이 볼 수 없는 **자기 변수 테이블**도 존재함

자식 프로세스는 부모 프로세스가 export한 변수만 볼 수 있음
- 이런 변수를 **environment variables** 라고 정의함

> [!QUOTE]
> 
> he shell also _imports_ environment variables at startup time and automatically marks the corresponding parameters as exported. Some environment variables are not imported for reasons of security or because they would interfere with the correct operation of other shell features.

쉘도 누군가의 자식 프로세스
- 터미널 에뮬레이터

1. 미리 등록 해놓은 environment variable을 자기 변수 테이블에 복사
2. 자동으로 `-x` 속성을 부여
	- 자동으로 process environment 복사
	- 자식 프로세스가 볼 수 있음

### *special* parameter

> [!QUOTE]
> 
> Parameters may also be _special_, that is, they have a predetermined meaning to the shell. Special parameters cannot have their type changed or their readonly attribute turned off, and if a special parameter is unset, then later recreated, the special properties will be retained.

셀이 미리 정의해둔, 고정된 의미의 parameter
- 예약됨
- 타입 변경 불가능

일반 변수(variable)에 `-r` 속성을 주면 변수가 읽기 전용이 됨
- value 변경 불가

`+r` 속성을 주면 읽기 전용이 꺼짐

special parameter `+r`로 읽기 전용을 끌 수 없음

일반 변수는 unset 가능하지만, special parameter는 unset 해도 다시 살아남
- 속성(the special properties)은 다음을 말함
	- read only
	- 타입 변경 불가능

#### 확인 예시

일반 parameter는 type을 바꿀 수 있음

```zsh
foo=hello
```

셸 내부 변수를 선언하는 명령어
- foo parameter의 값은 string (scalar)
- foo는 현재 셸 내부 변수

```zsh
typeset -p foo # parmeter foo의 현재 정의를 출력하라는 명령

# 출력 결과: typeset foo=hello
```

```zsh
typeset -i foo
```

foo를 정수 paramter로 다루겠다는 명령
- type 변경
- hello라는 문자열 값은 유효한 숫자 표현식이 아니라 정수의 기본값 0이 할당
- `-i` 어트리뷰트가 추가됨

정수 paramter로 변경 후 출력해보면 아래와 같음

```zsh
typeset -p foo

# 출력 결과: typeset -i foo=0
```

integer attribute를 다시 끌 수도 있음

```zsh
typeset +i foo
```

```zsh
typeset -p foo

# 출력 결과: typeset foo=0
```

일반 parameter는 readonly attribute도 끌 수 있음

아래와 같이 `-r`  어트리뷰트를 붙인 채 변수 선언

```zsh
readonly bar=value
```

```zsh
typeset -p bar

# 출력 결과: typeset -r bar=value
```

`-r` 어트리뷰트 제거 후 확인하기

```zsh
typeset +r bar
```

```zsh
typeset -p bar

# 출력 결과: typeset bar=value
```

일반 parameter는 unset 후 다시 만들면 이전 type attribute가 유지되지 않음

```zsh
typeset -i baz=123
typeset -p baz

# 출력 결과: typeset -i baz=123
```

```zsh
unset baz
typeset -p baz

# 출력 결과: typeset: no such variable: baz
```

아래와 같이 unset 후 새로 만들었을 때 이전 type attribute인 `-i`가 유지 되지 않음

```zsh
baz=abc
typeset -p baz

# 출력 결과: typeset baz=abc
```

반면 special parameter는 type 변경을 시도하면 실패함

```zsh
typeset -p RANDOM

# 출력 결과: typeset -i10 RANDOM=7475
```

```zsh
typeset +i RANDOM
```

타입 변경을 시도하면 아래와 같이 오류 메시지 출력

```zsh
zsh:typeset: RANDOM: can't change type of a special parameter
```

special parameter는 readonly attribute를 끄려고 해도 실패함

먼저 `RANDOM`에 readonly attribute를 붙임

```zsh
readonly RANDOM
typeset -p RANDOM

# 출력 결과: typeset -i10 -r RANDOM=7544
```

일반 parameter에서는 `typeset +r`로 readonly attribute를 끌 수 있었지만, special parameter에서는 실패함

```zsh
typeset +r RANDOM
```

```zsh
zsh:typeset: RANDOM: can't change type of a special parameter
```

에러 메시지는 type 변경처럼 보이지만, 여기서는 `+r`로 readonly attribute 제거를 시도한 상황

### 대입 시 expansion

> [!QUOTE]
> 
> The value of a scalar parameter may also be assigned by writing:
> 
> > name=value
>  
> In scalar assignment, value is expanded as a single string, in which the elements of arrays are joined together; filename expansion is not performed unless the option GLOB_ASSIGN is set.

expansion
- 셸 용어로 확장
	- 문자열을 가공/변환하는 처리

`name=value`
- 위 식에서 우변(value)에 어떤 expansion 규칙이 적용되어 좌변에 대입됨

파일 확장자는 기본적으로 확장이 적용되지 않음

#### 확인 예시

```zsh
arr=(a b c)
typeset -p arr

# 출력 결과: typeset -a arr=( a b c )
```

`-a`는 indexed array attribute

```zsh
foo=$arr
typeset -p foo

# 출력 결과: typeset foo='a b c'
```

array의 원소들이 join 되어 하나의 scalar string이 됨

```zsh
touch a.txt b.txt
```

위와 같은 파일들이 있을 때

```zsh
foo=*.txt
typeset -p foo

# 출력 결과: typeset foo='*.txt'
```

\*.txt 가 `a.txt b.txt`로 glob expansion 되지 않았음

```zsh
setopt GLOB_ASSIGN
foo=*.txt
typeset -p foo

# 출력 결과: typeset -a foo=( a.txt b.txt )
```

`GLOB_ASSIGN`을 키고 다시 시도해보자

glob expansion이 적용되어 배열로 적용됨

## 15.6 Parameters Used By The Shell

### `sh`, `ksh` emulation mode

> [!QUOTE]
> 
> The following parameters are used by the shell. Again, ‘\<S>’ indicates that the parameter is special and ‘\<Z>’ indicates that the parameter does not exist when the shell initializes in sh or ksh emulation mode.

`sh, ksh`emulation mode는 zsh이 다른 shell처럼 동작하려고 호환 모드로 시작되는 경우를 말함

### `path`

> [!QUOTE] 
> 
> `path <S> <Z> (PATH <S>)`

`<S>` path가 special parameter라는 표시
- [[#*special* parameter]]

`<Z>` path는 zsh 고유 성격의 parameter라서 `sh`, `ksh` emulation mode 에서 동작하지 않음

`PATH <S>`와 tied 됨
- [[#`PATH` & `path`]] 참고

> [!QUOTE]
> 
> An array (colon-separated list) of directories to search for commands. When this parameter is set, each directory is scanned and all files found are put in a hash table.

zsh는 `path`의 원소
- 이 원소는 디렉터리
	- 디렉터리 목록에서 명령어로 입력 받은 실행 파일을 찾음

zsh 내부에는 look up table 존재
- `path`가 설정되면 zsh는 scan해서 `git -> /opt/homebrew/bin/git` 이런식으로 내부 look up table에 등록
	- 여기서 설정은 `path=(/bin /usr/bin)` 처럼 parameter 에 값 대입을 말함
- 명령어를 실행할 때 매번 탐색하는 것이 아니라 look up table을 조회해 효율적으로 처리

```zsh
hash -L
```
- [[concepts/zsh/reference/shell-builtin-commands/questions#`hash -L`|Shell Builtin Commands/Hash/Hash -L]] 참고


### `PATH` & `path`

> [!QUOTE]
> 
> In cases where there are two parameters with an upper- and lowercase form of the same name, such as path and PATH, the lowercase form is an array and the uppercase form is a scalar with the elements of the array joined together by colons. These are similar to tied parameters created via ‘typeset -T’. The normal use for the colon-separated form is for exporting to the environment, while the array form is easier to manipulate within the shell. Note that unsetting either of the pair will unset the other; they retain their special properties when recreated, and recreating one of the pair will recreate the other.

`path` 소문자는 array 타입
- 셸 안에서 조작하기 편한 용도

`PATH` 대문자는 배열 원소들을 콜론으로 이어 붙인 scalar(string)
- 보통 환경변수로 export 하는 용도

둘은 `typeset -T`로 만든 tied parameter과 유사함
- 둘 중 하나를 unset 하면 나머지도 unset
- 하나를 다시 생성하면 나머지도 자동 재생성
- [[concepts/zsh/reference/shell-builtin-commands/questions#`typeset`|Shell Builtin Commands/typeset]] 설명과 원문 참고
