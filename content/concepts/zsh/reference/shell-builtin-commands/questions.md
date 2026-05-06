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

## `typeset -T`

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

## `hash`

> [!QUOTE]
> 
> hash can be used to directly modify the contents of the command hash table, and the named directory hash table. Normally one would modify these tables by modifying one’s PATH (for the command hash table) or by creating appropriate shell parameters (for the named directory hash table). The choice of hash table to work on is determined by the -d option; without the option the command hash table is used, and with the option the named directory hash table is used.

### `hash -L`

> [!QUOTE]
> 
> If the -L flag is present, then each hash table entry is printed in the form of a call to hash.
