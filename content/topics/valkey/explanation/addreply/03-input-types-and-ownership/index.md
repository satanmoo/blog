---
aliases:
  - 03. 입력 타입과 소유권
tags:
  - explanation
  - addreply
---
# 03. 입력 타입과 소유권

helper 함수가 받는 입력은 크게 세 가지 — `robj*`, [[topics/valkey/explanation/sds/00-what-is-sds/index|sds]], `char* + len`

각 타입마다 함수가 따로 있고, **메모리 소유권** 규칙도 다름

| 함수 | 입력 | RESP framing | 메모리 소유권 |
| --- | --- | --- | --- |
| `addReply()` | `robj*` | 없음, object 문자열만 추가 | object 소유권 유지 |
| `addReplySds()` | `sds` | 없음, 문자열만 추가 | 호출 후 `sdsfree()` |
| `addReplyProto()` | `char*`, `len` | 이미 만들어진 protocol 그대로 | 소유권 유지 |
| `addReplyBulk()` | `robj*` | bulk string | object 소유권 유지 |
| `addReplyBulkCBuffer()` | buffer, len | bulk string | 소유권 유지 |
| `addReplyBulkSds()` | `sds` | bulk string | 호출 후 `sdsfree()` |

핵심은 마지막 컬럼

- `robj*` 또는 raw buffer → 함수는 **복사** 만 하고 소유권은 호출자가 계속 가짐
- `sds` → 함수가 **소유권을 가져감**. 안에서 `sdsfree()` 호출

## sds 를 넘기는 경우의 주의

`addReplyBulkSds()` 와 `addReplySds()` 는 받은 sds 의 소유권을 가져감

```text
sds s = sdsempty();
s = sdscatfmt(s, "echo2_%S", arg);
addReplyBulkSds(c, s);
// 여기서부터 s 는 더 이상 호출자 것이 아님
```

- 함수 내부에서 응답 버퍼에 복사한 뒤 `sdsfree(s)` 호출
- 만약 `prepareClientToWrite()` 가 실패해도 함수 내부에서 `sdsfree()` 가 호출됨 (early-exit 경로에서도 해제됨)

> [!NOTE] use-after-free 주의
>
> `addReplyBulkSds(c, s)` 호출 뒤에 같은 `s` 를 다시 쓰거나 `sdsfree(s)` 를 또 하면 use-after-free 또는 double-free
>
> ```text
> addReplyBulkSds(c, s);
> sdsfree(s);              // double-free
> printf("%s\n", s);       // use-after-free
> ```
>
> 새 sds 를 만들어 응답으로 보낼 때 자주 만나는 함정

## 어떤 경로를 탈지

- 이미 있는 valkey object (예: `c->argv[i]`) 를 그대로 응답으로 → `addReplyBulk(c, obj)` (object 소유권 유지)
- 새 문자열을 만들어 응답으로 → `addReplyBulkSds(c, sdsempty + sdscatfmt)` (소유권 넘김)
- 외부 C string / buffer → `addReplyBulkCString(c, str)` / `addReplyBulkCBuffer(c, buf, len)`

[[topics/valkey/explanation/addreply/04-bulk-series/index|다음 페이지]] 부터 RESP 타입별 helper 계열을 자세히 봄. bulk string 부터
