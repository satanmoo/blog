---
aliases:
  - GET
references: https://valkey.io/commands/get/
tags:
  - concept
  - valkey
---
# GET

key 의 value 를 가져옴

key 가 없으면 `nil` 을 돌려줌

## 문법

```text
GET key
```

## 응답

[[concepts/valkey/concept/serialization-protocol-specification/bulk-string/index|bulk string]] 으로 value 를 돌려줌. 

없으면 `nil`로 응답
- RESP2 wire 에서는 `$-1\r\n` (null bulk string)로 인코딩
- RESP3 에서는 `_\r\n` (null) 로 인코딩

```text
get foo
$3
bar
```

- `$3` : 길이 3 인 bulk string
- `bar` : 실제 값
