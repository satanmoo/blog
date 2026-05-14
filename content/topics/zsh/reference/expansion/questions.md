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
- `~/docs/a.txt`
- `~/backup/`

unquoted '~'
- 따옴표로 감싸지지 않은 ~
- "~", '~' 은 여기에 해당되지 않음

checked portion
- ~ 로 시작하는 단어에서 '/'를 만나거나 단어의 끝
- '/', 단어 끝 공백은 포함되지 않음

예를 들어 `~+/foo/bar.txt`에서 checked portion은 `~+`

filename expansion은 각 word가 unquoted `~`로 시작하는지 검사함

시작한다면 `~`부터 `/` 또는 word 끝 전까지를 checked portion으로 보고, 이 부분을 다른 값으로 치환할 수 있는지 확인함

치환할 수 있으면 `~`와 checked portion이 적절한 값으로 바뀜

---

> [!Quote]
> 
> A ‘~’ by itself is replaced by the value of $HOME. A ‘~’ followed by a ‘+’ or a ‘-’ is replaced by current or previous working directory, respectively.
> 
> A ‘~’ followed by a number is replaced by the directory at that position in the directory stack. ‘~0’ is equivalent to ‘~+’, and ‘~1’ is the top of the stack. ‘~+’ followed by a number is replaced by the directory at that position in the directory stack. ‘~+0’ is equivalent to ‘~+’, and ‘~+1’ is the top of the stack. ‘~-’ followed by a number is replaced by the directory that many positions from the bottom of the stack. ‘~-0’ is the bottom of the stack. The PUSHD_MINUS option exchanges the effects of ‘~+’ and ‘~-’ where they are followed by a number.

대표적인 경우

- `~`: `$HOME`
- `~+`: 현재 working directory
- `~-`: 이전 working directory
- `~N`: directory stack의 N번째 directory

```zsh
% echo ~
/Users/{user}
```

### 14.7.2 Static named directories

> [!QUOTE]
>
> A ‘~’ followed by anything not already covered consisting of any number of alphanumeric characters or underscore (‘_’), hyphen (‘-’), or dot (‘.’) is looked up as a named directory, and replaced by the value of that named directory if found.

앞의 기본 케이스에 해당하지 않는 `~name` 형태에서, `name`이 아래 문자들로만 구성되면 zsh는 이를 named directory로 조회함

- alphabetic character
- digit
- `_`
- `-`
- `.`

찾으면 `~name`은 그 named directory의 path로 치환됨

--- 

> [!Quote]
> 
> Named directories are typically home directories for users on the system.

named directory는 보통 시스템 사용자의 home directory임

예를 들어 사용자 `user`가 있으면 `~user`는 보통 `user`의 home directory로 확장됨

```zsh
% cd ~user
% pwd
/Users/{user}
```

---

> [!Quote]
> 
> They may also be defined if the text after the ‘~’ is the name of a string shell parameter whose value begins with a ‘/’. Note that trailing slashes will be removed from the path to the directory (though the original parameter is not modified).

여기서 "They"는 named directories

값이 `/`로 시작하는 string shell parameter를 만들어도 named directory처럼 사용할 수 있음

```zsh
% proj=/Users/me/projects/blog
% cd ~proj
```

`proj` parameter의 값이 `/`로 시작하므로 `~proj`는 `/Users/me/projects/blog`로 확장될 수 있음

> [!QUOTE]
>
> It is also possible to define directory names using the -d option to the hash builtin.

`hash -d`로도 directory name을 정의할 수 있음

```zsh
% hash -d proj=/Users/me/projects/blog
% cd ~proj
```

이때 `hash -d`가 수정하는 대상은 named directory hash table임

즉 다음 매핑을 직접 넣는 것과 같음

```text
proj -> /Users/me/projects/blog
```
