---
aliases:
  - ZRANGE
references: https://valkey.io/commands/zrange/
tags:
  - concept
  - valkey
---
# ZRANGE

sorted set 에서 특정 범위의 member 를 돌려줌. 

기본 순서는 score **오름차순**(같은 score 는 lexicographic 순)

## 문법

```text
ZRANGE key start stop
```

- `start`, `stop` : 0 부터 시작하는 index. 음수는 끝에서부터 셈 (`-1` = 마지막)
- `0 -1` : 처음부터 끝까지 전체

## 응답

해당 범위의 member 를 [[concepts/valkey/concept/serialization-protocol-specification/array/index|array]] 로 돌려줌
- score는 a < b < c 순서

```text
zrange myzset 0 -1
*3
$1
a
$1
b
$1
c
```

옵션(BYSCORE, BYLEX, REV, LIMIT, WITHSCORES 등)은 [공식 문서](https://valkey.io/commands/zrange/) 참고
