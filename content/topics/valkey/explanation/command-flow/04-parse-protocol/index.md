---
aliases:
  - 04. 프로토콜 파싱
tags:
  - explanation
  - command-flow
---
# 04. 프로토콜 파싱

`processInputBuffer()` 는 query buffer 의 첫 바이트를 보고 파싱 방식을 분기함

| 입력 형태 | 파싱 함수 |
| --- | --- |
| Inline (평문 한 줄) | `parseInlineBuffer()` |
| RESP / Multibulk (`*` 로 시작) | `parseMultibulkBuffer()` |

분기 기준 자체는 [[topics/valkey/explanation/resp-protocol/02-inline-vs-multiline-command/index|02. inline command 와 multiline command]] 와 동일 — 같은 규칙을 클라이언트 측 시각과 서버 측 시각에서 본 셈

파싱이 끝나면 `client` 에 다음 값이 채워짐

- `c->argc` : 인자 개수
- `c->argv[]` : 명령 이름과 인자 배열 (각 원소는 [[topics/valkey/concept/serialization-protocol-specification/bulk-string/index|bulk string]] 으로 감싼 valkey object)

예를 들어 `echo hello` 가 들어왔다면 파싱 결과는

```text
c->argc    = 2
c->argv[0] = "echo"
c->argv[1] = "hello"
```

이제 `c->argv[0]` 으로 어떤 명령인지 [[topics/valkey/explanation/command-flow/05-lookup-command/index|다음 페이지]] 에서 찾음
