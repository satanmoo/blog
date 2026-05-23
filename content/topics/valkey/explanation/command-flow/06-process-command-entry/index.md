---
aliases:
  - 06. 명령어 처리 진입
tags:
  - explanation
  - command-flow
---
# 06. 명령어 처리 진입

`c->cmd` 가 정해졌어도 바로 실행하지 않음. 실행 가능한 상태인지 먼저 검사함

- `moduleCallCommandFilters()` : 모듈이 등록한 필터 적용. 필요하면 명령/인자가 변경될 수 있음
- `authRequired()` : 인증이 필요한 연결인지 확인
- `commandCheckExistence()` : `c->cmd == NULL` 이면 unknown command 에러
- `commandCheckArity()` : `c->cmd->arity` 와 `c->argc` 비교
- `ACLCheckAllPerm()` : ACL 권한 검사 (카테고리/key 패턴/채널)
- Cluster redirection : slot 이 이 노드 소유인지. 아니면 `MOVED`/`ASK` 응답
- Pub/Sub : 구독 중 connection 은 일부 명령만 허용
- Loading : RDB/AOF 로드 중에는 `CMD_LOADING` 플래그 있는 명령만 허용
- Script : Lua/Functions 실행 중에는 `CMD_NOSCRIPT` 명령 금지

전부 통과하면 두 경로 중 하나로 갈림

- 트랜잭션(`MULTI`) 안이면 `queueMultiCommand()` 로 큐에 적재. 지금 실행하지 않음
- 아니면 `call(c, flags)` 로 진짜 실행 — [[topics/valkey/explanation/command-flow/07-call-and-execute/index|다음 페이지]]
