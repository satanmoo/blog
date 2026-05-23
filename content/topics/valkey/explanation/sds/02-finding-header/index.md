---
aliases:
  - 02. sds header 찾기와 sdslen
tags:
  - explanation
  - sds
---
# 02. sds header 찾기와 sdslen

[[topics/valkey/explanation/sds/01-memory-layout/index|01]] 에서 본 것처럼 sds 포인터는 `buf[]` 를 가리키고, header 는 그 바로 앞에 숨어 있음

문제: header 의 크기가 타입마다 다른데, 어떻게 header 시작 주소를 찾을까?

## flags 는 buf 바로 앞 byte

답은 `flags` 가 `buf[]` 바로 앞에 위치한다는 점에 있음. 즉 `s[-1]` 이 곧 `flags`

```text
static inline unsigned char sdsType(const_sds s) {
    unsigned char flags = s[-1];
    return flags & SDS_TYPE_MASK;
}
```

- `s[-1]` 으로 `flags` 한 byte 를 읽음
- 하위 3 bit (`SDS_TYPE_MASK`) 가 sdshdr 타입 (5/8/16/32/64)

타입을 알면 해당 header 의 크기도 정해짐. `SDS_HDR(T, s)` 매크로가 그 차이만큼 뒤로 빠져서 header 시작 주소를 돌려줌

```text
#define SDS_HDR(T, s) \
    ((struct sdshdr##T *)((s) - (sizeof(struct sdshdr##T))))
```

`sizeof(struct sdshdr8)`, `sizeof(struct sdshdr16)` ... 가 컴파일 타임에 결정되므로, 매크로 호출 시 `T` 자리에 8/16/32/64 를 적어두면 됨

## sdslen 이 strlen 없이 즉답하는 이유

`sdslen()` 은 `s` 를 처음부터 끝까지 훑지 않음. header 의 `len` 만 읽으면 끝

```text
static inline size_t sdslen(const_sds s) {
    switch (sdsType(s)) {
    case SDS_TYPE_5:  return SDS_TYPE_5_LEN(s[-1]);
    case SDS_TYPE_8:  return SDS_HDR(8, s)->len;
    case SDS_TYPE_16: return SDS_HDR(16, s)->len;
    case SDS_TYPE_32: return SDS_HDR(32, s)->len;
    case SDS_TYPE_64: return SDS_HDR(64, s)->len;
    }
    return 0;
}
```

- `sdshdr5` 는 header 가 1 byte 라 `len` 필드가 따로 없고, `flags` 의 상위 5 bit 가 길이임. 그래서 `SDS_TYPE_5_LEN(s[-1])` 로 추출
- 나머지는 타입별 header 구조체로 캐스팅 후 `->len`

`strlen()` 처럼 `\0` 만날 때까지 한 byte 씩 보지 않으니 O(1)

같은 방식으로 `sdsavail()` 은 `alloc - len`, `sdssetlen()` 은 header 의 `len` 만 갱신함

[[topics/valkey/explanation/sds/03-sdsfree/index|다음 페이지]] 는 메모리 해제. sds 포인터를 그대로 `free()` 에 넘기면 왜 안 되는지
