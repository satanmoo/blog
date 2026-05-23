---
aliases:
  - 04. sdsempty / sdscatfmt / sdsMakeRoomFor
tags:
  - explanation
  - sds
---
# 04. sdsempty / sdscatfmt / sdsMakeRoomFor

새 sds 를 만들고, 거기에 값을 이어 붙이는 흐름은 helper 셋이 담당함

## sdsempty

빈 sds 를 만드는 helper

```text
sdsempty()
  ↓
sdsnewlen("", 0)
  ↓
_sdsnewlen(init, initlen, trymalloc=0)
  ↓
sdsReqType() → sdsHdrSize() → s_malloc_usable()
  ↓
sdswrite()
```

- `sdsReqType()` : 길이를 보고 sdshdr5/8/16/32/64 중 적절한 타입 선택
- `s_malloc_usable()` : `header_size + alloc + 1` 만큼 한 덩어리 할당
- `sdswrite()` : header 채우고 `\0` 마지막에 붙임

빈 문자열이라도 끝에 `\0` 이 붙음. 이후 append 가 필요하면 `sdsMakeRoomFor()` 가 알맞은 타입과 크기로 확장함

## sdscatfmt

포맷 문자열을 해석하면서 기존 sds 뒤에 값을 이어 붙임

```text
sdscatfmt(s, fmt, ...)
  ↓
초기 여유 공간 확보: sdsMakeRoomFor()
  ↓
fmt 를 한 글자씩 순회
  ↓
%s / %S / %i / %I / %u / %U / %% 처리
  ↓
필요하면 다시 sdsMakeRoomFor()
  ↓
memcpy() 후 sdsinclen()
```

`printf` 와 비슷해 보이지만 지원 포맷이 더 제한적임 (`%d` / `%f` 없음). 대신 sds 의 길이 정보를 활용해서 빠르게 동작함

새 sds 를 prefix + 값으로 조립할 때 자주 쓰임

```text
sds s = sdsempty();
s = sdscatfmt(s, "echo2_%S", arg);
```

## sdsMakeRoomFor

append 중 공간이 부족하면 호출됨. 버퍼를 확장하는 책임

- 이미 충분한 여유 공간 (`sdsavail()`) 이 있으면 그대로 반환
- 부족하면 현재 길이 + 추가 길이로 새 크기 계산
- **greedy** 모드에서는 재할당 횟수를 줄이려고 필요한 것보다 크게 잡음
- header 타입이 그대로 유지되면 `realloc`, 타입이 바뀌면 새로 할당하고 복사

> [!NOTE] 반환값을 반드시 다시 받아야 함
>
> `sdsMakeRoomFor()` 는 sds 포인터가 바뀔 수 있음 (realloc 이나 새 할당). 호출자는 반드시 반환값을 받아야 함
>
> ```text
> s = sdsMakeRoomFor(s, n);   // 반환값 받기 (필수)
> sdsMakeRoomFor(s, n);       // 위험. 옛 포인터가 dangling 일 수 있음
> ```

`sdscatlen()`, `sdscatfmt()` 등 append 계열은 모두 내부에서 이 흐름을 탐. 그래서 결과를 받아 다시 대입하는 패턴이 일반적

```text
s = sdscatfmt(s, "%S_%S", a, b);
s = sdscatlen(s, "...", 3);
```

여기까지가 sds 시리즈. 응답 버퍼에 sds 를 어떻게 넘기는지는 [[topics/valkey/explanation/addreply/00-overview/index|addreply 시리즈]] 에서 다룸
