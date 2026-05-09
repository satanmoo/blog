---
aliases:
  - 01. nc 를 통한 valkey 접속
tags:
  - explanation
  - resp-protocol
---
# 01. nc 를 통한 valkey 접속

> [!Info] macOS 에는 `telnet` 이 기본 제공되지 않음
> 
> 대신 기본 제공되는 `nc` 를 사용함 
> 아래 내용은 `telnet` 으로도 동일하게 동작

```zsh
% nc 127.0.0.1 6379
```

접속하면 입력 대기 상태가 됨

명령을 한 줄씩 입력해보면 응답이 그대로 돌아옴

```text
set foo bar
+OK
get foo
$3
bar
```

- [[concepts/valkey/concept/commands/set/index|SET]] : key 에 value 를 저장
- [[concepts/valkey/concept/commands/get/index|GET]] : key 의 value 를 가져옴

valkey-cli 가 아닌데도 동작함

응답 첫 글자(`+`, `$`)가 RESP 응답 타입을 의미함 — 자세한 건 [[concepts/valkey/explanation/resp-protocol/03-resp-v2-response-types/index|03. RESP v2 응답 타입]] 에서

그런데 한 줄짜리 평문 명령이 어떻게 동작하는지는 [[concepts/valkey/explanation/resp-protocol/02-inline-vs-multiline-command/index|다음 페이지]] 에서 다룸
