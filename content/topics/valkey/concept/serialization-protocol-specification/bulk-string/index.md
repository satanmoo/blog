---
aliases:
  - Bulk string
references: https://valkey.io/topics/protocol/#bulk-strings
tags:
  - concept
  - valkey
---
# Bulk string

길이가 앞에 붙는 바이너리 안전한(binary-safe) 문자열
- `\r\n` 이 데이터 안에 있어도 파서가 종료로 오해할 일이 없음

임의의 바이너리 데이터를 담을 수 있어서 _binary_ 또는 _blob_ 이라고도 부름

## 인코딩

```text
$<length>\r\n<data>\r\n
```

- `$` : bulk string 임을 나타내는 타입 구분자
- `<length>` : `<data>` 의 바이트 수 (10진수)
- `<data>` : 실제 페이로드

`<length>`(length prefix) 가 있어서 파서가 특수문자를 스캔할 필요 없이 정확한 바이트만큼 읽으면 됨

## 예시

`bar` 의 인코딩

```text
$3\r\nbar\r\n
```

## 특수 케이스

빈 문자열은 다음과 같이 인코딩

```text
$0\r\n\r\n
```

`null`은 다음과 같이 인코딩

```text
$-1\r\n
```

null bulk string 은 [[topics/valkey/concept/commands/get/index|GET]] 처럼 key 가 없을 때 응답으로 사용됨
