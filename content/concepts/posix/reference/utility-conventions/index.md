---
tags:
  - reference
  - posix
  - utility-conventions
references: https://pubs.opengroup.org/onlinepubs/9799919799/
aliases:
  - Utility Conventions
---
# Utility Conventions

## 용어 정리

```
utility_name [-a] [-b] [-c option_argument]
    [-d|-e] [-f[option_argument]] [operand...]
```

`utility_name`: 유틸리티(명령어) 이름
`option`: 유틸리티 뒤에 오는 옵션
`option_argument`: 옵션이 요구하는 인자
`operand`: 옵션들(인자 포함)이 다 끝난 후 나오는 데이터(대상)


### `option_arguemnt` 관련 제약

기본 규칙은 다음과 같음
- `option option_argument` 형태로 옵션 뒤 공백으로 구분
	- `[-c option_argument]`처럼 표기
- `option_argument`는 **not optional(필수)**

다음과 같은 예외 존재
- 유틸리티 구현자는 `[-coption_argument]` 처럼 공백 없는 것도 허용해야함
	- 역사적 호환성 때문
- `[-f[option_argument]]` 선택 인자는 공백 없이 붙여써야 함
	- 선택 인자를 생략하면 기본 동작
	- `-f` 그리고 공백 뒤의 단어를 절대 `-f`의 인자로 취급해선 안 됨

요약하면 
- 필수 인자는 붙여써도, 띄어써도 됨
- 선택 인자는 반드시 붙여써야 함

### `option_arguemnt` 순서

기본적으로 SYNOPSIS에 표기된 것들은 알파벳 순서

기본적으로 어떤 옵션이 먼저 오는지 순서가 실행에 영향을 주지 않음

다음과 같은 예외 존재
- OPTIONS 섹션에 명시된 경우

### parameter 표기

SYNOPSIS의 paramter은 다음과 같이 표기
- `option_argument`
- `<option argument>

### `option` 표기

세가지 스타일
- 개별 나열(옵션이 적을 때)
	- `utility_name [-a] [-b] [-c option_argument][-d|-e] [-f[option_argument]] [operand...]` 처럼 각 옵션을 개별적으로 나열
- 옵션을 그룹화
	- `utility_name [-abcDxyz] [-p arg] [operand]`
	- `abcDxyz` 옵션들은 모두 `option_argumnets`를 받지 않음
	- `p` 옵션은 `option_argumnets`를 받아서 따로 분리
- 완전히 추상화
	- `utility_name [options] [operands]`
	- SYNOPSIS 섹션만 봐서는 모르고, OPTION 섹션을 봐야할 수도 있음

### 숫자값 제약

`operand`, `option-argument`의 숫자값 제약이 있음
- 10 진법으로 해석
- 최소 보장 범위 0 ~ 2147483647
- 음수 허용 시 범위
- 파일 크기 관련 값은 시스템이 지원하는 최대 파일 크기까지 인식
- ...

### `[]` 기호

`[]`(square brackets)의 의미는 **optional**

### `|` 기호

베타적 OR

`[-d|-e]` 옵션 둘 중 하나만 사용 가능

SYNOPSIS 섹션에서 여려 줄로 표기해도 각 줄은 베타적

### `...` 기호

Ellipses(`...`)기호는 1번 이상의 반복을 나타냄

`[option...]`, `[operand...]`처럼 대괄호와 결합하는 경우 0번 이상의 반복을 표현
- 1번 이상이 optinal 이니 0번 이상으로 해석

`utility_name [-g option_argument]... [operand...]` 표기 분석은 다음과 같음
- `[-g option_argument]`이 통째로 0번 이상 반복
- `operand`도 0개 이상

`utility_name -f option_argument [-f option_argument]... [operand...]` 표기 분석은 다음과 같음
- `-f option_argument`이 1번 이상 반복
