---
aliases:
  - 00. valkey 명령이 정의된 곳
tags:
  - explanation
  - command-flow
---
# 00. valkey 명령이 정의된 곳

[[topics/valkey/concept/commands/echo/index|ECHO]] 같은 명령은 `src/` 아래 `commands` 로 시작하는 세 파일에 흩어져서 정의됨

- `src/commands.h` : `serverCommand` / `serverCommandArg` 같은 메타 구조체 선언
- `src/commands.c` : `serverCommandTable[]` 본체 — 등록할 명령 목록
- `src/commands.def` : 각 명령의 메타데이터와 함수 포인터를 `MAKE_CMD(...)` 매크로로 적어둔 자동 생성물

`commands.def` 에 echo 항목은 다음과 같이 들어가 있음

```text
{MAKE_CMD("echo", ..., echoCommand, 2,
         CMD_LOADING|CMD_STALE|CMD_FAST,
         ACL_CATEGORY_CONNECTION|ACL_CATEGORY_FAST,
         NULL, ECHO_Keyspecs, 0, NULL, 1),
 .args = ECHO_Args},
```

`MAKE_CMD(...)` 주요 필드

- `name` : `"echo"` — lookup 에 쓰이는 이름
- `proc` : `echoCommand` — 실제 명령 함수 포인터. 이후 `c->cmd->proc(c)` 에서 호출됨
- `arity` : `2` — 명령 이름 포함 인자 수. 음수면 "최소 인자 수" (예: `-3` 은 3 개 이상)
- `flags` : `CMD_LOADING|CMD_STALE|CMD_FAST` — 실행 가능 상태와 명령 성격
- `ACL` : `ACL_CATEGORY_CONNECTION|ACL_CATEGORY_FAST` — ACL 권한 검사 카테고리
- `keys` / `args` : key 인자 명세와 `COMMAND INFO` 설명

`commands.def` 항목들은 `commands.c` 에서 `serverCommandTable[]` 이라는 정적 배열로 묶임. 이 배열이 [[topics/valkey/explanation/command-flow/01-command-registration-on-startup/index|다음 페이지]] 에서 hashtable 로 옮겨짐
