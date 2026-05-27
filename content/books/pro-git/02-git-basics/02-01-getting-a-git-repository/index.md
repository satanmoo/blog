---
tags:
  - books
  - pro-git
---
# 2.1 Git Basics - Getting a Git Repository

## Getting a Git Repository

> [!quote]
> You typically obtain a Git repository in one of two ways:

Git repository를 얻는 방법은 크게 2가지
- `git init`
- `git clone`

> [!quote]
> You can take a local directory that is currently not under version control, and turn it into a Git repository, or

1. 버전 관리 중이 아닌 로컬 디렉터리를 Git repository로 만들 수 있다.

> [!quote]
> You can clone an existing Git repository from elsewhere.

2. 외부에 존재하는 Git repository를 clone할 수 있다.

> [!quote]
> In either case, you end up with a Git repository on your local machine, ready for work.

두 경우 모두 로컬 머신에 작업 가능한 Git repository가 생긴다.

### Initializing a Repository in an Existing Directory

> [!quote]
> If you have a project directory that is currently not under version control and you want to start controlling it with Git, you first need to go to that project’s directory. If you’ve never done this, it looks a little different depending on which system you’re running:

현재 버전 관리 중이 아닌 project directory를 Git으로 관리하려면 먼저 해당 project directory로 이동해야 함

macOS에서는 아래와 같이 이동

```zsh
$ cd /Users/user/my_project
```

그리고 아래 명령어를 입력한다.

```zsh
$ git init
```

> [!quote]
> This creates a new subdirectory named `.git` that contains all of your necessary repository files — a Git repository skeleton. At this point, nothing in your project is tracked yet. See Git Internals for more information about exactly what files are contained in the `.git` directory you just created.

`git init`을 실행하면 `.git`이라는 새 subdirectory가 만들어짐

`.git` directory에는 Git repository에 필요한 파일들이 들어 있음
- Git repository skeleton이라고 볼 수 있음

이 시점에는 project 안의 파일들이 아직 tracked 상태가 아님
- [[books/pro-git/01-getting-started/01-03-what-is-git/index#The Three States|the three states]] 참고

`git init`은 Git repository 구조만 만들 뿐, 기존 파일을 자동으로 tracking하지 않음

> [!quote]
> If you want to start version-controlling existing files (as opposed to an empty directory), you should probably begin tracking those files and do an initial commit. You can accomplish that with a few `git add` commands that specify the files you want to track, followed by a `git commit`:

빈 directory가 아니라 이미 파일이 있는 project를 version control하려면 다음 작업이 필요함
- 관리할 파일을 tracking 대상으로 추가
- initial commit 생성

tracking할 파일을 `git add`로 지정한 뒤 `git commit`을 실행 ^tracking-files-with-git-add

```zsh
$ git add *.c
$ git add LICENSE
$ git commit -m 'Initial project version'
```

### Cloning an Existing Repository

> [!quote]
> If you want to get a copy of an existing Git repository — for example, a project you’d like to contribute to — the command you need is `git clone`. If you’re familiar with other VCSs such as Subversion, you’ll notice that the command is "clone" and not "checkout". This is an important distinction — instead of getting just a working copy, Git receives a full copy of nearly all data that the server has. Every version of every file for the history of the project is pulled down by default when you run `git clone`. In fact, if your server disk gets corrupted, you can often use nearly any of the clones on any client to set the server back to the state it was in when it was cloned (you may lose some server-side hooks and such, but all the versioned data would be there — see Getting Git on a Server for more details).

기존 Git repository의 copy를 가져오려면 `git clone`을 사용함

다른 VCS의 `checkout`과 다르게 Git은 단순히 working copy만 가져오지 않음
- checkout vs clone

`git clone`은 server가 가진 거의 모든 데이터를 local로 가져옴
- project history에 있는 각 file의 모든 version

server disk가 손상된 경우에도 client에 있는 clone을 이용해 server를 clone 시점의 상태로 복구할 수 있음
- 단, server-side hook 같은 일부 server 전용 정보는 잃을 수 있음

> [!quote]
> You clone a repository with `git clone <url>`. For example, if you want to clone the Git linkable library called libgit2, you can do so like this:

repository는 `git clone <url>` 형태로 clone함

예를 들어 `libgit2` repository를 clone하려면 아래처럼 실행

```zsh
$ git clone https://github.com/libgit2/libgit2
```

> [!quote]
> That creates a directory named `libgit2`, initializes a `.git` directory inside it, pulls down all the data for that repository, and checks out a working copy of the latest version. If you go into the new `libgit2` directory that was just created, you’ll see the project files in there, ready to be worked on or used.

`git clone`을 실행하면 다음 일이 일어남
- `libgit2` directory 생성
- 그 안에 `.git` directory 초기화
- 해당 repository의 모든 data 다운로드
- 최신 version의 working copy checkout

`git clone` 이후 새로 생성된 디렉토리로 이동하면 프로젝트 파일을 볼 수 있고, 바로 작업할 수 있음

> [!quote]
> If you want to clone the repository into a directory named something other than `libgit2`, you can specify the new directory name as an additional argument:

repository를 `libgit2`가 아닌 다른 이름의 directory로 clone하고 싶다면 directory name을 추가 argument로 지정하면 됨

```zsh
$ git clone https://github.com/libgit2/libgit2 mylibgit
```

> [!quote]
> That command does the same thing as the previous one, but the target directory is called `mylibgit`.

위 명령어는 이전 `git clone`과 같은 일을 함

차이는 target directory name이 `mylibgit`이라는 점

> [!quote]
> Git has a number of different transfer protocols you can use. The previous example uses the `https://` protocol, but you may also see `git://` or `user@server:path/to/repo.git`, which uses the SSH transfer protocol.

Git은 여러 transfer protocol을 사용할 수 있음

위 예시는 `https://` protocol을 사용함

다른 형태도 볼 수 있음
- `git://`
- `user@server:path/to/repo.git`
	- SSH transfer protocol 사용
