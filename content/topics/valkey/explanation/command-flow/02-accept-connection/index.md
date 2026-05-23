---
aliases:
  - 02. 연결 수락
tags:
  - explanation
  - command-flow
---
# 02. 연결 수락

클라이언트 TCP 연결이 들어오면 valkey 는 `client` 객체를 만들고, 그 connection 에 read 이벤트 핸들러를 붙임

| 순서 | 함수 | 역할 |
| --- | --- | --- |
| 1 | `acceptCommonHandler()` | listen 소켓에서 accept 한 새 connection 처리 진입점 |
| 2 | `createClient()` | `client` 구조체 할당 |
| 3 | `connSetReadHandler(conn, readQueryFromClient)` | read 이벤트 콜백 등록 |

`client` 객체에는 이후 모든 단계가 다루게 될 상태가 들어 있음

- `c->querybuf` : socket 에서 읽은 raw 바이트가 쌓이는 query buffer
- `c->argc` / `c->argv[]` : 파싱이 끝난 뒤 채워질 명령 이름과 인자
- `c->cmd` : lookup 결과로 연결될 `serverCommand` 포인터
- `c->buf` / `c->reply` : 응답이 쌓일 출력 버퍼

이후 socket 에 읽을 데이터가 생기면 [[topics/valkey/explanation/command-flow/03-read-query/index|다음 페이지]] 의 `readQueryFromClient()` 가 호출됨
