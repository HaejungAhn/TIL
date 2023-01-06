**Ref.**

- `GIT REPO` https://github.com/apple/swift-collections
- `DOC` [Introducing Swift Collections](https://www.swift.org/blog/swift-collections/)

---

- 현재 Swift Standard Library에는 general 한 목적의 3가지 데이터 structure가 있다 : Array, Set, Dictionary
- 문제를 효율적으로 풀기 위해 & 불변성(invariant)을 유지하기 위해 Swift Collections 오픈소스 패키지가 추가되었다.
- Swift Collections 패키지의 최초 버전에는 가장 빈번하게 사용되는 세가지 data structure로 시작됐다.
    - double-ended queue(a.k.a “dequeue”)
    - ordered set
    - ordered dictionary

### Deque

- 배열과 비슷하다 : 순서가 있고, 랜덤 엑세스 가능하고, 변경가능하며(mutable), 인덱스를 통해 범위를 지정해서 값 변경이 가능하다.
- Deque의 가장 주요한 이점(Array와 비교했을 때)
    - 처음과 마지막에 위치한 데이터를 삭제하거나 삽입할 때 효율성을 제공한다. (근데 마지막에 삽입, 삭제하는 것은 문제 없지 않나?)
    - 따라서 FIFO queue를 만들 때 유용하다. → 편리한 Operation을 제공함.
- Deque의 operation
    - `prepend()`  `append()`
    - `popFirst()`  `popLast()`
- [MutableColleciton](https://developer.apple.com/documentation/swift/mutablecollection) 및 [RangeReplaceableCollection](https://developer.apple.com/documentation/swift/rangereplaceablecollection), RandomAccessCollectino을 구현하고 관련 메소드를 사용하여 컬렉션 요소에 액세스하고 변경할 수도 있다.(Array와 동일한 indexing sementic을 가지고 있음)
- Standard library의 모든 가변길이 collection과 같이, Deque은 value sementics을 구현한다. (값타입이라는 뜻임)
    - COW 최적화로 구현되어 졌다.
- Deque은 자신이 소유한 element들을 circular buffer(collection의 양 끝에서의 효율적인 삽입/삭제를 허용하는 것)에 저장한다.
    - 그러나 이것은 잠재적으로 연속적이지 않은 저장으로 인한 비용을 초래한다.
    - 대조적으로 Array는 (보통) 연속적인 buffer(새로운 데이터가 효율적으로 가장 마지막에 추가되는)에 데이터가 저장되기 때문에 앞쪽에 데이터를 삽입하는 것은 상대적으로 느리다.(공간을 만들기 위해 기존 요소를 이동시켜야 하기 때문)
- 맨 앞에 데이터를 삽입하기 위해 deque은 자신의 element들을 인접한 버퍼에 저장(maintaining)하는 것을 포기해야 했다. → 이로 인해 앞쪽에 element를 삽입/삭제할 필요가 없는 경우 Array보다 조금 느리게 작동하는 경향이 있다. (**앞쪽에 삽입/삭제가 반드시 필요한 경우라면 Deque을 쓰는게 유용하지만 그렇지 않다면 element들을 인접 버퍼에 저장할 수 없기 때문에 굳이 사용할 필요가 없다.**)
- Array와 달리 deque은 현재 underlying storage에 대한 direct unsafe access를 지원하지 않는다. 또한 capacity 프로퍼티도 없다. (reserveCapacity 메소드는 제공함)

### OrderedSet

- Array와 Set의 강력한 hybrid 형태다. (순서가 있는 Set) - Set이 가지고 있는 단점을 생각해보면 됨. 그걸 보완한 것.
- Hashable 프로토콜을 채택한 것이라면 무엇이든 Ordered Set의 element 타입으로 사용할 수 있다.
    - Hashable 프로토콜은 ~~한 프로토콜인데,
    - Hashable 프로토콜을 채택해야 하는 이유는 Set이 데이터를 저장하는 방식과 관련이 있음.
- 벤치마크 그래프를 보면 orderedSet에 element를 추가하는 것은 기존 Set에 비해 약간의 시간이 더 소요되는 것을 알 수 있다. (왜냐하면 순서때문에 그런 것 같음)
- Array와 같이 OrderedSet은 사용자가 지정한 순서로 element를 관리하고 효율적인 랜덤액세스를 제공한다.
    - 인덱스를 이용해서 제공함. (ex. someOrderedSets[0])
- Set과 같이 OrderedSet을 구성하는 elements들은 모두 unique하다. 그리고 효율적으로 element를 searching할 수 있다.(Array는 순서대로 하나씩 이동하면서 특정 element를 찾아야함)
    - 이걸 문서에서는 Membership testing이라고 하네

→ **각각의 element가 유니크해야하거나 또는 어떤 값이 collection의 member라는 것을 빠르게 결정할 필요가 있을 때 사용하면 좋다.**

- 두개의 ordered set이 동일한 순서로 동일한 element를 가지고 있다면 equality하다고 본다. (Array와 동일한 특성)
- OrderedSet은 element 저장에 standard한 array value(최소한의 overhead로 추출되어질 수 있는)를 사용한다.
    
    → ?뭔말이야.(`OrderedSet` uses a standard array value for element storage, which can be extracted with minimal overhead.)
    
    배열에 사용되는 element 타입을 그대로 사용하기 때문에 뭔가 이점이 있는듯?
    
    - Array만 받는 함수를 사용할 경우 이건 굉장한 option이다. 왜냐하면 별도의 converting 작업 없이 someOrderedSet`.element` 로 Array 받는 함수를 사용할 수 있기 때문이다.
- [SetAlgebra](https://developer.apple.com/documentation/swift/setalgebra) 채택이 필수적인 경우 OrderedSet은 elements의 효율적인 unordered(정렬되지 않은) view를 제공한다.
    - someOrderedSet`.unordered`
- OrderedSet은 RangeReplaceableCollection, MutableCollction과 SetAlgebra의 대부분의 기능을 구현하고 있다.
    - 이는 멤버 배열(효율적인 랜덤액세스 순회를 위한)과 해당 배열에 대한 인덱스의 해시테이블(효율적인 Membership test를 위한)을 유지함으로 달성된다.
    - 해시 테이블 내부에 저장된 인덱스는 스탠다드 Int보다 적은 비트로 인코딩 되는 경우가 많기 때문에 OrderedSet은 스탠다는 Set보다 종종 적은 메모리를 사용하는 경우가 있을 수 있다.

### OrderedDictoinary

- element의 순서가 중요하거나 다양한 위치에 있는 element에 효율적으로 접근해야할 필요가 있을 때 Dictionary보다는 OrderedDictionary를 사용하는게 좋다.
- 스탠다드 Dictionary와 마찬가지로 key 타입은 Hashtable 프로토콜을 채택한 것이면 무엇이든 상관 없다.
    - 스탠다드 Dictionary와 차이가 있다면, key-based와 더불어 index-based subscript를 제공한다는 것이다.
- 삽입된 순으로 순서가 유지된다.
- 마치 Array처럼 숫자형 인덱스를 사용해서 접근할 수 있다.
    
    ```swift
    responses[0] // nil (key-based subscript)
    responses.**elements**[0] // (200, "OK") (index-based subscript)
    ```
    
    - 모호함을 피하기 위해 OrderedDictionary는 Collection을 직접적으로 채택하고 있지 않다. 대신에 key-value 짝들에 대한 랜덤액세스 view를 제공한다.
- MutableCollection과 RangeReplaceableCollection의 일부 기능을 구현했지만 멤버 고유성(uniqueness)에 대한 요구사항으로 인해 완전하게 구현하지는 못한다.
- OrderedDictionary의 경우 key는 OrderedSet으로 구성되어 있고, 연관된 값은 일반 Array로 구성된다.