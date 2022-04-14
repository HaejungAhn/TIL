2022-03-05

---
Collection Type 공부할 때 Dictionary의 key, set 에 들어가는 데이터 타입은 반드시 Hashable 프로토콜을 구현해야 한다는 내용이 있어 한번 찾아봤던 적이 있음.

- 🤔 hashable - 자료구조(Hash Table) 할 때 다시 자세히 보자!
    
    - 프로토콜이다.
    
    - 정수 hash value를 생성하기 위해 Hasher에 의해 hash되어질 수 있는 타입.
    
    - 아니 도대체 hash value가 뭔데? ( 출처는 [이곳](https://applecider2020.tistory.com/14) )
    
       ✓ hash function을 이용하여 원본 데이터(객체)를 64bit의 Int 값으로 변환한 것. 즉, 데이터를 간단한 숫자로 변환한 것 
    
       ✓ 두개의 데이터를 비교할 때 데이터가 동일하다면 각 변수의 hash value도 동일하다.
    
       ✓ 단, 2개의 서로 다른 데이터가 동일한 hash value를 가질 수도 있다.
    
       ✓ 코드를 컴파일 및 실행할 때마다 모든 hash value는 변한다.
    
       ✓ Set에 저장될 타입, Dictionary의 Key는 반드시 Hashable 해야한다. 또한 Swift의 기본타입인 String, Int 등은 자동으로 Hashable 하다. 열거형이 associated value를 가지지 않으면 자동으로 Hashable 하다.
    
       ✓ Hash Value를 사용하는 이유 : Hash Value를 인덱스로 사용하여 값의 위치를 빠르게 찾을 수 있다. 즉, 값의 검색 속도가 빠르기 때문에 이를 사용한다. (O(1)의 시간복잡도를 가진다)
    
       ✓  [https://developer.apple.com/documentation/swift/hashable](https://developer.apple.com/documentation/swift/hashable)
    

기술면접 질문 준비하던 중 “Hashable은 왜 반드시 Equatable 프로토콜을 구현해야하는지 설명하시오.”라는 항목이 있어 찾아보게됨.

**결론 : Hashable이 Equatable을 상속받은 이유**

[Why does Hashable require Equatable?](https://forums.swift.org/t/why-does-hashable-require-equatable/16817/5)

이거 복기하다가 문득 든 생각이!  

Hash value는 정수형이고 정수는 이미 Equatable인데 왜 Hashable을 채택한 타입이 다시 Equatable을 구현해야하는 거지? 라는 생각이 들었다. 그리고 **Hash collision**이 생각남.

Equatable은 동등비교연산자를 사용할 수 있도록 만들어주는 프로토콜이고, Hashable은 데이터를 고정된 사이즈의 정수값으로 구성된 hash값을 만들 수 있는 프로토콜이다. 컬렉션 타입의 Set과 Dictionary key에 들어갈 타입은 Hashable을 채택해야한다. 아무래도 데이터를 고정된 사이즈로 만들다보니 같은 데이터가 아님에도 동일한 Hash 값이 만들어지는 문제가 발생하는데 이것을 **hash 충돌**이라고 한다. hash 충돌을 해결하는 방법 중 하나는 separate chaning을 적용하는 것인데 이는 hash 값으로 이루어진 bucket 안에 해당 hash값을 가진 데이터들을 연결해두는 방식이다. 이 안에서 정확한 데이터를 찾으려면 동등비교연산자가 필요하기 때문에 Hashable이 Equatable을 상속받도록 처리해둔 것이 아닐까 한다.

~~Hashable은 Hash 값을 만들기 위해 필요한 프로토콜이다. Hash 값이 필요한 이유를 생각해보면 Equatable이 상속된 이유를 추측해볼 수 있다.~~

~~Hash 값은 왜 필요할까? 문서에서도 언급되어있지만 Set의 데이터 타입으로, Dictionary key의 타입으로 사용하기 위해 필요하며 둘다 searching과 관련되어 있다. 데이터의 추가도 searching 후 추가할지 말지를 결정할 수 있고, 삭제도 마찬가지다. 포함여부를 비교할 때도 searching이 필요하다.~~ 

~~Set은 순서가 없는 동일타입의 데이터를 담을 수 있는 컬렉션이다. 여기서 검색을 하려면? 데이터 자체를 비교해야 한다. 데이터가 정수 하나, string 하나라면 상관이 없지만 많은 프로퍼티와 메소드가 포함된 class나 struct라면 비교가 어려울 수 있다. 이 복잡한 데이터를 하나의 정수값으로 만들어 search하기 쉽도록 만드는게 hash value다.~~ 

~~(실은 string 뿐만 아니라 숫자형 타입, boolean 까지 Swift의 standard library 대부분이 Hashable 프로토콜을 구현한 상태다)~~

[https://jcsoohwancho.github.io/2019-10-27-Equatable,-Comparable,-Hashable/](https://jcsoohwancho.github.io/2019-10-27-Equatable,-Comparable,-Hashable/)

# `Equatable`

- 채택하고 구현하면 ==, != 연산자를 사용할 수 있음.
- 필수 구현 메소드

```swift
static func == (lhs: <#Type#>, rhs: <#Type#>) -> Bool

필수 메소드의 자동구현 조건
✓ struct의 모든 저장프로퍼티가 Equatable을 채택할 경우
✓ enum의 경우 연관값이 없거나 모든 연관값의 타입이 Equatable을 채택한 경우

== 연산의 조건
[Reflexivity]  a == a는 항상 참이여야 한다.
[Symmetry]     a == b와 b == a가 항상 참이어야 한다.
[Transitivity] a == b이고 b == c이면 a == c 여야한다.
```

# `Comparable`

- 채택하고 구현하면 부등호(<, >, <=, =>)를 이용한 비교 연산 가능
- 필수 구현 메소드 : Equatable을 상속받기 때문에 == 구현 필요하며 < 연산 구현 필요

```swift
static func < (lhs: <#Type#>, rhs: <#Type#>) -> Bool

< 연산의 조건
[Irreflexivity] a < a 이면 항상 거짓이어야 한다.
[Asymmetry]     a < b 이면 b < a 는 항상 거짓이어야 한다.
[Transitivity]  a < b 이고 b < c 이면 a < c 여야한다.
```

# `Hashable`

- 채택하고 구현하면 Int 타입의 hash value를 만들어 낼 수 있다. Set과 Dictionary key 타입으로 사용 가능
- 필수 구현 메소드 : Equatable을 상속받기 때문에 == 구현 필요하며 hash() 구현 필요

```swift
struct GridPoint {
	var x: Int
	var y: Int
}

extension GridPoint: Hashable {
	static func == (lhs: GridPoint, rhs: GridPoint) -> Bool {
		return lhs.x == rhs.x && lhs.y == rhs.y
	}

	func **hash**(into hasher: inout Hasher) {
		**hasher.combine**(x)
		hasher.combine(y)
	}
}

필수 메소드의 자동구현 조건은 Equatable과 유사하다.
✓ struct의 모든 저장프로퍼티가 Hashable을 채택할 경우
✓ enum의 경우 연관값이 없거나 모든 연관값의 타입이 Hashable을 채택한 경우
```

<aside>
🤔 Hasher : struct, set과 dictionary에 사용되는 universial hash function

[`combine(_:)`](https://developer.apple.com/documentation/swift/hasher/2995578-combine)
주어진 값을 이용해 해시를 만듬.

</aside>