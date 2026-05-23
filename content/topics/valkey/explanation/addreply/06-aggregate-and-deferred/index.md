---
aliases:
  - 06. 길이 header 와 deferred reply
tags:
  - explanation
  - addreply
---
# 06. 길이 header 와 deferred reply

[[topics/valkey/concept/serialization-protocol-specification/array/index|Array]] / map / set 같은 collection 응답은 먼저 길이 header 를 쓰고, 그 다음에 원소를 차례로 채움

## aggregate length 계열

| 함수 | RESP prefix | 의미 |
| --- | --- | --- |
| `addReplyArrayLen(c, n)` | `*` | array |
| `addReplyMapLen(c, n)` | `%` / `*` | RESP3 map. RESP2 에서는 array 로 변환 |
| `addReplySetLen(c, n)` | `~` / `*` | RESP3 set. RESP2 에서는 array |
| `addReplyAttributeLen(c, n)` | <code>&#124;</code> | RESP3 attribute |
| `addReplyPushLen(c, n)` | `>` | RESP3 push |

공통 구현은 `addReplyAggregateLen()` 이고, 내부에서 `_addReplyLongLongWithPrefix()` 로 길이 header 를 만듦

기본 패턴

```text
addReplyArrayLen(c, 3);
addReplyBulkCString(c, "a");
addReplyBulkCString(c, "b");
addReplyBulkCString(c, "c");
```

wire 상에서는 `*3\r\n$1\r\na\r\n$1\r\nb\r\n$1\r\nc\r\n`

## deferred reply 계열

문제: 원소를 하나씩 만들면서 **원소 개수를 미리 알 수 없는** 명령이 있음 (`SCAN`, `HSCAN`, `KEYS *` 같은)

이런 경우 `addReplyDeferredLen(c)` 로 placeholder 노드를 먼저 넣고, 원소를 다 추가한 뒤 길이를 채움

```text
void *replylen = addReplyDeferredLen(c);    // placeholder
int n = 0;
while (...) {
    addReplyBulkCBuffer(c, key, len);
    n++;
}
setDeferredArrayLen(c, replylen, n);         // 나중에 길이 채움
```

흐름

```text
addReplyDeferredLen()
  ↓
reply list 에 NULL placeholder 추가
  ↓
원소들을 addReply*() 로 추가
  ↓
setDeferredArrayLen() / setDeferredMapLen() / ...
  ↓
setDeferredReply() 가 placeholder node 를 실제 protocol bytes 로 채움
```

## deferred / write-prepared helper 들

| 함수 | 용도 |
| --- | --- |
| `setDeferredReply()` | placeholder node 를 실제 protocol bytes 로 채움 |
| `setDeferredArrayLen()` | array length 채우기 |
| `setDeferredMapLen()` | map length |
| `setDeferredSetLen()` | set length |
| `setDeferredReplyBulkSds()` | deferred 위치에 bulk sds 채우고 free |
| `prepareClientForFutureWrites()` | 많은 원소를 쓸 때 write 준비 비용 절감 |
| `addWritePreparedReply*()` | 이미 준비된 client 에 반복 응답 추가 |

`LRANGE`, `KEYS`, `SMEMBERS` 처럼 원소 수가 미리 안 정해질 수 있는 명령은 이 패턴을 자주 씀

[[topics/valkey/explanation/addreply/07-which-to-pick/index|다음 페이지]] 는 어떤 helper 를 고를지 cheat-sheet
