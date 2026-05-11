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

### synopsis 분석

> [!QUOTE] synopsis
> 
> `-T [ scalar[=value] array[=(value ...)] [ sep ] ]`

`[]`는 optional 인자를 의미함

크게 가능한 형태는 3가지

#### 1. 인자 없음

`typeset -T`

`-T` 방식으로 tied 된 parameter 목록을 보여줌
- [[concepts/zsh/reference/parameters/questions|Parameters]]

#### 2. 인자 2개

scalar 인자 & array 인자를 받음

array의 인자가 :로  합쳐져서 scalar 값으로 scalar 인자에 묶임

```zsh
typeset -T MY_PATH my_path
typeset -p MY_PATH                                                     
# 출력 결과: typeset -T MY_PATH my_path=(  )
typeset -p my_path
# 출력 결과: typeset -aT MY_PATH my_path=(  )
```

이제 값을 array 쪽에 넣고 확인해보면
- 반드시 한 쪽에만 초기값을 할당해야 함

> [!QUOTE]
> 
> Only one of the scalar and array parameters may be assigned an initial value (the restrictions on assignment forms described above also apply).

```zsh
my_path=(/bin /usr/bin /opt/bin)
typeset -p MY_PATH my_path

# 출력 결과: 
# typeset -T MY_PATH my_path=( /bin /usr/bin /opt/bin )
# typeset -aT MY_PATH my_path=( /bin /usr/bin /opt/bin )
```

tied 된 것을 확인할 수 있음

```zsh
echo $MY_PATH

# 출력 결과: /bin:/usr/bin:/opt/bin
```

scalar 값으로 출력됨을 확인할 수 있음

#### 3. 인자 3개

seperator을 : 말고 다른 값으로 지정할 수 있음

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
hash foo=/some/executable
```

위 명령은 command hash table에 다음 매핑을 넣는 것과 같음

```text
foo -> /some/executable
```

command hash table은 command name을 실행 파일 경로에 연결하는 zsh 내부 table임
- [[concepts/zsh/explanation/command-execution/01-command-execution#command hash table|01. zsh command execution/command hash table]]

보통 command hash table은 `hash`로 직접 수정하기보다 `path` 또는 `PATH`를 수정해서 간접적으로 바꿈
- [[concepts/zsh/reference/parameters/questions#`path`|Parameters/path]]

`-d` 옵션이 있으면 named directory hash table을 대상으로 함

```zsh
hash -d proj=/Users/me/projects/blog
```

위 명령은 named directory hash table에 다음 매핑을 넣는 것과 같음

```text
proj -> /Users/me/projects/blog
```

그러면 `~proj`가 `/Users/me/projects/blog`로 확장될 수 있음

```zsh
cd ~proj
```

zsh의 Static named directories 설명에 따르면 `~` 뒤의 이름은 named directory로 조회되고, `hash -d`로 directory name을 정의할 수도 있음

또한 값이 `/`로 시작하는 string shell parameter를 만들어 named directory처럼 사용할 수도 있음

```zsh
proj=/Users/me/projects/blog
cd ~proj
```

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
hash -r
```

### `hash -f`

> [!QUOTE]
> 
> The -f option causes the selected hash table to be fully rebuilt immediately.

`hash -f`는 선택된 hash table을 즉시 다시 만듦

command hash table을 대상으로 하면 `PATH` 안의 absolute directory들을 기준으로 다시 hash함

```zsh
hash -f
```

### `hash -m`

> [!QUOTE]
> 
> The -m option causes the arguments to be taken as patterns (which should be quoted) and the elements of the hash table matching those patterns are printed. This is the only way to display a limited selection of hash table elements.

`hash -m`은 pattern과 일치하는 hash table entry만 출력함

```zsh
hash -m 'java*'
```

특정 entry만 확인하고 싶을 때 사용함

### `hash -L`

> [!QUOTE]
> 
> If the -L flag is present, then each hash table entry is printed in the form of a call to hash.

`hash -L`은 hash table entry를 다시 실행 가능한 `hash` command 형태로 출력함

```zsh
hash -L
```

예를 들어 command hash table에 `java` entry가 있으면 다음처럼 출력될 수 있음

```zsh
hash java=/Users/me/.asdf/shims/java
```
