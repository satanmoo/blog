---
tags:
  - reference
  - questions
  - zsh
aliases:
  - Shell Builtin Commands
references: https://zsh.sourceforge.io/Doc/Release/Shell-Builtin-Commands.html#Shell-Builtin-Commands
---
# Shell Builtin Commands

shell 외부의 실행 파일이 아니라, shell 프로그램 자체 안에 내장되어 있는 명령 

## `command`

> [!QUOTE] synopsis
> 
> `command [ -pvV ] simple command`

> [!QUOTE]
> 
> The simple command argument is taken as an external command instead of a function or builtin and is executed. If the POSIX_BUILTINS option is set, builtins will also be executed but certain special properties of them are suppressed. The -p flag causes a default path to be searched instead of that in $path. With the -v flag, command is similar to whence and with -V, it is equivalent to whence -v.

`command`는 simple command를 실행할 때 shell function이나 builtin보다 external command 실행을 우선하도록 만드는 builtin command임

예를 들어 같은 이름의 shell function이 있어도 `command name` 형태로 실행하면 function이 아니라 external command를 대상으로 함

`POSIX_BUILTINS` 옵션이 설정되어 있으면 builtin도 실행될 수 있지만, 일부 special builtin 성질은 억제됨

### `command -v`

`command -v`는 command name이 어떻게 해석되는지 확인함

zsh 문서에 따르면 `command -v`는 `whence`와 비슷함

```zsh
% command -v cd
cd
% command -v java
<home>/.asdf/shims/java
```

`cd`는 shell builtin이라서 command name 자체가 출력되고, `java` 같은 external command는 실행 파일 경로가 출력될 수 있음

### `command -V`

`command -V`는 `whence -v`와 같음

`command -v`보다 설명적인 형태로 command name의 해석 결과를 출력함

```zsh
% command -V cd
cd is a shell builtin
% command -V java
java is <home>/.asdf/shims/java
```

## `export`

> [!Quote] synopsis
> 
> export [ name[=value] ... ]

> [!Quote]
> 
> The specified names are marked for automatic export to the environment of subsequently executed commands. Equivalent to typeset -gx. If a parameter specified does not already exist, it is created in the global scope.

synopsis 의 `name`이 이후 실행되는 command의 environment로 자동으로 export되는 대상으로 표시됨
- [[topics/zsh/reference/parameters/questions#`-x` 속성 부여|Parameters/-x 속성 부여]] 에서 다룬 **process environment**
- 표시(marked)라고 표현한 이유는 다음과 같음
	- zsh 내부 관점에서 paramter의 어트리뷰트를 추가하는 개념
	- 개념적으로 `typeset -gx NAME=value`와 동일함
		- [[topics/zsh/reference/shell-builtin-commands/questions#`typeset -g`|typeset -g]] 참고
		- [[topics/zsh/reference/shell-builtin-commands/questions#`typeset -x`|typeset -x]] 참고

```zsh
% foo=bar
% typeset -p1 foo
typeset foo=bar
% export foo
% typeset -p1 foo
export foo=bar
```

## `hash`

> [!QUOTE]
> 
> hash can be used to directly modify the contents of the command hash table, and the named directory hash table. Normally one would modify these tables by modifying one’s PATH (for the command hash table) or by creating appropriate shell parameters (for the named directory hash table). The choice of hash table to work on is determined by the -d option; without the option the command hash table is used, and with the option the named directory hash table is used.

여기서 "one"은 셸 사용자를 가리키는 대명사

즉 `one's PATH`는 "사용자의 PATH" 또는 "자신의 PATH" 정도로 이해할 수 있음

`hash`는 zsh 내부의 두 hash table을 직접 수정할 수 있음

1. command hash table
2. named directory hash table

`-d` 옵션이 없으면 command hash table을 대상으로 함

```zsh
% hash foo=/some/executable
```

위 명령은 command hash table에 다음 매핑을 넣는 것과 같음

```text
foo -> /some/executable
```

command hash table은 command name을 실행 파일 경로에 연결하는 zsh 내부 table임
- command hash table 이 어떻게 사용되는지 [[topics/zsh/explanation/how-zsh-execute-command/index|zsh가 명령어를 실행하는 과정]] 참고

보통 command hash table은 `hash`로 직접 수정하기보다 `path` 또는 `PATH`를 수정해서 간접적으로 바꿈
- [[topics/zsh/reference/parameters/questions#`path`|Parameters/path]]

`-d` 옵션이 있으면 named directory hash table을 대상으로 함
- 자세한 `~name` 확장 규칙은 [[topics/zsh/reference/expansion/questions#1472-static-named-directories|Expansion/Static named directories]] 참고

문서에서 "creating appropriate shell parameters"라고 말하는 것은 이런 방식을 가리킴
- https://unix.stackexchange.com/questions/723248/how-can-one-modify-the-named-directory-hash-table-by-creating-appropriate-shell

### `hash -r`

> [!QUOTE]
> 
> The -r option causes the selected hash table to be emptied. It will be subsequently rebuilt in the normal fashion.

`hash -r`은 선택된 hash table을 비움

이후 zsh가 필요할 때 일반적인 방식으로 다시 만듦

`-d`가 없으면 command hash table을 비움

```zsh
% hash -r
```

`-d`와 함께 사용하면 named directory hash table을 비움

```zsh
% hash -d var=/usr/local
% hash -d
var=/usr/local
% hash -dr
% hash -d

```

### `hash -m`

> [!QUOTE]
> 
> The -m option causes the arguments to be taken as patterns (which should be quoted) and the elements of the hash table matching those patterns are printed. This is the only way to display a limited selection of hash table elements.

`hash -m`은 pattern과 일치하는 hash table entry만 출력함

```zsh
% hash -m 'java*'
java=<home>/.asdf/shims/java
```

특정 entry만 확인하고 싶을 때 사용함

### `hash -L`

> [!QUOTE]
> 
> If the -L flag is present, then each hash table entry is printed in the form of a call to hash.

`hash -L`은 hash table entry를 다시 실행 가능한 `hash` command 형태로 출력함
- 다른 셸에서 출력 결과를 그대로 입력해 실행할 수 있음

```zsh
% hash -L
```

예를 들어 command hash table에 `java` entry가 있으면 다음처럼 출력될 수 있음

```zsh
hash java=<home>/.asdf/shims/java
```

## `set`

> [!Quote]
> 
> If the -A flag is not present, but there are arguments beyond the options, the positional parameters are set. If the option list (if any) is terminated by ‘--’, and there are no further arguments, the positional parameters will be unset.

`-A가 없고`, 옵션 처리 뒤에 인자들이 남아 있으면 그 인자들로 positional parameters를 설정함

`set -- a b`
- `set`: builtin command
- `--`: 옵션 처리 종료
- `a`: 옵션 뒤에 남은 첫 번째 일반 인자
- `b`: 옵션 뒤에 남은 두 번째 일반 인자

```zsh
% echo $1
a
% echo $2
b
```

다음과 같이 셸의 positional parameters 전체를 제거할 수 있음

```zsh
% set --
```

```zsh
% echo $1

% echo $2

```

참고: https://unix.stackexchange.com/questions/18981/how-to-unset-the-positional-parameters

## `typeset`

> [!QUOTE]
> 
> Set or display attributes and values for shell parameters.

`typeset`은 shell parameter의 attribute와 value를 설정하거나 출력하는 builtin command임

### `typeset -p`

> [!QUOTE]
> 
> If the -p option is given, parameters and values are printed in the form of a typeset command with an assignment, regardless of other flags and options. Note that the -H flag on parameters is respected; no value will be shown for these parameters.

`typeset -p`는 parameter와 value를 `typeset` command 형태로 출력함

```zsh
% foo=bar
% typeset -p foo
typeset foo=bar
```

readonly attribute도 출력에 포함될 수 있음

```zsh
% readonly ro=val
% typeset -p ro
typeset -r ro=val
```

### `typeset -T`

> [!QUOTE]
> 
> This flag has a different meaning when used with -f; see below. Otherwise the -T option requires zero, two, or three arguments to be present. With no arguments, the list of parameters created in this fashion is shown. With two or three arguments, the first two are the name of a scalar and of an array parameter (in that order) that will be tied together in the manner of \$PATH and \$path. The optional third argument is a single-character separator which will be used to join the elements of the array to form the scalar; if absent, a colon is used, as with $PATH. Only the first character of the separator is significant; any remaining characters are ignored. Multibyte characters are not yet supported.

#### synopsis 분석

> [!QUOTE] synopsis
> 
> `-T [ scalar[=value] array[=(value ...)] [ sep ] ]`

`[]`는 optional 인자를 의미함

크게 가능한 형태는 3가지

#### 1. 인자 없음

`typeset -T`

`-T` 방식으로 tied 된 parameter 목록을 보여줌
- [[topics/zsh/reference/parameters/questions|Parameters]]

#### 2. 인자 2개

scalar 인자 & array 인자를 받음

array의 인자가 :로  합쳐져서 scalar 값으로 scalar 인자에 묶임

```zsh
% typeset -T MY_PATH my_path
% typeset -p MY_PATH
typeset -T MY_PATH my_path=(  )
% typeset -p my_path
typeset -aT MY_PATH my_path=(  )
```

이제 값을 array 쪽에 넣고 확인해보면
- 반드시 한 쪽에만 초기값을 할당해야 함

> [!QUOTE]
> 
> Only one of the scalar and array parameters may be assigned an initial value (the restrictions on assignment forms described above also apply).

```zsh
% my_path=(/bin /usr/bin /opt/bin)
% typeset -p MY_PATH my_path
typeset -T MY_PATH my_path=( /bin /usr/bin /opt/bin )
typeset -aT MY_PATH my_path=( /bin /usr/bin /opt/bin )
```

tied 된 것을 확인할 수 있음

```zsh
% echo $MY_PATH
/bin:/usr/bin:/opt/bin
```

scalar 값으로 출력됨을 확인할 수 있음

#### 3. 인자 3개

seperator을 : 말고 다른 값으로 지정할 수 있음

### `typeset -g`

> [!quote]
> 
> The -g (global) means that any resulting parameter will not be restricted to local scope. Note that this does not necessarily mean that the parameter will be global, as the flag will apply to any existing parameter (even if unset) from an enclosing function. This flag does not affect the parameter after creation, hence it has no effect when listing existing parameters, nor does the flag +g have any effect except in combination with -m (see below).

그냥 `typeset`은 local scope로 만들고, `-g` 옵션을 추가하면 outer scope를 건드림
- 반드시 global scope는 아님

```zsh
# local scope
% unset foo
% demo() { typeset foo=local; echo "inside: $foo" }
% demo
inside: local
% echo "outside: ${foo-unset}"
outside: unset
```

```zsh
# outer scope
% unset foo
% demo() { typeset -g foo=local; echo "inside: $foo" }
% demo
inside: local
% echo "outside: ${foo-unset}"
outside: local
```

```zsh
# -g 옵션이 global scope는 아님을 보여줌
% unset foo
% outer() { typeset foo=outer; inner() { typeset -g foo=changed }; inner; echo "outer: $foo" }
% outer
outer: changed
% echo "top: ${foo-unset}"
top: unset
```

### `typeset -x`

> [!quote]
> 
> Mark for automatic export to the environment of subsequently executed commands. If the option GLOBAL_EXPORT is set, this implies the option -g, unless +g is also explicitly given; in other words the parameter is not made local to the enclosing function. This is for compatibility with previous versions of zsh.

`-x`는 parameter를 이후 실행되는 command의 environment로 자동 export되도록 표시
- [[topics/zsh/reference/parameters/questions#process environment|Parameters -x 옵션 & process environment]] 참고

```zsh
% typeset -x foo=bar
% typeset -p1 foo
export foo=bar
% zsh -fc 'echo $foo' # 새로운 zsh 프로세스 실행
bar
```

위 예시는 새로운 zsh 프로세스가 `foo` paramter를 볼 수 있음을 보여줌

`zsh -fc`
- [[topics/zsh/reference/invocation/questions#`-c` 옵션|Invocation -c 옵션]] 참고
- `-f` 옵션은 startup file을 읽지 않게 하는 옵션임

## `unset`

### synopsis

> [!quote] synopsis
> `unset [ -fmv ] name ...`

옵션 없이 사용하면 다음과 같음
- 이름이 `name`인 paramter을 unset 상태로 만듬

---

> [!quote]
> 
> Each named parameter is unset. Local parameters remain local even if unset; they appear unset within scope, but the previous value will still reappear when the scope ends.

여기서 말하는 "named parameter"은 이름을 가진 paramater

```zsh
% x=hello
% echo $x
hello
% unset x
% echo ${x-unset}
unset
```

local paramter를 unset해도, 바깥 paramter까지 지워지는 것은 아님

```zsh
% x=outer
% f() { local x=inner; echo "before: $x"; unset x; echo "after: ${x-unset}"; }
% f
before: inner
after: unset
% echo $x
outer
```
