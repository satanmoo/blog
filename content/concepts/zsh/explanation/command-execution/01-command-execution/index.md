---
aliases:
  - 01. zsh command execution
tags:
  - explanation
  - zsh
references:
  - https://zsh.sourceforge.io/Doc/Release/Command-Execution.html#Command-Execution
  - https://zsh.sourceforge.io/Doc/Release/Parameters.html#Parameters-Used-By-The-Shell
  - https://zsh.sourceforge.io/Doc/Release/Shell-Builtin-Commands.html#Shell-Builtin-Commands
  - https://unix.stackexchange.com/questions/805823/how-does-zsh-use-its-command-hash-table-when-searching-path-for-external-comman
---
# 01. zsh command execution

[[concepts/zsh/explanation/command-execution/00-simple-command|00. zsh simple command]]에서 본 것처럼

```zsh
java -version
```

이 입력은 simple command로 해석될 수 있음

simple command에서 첫 번째 word인 `java`는 실행할 command name이고, `-version`은 그 command에 전달되는 argument임

이제 zsh는 `java`라는 command name이 무엇을 가리키는지 찾아야 함

## command name에 slash가 없는 경우

[zsh 문서의 Command Execution](https://zsh.sourceforge.io/Doc/Release/Command-Execution.html#Command-Execution) 설명은 command name에 `/`가 없는 경우를 기준으로 다음 순서로 설명함

> [!QUOTE]
>
> If a command name contains no slashes, the shell attempts to locate it. If there exists a shell function by that name, the function is invoked as described in Functions. If there exists a shell builtin by that name, the builtin is invoked.

즉 `java`처럼 `/`가 없는 command name이면 zsh는 먼저 shell function과 shell builtin을 확인함

예를 들어 `cd`는 external command가 아니라 shell builtin임

```zsh
% type cd
cd is a shell builtin
```

반면 `java`가 shell function도 아니고 shell builtin도 아니면 zsh는 external command를 찾아야 함

## external command와 `path`

[zsh 문서의 Command Execution](https://zsh.sourceforge.io/Doc/Release/Command-Execution.html#Command-Execution) 설명은 external command 검색을 다음처럼 설명함

> [!QUOTE]
>
> Otherwise, the shell searches each element of `$path` for a directory containing an executable file by that name.

여기서 `$path`는 zsh의 special parameter임
- [[concepts/zsh/reference/parameters/questions#special parameter|special paramter]]

`path`는 디렉터리들의 array이고, `PATH`는 그 원소들을 `:`로 이어 붙인 scalar임
- [[concepts/zsh/reference/parameters/questions#`PATH` & `path`|Parameters/PATH & path]]

```zsh
% typeset -p path
typeset -aT PATH path=( /opt/homebrew/bin /usr/bin /bin )
```

이 말은 zsh가 external command를 찾을 때 `/opt/homebrew/bin`, `/usr/bin`, `/bin` 같은 디렉터리 목록을 기준으로 찾는다는 뜻임

## command hash table

[zsh 문서의 Parameters](https://zsh.sourceforge.io/Doc/Release/Parameters.html#Parameters) 에서 `path` 에 대해서 다음처럼 설명함

> [!QUOTE]
>
> An array (colon-separated list) of directories to search for commands. When this parameter is set, each directory is scanned and all files found are put in a hash table.

여기서 말하는 hash table이 command hash table임

command hash table은 zsh 내부의 매핑으로 이해하면 됨

```text
java -> /Users/me/.asdf/shims/java
git  -> /opt/homebrew/bin/git
ls   -> /bin/ls
```

즉 command name을 실행 파일 경로에 연결하는 table임

`path`가 설정되면 zsh는 각 디렉터리를 scan해서 그 안의 파일들을 command hash table에 넣음
- [[concepts/zsh/reference/parameters/questions#`path`|Parameters/path]]

## 실행할 때 hash table을 사용함

외부 명령 실행 흐름은 대략 다음처럼 볼 수 있음

1. `java -version`을 simple command로 해석함
2. `java`라는 command name이 shell function인지 확인함
3. shell builtin인지 확인함
4. external command가 필요하면 command hash table을 통해 `java`의 경로를 찾음
5. 찾은 실행 파일에 `-version` argument를 전달해서 실행함

실제로 command hash table의 내용은 `hash -L`로 확인할 수 있음

```zsh
hash -L
```

예를 들어 다음과 같은 출력이 나올 수 있음

```zsh
hash java=/Users/me/.asdf/shims/java
hash git=/opt/homebrew/bin/git
```

`hash -L`은 현재 hash table entry를 다시 실행 가능한 `hash` command 형태로 출력함
- [[concepts/zsh/reference/shell-builtin-commands/questions#`hash -L`|Shell Builtin Commands/hash -L]]

## stale entry

command hash table은 내부 캐시처럼 동작하기 때문에 stale entry가 생길 수 있음

예를 들어 command hash table에 다음 entry가 있다고 하자

```zsh
hash foo=/foo
```

그러면 zsh는 `foo`라는 command name을 `/foo` 경로와 연결해서 알고 있음

하지만 실제 `/foo` 파일이 없으면 이 entry는 stale entry임

Unix Stack Exchange 답변에 따르면, zsh가 이런 stale entry를 실행하려고 하면 해당 entry를 정리함

```zsh
hash foo=/foo
hash -m foo
# foo=/foo

foo
# zsh: foo: command not found...

hash -m foo
# 출력 없음
```

즉 잘못된 hash entry 하나를 실행하다가 실패하면 그 entry는 제거될 수 있음

반대로 hash entry가 잘못되어 있지만 같은 command name을 `$path`에서 다시 찾을 수 있으면, zsh는 그 entry를 올바른 경로로 갱신할 수 있음

## `path` 재설정과 rebuild

`path` 또는 `PATH`를 다시 설정하면 command hash table은 다시 만들어질 수 있음

예를 들어 의미상 값이 같더라도 아래처럼 대입하면 `PATH` parameter를 다시 설정하는 것임

```zsh
PATH="$PATH"
```

Unix Stack Exchange 답변의 실험에서는 이렇게 `PATH`를 다시 설정하면 command hash table이 전체적으로 rebuild되는 것으로 설명함

따라서 다음처럼 이해할 수 있음

- `path`/`PATH` 설정: 디렉터리를 scan해서 command hash table을 만듦
- external command 실행: command hash table을 사용해서 command name을 경로로 해석함
- stale entry 실행 실패: 해당 entry가 정리되거나 갱신될 수 있음
- `path`/`PATH` 재설정: command hash table이 다시 만들어질 수 있음

## `hash` builtin과의 관계

`hash` builtin은 이 command hash table을 직접 확인하거나 수정하는 명령임

```zsh
hash foo=/some/executable
```

위 명령은 command hash table에 다음 매핑을 직접 넣는 것과 같음

```text
foo -> /some/executable
```

하지만 보통 command hash table은 `hash`로 직접 수정하기보다 `path`/`PATH`를 수정해서 간접적으로 바꿈

`hash` builtin은 command hash table 말고 named directory hash table도 다룰 수 있음
- [[concepts/zsh/reference/shell-builtin-commands/questions#`hash`|Shell Builtin Commands/hash]]
