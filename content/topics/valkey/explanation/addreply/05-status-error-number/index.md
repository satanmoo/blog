---
aliases:
  - 05. status / error / 숫자 응답
tags:
  - explanation
  - addreply
---
# 05. status / error / 숫자 응답

bulk 가 아닌 단순 응답을 만들 때 쓰는 helper 들

## status 계열 (`+...\r\n`)

| 함수 | RESP | 역할 |
| --- | --- | --- |
| `addReplyStatus()` | `+...\r\n` | null-terminated status string |
| `addReplyStatusLength()` | `+...\r\n` | 길이가 명시된 status |
| `addReplyStatusFormat()` | `+...\r\n` | printf 스타일 format |

`SET key value` → `+OK\r\n` 응답이 대표적

```text
addReplyStatus(c, "OK");
```

## error 계열 (`-...\r\n`)

| 함수 | RESP | 역할 |
| --- | --- | --- |
| `addReplyError()` | `-ERR ...\r\n` | 일반 error (자동으로 `ERR ` prefix 붙음) |
| `addReplyErrorLength()` | `-...\r\n` | 길이 명시 |
| `addReplyErrorSds*()` | `-...\r\n` | sds 받음, 호출 후 free |
| `addReplyErrorFormat*()` | `-...\r\n` | printf 스타일 format |

error 응답은 추가 부수 효과가 있음 — `afterErrorReply()` 가 호출되어 다음을 처리함

- error 통계 카운터 증가
- 로깅 (옵션)
- 모듈의 deferred error 처리

그래서 단순히 RESP wire 만 만드는 게 아니라 valkey 전체에서 "이 명령이 에러로 끝났다" 를 기록하는 효과까지 따라옴

## 숫자 계열

| 함수 | RESP2 | RESP3 |
| --- | --- | --- |
| `addReplyLongLong()` | `:n\r\n` | `:n\r\n` |
| `addReplyDouble()` | bulk string | `,d\r\n` |
| `addReplyBigNum()` | bulk string | `(n\r\n` |
| `addReplyHumanLongDouble()` | bulk string | `,d\r\n` |

RESP2 는 정수 외 숫자 타입이 없어서 bulk string 으로 fallback. RESP3 에서는 전용 타입이 있어서 클라이언트가 정확히 double/big number 로 받음

`INCR foo` → `:5\r\n` 응답처럼 정수 응답은 `addReplyLongLong()` 한 줄이면 됨

## 특수 RESP 타입

| 함수 | RESP2 | RESP3 |
| --- | --- | --- |
| `addReplyNull()` | `$-1\r\n` | `_\r\n` |
| `addReplyBool()` | `:0\r\n` / `:1\r\n` | `#f\r\n` / `#t\r\n` |
| `addReplyNullArray()` | `*-1\r\n` | `_\r\n` |

명령 함수는 직접 RESP2/3 분기하지 않음. 알맞은 `addReplyXxx()` 만 부르면 helper 가 client 의 RESP 버전을 보고 알아서 표기를 고름

[[topics/valkey/explanation/addreply/06-aggregate-and-deferred/index|다음 페이지]] 는 array / map / set 같은 collection 응답
