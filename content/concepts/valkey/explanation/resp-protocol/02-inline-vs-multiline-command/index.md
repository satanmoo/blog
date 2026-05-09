---
aliases:
  - 02. inline command 와 multiline command
tags:
  - explanation
  - resp-protocol
---
# 02. inline command 와 multiline command

RESP 의 기본 요청 형식은 [[concepts/valkey/concept/serialization-protocol-specification/array/index|multibulk(Array of Bulk Strings)]] 임 
- 명령 이름과 인자를 bulk string 배열로 묶어 보냄

`set foo bar` 를 RESP multibulk 로 표현하면 다음과 같음

```text
*3\r\n
$3\r\nset\r\n
$3\r\nfoo\r\n
$3\r\nbar\r\n
```

- `*3` : 원소 3 개짜리 배열
- `$3` : 길이 3 인 bulk string
- 각 라인은 `\r\n` 으로 구분

그런데 [[concepts/valkey/explanation/resp-protocol/01-connect-with-nc/index|이전 페이지]] 처럼 nc 에 평문으로 `set foo bar` 만 입력해도 동일하게 동작함

이유: valkey 는 클라이언트가 보낸 첫 바이트를 보고 파싱 방식을 결정함

- 첫 바이트가 `*` → multibulk command 로 파싱
- 그 외 → inline command 로 파싱 (공백으로 구분된 한 줄 평문)

inline command 는 사람이 직접 입력하기 쉽지만, 일부 명령은 inline 으로 지원되지 않을 수 있음

> [!Info] inline command 의 한계
> 
> 개행이나 공백이 포함된 바이너리 데이터를 인자로 보내야 한다면 multibulk 만 가능
