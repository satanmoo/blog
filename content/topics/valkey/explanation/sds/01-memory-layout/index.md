---
aliases:
  - 01. sds 메모리 구조
tags:
  - explanation
  - sds
---
# 01. sds 메모리 구조

sds 는 header 와 buf 가 한 덩어리로 잡힌 메모리

```text
[ len ][ alloc ][ flags ][ buf[] = s ][ \0 ]
                          ↑
                       sds 포인터
```

| 필드 | 의미 |
| --- | --- |
| `len` | 현재 문자열 길이 |
| `alloc` | 사용 가능한 전체 버퍼 크기 (header 제외) |
| `flags` | SDS 타입 정보 (하위 3 bit) |
| `buf[]` | 사용자에게 보이는 문자열 영역. 끝에 `\0` 가 붙음 |

핵심은 **sds 포인터가 header 가 아니라 `buf[]` 시작 주소를 가리킨다** 는 점. 그래서 `char *` 처럼 다룰 수 있는 동시에, 한 byte 만 앞으로 가면 header 에 접근할 수 있음

## sdshdr 타입

길이가 짧은 문자열에 큰 header 를 붙이는 건 낭비라서, valkey 는 문자열 길이에 따라 다른 크기의 header 를 씀

| 타입 | 표현 가능 최대 길이 | header 크기 |
| --- | --- | --- |
| `sdshdr5` | 31 byte (5 bit) | 1 byte (flags 안에 len 포함) |
| `sdshdr8` | 255 byte | 3 byte |
| `sdshdr16` | 64 KB | 5 byte |
| `sdshdr32` | 4 GB | 9 byte |
| `sdshdr64` | 그 이상 | 17 byte |

문자열을 새로 만들 때 길이를 보고 적절한 타입을 고름 (`sdsReqType()`). append 중 더 큰 타입이 필요해지면 새로 할당해서 옮김

소스 기준: `src/sds.h` 의 `typedef char *sds`, `sdshdr5/8/16/32/64` 구조체

[[topics/valkey/explanation/sds/02-finding-header/index|다음 페이지]] 에서 sds 포인터로부터 header 를 어떻게 찾는지 봄
