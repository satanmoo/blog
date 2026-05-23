---
aliases:
  - 02. 가장 낮은 레벨 helper
tags:
  - explanation
  - addreply
---
# 02. 가장 낮은 레벨 helper

대부분의 high-level `addReply*` 함수는 결국 다음 네 함수 중 하나로 내려옴

| 함수 | 역할 | 내부 동작 |
| --- | --- | --- |
| `_addReplyToBufferOrList()` | bytes 를 실제 출력 버퍼에 추가 | `c->buf` 에 먼저 쓰고, 넘치면 `c->reply` list |
| `_addReplyProtoToList()` | reply list 에 protocol bytes 추가 | `_addReplyPayloadToList()` 호출 |
| `addReplyProto()` | 이미 RESP 형태인 bytes 그대로 추가 | `prepareClientToWrite()` → `_addReplyToBufferOrList()` |
| `_addReplyLongLongWithPrefix()` | `$3\r\n`, `*2\r\n`, `:1\r\n` 같은 길이 header 생성 | shared header 최적화 또는 `ll2string()` |

## 길이 header 가 자주 나오는 이유

RESP 응답은 거의 항상 **타입 prefix + 길이 + payload + CRLF** 형태. 즉 길이 header 가 따로 만들어져야 함

- bulk string: `$3\r\n` + `bar` + `\r\n`
- array: `*2\r\n` + element1 + element2
- integer: `:42\r\n`

`_addReplyLongLongWithPrefix()` 가 이 길이 header 를 만들어서 버퍼에 추가하는 역할. 자주 쓰이는 길이는 미리 만들어둔 **shared header** 를 그대로 가리키게 해서 `ll2string()` 호출도 피함

```text
_addReplyLongLongWithPrefix(c, len, prefix)
  ↓
shared header 캐시에 있나?
  ├─ 예 → 그대로 addReplyProto()
  └─ 아니오 → ll2string() 으로 문자열 만들고 추가
```

## 호출 관계

high-level helper 가 이 네 함수를 어떻게 조합하는지 한 예 — `addReplyBulkSds()`

```text
addReplyBulkSds(c, s)
  ↓
prepareClientToWrite(c)
  ↓
_addReplyLongLongWithPrefix(c, sdslen(s), '$')   ← "$3\r\n"
  ↓
_addReplyToBufferOrList(c, s, sdslen(s))         ← "bar"
  ↓
sdsfree(s)
  ↓
_addReplyToBufferOrList(c, "\r\n", 2)            ← "\r\n"
```

bulk string 한 번 보내는데 안에서 길이 header / payload / CRLF 가 별도 호출로 나뉨

high-level 함수를 새로 짤 일은 거의 없고, 보통은 이미 있는 helper 중 하나를 골라서 쓰면 됨 — 다음 페이지부터 그 helper 들을 분류해서 봄

[[topics/valkey/explanation/addreply/03-input-types-and-ownership/index|다음 페이지]] 는 helper 가 받는 입력 타입과 소유권 규칙
