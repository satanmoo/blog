## 터미널에서 크롬 프로필 목록 확인하는 함수 & 특정 프로필을 여는 함수

크롬 주소창에:

```Text
chrome://version
```

접속 후 **Profile Path** 항목 확인

```
$HOME/Library/Application Support/Google/Chrome
```

위 경로에 프로필 파일로 저장됨을 확인

`~/.zshrc.local`에 추가

```zsh
chrome() {
  local chrome_dir="$HOME/Library/Application Support/Google/Chrome"

  # 원하는 별칭 -> 실제 profile directory
  # (오른쪽 값은 반드시 Default / Profile N 형태, 크롬에서 이렇게 지음)
  typeset -A CHROME_PROFILES=(
    infra     "Default"
    personal  "Profile 2"
    unity-dev "Profile 3"
    ent       "Profile 4"
  )

  _chrome_list_profiles() {
    [ -d "$chrome_dir" ] || { echo "Chrome data dir not found: $chrome_dir"; return 1; }

    echo "Aliases:"
    for k in ${(ok)CHROME_PROFILES}; do
      printf "  %-10s -> %s\n" "$k" "${CHROME_PROFILES[$k]}"
    done
    echo
    echo "Raw profile directories:"
    ls -1 "$chrome_dir" | grep -E '^(Default|Profile [0-9]+)$' | sort -V
  }

  # ls / list / 인자없음
  if [[ $# -eq 0 || "$1" == "ls" || "$1" == "list" ]]; then
    _chrome_list_profiles
    return
  fi

  local key="$1"
  local target="${CHROME_PROFILES[$key]}"

  # 별칭 있으면 매핑 사용, 없으면 입력값 자체를 profile dir로 시도
  if [[ -z "$target" ]]; then
    target="$*"
  fi

  if [[ ! -d "$chrome_dir/$target" ]]; then
    echo "Profile not found: $target"
    echo
    _chrome_list_profiles
    return 1
  fi

  open -na "Google Chrome" --args --profile-directory="$target"
}
```

적용:

```zsh
source ~/.zshrc
```

사용:

```zsh
chrome ls
chrome {profile-alias}
```
