---
tags:
  - reference
  - questions
  - zsh
aliases:
  - Shell Grammer
references: https://zsh.sourceforge.io/Doc/Release/Shell-Grammar.html#Shell-Grammar
---
# Shell Grammer

## 6.1 Simple Commands & Pipelines

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

## 6.5 Reserved Words

> [!Quote]
> 
> The following words are recognized as reserved words when used as the first word of a command unless quoted or disabled using disable -r:
> 
> do done esac then elif else fi for case if while function repeat time until select coproc nocorrect foreach end ! \[\[ { } declare export float integer local readonly typeset
> 
> Additionally, ‘}’ is recognized in any position if neither the IGNORE_BRACES option nor the IGNORE_CLOSE_BRACES option is set.

예약어는 기본적으로 command 의 가장 첫번째 단어 자리에서 인식
