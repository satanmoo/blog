---
aliases:
  - Invocation
tags:
  - reference
  - questions
  - zsh
references: https://zsh.sourceforge.io/Doc/Release/Invocation.html#Invocation
---
# Invocation

## 4.1 Invocation

> [!Quote] The following flags are interpreted by the shell when invoked to determine where the shell will read commands from:

invoke
- zsh 프로그램을 새 프로세스로 시작하는 개념

아래와 같이 확인 가능

```zsh
% echo $$
12345     # 현재 zsh 프로세스 PID

% zsh     # 새 zsh invoke

% echo $$
12399     # 안쪽 zsh 프로세스 PID
```

---
### `-c` 옵션

> [!Quote] 
> 
> -c 
> 
> Take the first argument as a command to execute, rather than reading commands from a script or standard input. If any further arguments are given, the first one is assigned to $0, rather than being used as a positional parameter.

`-c` 옵션을 사용하는 예시는 다음과 같음

```zsh
% zsh -c 'print "0=$0 1=$1 2=$2"' name a b
0=name 1=a 2=b
```

`-c` 다음 첫 번째 인자인 `print...` 이 실행할 command
그 뒤 첫 번째 인자인 name을 `$0`으로 사용
나머지 인자(띄어쓰기로 구분)를 `$1`, `$2`로 사용
- [[topics/zsh/reference/parameters/questions#15.3 Positional Parameters|Positional Parameters]] 참고

---
### `-s` 옵션

> [!Quote]
> 
> -s
> 
> Force shell to read commands from the standard input. If the -s flag is not present and an argument is given, the first argument is taken to be the pathname of a script to execute.

`-s` 옵션을 사용하는 예시는 다음과 같음

```zsh
% zsh -s arg1 arg2 
% echo $1
arg1
% echo $2
arg2
```

`zsh -s arg1 arg2`

부모 zsh가 파싱해서 새 zsh 프로세스를 실행하는 순간, 새 zsh에는 아래 인자 목록이 전달됨
- `argv[0] = zsh`
- `argv[1] = -s`
- `argv[2] = arg1`
- `argv[3] = arg2`

새 zsh는 시작하면서 -s를 처리함
  - `-s` 
	  - 명령은 stdin에서 읽음
	  - `echo $1` 이 새로운 zsh 프로세스의 stdin에 입력한 명령
  - 남은 인자 `arg1`, `arg2`
	  - positional parameters로 사용
		  - [[topics/zsh/reference/parameters/questions#15.3 Positional Parameters|Positional Parameters]] 참고

`-s`  옵션을 사용하되 인자가 있는 예시는 다음과 같음

```zsh
% vim script.zsh
```

위와 같이 텍스트 에디터를 연 뒤

```text
echo "script path: $0"
echo "first arg: $1"
echo "second arg: $2"
```

위와 같이 작성하고 저장

```zsh
% zsh script.zsh a b
script path: script.zsh
first arg: a
second arg: b
```

첫번째 인자인 `script.zsh`를 실행할 스크립트 파일 경로로 간주

반면 `-s`를 붙여서 실행하면 다음과 같음

```zsh
% zsh -s script.zsh a b
```

새로운 zsh 프로세스가 시작되고 positional parameters은 다음과 같음
- `$0`: `script.zsh`
- `$1`: `a`
- `$2`: `b`
- [[topics/zsh/reference/parameters/questions#15.3 Positional Parameters|Positional Parameters]] 참고

`script.zsh`를 실행하지 않음

```zsh
% echo $1
script.zsh
% echo $2
a
% echo $3
b
```

---

> [!Quote]
> 
> If the option PATH_SCRIPT is set, and the file name does not contain a directory path (i.e. there is no ‘/’ in the name), first the current directory and then the command path given by the variable PATH are searched for the script. If the option is not set or the file name contains a ‘/’ it is used directly.

`-s`, `-c` 옵션없이 zsh 를 실행할 때 스크립트 파일(위에서 `script.zsh`)를 어디서 찾는지에 대한 설명
