---
aliases:
  - unity-mcp-with-python
tags:
  - tutorial
  - mcp
  - unity
references:
  - https://github.com/CoplayDev/unity-mcp
---
# MCP for Unity가 Python을 감지하도록 설정하기

## 1. MCP for Unity Unity Package 설치

유니티 에디터에서 아래로 이동

- `Window > Package Manager > + > Add package from git URL...`

이동 뒤 아래 입력
```
https://github.com/CoplayDev/unity-mcp.git?path=/MCPForUnity#main
```

## 2. Python 3.10+ 그리고 uv 설치

### uv 설치

```zsh
% brew install uv
```

`Window > MCP For Unity > Local Setup Window` 에서 UV 감지하는 것 확인하기

![[Pasted image 20260518183439.png]]

### Python 3.10+ 설치

문서 작성 시점 기준 최신 Python 설치

uv로 Python을 설치하고 버전 관리할 수 있음
- [https://docs.astral.sh/uv/getting-started/features/](https://docs.astral.sh/uv/guides/install-python/) 참고

아래 커맨드로 Python 설치

```zsh
% uv python install
Installed Python 3.14.5 in 5.14s
 + cpython-3.14.5-macos-aarch64-none (python3.14)
warning: `<home>/.local/bin` is not on your PATH. To use installed Python executables, run `export PATH="<home>/.local/bin:$PATH"` or `uv python update-shell`.
```

아래 명령으로 uv가 설치한 python의 경로를 확인할 수 있음

```zsh
% uv python find
<home>/.local/share/uv/python/cpython-3.14-macos-aarch64-none/bin/python3.14
```

하지만 `Window > MCP For Unity > Local Setup Window` 에서 MCP for Unity가 Python을 감지하지 못하는 것을 확인할 수 있음
![[Pasted image 20260518183439.png]]
셸에서 나온 대로 `uv python update-shell`을 실행하면 다음이 셸에 출력됨

```zsh
Created configuration file: <home>/.zshenv
Restart your shell to apply changes
```

내부적으로 `.zshenv` 파일을 생성하는 동작

하지만 이 동작을 수행해도 MCP for Unity가 Python을 감지하지 못함

```zsh
% which python3
/usr/bin/python3
% which python
python not found
% which python3.14
<home>/.local/bin/python3.14
```

셸에서 위를 실행해본 결과 python이 여러 개 설치되어 있고, uv가 `python`과 `python3` executable을 설치하지 않은 상태

아래 명령어를 셸에서 실행하면 여러 개 설치된 것을 확인할 수 있음

```zsh
% uv python list
cpython-3.15.0b1-macos-aarch64-none                 <download available>
cpython-3.15.0b1+freethreaded-macos-aarch64-none    <download available>
cpython-3.14.5-macos-aarch64-none                   <home>/.local/bin/python3.14 -> <home>/.local/share/uv/python/cpython-3.14-macos-aarch64-none/bin/python3.14
cpython-3.14.5-macos-aarch64-none                   <home>/.local/share/uv/python/cpython-3.14-macos-aarch64-none/bin/python3.14
cpython-3.14.5+freethreaded-macos-aarch64-none      <download available>
cpython-3.13.13-macos-aarch64-none                  <download available>
cpython-3.13.13+freethreaded-macos-aarch64-none     <download available>
cpython-3.12.13-macos-aarch64-none                  <download available>
cpython-3.11.15-macos-aarch64-none                  <download available>
cpython-3.10.20-macos-aarch64-none                  <download available>
cpython-3.9.25-macos-aarch64-none                   <download available>
cpython-3.9.6-macos-aarch64-none                    /usr/bin/python3
cpython-3.8.20-macos-aarch64-none                   <download available>
pypy-3.11.15-macos-aarch64-none                     <download available>
pypy-3.10.16-macos-aarch64-none                     <download available>
pypy-3.9.19-macos-aarch64-none                      <download available>
pypy-3.8.16-macos-aarch64-none                      <download available>
graalpy-3.12.0-macos-aarch64-none                   <download available>
graalpy-3.11.0-macos-aarch64-none                   <download available>
graalpy-3.10.0-macos-aarch64-none                   <download available>
graalpy-3.8.5-macos-aarch64-none                    <download available>
```

아래 명령어를 통해 PATH의 원소를 확인해보자
- 원소의 순서가 어떤 python을 실행하는지 영향을 주기 때문
	- [[topics/zsh/explanation/how-zsh-execute-command/index|zsh가 명령어를 실행하는 과정]] 참고

```zsh
% typeset -p1 PATH
export -T PATH path=(
  <home>/.asdf/shims
  <home>/.local/share/zinit/polaris/bin
  /opt/homebrew/bin
  /opt/homebrew/sbin
  /usr/local/bin
  /System/Cryptexes/App/usr/bin
  /usr/bin
  /bin
  /usr/sbin
  /sbin
  /var/run/com.apple.security.cryptexd/codex.system/bootstrap/usr/local/bin
  /var/run/com.apple.security.cryptexd/codex.system/bootstrap/usr/bin
  /var/run/com.apple.security.cryptexd/codex.system/bootstrap/usr/appleinternal/bin
  /opt/pmk/env/global/bin
  <home>/.local/bin
  '<home>/Library/Application Support/JetBrains/Toolbox/scripts'
)
```

`/usr/bin`이 `<home>/.local/bin`보다 먼저 오기 때문에 `which python3`의 결과 uv로 설치한 python이 아니라 기존에 설치된 python이 나옴

> [!quote]
>
> uv only installs a _versioned_ executable by default. To install `python` and `python3` executables, include the experimental `--default` option:
>
> `uv python install --default`
>
> [Installing Python](https://docs.astral.sh/uv/guides/install-python/#getting-started)

위 인용을 보면 uv로 특정 버전의 python을 설치하면 `python3.14` executable만 설치됨을 알 수 있음

```zsh
% uv python install --default
warning: The `--default` option is experimental and may change without warning. Pass `--preview-features python-install-default` to disable this warning
Installed Python 3.14.5 in 123ms
 + cpython-3.14.5-macos-aarch64-none (python, python3)
```

셸에서 지시하는 대로 `--preview` 플래그를 붙여서 실행하면

```zsh
% uv python install --default --preview
Python is already installed. Use `uv python install <request>` to install another version.
```

이미 설치되었다고 나오지만

```zsh
% uv python list
cpython-3.15.0b1-macos-aarch64-none                 <download available>
cpython-3.15.0b1+freethreaded-macos-aarch64-none    <download available>
cpython-3.14.5-macos-aarch64-none                   <home>/.local/bin/python3.14 -> <home>/.local/share/uv/python/cpython-3.14-macos-aarch64-none/bin/python3.14
cpython-3.14.5-macos-aarch64-none                   <home>/.local/bin/python3 -> <home>/.local/share/uv/python/cpython-3.14-macos-aarch64-none/bin/python3.14
cpython-3.14.5-macos-aarch64-none                   <home>/.local/bin/python -> <home>/.local/share/uv/python/cpython-3.14-macos-aarch64-none/bin/python3.14
cpython-3.14.5-macos-aarch64-none                   <home>/.local/share/uv/python/cpython-3.14-macos-aarch64-none/bin/python3.14
cpython-3.14.5+freethreaded-macos-aarch64-none      <download available>
cpython-3.13.13-macos-aarch64-none                  <download available>
cpython-3.13.13+freethreaded-macos-aarch64-none     <download available>
cpython-3.12.13-macos-aarch64-none                  <download available>
cpython-3.11.15-macos-aarch64-none                  <download available>
cpython-3.10.20-macos-aarch64-none                  <download available>
cpython-3.9.25-macos-aarch64-none                   <download available>
cpython-3.9.6-macos-aarch64-none                    /usr/bin/python3
cpython-3.8.20-macos-aarch64-none                   <download available>
pypy-3.11.15-macos-aarch64-none                     <download available>
pypy-3.10.16-macos-aarch64-none                     <download available>
pypy-3.9.19-macos-aarch64-none                      <download available>
pypy-3.8.16-macos-aarch64-none                      <download available>
graalpy-3.12.0-macos-aarch64-none                   <download available>
graalpy-3.11.0-macos-aarch64-none                   <download available>
graalpy-3.10.0-macos-aarch64-none                   <download available>
graalpy-3.8.5-macos-aarch64-none                    <download available>
```

python 그리고 python3 executable이 uv로 설치한 python을 참조하는 것을 확인할 수 있음
- 여전히 python3 는 PATH 원소의 순서 때문에 `/usr/bin`의 python이 나오긴 함

```zsh
% which python
<home>/.local/bin/python
% which python3
/usr/bin/python3
```

이제 `Window > MCP For Unity > Local Setup Window` 에서 MCP for Unity가 Python을 감지함
![[Pasted image 20260518191315.png]]

## 3. MCP For Unity 실행

`Window > MCP For Unity > Toggle MCP Window` 로 이동

![[Pasted image 20260518201330.png]]

Start Server 로 MCP 서버 실행

유니티 프로젝트의 루트로 이동해 `.codex/config.toml` 에 아래 추가
- `Window > MCP For Unity > Toggle MCP Window > Client Configuration` 창에서 GUI로 설정하는 경우 `~/.codex/config.toml`로 설정 파일을 생성하기 때문에 codex를 여러 프로젝트에서 사용하는 경우 불편할 수 있음. 따라서 프로젝트 루트에 설정 파일을 작성함
	- https://developers.openai.com/codex/config-basic#configuration-precedence 참고

```toml
[features]
rmcp_client = true

[mcp_servers.unityMCP]
url = "http://127.0.0.1:8080/mcp"
```
