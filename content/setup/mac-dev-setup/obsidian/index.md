# obsidan

아래를 진행하기 전 [[terminal]] 설정을 완료하자

## shortcut 쉘 함수로 만들기

`~/.zshrc.local`에 추가:

```zsh
ov() {
  local dir="${1:-$PWD}"

  if [ ! -d "$dir/.obsidian" ]; then
    echo "❌ '$dir' 는 Obsidian vault가 아닙니다 (.obsidian 없음)"
    return 1
  fi

  open -a "Obsidian" "$dir"
}
```

적용:

```zsh
source ~/.zshrc
```

사용:

```zsh
ov # 현재 경로의 obsidain vault 열기
ov {path} # path의 obsidian vault 열기
```
