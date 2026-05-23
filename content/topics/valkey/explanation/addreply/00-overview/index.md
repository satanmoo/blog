---
aliases:
  - 00. command 함수는 socket 에 직접 쓰지 않는다
tags:
  - explanation
  - addreply
---
# 00. command 함수는 socket 에 직접 쓰지 않는다

`echoCommand`, `getCommand`, `setCommand` 같은 명령 함수들은 결과를 직접 socket 에 쓰지 않음. 대신 client 의 응답 버퍼에 쌓아두고, 이벤트 루프가 알아서 내보냄

이 흐름은 valkey4 의 [[topics/valkey/explanation/command-flow/08-reply/index|08. 응답 작성과 전송]] 에서 한 번 봤음. 이번 시리즈는 같은 흐름을 helper API 시각에서 다시 봄

전체 흐름

```text
command 함수
  ↓
addReply*()
  ↓
prepareClientToWrite()
  ↓
_addReplyToBufferOrList()
  ↓
c->buf 또는 c->reply list
  ↓
(write 이벤트 발생)
  ↓
sendReplyToClient() → writeToClient() → connWrite()
```

명령 함수가 호출하는 함수는 `addReply` 로 시작하는 **수십 개의 helper** 중 하나. 어떤 helper 를 고르느냐에 따라 RESP wire 상에서 다른 응답이 나옴

예를 들어 같은 문자열 `OK` 라도

- `addReplyStatus(c, "OK")` → `+OK\r\n` (simple string)
- `addReplyBulkCString(c, "OK")` → `$2\r\nOK\r\n` (bulk string)
- `addReplyError(c, "OK")` → `-OK\r\n` (error)

전부 다른 응답임. 그래서 helper 가 많고, 고를 줄 알아야 함

이 시리즈는 다음 순서로 다룸

- 01: `c->buf` 와 `c->reply` 가 어떻게 나뉘는지
- 02: 가장 낮은 레벨 helper (`_addReplyToBufferOrList`, `_addReplyLongLongWithPrefix`)
- 03: 입력 타입 (`robj*` / `sds` / raw bytes) 과 소유권
- 04: bulk string 계열 (`addReplyBulk*`)
- 05: status / error / 숫자
- 06: aggregate length 와 deferred reply
- 07: 어떤 helper 를 고를지 cheat-sheet

[[topics/valkey/explanation/addreply/01-buffer-vs-list/index|다음 페이지]] 부터 시작
