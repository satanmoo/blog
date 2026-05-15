---
tags:
  - ssh
  - git
  - series/ssh-github-multi-account
---
# SSH로 GitHub 멀티 계정 관리하기 (1)

macOS에서 GitHub 계정을 여러 개(개인/회사) SSH로 안전하게 분리 운영하는 법을 알아보자.

## 공용 `.gitconfig` 파일 작성

```zsh
# $HOME/.gitconfig

[commit]
	gpgsign = true

[tag]
	gpgsign = true

[gpg]
	format = ssh

# 디렉토리별 설정 라우팅
[includeIf "gitdir:~/work/"]
	path = ~/.gitconfig-work

[includeIf "gitdir:~/personal/"]
	path = ~/.gitconfig-personal
```

위 변수들을 `$HOME/.gitconfig`에 추가하자

각 변수의 의도는 다음과 같다.
- **`commit.gpgsign = true`**: 모든 커밋을 기본적으로 서명하기 위함
- **`tag.gpgsign = true`**: 모든 태그를 기본적으로 서명하기 위함
- **`gpg.format = ssh`**: 커밋/태그 서명 방식을 SSH 키 기반으로 하기 위함
- **`includeIf.gitdir`**: 로컬 저장소 경로를 기준으로 계정 설정 파일을 자동으로 분기하기 위함

## 계정별 `.gitconfig`파일 작성

```zsh
# ~/.gitconfig-work
[user]
	name = <work-name>
	email = <work-email>
	signingkey = key::ssh-ed25519 <work-public-key>
```

```zsh
# ~/.gitconfig-personal
[user]
	name = <personal-name>
	email = <personal-email>
	signingkey = key::ssh-ed25519 <personal-public-key>
```

**`user.name`** : 

> [!TODO]
> 
> gpgsign
> commit
> tag
> gpg.format 관련
> user.name (author)
> user.email (committer)
> user.signingkey