---
aliases:
  - 04. RESP v3 와 hello 3
tags:
  - explanation
  - resp-protocol
---
# 04. RESP v3 와 hello 3

[[topics/valkey/explanation/resp-protocol/03-resp-v2-response-types/index|RESP v2]] 는 표현할 수 있는 타입이 적어서 (boolean, null, double 등을 별도로 표시할 수 없음) 풍부한 응답을 다루기 부족함

v3 가 도입되었지만 기본은 여전히 v2 
- 커넥션마다 `hello 3` 명령을 보내야 v3 로 업그레이드 됨

```text
hello 3
```

위 명령을 보낸 이후 응답부터는 v3 추가 타입이 사용됨

| 구분자 | type       | 설명             | 예시                       |
| --- | ---------- | -------------- | ------------------------ |
| `!` | blob error | 길이 prefix 붙는 에러 | `!21\r\nERR ...`         |
| `(` | big number | 큰 정수           | `(12345678901234...\r\n` |
| `,` | double     | 실수             | `,3.14\r\n`              |
| `_` | null       | null           | `_\r\n`                  |
| `#` | boolean    | boolean        | `#t\r\n` / `#f\r\n`      |

v3 는 커넥션 단위로 동작하므로, 다른 커넥션은 영향 받지 않고 v2 그대로 사용함
