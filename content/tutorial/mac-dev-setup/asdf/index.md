---
tags:
  - mac-dev-setup
  - setup
aliases:
  - asdf 설정
---
# asdf 설정

## 1. 다운로드 & 설정

```zsh
brew install asdf
```

`~/.zshrc.local`에 아래 항목 추가:

```zsh
export PATH="${ASDF_DATA_DIR:-$HOME/.asdf}/shims:$PATH"
```

> [!NOTE] 이 줄은 asdf shim 디렉터리를 `PATH` 앞에 추가하는 설정이다. 자세한 설명은 [[how-to-guide/java-execution-path/01-zsh-command-execution/index#asdf의 PATH 설정 탐구|asdf의 PATH 설정 탐구]] 참고.

### 1-1. JDK

#### 1-1-1. Java 플러그인 추가

(최초 1회) java 플러그인 추가
  
  ```zsh
asdf plugin add java https://github.com/halcyon/asdf-java.git
  ```

> [!INFO] 참고:
> https://github.com/halcyon/asdf-java

#### 1-1-2. 설치 가능한 JDK 조회

```zsh
asdf list all java
```

#### 1-1-3. JDK 설치

```zsh
# Temurin 21 설치
asdf install java latest:temurin-21
```

#### 1-1-4. 설치확인

```zsh
asdf list java
```

#### 1-1-5. `JAVA_HOME` 설정

`~/.zshrc.local`에 아래 항목 추가

```zsh
asdf_update_java_home() {
  local java_path

  # asdf 미설정 디렉터리에서 경고 출력 억제
  java_path="$(asdf which java 2>/dev/null)"

  if [[ -n "$java_path" ]]; then
    export JAVA_HOME="$(dirname "$(dirname "${java_path:A}")")"
    export JDK_HOME="$JAVA_HOME"
  else
    # 이전 디렉터리의 값이 남지 않도록 정리
    unset JAVA_HOME
    unset JDK_HOME
  fi
}

autoload -U add-zsh-hook
add-zsh-hook precmd asdf_update_java_home
add-zsh-hook chpwd asdf_update_java_home

# 현재 셸에 즉시 반영
asdf_update_java_home
```

[원본](https://github.com/halcyon/asdf-java/blob/master/set-java-home.zsh )을 수정함


> [!INFO] 참고: 
> https://github.com/halcyon/asdf-java?tab=readme-ov-file#java_home


#### 1-1-6. macOS `JAVA_HOME` integration:

`~/.asdfrc`에 아래 항목 추가

```zsh
java_macos_integration_enable=yes
```

> [!INFO] 참고: 
> https://github.com/halcyon/asdf-java?tab=readme-ov-file#macos


#### 1-1-7. workspace에 latest major version 고정

workspace 폴더로 이동 후 아래 실행:

```zsh
asdf set java latest:temurin-21
```

이 명령어를 실행하면 `workspace/.tool-versions`에 write
- temurin-21 중에 최신을 등록함
- https://github.com/halcyon/asdf-java#latest 참고

확인:

```zsh
cat .tool-versions                       
```

```zsh
asdf current java
```

> [!NOTE] [[#1-1-2. JDK 설치]]에서 설치한 버젼과 달라지는 경우(최신 버젼이 바뀐 경우)
> 
> `asdf set java latest:temurin-21` 그리고 `asdf current java`를 실행하면 최신 버젼이 설치되지 않았다고 출력해줌
> 
> `asdf install java`를 실행하면 `asdf set java latest:temurin-21`이 갱신한 `.tool-versions`에 따라 새로 JDK 다운로드

> [!INFO] 참고:
> https://github.com/halcyon/asdf-java?tab=readme-ov-file#latestj
