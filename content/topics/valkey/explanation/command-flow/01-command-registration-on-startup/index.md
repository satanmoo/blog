---
aliases:
  - 01. 서버 시작 시 명령어 등록
tags:
  - explanation
  - command-flow
---
# 01. 서버 시작 시 명령어 등록

[[topics/valkey/explanation/command-flow/00-where-commands-are-defined/index|이전 페이지]] 의 `serverCommandTable[]` 은 정적 배열이라 그대로는 빠른 lookup 이 어려움

서버 초기화 중 hashtable 로 옮겨 담음

```text
server.commands      = hashtableCreate(&commandSetType);
server.orig_commands = hashtableCreate(&originalCommandSetType);
populateCommandTable();
```

- `server.commands` : 현재 이름 기준. `rename-command` 설정이 적용된 결과
- `server.orig_commands` : 원래 이름 기준. rename 영향을 받지 않음 — 모듈/스크립트가 원본 이름을 참조할 때 사용

`populateCommandTable()` 은 `serverCommandTable[]` 을 순회하면서 한 항목씩 등록함

```text
c = serverCommandTable + j;
c->fullname     = sdsnew(c->declared_name);
c->current_name = c->fullname;
populateCommandStructure(c);
hashtableAdd(server.commands, c);
hashtableAdd(server.orig_commands, c);
```

이 시점이 끝나면 `server.commands` 안에 `"echo"` → `serverCommand*` 매핑이 들어가 있음

요청이 들어왔을 때 이 hashtable 이 [[topics/valkey/explanation/command-flow/05-lookup-command/index|05 단계]] 에서 검색됨

요청 처리 흐름 자체는 [[topics/valkey/explanation/command-flow/02-accept-connection/index|다음 페이지]] 부터
