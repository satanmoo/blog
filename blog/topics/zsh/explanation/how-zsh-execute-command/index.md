---
tags:
  - zsh
  - explanation
aliases:
  - how zsh command execution
---
# how zsh command execution

터미널에서 아래처럼 입력한다고 하자

```zsh
% grep TODO README.md
```

zsh의 Shell Grammar 기준으로 이 입력은 [[topics/zsh/reference/shell-grammer/questions#Simple Commands|Simple Command]]로 볼 수 있음

이 입력에는 세 개의 word가 있음 (공백에 따라 구분)
- `grep`
- `TODO`
- `README.md`

simple command에서 첫 번째 word는 실행할 command name이고, 나머지 word는 command에 전달되는 argument임

따라서 이 입력에서 command name은 `grep`이고, argument는 `TODO`, `README.md`임

command가 아직 실행된 것은 아님

이제 zsh는 `grep`이라는 command name이 무엇을 가리키는지 찾아야 함

## command name과 argument

command name은 실행 대상을 찾는 데 사용됨

argument는 command name이 가리키는 대상이 실행될 때 전달됨

예를 들어 아래 입력에서 command name은 `cd`이고 argument는 `/tmp`임

```zsh
% cd /tmp
```

아래 입력에서 command name은 `/bin/ls`이고 argument는 `-l`임

```zsh
% /bin/ls -l
```

zsh가 command name을 해석하는 방식은 command name에 `/`가 포함되어 있는지에 따라 달라짐

## command name에 slash가 있는 경우

[zsh 문서의 Command Execution](https://zsh.sourceforge.io/Doc/Release/Command-Execution.html#Command-Execution) 설명은 command name에 `/`가 없는 경우를 기준으로 shell function, shell builtin, `$path` 검색 흐름을 설명함

따라서 command name에 `/`가 있으면 이 흐름에 들어가지 않는 것으로 이해할 수 있음

```zsh
% /bin/ls -l
```

이 예시에서 command name은 `/bin/ls`이고 argument는 `-l`임

즉 `ls`라는 command name을 `$path`에서 검색하는 예시가 아니라, `/bin/ls`라는 경로로 주어진 command name을 직접 실행하는 예시임

## command name에 slash가 없는 경우

[zsh 문서의 Command Execution](https://zsh.sourceforge.io/Doc/Release/Command-Execution.html#Command-Execution) 설명은 command name에 `/`가 없는 경우를 기준으로 다음 순서로 설명함

> [!QUOTE]
>
> If a command name contains no slashes, the shell attempts to locate it. If there exists a shell function by that name, the function is invoked as described in Functions. If there exists a shell builtin by that name, the builtin is invoked.

즉 `grep`이나 `cd`처럼 `/`가 없는 command name이면 zsh는 먼저 shell function과 shell builtin을 확인함

## shell function

같은 이름의 shell function이 있으면 zsh는 그 function을 호출함

```zsh
% example() { print "function"; }
% command -V example
example is a shell function
```

이 경우 `example`은 external command 검색으로 넘어가지 않음
- [[topics/zsh/reference/shell-builtin-commands/questions#`command -V`|Shell Builtin Commands/command -V]] 참고

## shell builtin

예를 들어 `cd`는 external command가 아니라 shell builtin임

```zsh
% command -V cd
cd is a shell builtin
```

shell builtin은 현재 shell 프로세스 안에서 실행되는 명령임
- [[topics/zsh/reference/shell-builtin-commands/questions|Shell Builtin Commands]] 참고

`cd`처럼 shell 상태를 직접 바꿔야 하는 명령은 builtin이어야 함

## external command와 `path`

`/`가 없는 command name이 shell function도 아니고 shell builtin도 아니면 zsh는 external command를 찾아야 함

[zsh 문서의 Command Execution](https://zsh.sourceforge.io/Doc/Release/Command-Execution.html#Command-Execution) 설명은 external command 검색을 다음처럼 설명함

> [!QUOTE]
>
> Otherwise, the shell searches each element of `$path` for a directory containing an executable file by that name.

`path`는 디렉터리들의 array이고, `PATH`는 그 원소들을 `:`로 이어 붙인 scalar임
- [[topics/zsh/reference/parameters/questions#`PATH` & `path`|Parameters/PATH & path]]

```zsh
% typeset -p1 path
typeset -aT PATH path=(
   /opt/homebrew/bin
   /usr/bin
   /bin
)
```

이 말은 zsh가 external command를 찾을 때 `/opt/homebrew/bin`, `/usr/bin`, `/bin` 같은 `$path`의 각 디렉터리에서 command name과 같은 이름의 executable file을 찾는다는 뜻임
- 이때 출력에서 앞에 나오는 directory가 뒤에 나오는 directory보다 검색 우선순위가 높음

예를 들어 같은 이름의 executable file이 서로 다른 `$path` 원소에 있으면, 앞에 있는 directory의 파일이 먼저 선택됨

```zsh
% original_path=("${path[@]}")
% typeset -p1 original_path
typeset -a original_path=(
  /opt/homebrew/bin
  /opt/homebrew/sbin
  /usr/local/bin
  /usr/bin
  /bin
  /usr/sbin
  /sbin
  ...
)
% tmp=$(mktemp -d)
% typeset -p1 tmp
typeset tmp=/var/folders/w8/cc8p7b290972mnkcffwyf3mc0000gn/T/tmp.lZ57AZWCaH
% mkdir -p "$tmp/first" "$tmp/second"
% ls "$tmp"
first   second
% printf '%s\n%s\n' '#!/bin/sh' 'echo first' > "$tmp/first/demo-command"
% printf '%s\n%s\n' '#!/bin/sh' 'echo second' > "$tmp/second/demo-command"
% cat "$tmp/first/demo-command"
#!/bin/sh
echo first
% cat "$tmp/second/demo-command"
#!/bin/sh
echo second
% chmod +x "$tmp/first/demo-command" "$tmp/second/demo-command"
% path=("$tmp/first" "$tmp/second" "${original_path[@]}")
% typeset -p1 path
typeset -aT PATH path=(
  /var/folders/w8/cc8p7b290972mnkcffwyf3mc0000gn/T/tmp.lZ57AZWCaH/first
  /var/folders/w8/cc8p7b290972mnkcffwyf3mc0000gn/T/tmp.lZ57AZWCaH/second
  ...
)
% command -V demo-command
demo-command is /.../first/demo-command
% demo-command
first
% path=("$tmp/second" "$tmp/first" "${original_path[@]}")
% command -V demo-command
demo-command is /.../second/demo-command
% demo-command
second
% path=("${original_path[@]}") # 원상 복원
% rm -rf "$tmp"
```

위에서 사용한 셸 명령에 대한 설명
- `original_path=("${path[@]}")`는 현재 `path` 배열을 복사해 두는 명령
- `mktemp -d`는 임시 디렉터리 하나를 만들고 그 경로를 출력하는 명령
- `printf '%s\n%s\n' '#!/bin/sh' 'echo first' > "$tmp/first/demo-command"`는 두 줄짜리 shell script를 `demo-command` 파일에 쓰는 명령
- `chmod +x`는 만든 파일을 실행 가능하게 바꾸는 명령

즉 같은 command name이 여러 `$path` 원소에 있어도 앞에 있는 directory의 executable file이 먼저 선택됨

## command hash table

[zsh 문서의 Parameters](https://zsh.sourceforge.io/Doc/Release/Parameters.html#Parameters) 에서 `path` 에 대해서 다음처럼 설명함

> [!QUOTE]
>
> An array (colon-separated list) of directories to search for commands. When this parameter is set, each directory is scanned and all files found are put in a hash table.

여기서 말하는 hash table이 command hash table임

command hash table은 zsh 내부의 매핑으로 이해하면 됨

```text
grep -> /usr/bin/grep
git  -> /opt/homebrew/bin/git
ls   -> /bin/ls
```

즉 command name을 실행 파일 경로에 연결하는 table임
- zsh는 command hash table을 통해 이미 찾은 command location을 기록하고 재사용할 수 있음

`path`가 설정되면 zsh는 각 디렉터리를 scan해서 그 안의 파일들을 command hash table에 넣음

어떤 command가 command hash table에 기록되어 있는지는 `hash -L`로 확인할 수 있음
- [[topics/zsh/reference/shell-builtin-commands/questions#`hash -L`|Shell Builtin Commands/Hash/Hash -L]] 참고

`command -V`는 command name의 해석 결과를 확인하는 것이고, `hash -L`은 현재 command hash table entry를 확인하는 것이므로 둘은 같은 확인이 아님

```zsh
% hash -L | grep grep
hash grep=/usr/bin/grep
```

> [!NOTE] command hash table과 external command 실행 흐름

정리하면 외부 명령 실행 흐름은 대략 다음처럼 볼 수 있음

1. `grep TODO README.md`를 simple command로 해석함
2. `grep`이라는 command name이 shell function인지 확인함
3. shell builtin인지 확인함
4. external command가 필요하면 `$path`를 기준으로 `grep`이라는 executable file을 찾음
5. zsh는 이 과정에서 command hash table에 기록된 경로를 재사용하거나, 필요하면 `$path`를 검색해 entry를 만들거나 갱신할 수 있음
6. 찾은 실행 파일에 `TODO`, `README.md` argument를 전달해서 실행함

### stale entry

command hash table은 내부 캐시처럼 동작하기 때문에 stale entry가 생길 수 있음

예를 들어 command hash table에 다음 entry가 있다고 하자

```text
foo -> /foo
```

그러면 zsh는 `foo`라는 command name을 `/foo` 경로와 연결해서 알고 있음

하지만 실제 `/foo` 파일이 없으면 이 entry는 stale entry임

zsh가 이런 stale entry를 실행하려고 하면 해당 entry를 정리함

```zsh
% hash foo=/foo
% hash -m foo
foo=/foo
% foo
zsh: foo: command not found...
% hash -m foo
```

즉 잘못된 hash entry 하나를 실행하다가 실패하면 그 entry는 제거될 수 있음
- https://unix.stackexchange.com/questions/805823/how-does-zsh-use-its-command-hash-table-when-searching-path-for-external-comman 참고

반대로 hash entry가 잘못되어 있지만 같은 command name을 `$path`에서 다시 찾을 수 있으면, zsh는 그 entry를 올바른 경로로 갱신할 수 있음

### `path` 재설정과 rebuild

`path` 또는 `PATH`를 다시 설정하면 command hash table은 다시 만들어질 수 있음

예를 들어 의미상 값이 같더라도 아래처럼 대입하면 `PATH` parameter를 다시 설정하는 것임

```zsh
% PATH="$PATH"
```

따라서 다음처럼 이해할 수 있음

- `path`/`PATH` 설정: 디렉터리를 scan해서 command hash table을 만듦
- external command 실행: `$path`를 기준으로 executable file을 찾고, command hash table entry를 재사용하거나 갱신할 수 있음
	- stale entry 실행 실패: 해당 entry가 정리되거나 갱신될 수 있음
- `path`/`PATH` 재설정: command hash table이 다시 만들어질 수 있음

### `hash` builtin과의 관계

`hash` builtin은 이 command hash table을 직접 확인하거나 수정하는 명령임

```zsh
% hash foo=/some/executable
```

위 명령은 command hash table에 다음 매핑을 직접 넣는 것과 같음

```text
foo -> /some/executable
```

하지만 보통 command hash table은 `hash`로 직접 수정하기보다 `path`/`PATH`를 수정해서 간접적으로 바꿈
- [[topics/zsh/reference/shell-builtin-commands/questions#`hash`|hash]] 참고
