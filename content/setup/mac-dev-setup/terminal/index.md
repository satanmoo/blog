---
tags:
  - mac
  - wezterm
---
## 1. Wezterm 설치

```bash
brew install --cask wezterm
```

## 2. zinit 설치

```bash
brew install zinit
```

## 3. Wezterm 설정

```zsh
mkdir -p ~/.config/wezterm
nvim ~/.config/wezterm/wezterm.lua
```

```lua
local wezterm = require 'wezterm'
local config = wezterm.config_builder()

-- 렌더링
config.front_end = "WebGpu"

-- 컬러스킴
config.color_scheme = "iTerm2 Dark Background"

-- 창 설정
config.window_decorations = "RESIZE"
config.window_padding = { left = 8, right = 8, top = 8, bottom = 8 }

return config
```

## 4. zsh 설정

```zsh
nvim ~/.zshrc
```

```zsh
# If not running interactively, don't do anything
[[ -o interactive ]] || return

# Enable Powerlevel10k instant prompt
if [[ -r "${XDG_CACHE_HOME:-${HOME}/.cache}/p10k-instant-prompt-${(%):-%n}.zsh" ]]; then
  source "${XDG_CACHE_HOME:-${HOME}/.cache}/p10k-instant-prompt-${(%):-%n}.zsh"
fi

# Homebrew completions (Apple Silicon)
if [[ -d /opt/homebrew ]]; then
  fpath=("${fpath[@]}" /opt/homebrew/share/zsh/site-functions)
fi

# zinit
source "$(brew --prefix zinit)/zinit.zsh"

zinit ice depth=1
zinit light romkatv/powerlevel10k

zinit light zdharma/fast-syntax-highlighting
zinit light zsh-users/zsh-completions

ZSH_AUTOSUGGEST_USE_ASYNC=1
zinit light zsh-users/zsh-autosuggestions

zinit light zsh-users/zsh-history-substring-search
bindkey '^[[A' history-substring-search-up
bindkey '^[[B' history-substring-search-down

autoload -Uz compinit
compinit
zinit cdreplay

# powerlevel10k
[[ ! -f ~/.p10k.zsh ]] || source ~/.p10k.zsh

# History
setopt EXTENDED_HISTORY HIST_IGNORE_ALL_DUPS SHARE_HISTORY
HISTSIZE=9000000
SAVEHIST="${HISTSIZE}"
HISTFILE=~/.zsh_history

# AUTO_CD
setopt AUTO_CD

# General
alias mv='mv -i'
alias cp='cp -i'
export LANG=en_US.UTF-8
export EDITOR=nvim

# Load local config
if [[ -f ~/.zshrc.local ]]; then
  source ~/.zshrc.local
fi
```

저장 후 `exec zsh` 하면 `p10k` configure 마법사가 나옴

마법사가 끝나면 `~/.p10k.zsh`에 설정이 저장됨

