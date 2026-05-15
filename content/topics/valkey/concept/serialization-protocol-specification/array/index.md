---
aliases:
  - Array
  - Multibulk
references: https://valkey.io/topics/protocol/#arrays
tags:
  - concept
  - valkey
---
# Array

RESP 타입의 배열. 구버전에서는 **multibulk** 라고도 불림

클라이언트가 valkey 서버로 명령을 보낼 때 쓰는 기본 형식 
- 명령과 인자를 [[topics/valkey/concept/serialization-protocol-specification/bulk-string/index|bulk string]] 으로 묶은 array 로 전송

> [!QUOTE] [RESP protocol description](https://valkey.io/topics/protocol/)
> 
> Clients send commands to a Valkey server as an array of bulk strings. The first (and sometimes also the second) bulk string in the array is the command's name. Subsequent elements of the array are the arguments for the command.

## 인코딩

```text
*<count>\r\n<element-1>...<element-N>
```

- `*` : array 임을 나타내는 타입 구분자
- `<count>` : 원소 개수 (10진수)
- 각 element 는 RESP 타입(보통 bulk string) 으로 이어짐

## 예시

`set foo bar` 의 인코딩 — 3 개 원소짜리 array, 각 원소가 길이 3 짜리 bulk string

```text
*3\r\n$3\r\nset\r\n$3\r\nfoo\r\n$3\r\nbar\r\n
```

## 특수 케이스

빈 배열은 다음과 같이 인코딩

```text
*0\r\n
```

`null`은 다음과 같이 인코딩

```text
*-1\r\n
```

null array 는 `BLPOP` timeout 처럼 결과가 없는 경우의 응답으로 사용됨
