---
aliases:
  - git-init
references: https://git-scm.com/docs/git-init
tags:
  - reference
  - git
---
## Name

> [!quote]
> 
> git-init - Create an empty Git repository or reinitialize an existing one

```zsh
% git init
Initialized empty Git repository in <current-directory>
% git init
Reinitialized existing Git repository in <current-directory>
```

이미 `.git` 폴더가 존재하는 곳에서 `git init` 명령어를 입력하면 재초기화

## Description

> [!quote]
> 
> This command creates an empty Git repository - basically a `.git` directory with subdirectories for `objects`, `refs/heads`, `refs/tags`, and template files. An initial branch without any commits will be created (see the `--initial-branch` option below for its name).

`.git` 폴더는 아래와 같은 구성

```zsh
% tree .git -L 1
.git
├── config
├── description
├── HEAD
├── hooks
├── info
├── objects
└── refs

```
 