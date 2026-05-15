---
aliases:
  - ZADD
references: https://valkey.io/commands/zadd/
tags:
  - concept
  - valkey
---
# ZADD

sorted set 에 score 와 member 쌍을 추가함. 
- sorted set 은 score 로 정렬되는 unique member 집합

## 문법

```text
ZADD key score member [score member ...]
```

## 응답

새로 추가된 member 개수를 integer 로 돌려줌

```text
zadd myzset 1 a 2 b 3 c
:3
```

3 개 모두 새 member 라서 `:3` 임. 이미 존재하는 member 의 score 를 갱신하는 경우는 카운트되지 않음

옵션(NX, XX, GT, LT, CH, INCR 등)은 [공식 문서](https://valkey.io/commands/zadd/) 참고
