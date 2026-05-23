---
aliases:
  - 03. 데이터 수신
tags:
  - explanation
  - command-flow
---
# 03. 데이터 수신

read 이벤트가 발생하면 [[topics/valkey/explanation/command-flow/02-accept-connection/index|이전 페이지]] 에서 등록한 `readQueryFromClient()` 가 불림

```text
readQueryFromClient()
  ↓
readToQueryBuf()
  ↓
connRead()
  ↓
handleReadResult()
  ↓
processInputBuffer()
```

- `readToQueryBuf()` : socket 에서 데이터를 읽음
- `connRead()` : 추상화된 connection 레이어 (TCP / TLS / Unix socket) 의 실제 read 호출
- 읽은 데이터는 `client` 의 query buffer (`c->querybuf`) 에 쌓임
- `handleReadResult()` : 부분 읽기/연결 종료/에러를 정리
- `processInputBuffer()` : 모인 query buffer 를 보고 파싱을 시작

여기까지는 아직 raw 바이트 상태. 어떤 명령인지조차 결정되지 않음

파싱은 [[topics/valkey/explanation/command-flow/04-parse-protocol/index|다음 페이지]] 부터
