---
aliases:
  - 01. c->buf vs c->reply
tags:
  - explanation
  - addreply
---
# 01. c->buf vs c->reply

client 객체에는 응답을 쌓을 곳이 두 개 있음

- `c->buf` : 고정 크기 byte 배열. 작은 응답을 빠르게 쌓는 용도
- `c->reply` : 동적 리스트. `c->buf` 가 꽉 차거나 응답이 크면 여기로 넘어감

helper 가 호출되면 `_addReplyToBufferOrList()` 가 어느 쪽에 쓸지 결정함

```text
_addReplyToBufferOrList(c, s, len)
  ↓
c->buf 에 자리가 있나?
  ├─ 예 → c->buf 에 append
  └─ 아니오 → c->reply 끝 노드에 append (또는 새 노드 생성)
```

## 왜 둘로 나뉘었나

- 작은 응답 (`+OK\r\n`, `:3\r\n`, 짧은 bulk string) 이 압도적으로 많음
- 매번 list 노드를 새로 잡는 건 비싸므로, 고정 버퍼에 빠르게 쌓는 게 유리
- 대신 큰 응답 (긴 `GET` 값, `LRANGE` 결과, `KEYS *`) 은 고정 크기로 감당이 안 됨
- 그래서 두 단계로 나눠둠

## pending write queue

응답이 `c->buf` / `c->reply` 에 쌓였다고 바로 socket 에 나가지는 않음. 이벤트 루프가 다음 사이클에 모아서 처리함

helper 안에서 `prepareClientToWrite(c)` 가 client 를 **pending write queue** 에 넣음. 이벤트 루프가 끝나기 전에 큐에 있는 client 들의 버퍼를 한꺼번에 `writeToClient()` 로 socket 에 씀

```text
addReply*()
  ↓
prepareClientToWrite(c)
  ↓
putClientInPendingWriteQueue(c)
  ↓
... (이벤트 루프 끝)
  ↓
handleClientsWithPendingWrites()
  ↓
writeToClient() → connWrite() / writevToClient()
```

이렇게 모아서 쓰는 덕분에 한 요청에 대한 여러 `addReply*()` 호출이 syscall 한 번에 묶여 나갈 수 있음

> [!Info] writev 최적화
>
> `c->reply` 가 여러 노드일 때 `writevToClient()` 는 노드 여러 개를 `iovec` 으로 묶어 한 번에 write 함

[[topics/valkey/explanation/addreply/02-low-level-helpers/index|다음 페이지]] 는 실제로 byte 를 버퍼에 쌓는 가장 낮은 레벨 helper 들
