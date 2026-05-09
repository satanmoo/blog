---
aliases:
  - SET
references: https://valkey.io/commands/set/
tags:
  - concept
  - valkey
---
# SET

key 에 string value 를 저장함

이미 값이 있으면 타입과 관계없이 덮어씀

## 문법

```text
SET key value
```

## 응답

성공 시 simple string `OK` 를 돌려줌

```text
set foo bar
+OK
```

옵션(EX, NX 등)은 [공식 문서](https://valkey.io/commands/set/) 참고
