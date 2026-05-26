---
tags:
  - books
  - pro-git
---
# 1.6 Getting Started - First-Time Git Setup

## First-Time Git Setup

> [!quote]
>  
>  Git comes with a tool called `git config` that lets you get and set configuration variables that control all aspects of how Git looks and operates. These variables can be stored in three different places:

Git은 `git config`라는 툴을 포함함

`git config`를 통해 다음 작업을 수행할 수 있음
- configuration variable 조회 & 설정
	- configuration variable은 Git의 모습과 동작을 제어함
- configuration variable은 다음 3가지 파일에 저장됨
	1. `[path]/etc/gitconfig` file
	2. `~/.gitconfig` or `~/.config/git/config`
	3. `config` file in the Git directory (that is, `.git/config`)

^system-git-config
> [!quote]
> 
> 1. `[path]/etc/gitconfig` file: Contains values applied to every user on the system and all their repositories. If you pass the option `--system` to `git config`, it reads and writes from this file specifically. Because this is a system configuration file, you would need administrative or superuser privilege to make changes to it.

시스템에 모든 유저에게 적용되는 파일

`git config`에 `--system`옵션을 넣어서 configuration file을 이 파일에 쓰고 읽을 수 있음

이 파일을 수정하려면 관리자 권한이 필요함

^global-git-config
> [!quote]
> 
> 2. `~/.gitconfig` or `~/.config/git/config` file: Values specific personally to you, the user. You can make Git read and write to this file specifically by passing the `--global` option, and this affects _all_ of the repositories you work with on your system.

유저 하나에 적용되는 파일

`git config`에 `--global`옵션을 넣어서 configuration file을 이 파일에 쓰고 읽을 수 있음

^local-git-config
> [!quote]
> 
> 3. `config` file in the Git directory (that is, `.git/config`) of whatever repository you’re currently using: Specific to that single repository. You can force Git to read from and write to this file with the `--local` option, but that is in fact the default. Unsurprisingly, you need to be located somewhere in a Git repository for this option to work properly.

현재 사용 중인 저장소의 Git 디렉터리 안에 있는 config 파일

하나의 리포지토리에 적용되는 파일

`git config`에 `--local`옵션을 넣어서 configuration file을 이 파일에 쓰고 읽을 수 있음
- 이 동작은 옵션을 붙이지 않을 때 기본 동작

^git-config-precedence
> [!quote]
> 
> Each level overrides values in the previous level, so values in `.git/config` trump those in `[path]/etc/gitconfig`.

아래 우선 순위로 설정을 덮어씀
- local > global > system
- "previous level"은 먼저 읽은 값
	- 읽는 순서는 system -> global -> local
	- 나중에 읽은 값을 덮어씀

^list-git-config-with-origin
> [!quote]
> 
> You can view all of your settings and where they are coming from using:
> 
> `git config --list --show-origin`

모든 설정을 보는 방법
- 3가지 파일 중 어디서 설정이 왔는지 확인 가능

### Your Identity

> [!quote]
> 
> The first thing you should do when you install Git is to set your user name and email address. This is important because every Git commit uses this information, and it’s immutably baked into the commits you start creating:

Git은 다음 정보를 변경 불가능(immutable)한 형태로 commit에 포함함
- name
- email

```zsh
$ git config --global user.name "John Doe"
$ git config --global user.email johndoe@example.com
```

위와 같이 `--global` 옵션을 사용하면 한 번만 위 작업을 수행하면 됨
- 유저에 적용되기 때문
- [[#^global-git-config]] 참고

### Your default branch name

> [!quote]
> 
> By default Git will create a branch called master when you create a new repository with git init. From Git version 2.28 onwards, you can set a different name for the initial branch

`git init`으로 현재 디렉터리를 새로운 리포지토리로 초기화 가능
- 기본 브랜치 이름은 `master`
- 초기화하면서 기본 브랜치 이름을 다르게 설정할 수 있음

> [!quote]
> 
> To set _main_ as the default branch name do:

아래와 같이 `init.defaultBranch` configuration value의 값을 설정하면 됨

```zsh
$ git config --global init.defaultBranch main
```


### Checking Your Settings

> [!quote]
> 
> If you want to check your configuration settings, you can use the `git config --list` command to list all the settings Git can find at that point:

`git config --list` 도구를 사용하면 그 시점에 Git이 찾을 수 있는 설정을 불러옴

> [!quote]
> 
> You may see keys more than once, because Git reads the same key from different files (`[path]/etc/gitconfig` and `~/.gitconfig`, for example). In this case, Git uses the last value for each unique key it sees.

같은 키(configuration value)가 두 번 이상 보일 수 있음
- 위에서 본 3가지 파일에 설정이 저장될 수 있기 때문
- 여러 개가 보이는 경우 실제로 적용되는 옵션은 우선 순위에 따라 적용
	- [[#^git-config-precedence]] 참고
	- "last value"는 설정 우선 순위에서 가장 마지막에 읽은 값

> [!quote]
> 
> You can also check what Git thinks a specific key’s value is by typing `git config <key>`:

특정 configuration value의 값만 읽을 수 있음 ^read-specific-config-value
- 이 때 우선 순위에 따라 최종적으로 적용되는 값 하나만 불러옴
	- "what Git thinks"라는 표현이 적용되는 값을 읽어오는 개념

^show-config-origin-for-key
> [!quote]
> 
> Since Git might read the same configuration variable value from more than one file, it’s possible that you have an unexpected value for one of these values and you don’t know why. In cases like that, you can query Git as to the _origin_ for that value, and it will tell you which configuration file had the final say in setting that value

```zsh
$ git config --show-origin rerere.autoUpdate
file:/home/johndoe/.gitconfig	false
```

[[#^list-git-config-with-origin]]에서 본 `--show-origin` 옵션을 사용하면 Git에게 configuration value 값의 출처를 물어볼 수 있음
