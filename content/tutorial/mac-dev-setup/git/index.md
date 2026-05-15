---
tags:
  - git
  - mac
---
## 1. Git 초기 설정

###  1-1. 전역에서 `.DS_Store` 파일 무시
```bash
# macOS .DS_Store 글로벌 무시
echo '.DS_Store' >> ~/.gitignore_global
git config --global core.excludesfile ~/.gitignore_global
```

### 1-2. 공통 설정
```
# ~/.gitconfig

# user 섹션은 디렉토리별로 덮어씀
# github desktop 자동 생성
[user]
	name = <placeholder>
	email = <placeholder>
	signingkey = "key::ssh-ed25519 AAAA..."

[core]
	editor = nvim
	excludesfile = /Users/sigma/.gitignore_global
	autocrlf = false
	quotepath = false
	precomposeunicode = true

[init]
	defaultBranch = main

[log]
	date = iso8601

[color]
	ui = auto

[fetch]
	prune = true
	prunetags = true

[push]
	default = simple

[pull]
	rebase = true

[rebase]
	autostash = true
	autosquash = true

[merge]
	conflictstyle = zdiff3

[commit]
	verbose = true
	gpgsign = true

[tag]
	gpgsign = true

[gpg]
	format = ssh

[credential]
	helper = git-credential-cache

[rerere]
	enabled = true

[help]
	autocorrect = prompt

[url "git@github.com:"]
	insteadOf = https://github.com/

# 디렉토리별 계정 분리
# gitdir는 디렉토리 prefix 매칭이라 .../로 끝나야함
[includeIf "gitdir:~/work/"]
	path = ~/.gitconfig-work

[includeIf "gitdir:~/personal/"]
	path = ~/.gitconfig-personal

# github desktop 자동 생성
[filter "lfs"]
	process = git-lfs filter-process
	required = true
	clean = git-lfs clean -- %f
	smudge = git-lfs smudge -- %f

```

### 1-3. 디렉토리별 설정

- 공통(전역) 설정파일(`~/.gitconfig`)에 includeIf 섹션 추가
- `touch ~/.gitconfig-{placeholder}`
	- 디렉토리 이름과 `{placeholder}` 값 동일하게 짓기
### 1-4. 디렉토리별로  자격 증명 분리

**Secretive** 다운로드
```zsh
brew install --cask secretive
```

Sercretive에서 public key 생성하고 프롬프트 따라하기
#### 1-4-1. 서명 설정
![[Pasted image 20260412233120.png]]
```zsh
# ~/.gitconfig-{placeholder}

[user]
    name = <your-name>
    email = <your-email>
    signingkey = <public-key-path>

[commit]
    gpgsign = true

[gpg]
    format = ssh

[gpg "ssh"]
    allowedSignersFile = ~/.gitallowedsigners
```

키 생성 하고, 디렉토리별 `~/.gitconfig-{placeholder}` 파일에 위와 같이 작성
- 키 생성 시 이름은 알기 쉽게 `GitHub {placeholder}`

![[Pasted image 20260412233128.png]]
```zsh
# {placeholder} ex) personal
<your-email> <public-key>
```

`~/.gitallowedsigners` 파일에 public key 생성할 때 마다 한 줄씩 추가

#### 1-4-2. SSH 설정

![[Pasted image 20260412233142.png]]
모든 호스트에 접속할 때 Secretive의 SSH Agent를 사용하도록 설정

프롬프트에서 나온것을 그대로 `~/.ssh/config` 파일에 추가하지 말고 아래와 같이 작성

```zsh
# ~/.ssh/config

Include config.local

# 개인 계정
Host github-personal
  HostName github.com
  User git
  IdentitiesOnly yes

# 작업 계정
Host github-work
  HostName github.com
  User git
  IdentitiesOnly yes

```

우선 위와 같이 mac에서 동일한 공통 설정 작성
- 로컬 맥의 정보가 들어가지 않음

```zsh
# ~/.ssh/config.local
Host *
  IdentityAgent ~/Library/Containers/com.maxgoedjen.Secretive.SecretAgent/Data/socket.ssh
  
Host github-personal
	IdentityFile <personal-public-key-path>

Host github-work
	IdentityFile <work-public-key-path>
```

프롬프트에서 알려주는 문장(`IdentiyAgent`)은 `~/.ssh/config.local`에 작성
- 현재 사용하는 mac에 대한 설정
- `IdentityFile` 경로도 로컬에 의존적이라 `~/.ssh/config.local`에 작성
	- Secertiv Public Key Path 값

```bash
chmod 600 ~/.ssh/config
chmod 600 ~/.ssh/config.local
```

파일 권한 설정

#### 1-4-3. zsh 설정
![[Pasted image 20260412233744.png]]
프롬프트 그대로 수행하지 말고 `~/.zshrc.local`에 추가
- Git 서명 시 참조하는 `SSH_AUTH_SOCK` 환경변수가 Secretive를 참조하도록 변경

#### 1-4-4. GitHub에 공개키 등록
- GitHub → Settings → SSH and GPG keys
- **Authentication Key**로 등록 (SSH 접속용)
- **Signing Key**로 등록 (커밋 서명용)
	- 같은 공개키를 두 번 등록

#### 1-4-5. SSH 연결 테스트
```bash
ssh -T git@github-personal
# Touch ID 팝업 → "Hi <personal-user-name>!" 나오면 성공
ssh -T git@github-work
# Touch ID 팝업 → "Hi <work-user-name>!" 나오면 성공
```
