---
tags:
  - posix
  - reference
  - questions
---
## "standard utility"는 무엇인가?

> [!QUOTE] [Utility](https://pubs.opengroup.org/onlinepubs/9799919799/basedefs/V1_chap03.html)
> A program, excluding special built-in utilities provided as part of the Shell Command Language, that can be called by name from a shell to perform a specific task, or related set of tasks.

Utility: `git`과 같이 이름으로 호출되는 프로그램

> [!QUOTE] [Standard Utilities](https://pubs.opengroup.org/onlinepubs/9799919799/basedefs/V1_chap03.html)
> 
> The utilities described in the Shell and Utilities volume of POSIX.1-2024.

Standard Utilities: POSIX.1-2024 문서의 Shell and Utilities 권에 기술된 [유틸리티의 집합](https://pubs.opengroup.org/onlinepubs/9799919799/idx/utilities.html)

![[Screenshot 2026-04-24 at 8.55.40 PM.png]]
## "Unless otherwise noted" 해석하기

> [!Quote]
> 
> Within POSIX.1-2024, a special notation is used for describing the syntax of a utility's arguments. Unless otherwise noted, all utility descriptions use this notation, which is illustrated by this example (see XCU [_2.9.1 Simple Commands_](https://pubs.opengroup.org/onlinepubs/9799919799/utilities/V3_chap02.html#tag_19_09_01)):

"별도의 언급이 없는 한"으로 해석

## `<hypen-minus>`란?

U+002D

흔히 사용하는 `-`

## "an optional option-argument (if present) is included within the same argument string as the option"

`[-coption_argument]` 처럼 공백 없이 붙여써서 하나의 문자열(same string)으로 취급하는 상황을 설명함

## parameters란?

실제 값으로 치환되는 것들

```
utility_name [-a] [-b] [-c option_argument]
    [-d|-e] [-f[option_argument]] [operand...]
```

여기서 `utility_name`, `option` 을 제외한 나머지

## flags

`options`와 같은 말
