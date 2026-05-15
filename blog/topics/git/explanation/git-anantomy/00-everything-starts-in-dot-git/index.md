---
aliases:
  - 00. 모든 것은 .git 폴더에서 시작
tags:
  - git-anatomy
  - explanation
---
# 00. 모든 것은 .git 폴더에서 시작

```zsh
git init
```

`git init` 을 실행하면 다음과 같은 메시지가 출력됨
- 여기서 `<path>`는 `git init`을 실행하는 현재 디렉토리 절대 경로

```zsh
Initialized empty Git repository in <path>/.git/
```

실행 결과 현재 디렉토리에 .git 폴더가 생성됨
- 이 폴더는 Git이 모든 데이터를 저장하는 디렉토리
- 이 폴더를 삭제하면 모든 히스토리가 사라짐

.git 폴더의 구성을 확인해보면 다음과 같음

```zsh
tree .git -L 1

.git
├── config
├── description
├── HEAD
├── hooks
├── info
├── objects
└── refs

5 directories, 3 files
```

각 구성요소가 어떤 역할을 하는지는 [[topics/git/reference/gitrepository-layout/index|gitrepository-layout]] 참고
`git init`의 자세한 동작은 [[topics/git/reference/init/index|git-init]] 참고
