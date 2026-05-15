---
aliases:
  - 00. valkey 의 RESP 프로토콜
tags:
  - explanation
  - resp-protocol
---
# 00. valkey 의 RESP 프로토콜

> [!NOTE] valkey-server 가 6379 포트에서 동작 중이라고 가정 ([[tutorial/valkey-local-run/index|로컬에서 Valkey 실행하기]] 참고)

valkey-cli 로 명령을 입력하면 valkey-server 까지 어떻게 전달될까?

```zsh
❯ ./cmake-build-debug/bin/valkey-cli
127.0.0.1:6379> set foo bar
OK
```

valkey-cli 와 valkey-server 는 TCP 연결 위에서 **RESP(Redis Serialization Protocol)** 라는 텍스트 기반 프로토콜로 주고받음

즉 RESP 형식으로 요청을 보낼 수 있는 도구라면 valkey-cli 가 아니더라도 valkey 와 대화할 수 있음

[[topics/valkey/explanation/resp-protocol/01-connect-with-nc/index|다음 페이지]] 에서 `nc` 로 직접 접속해 명령을 보내봄
