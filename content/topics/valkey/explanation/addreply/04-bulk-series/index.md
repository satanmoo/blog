---
aliases:
  - 04. bulk string 계열
tags:
  - explanation
  - addreply
---
# 04. bulk string 계열

[[topics/valkey/explanation/resp-protocol/03-resp-v2-response-types/index|bulk string]] (`$len\r\n payload \r\n`) 으로 응답할 때 쓰는 helper 들

## addReplyBulk

```text
addReplyBulk(c, obj)
  ↓
copy avoidance 되면 object reference 만 저장 후 종료
  ↓
아니면: addReplyBulkLen(c, obj) → addReply(c, obj) → addReplyProto(c, "\r\n")
```

- `obj` 가 공유 가능한 valkey object 면 reference 만 reply list 에 매단 뒤 끝 (실제 복사 안 함)
- 아니면 `$len\r\n` + payload + `\r\n` 순서로 차례차례 버퍼에 적재

`addReplyBulkLen(c, obj)` 가 내부에서 `_addReplyLongLongWithPrefix(c, len, '$')` 를 호출함

## 다른 입력에서 bulk string 만드는 helper

| 함수 | 용도 |
| --- | --- |
| `addReplyBulkCString(c, str)` | `NULL` 이면 null reply, 아니면 C string 을 bulk |
| `addReplyBulkCBuffer(c, buf, len)` | 길이 명시된 raw buffer 를 bulk |
| `addReplyBulkLongLong(c, n)` | 정수를 문자열로 바꾼 뒤 bulk (정수 응답이 아니라 문자열로 보낼 때) |
| `addReplyBulkSds(c, s)` | sds 를 bulk. 소유권 가져감 (`sdsfree`) |
| `addReplyVerbatim(...)` | RESP3 verbatim string, RESP2 에서는 bulk 로 fallback |

`addReplyVerbatim()` 은 텍스트 종류 (`txt:`, `mkd:` 같은 prefix) 까지 함께 전달하는 RESP3 응답. RESP2 클라이언트한테는 일반 bulk 처럼 보임

## echoCommand 의 경우

이미 본 echoCommand 본체

```text
void echoCommand(client *c) {
    addReplyBulk(c, c->argv[1]);
}
```

- `c->argv[1]` 은 `robj*` 이고 이미 valkey object 임
- 그래서 `addReplyBulk` 로 충분 — copy avoidance 경로를 탈 확률이 높음
- 만약 새 문자열을 prefix 붙여서 만든다면 `addReplyBulkSds(c, sdscatfmt(sdsempty(), "...", ...))` 같은 경로가 됨

## 길이 0 / null

- 빈 bulk string: `$0\r\n\r\n`
- null bulk string: `$-1\r\n` (RESP2) — `addReplyNull()` 이 RESP2 에서는 이걸 보냄

[[topics/valkey/explanation/addreply/05-status-error-number/index|다음 페이지]] 는 status / error / 숫자 계열
