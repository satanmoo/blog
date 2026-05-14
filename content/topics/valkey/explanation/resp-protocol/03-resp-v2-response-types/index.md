---
aliases:
  - 03. RESP v2 응답 타입
tags:
  - explanation
  - resp-protocol
---
# 03. RESP v2 응답 타입

RESP v2 응답은 첫 글자가 타입을 의미하고, 각 라인은 `\r\n` 으로 구분됨

| 구분자 | type                                                                                            | 설명                 | 예시              |
| --- | ----------------------------------------------------------------------------------------------- | ------------------ | --------------- |
| `+` | simple string                                                                                   | 특수문자 없는 일반 문자열     | `+OK\r\n`       |
| `-` | error                                                                                           | 에러 문자열             | `-ERR ...\r\n`  |
| `:` | integer                                                                                         | 정수                 | `:3\r\n`        |
| `$` | [[topics/valkey/concept/serialization-protocol-specification/bulk-string/index\|bulk string]] | 길이 prefix 가 붙는 문자열 | `$3\r\nbar\r\n` |
| `*` | [[topics/valkey/concept/serialization-protocol-specification/array/index\|multibulk(array)]]  | 배열                 | `*3\r\n...`     |

[[topics/valkey/explanation/resp-protocol/01-connect-with-nc/index|nc 로 접속한]] 상태에서 [[topics/valkey/concept/commands/zadd/index|ZADD]] / [[topics/valkey/concept/commands/zrange/index|ZRANGE]] 를 실행하면 `:` 와 `*` 응답을 직접 볼 수 있음

```text
zadd myzset 1 a 2 b 3 c
:3
zrange myzset 0 -1
*3
$1
a
$1
b
$1
c
```

- `:3` : 추가된 원소 개수 3 (integer)
- `*3` : 원소 3 개짜리 배열 — 각 원소가 `$1\r\n<문자>` 로 이어짐
