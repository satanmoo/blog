---
aliases:
  - 데이터의 3단계 파이프라인
tags:
  - explanation
  - git-anatomy
---
# 데이터의 3단계 파이프라인

새 파일이 Git의 Repository에 영구 저장되기까지는 세 단계를 거침
1. Working Directory
2. Staging Area
3. Repository

각 단계를 직접 확인해보자

## Working Directory

우선 [[topics/git/explanation/git-anantomy/00-everything-starts-in-dot-git/index|00. 모든 것은 .git 폴더에서 시작]]에서 `git init`을 실행한 디렉토리에서 파일을 생성하자

```zsh
echo "hello" > hello.txt
```

생성 후 확인해보면 다음과 같음

```zsh
ls -1a
.
..
.git
hello.txt
```

이렇게 생성된 파일은 **추적되지 않는 새 파일**
- 디스크에는 존재하지만 .git 폴더에는 어떤 흔적도 없음

```zsh
ls -1a .git/objects
.
..
info
pack
```

`.git/objects` 폴더에 변화가 없음

```zsh
git status

On branch main

No commits yet

Untracked files:
  (use "git add <file>..." to include in what will be committed)
	hello.txt

nothing added to commit but untracked files present (use "git add" to track)
```

Git에서 **추적되지 않는 새 파일**은 위에서 말한 3단계 중 **Working Directory에만** 포함됨

> [!NOTE] Working Tree
> 
> 여기서 말하는 Working Directory의 공식 용어는 [[topics/git/reference/gitglossary/index#working tree|Working Tree]]
> 
> Working Tree 는 **추적되지 않는 새 파일**과 **HEAD 커밋의 tree 내용(베이스 스냅샷)**을 모두 포함함

## Staging area

`git add` 명령어를 실행하면, 추적되지 않는 파일을 **Staging area**에 추가함
- [[topics/git/reference/add/index|git-add]] 참고
 
```zsh
git add hello.txt
```

`git add`를 하면 두 가지 일이 일어남
- 파일 내용이 **blob 객체**로 `.git/objects/`에 저장
- "이 blob이 `hello.txt`라는 이름으로 다음 커밋에 포함될 것"이라는 정보가 `.git/index` 파일에 기록

### `.git/objects/` 폴더 확인

```zsh
tree .git/objects

.git/objects
├── ce
│   └── 013625030ba8dba906f756967f9e9ca394464a
├── info
└── pack

4 directories, 1 file
```

`ce`라는 폴더에 blob 객체가 생성된 것을 확인

### `.git/index` 파일 확인

`git add` 전

```zsh
xxd .git/index

00000000: 4449 5243 0000 0002 0000 0000 39d8 9013  DIRC........9...
00000010: 9ee5 356c 7ef5 7221 6ceb cd27 aa41 f9df  ..5l~.r!l..'.A..
```

후

```zsh
xxd .git/index

00000000: 4449 5243 0000 0002 0000 0001 69ed e0a0  DIRC........i...
00000010: 0ac4 83df 69ed e0a0 0ac4 83df 0100 000d  ....i...........
00000020: 0017 f1fe 0000 81a4 0000 01f5 0000 0014  ................
00000030: 0000 0006 ce01 3625 030b a8db a906 f756  ......6%.......V
00000040: 967f 9e9c a394 464a 0009 6865 6c6c 6f2e  ......FJ..hello.
00000050: 7478 7400 2a29 c0c6 8ba2 79c6 1612 2e5a  txt.*)....y....Z
00000060: 632d f3e9 d570 120d                      c-...p..
```

`git status`를 실행하면 아래와 같이 친절하게 알려줌

```zsh
git status

On branch main

No commits yet

Changes to be committed:
  (use "git rm --cached <file>..." to unstage)
	new file:   hello.txt

```
