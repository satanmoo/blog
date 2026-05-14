---
tags:
  - reference
  - git
  - questions
references: https://git-scm.com/docs/git-config
aliases:
  - git-config questions
---
## `$(prefix)/etc/gitconfig` 에서 `$(prefix)`

`$(prefix)`는 Git이 설치된 경로
- macOS (Homebrew, Apple Silicon) 기준 `/opt/homebrew/etc/gitconfig`

Git의 빌드 시스템(Makefile)에서 쓰는 변수

> [!QUOTE] prefix
> 
> A prefix used in constructing the default values of the variables listed below. The default value of `prefix` should be /usr/local. When building the complete GNU system, the prefix will be empty and /usr will be a symbolic link to /. (If you are using Autoconf, write it as ‘@prefix@’.)
> 
> Running ‘make install’ with a different value of `prefix` from the one used to build the program should _not_ recompile the program.
> 
> [GNU make/Makefile Conventions/Variables for Installation Directories](https://www.gnu.org/software/make/manual/html_node/Directory-Variables.html) 
