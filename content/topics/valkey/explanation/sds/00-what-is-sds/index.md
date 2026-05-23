---
aliases:
  - 00. sds 란 무엇인가
tags:
  - explanation
  - sds
---
# 00. sds 란 무엇인가

valkey 의 문자열 값은 대부분 **sds (Simple Dynamic Strings)** 형태로 다뤄짐. `c->argv[i]` 안의 문자열, 응답 버퍼에 쌓는 데이터, config 값, key 이름 등 거의 다 sds

선언은 단순함

```text
typedef char *sds;
```

`char *` 라서 일반 C 문자열처럼 `printf("%s", s)` 로 출력하거나 `memcpy` 로 복사할 수 있음

하지만 일반 C 문자열과 두 가지가 다름

- 문자열 **앞쪽 메모리**에 길이/할당 크기/타입을 가진 header 가 숨어 있음
- 그래서 `strlen()` 처럼 `\0` 만날 때까지 훑지 않고 즉답 가능 (binary-safe)

즉 sds 는 "header + buf" 한 덩어리이지만, 사용자에게는 그 안쪽 `buf` 주소만 노출됨

```text
[ header ][ buf[] ][ \0 ]
          ↑
       sds 포인터
```

이 구조 덕분에 다음이 가능해짐

- `\0` 이 데이터 안에 들어가도 길이를 잃지 않음 (binary safe)
- 길이 조회가 O(1)
- 기존 C string API 와 호환

다음 페이지부터 [[topics/valkey/explanation/sds/01-memory-layout/index|메모리 구조]] 를 자세히 봄
