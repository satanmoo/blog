---
aliases:
  - 03. sdsfree 는 buf 를 바로 free 하지 않는다
tags:
  - explanation
  - sds
---
# 03. sdsfree 는 buf 를 바로 free 하지 않는다

sds 포인터를 그대로 `free()` 에 넘기면 segfault 또는 heap corruption 이 남

이유는 [[topics/valkey/explanation/sds/01-memory-layout/index|01]] 에서 본 메모리 구조 때문 — allocator 가 돌려준 포인터(= header 시작) 와 사용자에게 노출되는 sds 포인터(= `buf[]`) 가 다름

```text
[ header ][ ... ][ flags ][ buf[] = s ][ \0 ]
↑                          ↑
allocator 가 돌려준 포인터    sds 포인터
```

해제할 때는 sds 포인터를 header 크기만큼 뒤로 되돌려서 원래 할당 시작 주소를 복원해야 함

```text
void *sdsAllocPtr(const_sds s) {
    return (void *)(s - sdsHdrSize(sdsType(s)));
}

void sdsfree(sds s) {
    if (s == NULL) return;
    s_free_with_size(sdsAllocPtr(s), sdsAllocSize(s));
}
```

흐름

```text
sdsfree(s)
  ↓
sdsType(s)            // s[-1] 으로 타입 결정
  ↓
sdsHdrSize(type)      // 타입별 header 크기
  ↓
s - header_size       // 원래 할당 시작 주소
  ↓
s_free_with_size(...)
```

즉 `free(s)` 가 아니라 `free(s - header_size)` 라는 점이 핵심

`sdsAllocSize()` 는 header + alloc + null terminator 까지 포함한 전체 크기를 계산해서, size-aware allocator 가 정확한 길이로 반환할 수 있게 함

## 왜 굳이 이렇게 했나

할당은 header 와 buf 를 **한 덩어리** 로 잡음. 두 번 잡으면 두 번 해제해야 하고, cache locality 도 나빠짐. 그래서

- allocator 한 번에 `header_size + alloc + 1` 만큼 할당
- 사용자에게는 `buf` 주소를 sds 로 돌려줌 (`s = ptr + header_size`)
- 해제할 때는 다시 `ptr = s - header_size` 로 되돌아감

이 비대칭이 모든 sds 함수 (`sdslen`, `sdsfree`, `sdsMakeRoomFor` 등) 의 동작 방식을 결정함

> [!NOTE] 직접 `free(s)` 금지
>
> sds 를 `free()` 에 직접 넘기면 allocator 는 잘못된 포인터를 받음. 반드시 `sdsfree(s)` 사용

[[topics/valkey/explanation/sds/04-helpers/index|다음 페이지]] 는 sds 생성/확장 helper 들 (`sdsempty`, `sdscatfmt`, `sdsMakeRoomFor`)
