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

### special parameter

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

### scalar assignment

> [!QUOTE]
> 
> The value of a scalar parameter may also be assigned by writing:
> 
> > name=value
> 
> In scalar assignment, value is expanded as a single string, in which the elements of arrays are joined together; filename expansion is not performed unless the option GLOB_ASSIGN is set.

scalar parameter에는 `name=value` 형태로 값을 할당할 수 있음

```zsh
foo=hello
typeset -p foo

# 출력 결과: typeset foo=hello
```

expansion
- 셸 용어로 확장
	- 문자열을 가공/변환하는 처리

`name=value`
- 위 식에서 우변(value)에 scalar assignment expansion 규칙이 적용되어 좌변에 대입됨

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

## 15.2 Array Parameters

> [!QUOTE]
> 
> To assign an array value, write one of:
> 
> > `set -A` name value ...
> 
> > name`=(`value ...`)`
> 
> > name`=(``[`key`]=`value ...`)`

array parameter에 값을 할당하는 방법은 여러 가지가 있음

`set -A`를 사용할 수 있음

```zsh
% set -A arr a b c
% typeset -p arr
typeset -a arr=( a b c )
```

`name=(value ...)` 형태의 array assignment를 사용할 수도 있음

```zsh
% arr=(a b c)
% typeset -p arr
typeset -a arr=( a b c )
```

`name=([key]=value ...)` 형태로 index를 직접 지정할 수도 있음

```zsh
% arr=([1]=a [3]=c)
% typeset -p arr
typeset -a arr=( a '' c )
```

`[key]` 형식은 index를 직접 지정하고 싶을 때 사용하는 형태임

값을 순서대로 넣는 일반적인 경우에는 보통 `name=(value ...)` 형태만으로 충분함

> [!QUOTE]
> 
> To append to an array without changing the existing values, use one of the following:
> 
> > name`+=(`value ...`)`
> 
> > name`+=(``[`key`]=`value ...`)`

기존 array 값을 유지하면서 뒤에 원소를 추가할 수도 있음

```zsh
arr+=(d e)
typeset -p arr

# 출력 결과: typeset -a arr=( a b c d e )
```

## 15.3 Positional Parameters

> [!Quote] The positional parameters provide access to the command-line arguments of a shell function, shell script, or the shell itself; see [Invocation](https://zsh.sourceforge.io/Doc/Release/Invocation.html#Invocation), and also [Functions](https://zsh.sourceforge.io/Doc/Release/Functions.html#Functions). The parameter n, where n is a number, is the nth positional parameter. The parameter ‘$0’ is a special case, see [Parameters Set By The Shell](https://zsh.sourceforge.io/Doc/Release/Parameters.html#Parameters-Set-By-The-Shell).

shell function 예시는 다음과 같음

```zsh
% f () { print "1=$1"; print "2=$2"; }
% f a b
1=a
2=b
```

`$1`은 첫번째 인자, `$2`는 두번째 인자
- 현재 실행 문맥에 들어온 인자들을 번호로 꺼내는 개념

shell script 예시는 다음과 같음

```zsh
vim script.zsh
```

위와 같이 텍스트 에디터를 연 뒤

```vim
print "0=$0"
print "1=$1"
print "2=$2"
print "all=$@"
```

위와 같이 작성하고 저장

```zsh
% zsh script.zsh a b
0=script.zsh
1=a
2=b
all=a b
```

shell itself 예시는 다음과 같음
- [[concepts/zsh/reference/invocation/questions#`-s` 옵션|Invocation -s 옵션]] 참고

```zsh
% zsh -s arg1 arg2
% echo $1
arg1
% echo $2
arg2
```

---

> [!Quote]
> 
> Positional parameters may be changed after the shell or function starts by using the set builtin, by assigning to the argv array, or by direct assignment of the form ‘n=value’ where n is the number of the positional parameter to be changed. This also creates (with empty values) any of the positions from 1 to n that do not already have values. Note that, because the positional parameters form an array, an array assignment of the form ‘n=(value ...)’ is allowed, and has the effect of shifting all the values at positions greater than n by as many positions as necessary to accommodate the new values.

positional parameters가 프로세스 시작할 때 만 정해지는 것이 아니라, 실행 중에도 바뀔 수 있음을 설명함

`set` builtin 을 사용하는 예시는 다음과 같음
- [[concepts/zsh/reference/shell-builtin-commands/questions#`set`|Shell Builtin Commands-set]]  참고

```zsh
% zsh -s a b c # 새로운 zsh 프로세스 시작
% echo $1
a
% echo $2
b
% echo $3
c
```

`set` builtin으로 positional parameters를 다시 설정할 수 있음

```zsh
% set -- x y
% echo $1
x
% echo $2
y
% echo ${3-unset}
unset
```

`argv` array에 할당해도 positional parameters가 바뀜
- `argv` 자체에 대한 설명은 [[#15.5 Parameters Set By The Shell]] 참고

```zsh
% argv=(x y z)
% echo $1
x
% echo $2
y
% echo $3
z
```

`n=value` 형태로 특정 positional parameter만 직접 바꿀 수도 있음

```zsh
% set -- a b c
% 2=changed
% echo $1
a
% echo $2
changed
% echo $3
c
```

위 세 방법은 모두 shell, function, script가 시작된 뒤 positional parameters를 변경하는 방법임

## 15.5 Parameters Set By The Shell

> [!QUOTE]
> 
> In the parameter lists that follow, the mark ‘\<S>’ indicates that the parameter is special. ‘\<Z>’ indicates that the parameter does not exist when the shell initializes in sh or ksh emulation mode.

`<S>`는 special parameter를 의미함

`<Z>`는 shell이 `sh` 또는 `ksh` emulation mode로 초기화될 때 존재하지 않는 parameter를 의미함

> [!QUOTE]
> 
> The parameters ‘!’, ‘#’, ‘*’, ‘-’, ‘?’, ‘@’, ‘$’, ‘ARGC’, ‘HISTCMD’, ‘LINENO’, ‘PPID’, ‘status’, ‘TTYIDLE’, ‘zsh_eval_context’, ‘ZSH_EVAL_CONTEXT’, and ‘ZSH_SUBSHELL’ are read-only and thus cannot be restored by the user, so they are not output by ‘typeset -p’. This also applies to many read-only parameters loaded from modules.

restoring
- 재현하는 개념
- [[concepts/zsh/reference/shell-builtin-commands/questions#`typeset -p`|typeset -p]] 에서 말하는 `typeset`command 형태가 재현하는 개념

```zsh
% x=hello;readonly ro val;arr=(one two)
% typeset -p x ro arr
typeset x=hello
typeset -r ro=val
typeset -a arr=( one two )
```

`typeset -p` 의 출력은 그냥 설명용 텍스트가 아니라 다시 zsh에 입력해서 실행할 수 있는 shell snippet
- 다시 zsh에 입력해서 사용할 수 있기에 "restoring"이라고 부름
- https://unix.stackexchange.com/questions/805913/what-does-zsh-mean-by-read-only-parameters-cannot-be-restored-by-the-user-in-r/805915#805915 참고

`status`, `ARGC`는 값은 존재하지만 `typeset -p`로 출력되지 않음
- 셸에서 사용자가 설정할 수 없는 값(Parameters Set By The Shell)이니까 재현이 성립하지 않음

```zsh
% echo $status
0
% echo $ARGC
0
% typeset -p status ARGC
```

> [!Quote]
> 
> argv `<S> <Z>`
> 
> Same as `*`. Assigning to argv changes the local positional parameters, but argv is _not_ itself a local parameter. Deleting argv with unset in any function deletes it everywhere, although only the innermost positional parameter array is deleted (so * and @ in other scopes are not affected).

> [!TODO] argv 실습

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
- [[#special parameter]]

`<Z>` path는 zsh 고유 성격의 parameter라서 `sh`, `ksh` emulation mode 에서 동작하지 않음

`PATH <S>`와 tied 됨
- [[#`PATH` & `path`]] 참고

> [!QUOTE]
> 
> An array (colon-separated list) of directories to search for commands. When this parameter is set, each directory is scanned and all files found are put in a hash table.

여기서 `set`은 `set` builtin command만 말하는 것이 아님

`path` parameter의 값이 설정되거나 변경되는 경우 전반을 말한다고 이해하면 됨
- 구체적인 array assignment 문법은 [[#15.2 Array Parameters]] 참고

예를 들어 `path`는 array parameter이므로 다음처럼 값을 설정할 수 있음

```zsh
path=(/bin /usr/bin)
```

```zsh
set -A path /bin /usr/bin
```

기존 값을 유지하면서 뒤에 디렉터리를 추가할 수도 있음

```zsh
path+=(/opt/homebrew/bin)
```

`path`의 원소는 디렉터리임

zsh는 이 디렉터리 목록에서 command name으로 입력 받은 실행 파일을 찾음

zsh 내부에는 command hash table이 존재함
- `path`가 설정되면 zsh는 scan해서 `git -> /opt/homebrew/bin/git` 이런식으로 command hash table에 등록
	- command hash table은 command name을 실행 파일 경로에 연결하는 zsh 내부 table임
	- [[concepts/zsh/explanation/command-execution/01-command-execution#command hash table|01. zsh command execution/command hash table]] 참고
- 명령어를 실행할 때 매번 물리적으로 모든 디렉터리를 scan하는 것이 아니라 command hash table을 조회해 효율적으로 처리

command hash table 을 출력하기 위해 아래 명령어를 실행하면 됨

```zsh
hash -L
```
- [[concepts/zsh/reference/shell-builtin-commands/questions#`hash -L`|Shell Builtin Commands/Hash/Hash -L]] 참고

#### `PATH` & `path`

[zsh 문서 Paratmeters](https://zsh.sourceforge.io/Doc/Release/Parameters.html#Parameters)에서 "15.6 Parameters Used By The Shell" 섹션에는 아래와 같이 설명함

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

따라서 `PATH` scalar 쪽을 설정해도 `path` array가 함께 바뀜
- scalar assignment 문법 자체는 [[#scalar assignment]] 참고

```zsh
PATH=/bin:/usr/bin
typeset -p path

# 출력 결과: typeset -aT PATH path=( /bin /usr/bin )
```

`PATH`는 보통 environment variable로 export 하는 용도이므로 다음처럼 설정하는 경우도 많음

```zsh
export PATH=/bin:/usr/bin
```
