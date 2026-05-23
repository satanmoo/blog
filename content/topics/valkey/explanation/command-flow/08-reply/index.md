---
aliases:
  - 08. 응답 작성과 전송
tags:
  - explanation
  - command-flow
---
# 08. 응답 작성과 전송

명령 함수는 결과를 바로 socket 에 쓰지 않음. client 의 응답 버퍼에 먼저 쌓아둠

응답 작성 흐름

```text
addReply()
  ↓
prepareClientToWrite()
  ↓
putClientInPendingWriteQueue()
  ↓
_addReplyToBufferOrList()
  ↓
c->buf 또는 c->reply 에 적재
```

- 작은 응답은 고정 크기 `c->buf` 에 들어감
- `c->buf` 가 꽉 차거나 너무 큰 응답은 `c->reply` 리스트로 넘어감
- client 는 `pending write queue` 에 들어가서 다음 이벤트 루프 사이클에서 처리됨

전송 흐름

```text
sendReplyToClient()
  ↓
writeToClient()
  ↓
_writeToClient()
  ↓
connWrite() / writevToClient()
```

write 이벤트가 발생하거나 이벤트 루프가 pending write queue 를 처리할 때 위 흐름으로 실제 socket 에 씀

socket 으로 나가는 바이트 모양은 [[topics/valkey/explanation/resp-protocol/03-resp-v2-response-types/index|03. RESP v2 응답 타입]] 에서 본 형태 — `+OK\r\n`, `$3\r\nbar\r\n`, `*3\r\n...` 같은 RESP 형식

`addReply*` 계열의 입력 타입·RESP 타입별 분기는 [[topics/valkey/explanation/addreply/00-overview/index|addreply 시리즈]] 에서 자세히 다룸
