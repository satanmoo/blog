---
tags:
  - reference
  - questions
  - zsh
  - shell-grammer
aliases:
  - Shell Grammer
references: https://zsh.sourceforge.io/Doc/Release/Shell-Grammar.html#Shell-Grammar
---
# Shell Grammer

## 용어 정리

### Simple Commands

> [!QUOTE] simple command
> 
> A _simple command_ is a sequence of optional parameter assignments followed by blank-separated words, with optional redirections interspersed.

### Word

> [!QUOTE] Word
> 
> The first word is the command to be executed, and the remaining words, if any, are arguments to the command.

`echo foo`

위 simple command 에서 첫번째 단어 `echo`는 실행할 **command**, 공백으로 구분된 두번째 단어는 **argument**다.
- argument는 optional
