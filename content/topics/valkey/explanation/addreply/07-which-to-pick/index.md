---
aliases:
  - 07. 어떤 addReply 를 고를까
tags:
  - explanation
  - addreply
---
# 07. 어떤 addReply 를 고를까

명령 함수를 새로 짜거나 기존 함수 응답을 고칠 때, 어떤 `addReply*` 를 부를지 정하는 cheat-sheet

## 만들고 싶은 응답 형태 → 함수

| 응답 형태 | 주로 쓰는 함수 | 메모 |
| --- | --- | --- |
| 이미 있는 object 문자열 (예: `c->argv[i]`) | `addReplyBulk(c, obj)` | object 소유권 유지. copy avoidance |
| 새로 만든 sds 문자열 | `addReplyBulkSds(c, s)` | sds 소유권 넘김. 호출 후 다시 쓰면 안 됨 |
| C string | `addReplyBulkCString(c, str)` | NULL 이면 null reply |
| raw buffer | `addReplyBulkCBuffer(c, buf, len)` | |
| `OK` 같은 상태 | `addReplyStatus(c, "OK")` | `+OK\r\n` |
| 에러 | `addReplyError(c, "...")` | `-ERR ...\r\n` + `afterErrorReply()` |
| 정수 | `addReplyLongLong(c, n)` | `:n\r\n` |
| 부울 | `addReplyBool(c, v)` | RESP2 정수, RESP3 `#t`/`#f` |
| null | `addReplyNull(c)` | RESP2 `$-1`, RESP3 `_` |
| 배열 / 맵 / 셋 | `addReplyArrayLen()` 등 후 원소별 `addReply*()` | 길이 header 먼저 |
| 원소 수를 모를 때 | `addReplyDeferredLen()` + `setDeferredArrayLen()` | placeholder 채우기 |

## 예시: prefix 붙인 echo 응답

이미 있는 `c->argv[1]` 앞에 `echo2_` 를 붙여 돌려준다고 가정 — [[topics/valkey/explanation/sds/00-what-is-sds/index|sds]] 시리즈에서 본 helper 와 결합

```text
void echoxxxCommand(client *c) {
    sds s = sdsempty();
    s = sdscatfmt(s, "echo2_%S", c->argv[1]->ptr);
    addReplyBulkSds(c, s);
}
```

- `sdsempty()` 로 새 sds 시작
- `sdscatfmt()` 로 prefix + 원본 이어 붙이기 — 반환값 재대입 필수
- `addReplyBulkSds()` 가 sds 소유권 가져감 — 이후 `s` 다시 쓰면 안 됨

## 명령 전용 local helper 도 있음

공통 API 외에도 일부 파일에는 그 도메인에서만 쓰는 `addReply...` 함수가 있음

- `server.c` : `COMMAND INFO`, `COMMAND DOCS` 응답 조립
- `sentinel.c` : Sentinel instance 응답
- `cluster_legacy.c` / `cluster_slot_stats.c` : cluster link / slot stats
- `t_stream.c` : stream ID 응답
- `geo.c` : distance 응답

이들은 공통 helper 가 아니라 해당 명령의 복잡한 응답을 조립하기 위한 local helper. 새 명령을 짤 때 비슷한 패턴이 필요하면 참고하면 됨

## 정리

- 응답 RESP 타입을 먼저 정하고 (bulk / status / error / 숫자 / collection)
- 입력 메모리 타입에 맞는 helper 선택 (`robj*` / `sds` / `char*`)
- sds 를 넘길 때만 소유권 이전을 의식

helper 가 많아 보이지만, 실제 명령 작성에 자주 쓰이는 건 위 표의 6~8 개 정도

addreply 시리즈 끝
