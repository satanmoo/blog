---
aliases:
  - 05. 명령어 lookup
tags:
  - explanation
  - command-flow
---
# 05. 명령어 lookup

파싱된 `c->argv[0]` 을 키로 [[topics/valkey/explanation/command-flow/01-command-registration-on-startup/index|01 에서 만든]] `server.commands` 를 검색함

```text
prepareCommandQueue()
  ↓
prepareCommand()
  ↓
prepareCommandGeneric()
  ↓
lookupCommand()
  ↓
lookupCommandLogic()
  ↓
hashtableFind()
```

- `lookupCommand()` : `server.commands` 에서 명령 이름을 찾음
- 결과는 `c->cmd` 에 연결됨. 이후 모든 단계가 `c->cmd` 의 메타데이터(flags, arity, ACL category, proc) 를 보고 동작함
- 서브명령(`COMMAND INFO`, `CLIENT LIST` 같은) 이면 `c->argv[0]` 과 `c->argv[1]` 을 합쳐서 다시 lookup 함

명령이 없으면 `c->cmd == NULL`. 이 경우는 [[topics/valkey/explanation/command-flow/06-process-command-entry/index|다음 페이지]] 의 `commandCheckExistence()` 에서 unknown command 에러로 잘림
