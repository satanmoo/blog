---
tags:
  - reference
  - questions
  - zsh
aliases:
  - Expansion
references: https://zsh.sourceforge.io/Doc/Release/Expansion.html
---
# Expansion

## 14.7 Filename Expansion

> [!QUOTE]
> 
> Each word is checked to see if it begins with an unquoted ‘~’. If it does, then the word up to a ‘/’, or the end of the word if there is no ‘/’, is checked to see if it can be substituted in one of the ways described here. If so, then the ‘~’ and the checked portion are replaced with the appropriate substitute value.

word
- 쉘에서 단어는 공백(space, tab)으로 쪼개진 조각

예를 들어 `cp ~/docs/a.txt ~/backup/`에서 단어는 다음과 같음
- `cp`
- `~docs/a.txt`
- `~/backup/`

unquoted '~'
- 따옴표로 감싸지지 않은 ~
- "~", '~' 은 여기에 해당되지 않음

checked portion
- ~ 로 시작하는 단어에서 '/'를 만나거나 단어의 끝
- '/', 단어 끝 공백은 포함되지 않음

예를 들어 `~+/foo/bar.txt`에서 checked portion은 `~+`

