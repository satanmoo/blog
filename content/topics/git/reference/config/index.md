---
tags:
  - reference
  - git
references: https://git-scm.com/docs/git-config
aliases:
  - git-config
---
# git-config

## FILES

`git config`는 여러 파일로 부터 설정을 읽고, 파일에서 우선 순위가 존재함
- 4단계 계층으로 아래로 갈 수록 좁음
	- 시스템
	- 글로벌
	- 리포지토리
	- 워크트리
- 좁은 파일이 넓은 파일을 덮어씀
