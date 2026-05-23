---
aliases:
  - 07. 명령어 실행 (call)
tags:
  - explanation
  - command-flow
---
# 07. 명령어 실행 (call)

실제 명령 함수는 `call()` 안에서 호출됨

```text
call()
  ↓
server.executing_client = c
  ↓
c->flag.executing_command = 1
  ↓
c->cmd->proc(c)
```

- `server.executing_client = c` : 지금 실행 중인 client 를 전역에 기록 (모듈/디버깅에서 참조)
- `c->flag.executing_command = 1` : 재진입 방지/통계용 플래그
- `c->cmd->proc(c)` : [[topics/valkey/explanation/command-flow/00-where-commands-are-defined/index|00 단계]] 의 `MAKE_CMD(...)` 에 적힌 함수 포인터를 호출하는 지점

[[topics/valkey/concept/commands/echo/index|ECHO]] 의 본체는 단순함

```text
void echoCommand(client *c) {
    addReplyBulk(c, c->argv[1]);
}
```

`c->argv[1]` 을 그대로 bulk string 으로 응답 버퍼에 적재함

`setCommand`/`getCommand`/`zaddCommand` 등도 형태는 같음. 차이는 `proc` 안에서 `server.db` 와 어떻게 상호작용하느냐 뿐

`addReplyBulk()` 이후 응답이 socket 으로 어떻게 나가는지는 [[topics/valkey/explanation/command-flow/08-reply/index|다음 페이지]] 에서
