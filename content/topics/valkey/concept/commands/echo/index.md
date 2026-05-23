---
aliases:
  - ECHO
references: https://valkey.io/commands/echo/
tags:
  - concept
  - valkey
---
# ECHO

받은 message 를 그대로 돌려줌

연결이 살아 있는지 확인하거나, 서버 응답 형태를 직접 확인할 때 자주 쓰임

## 문법

```text
ECHO message
```

## 응답

[[topics/valkey/concept/serialization-protocol-specification/bulk-string/index|bulk string]] 으로 입력 message 를 그대로 돌려줌

```text
echo hello
$5
hello
```

- `$5` : 길이 5 인 bulk string
- `hello` : 받은 message 그대로
