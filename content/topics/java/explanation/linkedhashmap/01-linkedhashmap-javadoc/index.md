---
aliases:
  - 01. LinkedHashMap javadoc
tags:
  - explanation
  - linkedhashmap
  - java
references:
  - https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/util/LinkedHashMap.html
---
# 01. LinkedHashMap javadoc

> [!quote]
>
> Hash table and linked list implementation of the `Map` interface, with predictable iteration order. 

**LinkedHashMap** = **hash table** + linked list
- **Map** 인터페이스의 구현체
- predictable **iteration order**를 가짐

> [!quote]
> 
> This implementation differs from `HashMap` in that it maintains a doubly-linked list running through all of its entries. This linked list defines the iteration ordering, which is normally the order in which keys were inserted into the map (_insertion-order_).

*LinkedHashMap*은 **HashMap**과 차이점을 가짐
- 내부적으로 **doubly-linked list**를 관리함
	- *doubly-linked list*는 *iteration order*을 결정함
		- *iteration order*의 기본값은 **insertion-order** ^insertion-order-default

> [!quote]
> 
> Note that insertion order is not affected if a key is _re-inserted_ into the map. (A key `k` is reinserted into a map `m` if `m.put(k, v)` is invoked when `m.containsKey(k)` would return `true` immediately prior to the invocation.)

**re-insert**은 이미 있는 key에 다시 `put()`하는 개념
- *re-insert*한다고 *insertion-order*는 바뀌지 않음

> [!quote]
> 
> This implementation spares its clients from the unspecified, generally chaotic ordering provided by `HashMap` (and `Hashtable`), without incurring the increased cost associated with `TreeMap`. 

"spares someone from X": 누군가 X를 안 겪어도 되게 해줌

*LinkedHashMap*은 다음의 장점을 가짐
- *HashMap*을 사용하면 겪는 chaotic ordering을 겪지 않게 도와줌
- **TreeMap**처럼 정렬 비용을 추가로 내지도 않음

> [!quote]
>
> It can be used to produce a copy of a map that has the same order as the original, regardless of the original map's implementation:
>
> ```java
> void foo(Map m) {
>     Map copy = new LinkedHashMap(m);
>     ...
> }
> ```
>
> This technique is particularly useful if a module takes a map on input, copies it, and later returns results whose order is determined by that of the copy. (Clients generally appreciate having things returned in the same order they were presented.)

*LinkedHashMap*은 순서가 필요한 *Map* 구현체의 복사본을 만들 때 유용함
- 입력받은 *Map*의 순서를 보존해서 결과를 돌려주고 싶을 때 사용 가능
	- 여기서 입력받은 *Map*의 순서는 `entrySet()`의 순회 결과
		- 따라서 입력받은 *Map*이 *HashMap*이면 원본이 순서가 정의되지 않으니, 복사본 결과도 순서가 정의되지 않음

> [!quote]
> 
> A special `LinkedHashMap(int,float,boolean)` constructor is provided to create a linked hash map whose order of iteration is the order in which its entries were last accessed, from least-recently accessed to most-recently (_access-order_). 

*LinkedHashMap*은 기본적으로 *insertion-order*
- [[#^insertion-order-default]]

**access-order**로 *iteration order*을 바꿀 수 있는 기능을 생성자로 제공

> [!quote]
> 
> This kind of map is well-suited to building LRU caches. Invoking the `put`, `putIfAbsent`, `get`, `getOrDefault`, `compute`, `computeIfAbsent`, `computeIfPresent`, or `merge` methods results in an access to the corresponding entry (assuming it exists after the invocation completes). 

*access-order*는 **LRU caches**를 구현할 때 유용함
- 인용에 나열된 여러 메소드들은 *access-order*을 갱신함

> [!quote]
>
> The `replace` methods only result in an access of the entry if the value is replaced. The `putAll` method generates one entry access for each mapping in the specified map, in the order that key-value mappings are provided by the specified map's entry set iterator. _No other methods generate entry accesses._ In particular, operations on collection-views do _not_ affect the order of iteration of the backing map.

`replace`계열 메소드는 entry의 value가 바뀐 경우 entry access로 간주

 `putAll` 메소드는 인자로 주어진 `map`의 iterator 가 순회하는 순서에 따라 한 번씩 entry access 발생

특히 **collection-view**에서 하는 작업은 backing map의 *iteration order*에 영향을 주지 않음
- *collection-view*의 예는 다음과 같음
	- `map.keySet()`
	- `map.values()`
	- `map.entrySet()`

> [!quote]
>
> The `removeEldestEntry(Map.Entry)` method may be overridden to impose a policy for removing stale mappings automatically when new mappings are added to the map.

`removeEldestEntry`는 오래된 mapping 제거 정책을 넣는 hook
- 이 메소드를 오버라이드해서 새 mapping이 추가될 때 자동 제거 기준을 만들 수 있음

> [!quote]
>
> This class provides all of the optional `Map` operations, and permits null elements. Like `HashMap`, it provides constant-time performance for the basic operations (`add`, `contains` and `remove`), assuming the hash function disperses elements properly among the buckets. 

기본 성능은 *HashMap*과 비슷하지만 linked list 유지 비용이 조금 있음
- *Map*의 연산 제공
	- null 허용
- 기본 작업은 hash가 잘 퍼진다는 전제에서 constant-time

> [!quote]
> 
> Performance is likely to be just slightly below that of `HashMap`, due to the added expense of maintaining the linked list, with one exception: Iteration over the collection-views of a `LinkedHashMap` requires time proportional to the _size_ of the map, regardless of its capacity. Iteration over a `HashMap` is likely to be more expensive, requiring time proportional to its _capacity_.

*HashMap*보다 성능이 낮을 수 있음
- linked list 유지 비용

*HashMap*보다 성능이 좋은 경우도 있음
- *LinkedHashMap*의 *collection-view*는 순회하는 데 *Map*의 ==size==에 비례함
	- ==capacity==와 무관함
- *HashMap*의 *collection-view*는 순회하는 데 *Map*의 capacity에 비례함
	- 내부적으로 **bucket** 배열 전체를 탐색하기 때문
	- entry 수(size)가 작아도 *bucket* 배열의 크기(capacity)가 크다면 성능이 떨어짐

참고로 "Iteration over a `HashMap`" 표현에서 "*collection-view*"가 생략됐다고 생각하면 됨
- *HashMap*은 `Iterable`을 구현하지 않기 때문

> [!quote]
>
> A linked hash map has two parameters that affect its performance: _initial capacity_ and _load factor_. They are defined precisely as for `HashMap`. Note, however, that the penalty for choosing an excessively high value for initial capacity is less severe for this class than for `HashMap`, as iteration times for this class are unaffected by capacity.

성능 파라미터는 *HashMap*처럼 2가지
- **initial capacity**
	- capacity를 크게 잡아도 순회 시간이 늘어나는 정도(penalty)가 *HashMap*보다 덜함
- **load factor**

> [!quote]
>
> **Note that this implementation is not synchronized.** If multiple threads access a linked hash map concurrently, and at least one of the threads modifies the map structurally, it _must_ be synchronized externally. This is typically accomplished by synchronizing on some object that naturally encapsulates the map.

*LinkedHashMap* 자체는 synchronized가 아님
- 여러 스레드가 동시에 접근하는 상황에서 하나의 스레드라도 **structural modification**을 하면 외부 동기화 필요
	- *structural modification*에 대한 설명은 [[#^structural-modification-definition|아래 설명]]

일반적인 외부 동기화 방식은 *Map*을 감싸는 객체 기준으로 `synchronized`를 거는 방식

> [!quote]
>
> If no such object exists, the map should be "wrapped" using the `Collections.synchronizedMap` method. This is best done at creation time, to prevent accidental unsynchronized access to the map:
>
> ```java
> Map m = Collections.synchronizedMap(new LinkedHashMap(...));
> ```

생성 시점에 `Collections.synchronizedMap`으로 동기화된 객체 생성 가능
- 생성 시점에 감싸야 실수로 비동기 접근하는 것을 줄일 수 있음

^structural-modification-definition
> [!quote]
>
> A structural modification is any operation that adds or deletes one or more mappings or, in the case of access-ordered linked hash maps, affects iteration order. 

*structural modification*는 다음 연산을 포함함
- 추가
- 삭제
- *access-order*일 때 *iteration order*을 바꾸는 작업

> [!quote]
> 
> In insertion-ordered linked hash maps, merely changing the value associated with a key that is already contained in the map is not a structural modification. **In access-ordered linked hash maps, merely querying the map with `get` is a structural modification.**)

*insertion-order*에서는 기존 key의 value를 변경하는 동작은 *structural modification*에 포함되지 않음

*access-order*에서는 `get()`으로 조회만 해도 *structural modification*에 포함됨

> [!quote]
>
> The iterators returned by the `iterator` method of the collections returned by all of this class's collection view methods are _fail-fast_: if the map is structurally modified at any time after the iterator is created, in any way except through the iterator's own `remove` method, the iterator will throw a `ConcurrentModificationException`. 

*collection-view* 메서드들이 반환하는 collection에서 얻은 iterator는 **fail-fast**
- iterator 생성 후 *structural modification*이 생기면 `ConcurrentModificationException`

> [!quote]
> 
> Thus, in the face of concurrent modification, the iterator fails quickly and cleanly, rather than risking arbitrary, non-deterministic behavior at an undetermined time in the future.

**concurrent modification** 상황에서 문제를 나중에 애매하게 터뜨리지 않고 빨리 드러내는 목적

> [!quote]
>
> Note that the fail-fast behavior of an iterator cannot be guaranteed as it is, generally speaking, impossible to make any hard guarantees in the presence of unsynchronized concurrent modification. Fail-fast iterators throw `ConcurrentModificationException` on a best-effort basis.

*fail-fast*는 보장되는 동작이 아니라 best-effort

> [!quote]
> Therefore, it would be wrong to write a program that depended on this exception for its correctness: _the fail-fast behavior of iterators should be used only to detect bugs._

*concurrent modification* 상황에서 항상 예외가 난다고 가정하면 안 됨
- `ConcurrentModificationException`을 이용해 기능을 구현하면 안 됨
- 버그 감지용으로만 사용할 것

> [!quote]
>
> The spliterators returned by the spliterator method of the collections returned by all of this class's collection view methods are _late-binding_, _fail-fast_, and additionally report `Spliterator.ORDERED`.

*collection-view* 메서드들이 반환하는 collection에서 얻은 spliterator도 순서 정보를 가짐
- **late-binding**
	- parallel stream에서 유용
- *fail-fast*
- `Spliterator.ORDERED`
	- 정해진 순서가 있다는 표시
