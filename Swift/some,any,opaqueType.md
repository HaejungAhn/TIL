📚 참고 아티클

- [What is the “some” keyword in Swift?](https://www.donnywals.com/what-is-the-some-keyword-in-swift/)
- [Swift Type Eraser Wrapper 패턴 이해하기 중 ‘Self 제약이 있는 Protocol’](https://blog.burt.pe.kr/posts/skyfe79-blog.contents-1118038013-post-44/#self-%EC%A0%9C%EC%95%BD%EC%9D%B4-%EC%9E%88%EB%8A%94-protocol)
- [What is the “any” keyword in Swift?](https://www.donnywals.com/what-is-the-any-keyword-in-swift/)
- [Swift 5.6 ) Introduces existential any](https://zeddios.tistory.com/1348)
- [What’s the difference between any and some in Siwft 5.7?](https://www.donnywals.com/whats-the-difference-between-any-and-some-in-swift-5-7/)

---

한줄 요약
- some은 연관타입이나 Self requirement를 가진 protocol을 리턴 타입으로써 사용하기 위해 문법적으로 가정을 하는 경우에 사용되어질 수 있다. some을 이용하면 리턴타입이 모두 동일한 연관타입과 Self requirement를 충족했음을 가정하게 되는 것이다.

- any는 existential type을 명시적으로 드러내도록 하는 문법적 장치인데, 이를 통해 개발자들이 개발을 할 때 한번 더 (다른 방법을) 생각할 수 있도록 유도하기 위해 만들어졌다. 왜냐하면 existential type(프로토콜이 타입으로 사용될 때를 이르는 용어)을 이용하면 사전에 필요한 메모리 양을 알 수 없고 dynamic dispatch를 해야하기 때문에 상대적으로 성능이 안좋은데도 불구하고 (제네릭&concrete를 사용하는 것에 비해) 타이핑하기 쉽고 문법적으로 이런 비용이 드러나지 않아 자주 사용되기 때문이다.
---

some, any에 대해 알아보기에 앞서 opaque result type에 대해 알아야 한다.

# opaque result type

프로토콜을 사용해서 인터페이스나 object에 대한 contract를 정의할 수 있다. 그리고 이를 통해 서로 다른 concrete type이라도 하나의 배열안에 넣는게 가능하다. 

```swift
protocol ListItemDisplayble {
	var name: String { get }
}

struct Shoe: ListItemDisplayble {
	let name: String
}

struct Shorts: ListItemDisplayble {
	let name: String
}

var mixedList: [ListItemDisplayble] = [Shoe(name: "신발"), Shorts(name: "쇼츠")]
```

위에 있는 코드를 조금 더 개선해보면 아래와 같다. `ListItemDisplayable` 을 연관타입으로 활용하여 ListDataSource를 만들었다.

```swift
protocol ListDataSource {
  associatedtype ListItem: ListItemDisplayable

  var items: [ListItem] { get }
  var numberOfItems: Int { get }
  func itemAt(_ index: Int) -> ListItem
}
```

이제 `ListDataSource` 를 채택한 `ShoeDataSource` 를 만들고 이를 활용할 수 있게됐다. 
(코드는 생략하겠음. 연관타입에 Shoe가 들어감)

이제 `ListDataSource`를 채택한 concrete type의 datasource를 만들 수 있는 `ViewModelGenerator`를 만들어보자.

```swift
struct ViewModelGenerator {
  func listProvider(for items: [Shoe]) -> ListDataSource {
    return ShoesDataSource(items: items)
  }
}
```

위 코드는 컴파일 에러가 난다. **왜냐하면 ListDataSource는 `associatedtype`이라는 제약을 가진 프로토콜이기 때문**이다.

이를 수정하기 위해 리턴타입을 ShoesDataSource로 변경할 수도 있지만, 이는 ViewModelGenerator를 사용하는 클라이언트에게 구현에 대한 세부사항을 노출하고 싶지 않은 우리의 의도와 반대되는 것이다. 불투명한(opaque) result type이 필요한 이유가 바로 이것이다.

# some

이 문제를 해결하기 위해 아래와 같이 코드를 재작성할 수 있다.

```swift
struct ViewModelGenerator {
  func listProvider(for items: [Shoe]) -> **some** ListDataSource {
    return ShoesDataSource(items: items)
  }
}
```

`some` 키워드를 result type 앞에 추가해줌을써 클라이언트에게 구체적인 타입은 숨기면서도 아래와 같은 것들을 강제할 수 있다. (컴파일러가 아래와 같은 것들을 허용한 것)

- ListDataSource를 채택한 something을 리턴할 수 있다.
- 리턴되는 object의 연관타입은 ListDataSource에 의해 설정된 모든 requirements와 일치한다.
- listProvider(for:) 메소드를 호출하면 항상 동일한 타입을 리턴한다.

**Opaque result types and Self requirements**

🤚 ”Self requirements”란? (참고 : [여기](https://blog.burt.pe.kr/posts/skyfe79-blog.contents-1118038013-post-44/#self-%EC%A0%9C%EC%95%BD%EA%B3%BC-%EC%97%B0%EA%B4%80-%ED%83%80%EC%9E%85%EC%9D%B4-%EC%97%86%EB%8A%94-protocol))

*— 위 링크에서는 프로토콜 메소드의 매개변수로 `Self`를 사용했을 때 컴파일 에러가 발생한다고 언급되어 있으나 Playground에서 테스트 시 큰 이상은 없었다. 또한 Standard library에 포함된 Equatable도 매개변수로 Self를 넣은 것을 보니 이렇게 사용한다고 해서 컴파일 타임 에러가 발생하는 것은 아닌 것 같다.*

*— `Self`를 사용하고 있는 상태에서 result type으로써 해당 프로토콜을 지정하려고 할 때, 혹은 메소드 매개변수의 타입으로써 사용하려고 할 때 컴파일 에러가 뜨며 any 키워드를 사용하라고 제안하고 있다.*

쉽게 말하면, protocol 선언 시 Self 키워드를 사용하는 경우를 말한다. Equatable을 예로 들자면 아래와 같다.

```swift
/// `Equatable` protocol has a `Self` requirement.
public protocol Equatable {
	static func == (lhs: **Self**, rhs: **Self**) -> Bool
}
```

Equatable을 채택한 프로토콜을 result type으로 사용하면 컴파일 에러가 뜬다.

```swift
protocol ListItemDisplayable: Equatable {
  var name: String { get }
}

func createAnItem() -> ListItemDisplayable {
  return Shoe(name: "a comparable shoe: \(UUID().uuidString)")
}
```

프로토콜 그 자체로는 타입에 대한 정보를 담고 있지 않기 때문에(런타임 시 concrete type이 결정되기 때문에) 이러한 에러가 발생하는 것이다(로 이해함)

이런 경우 createAnItem() 메소드의 리턴 타입 앞에 some을 붙여줌으로써 컴파일 에러를 해결할 수 있다.

****Opaque return types as reverse generics****

opaque result type이 있기 전에, 연관타입을 가진 프로토콜을 리턴 타입으로써 사용할 수 있는 유일한 방법은 해당 메소드에 generic 제약을 거는 것 뿐이었다. 이 방법은 단점은 함수 스스로 리턴타입을 결정하는 것이 아닌, 메소드의 caller가 이 타입을 정해야 한다는 것이다.

```swift
protocol ListDataSource {
  associatedtype ListItem: ListItemDisplayable
	// .. 이하 생략 ..
}

// opaque result type을 사용하지 않은 경우
// 리턴 타입으로 ListDataSource를 사용하기 위해 제네릭 제약을 사용함. -> 클라이언트 단에서 구체적인 타입 지정 필수
func createViewModel<T: ListDataSource>(for list: [T.ListItem]) -> **T** {
  return T.init(items: list)
}

// opaque result type을 사용
// 리턴 타입이 이미 ListDataSource로 지정됨. 파라미터에 제약을 걸기 위해 제네릭 제약을 사용함. -> 클라이언트 단에서 타입 지정 하지 않아도 ListDataSource 리턴됨.
func createOpaqueViewModel<T: ListItemDisplayable>(for list: [T]) -> **some ListDataSource** {
  return GenericViewModel<T>(items: list)
}

// 클라이언트단 코드
let shoes: GenericViewModel<Shoe> = createViewModel(for: shoeList)
let opaqueShoes = createOpaqueViewModel(for: shoeList)
```

# any

some과 사용되는 곳이 유사하다고 느낄것이다. 둘다 protocol 이름 앞에 붙고, 프로토콜이 사용되는 방식에 대한 것이기 때문이다. 하지만 깊게 파다보면 some과 any가 많이 다르다는 것을 알 수 있다.

**Intended use for `any` keyword**

```swift
protocol Networking {
    func fetchPosts() async throws -> [Post]
    // ...
}

struct PostsDataSource {
    let networking: **any** Networking
    // ...
}
```

****Understanding what an existential is in Swift****

some은 프로토콜을 return teyp으로 가지는 함수에서, 모든 객체가 동일한 concrete type을 가질 것으로 기대하면서 프로토콜의 연관타입 또는 Self requirement를 다소 무시하거나 제거하는 코드를 작성하도록 허용해주지만, any 키워드는 단순히 주어진 유형이 소위 “실존적(existential)”이라는 주석을 추가한다. (😵‍💫)

> While `some` allows us to write code that more or less ignores, or discards, a protocol’s associated type and/or `Self` requirement while expecting that every returned object in a function that returns `some Protocol` has the same concrete type, the `any` keyword simply annotates that a given type is a so-called existential.
> 

실존적이라는 것에 대해 잘 모를수도 있겠지만 어딘가에서 봤을 것이다. 예를 들어서 위에서 봤던 PostsDataSource는 old한 방식으로 아래와 같이 작성되어질 수 있다. (any 키워드가 없는 형태)

```swift
struct PostsDataSource {
    let networking: Networking
    // ...
}
```

우리가 사용하는 Networking 객체는 실존적(existensial)이다. 이 말은 즉, let networking은 Networking을 채택한 object라는 의미다.

이렇게 정의된 상태에서 컴파일러는 networking이라는 object가 어떤 객체가 될지, 그 객체의 유형이 무엇인지 모른다. 컴파일러가 아는 것은 PostsDataSource가 이니셜라이저될 때 networking에 할당될 객체, 즉 임의의(any)객체가 있으며 Networking을 채택하고 있다는 것 뿐이다.

컴파일 타임에는 Networking을 채택한 어떤 객체를 넣는 박스를 가지고 있으며, 그 박스 안에 무엇이 들어있는지는 런타임에 알 수 있다.

따라서 상대적으로 컴파일 시점이나 런타임에 얼마만큼의 메모리가 필요할지 알 수 없고 dynamic dispatch를 이용해 메소드를 호출해야한다는 특징이 있다.

---

이해가 안돼서 추가적으로 [한글로 된 블로그](https://zeddios.tistory.com/1348)를 찾아봄.

> [Swift Language Guide - Protocols as Types](https://docs.swift.org/swift-book/LanguageGuide/Protocols.html)
Using a protocol as a type is sometimes called an *existential type*, which comes from the phrase “there exists a type *T* such that *T* conforms to the protocol”.
> 

existential은 실존적이라는 의미이고, Protocol이 타입으로 사용이 되면 이를 `existential type`이라고 부른다.

블로그에서도 언급되어 있는데 existential type을 사용하면 제네릭&concrete type을 사용한 것과 달리 dynamic dispatch가 발생하고 있는 비용이 많이 드는 작업이다. 그럼에도 불구하고 minimal spelling(코드 작성량이 적음), 코드상으로 existential type에 대한 비용이 전혀 드러나지 않고 있기 때문에 개발자들은 주로 existental type을 훨씬 더 많이 사용한다.

Swift 5.6에서는 existential type과 any를 같이 사용할 수 있지만, Swift 6부터는 에러를 낼 계획이라고…(😱)

프로토콜이 아닌 타입에 any를 붙이면 에러가 발생한다. Any, AnyObject에는 안붙여줘도 되지만 붙여줘도 에러는 발생하지 않는다고 한다. 참고할 것.

---

이 글을 쓴 필자가 proposal을 읽고 나서 깨달은 것은, Swift 개발팀이 가능하다면 existentials 대신 제네릭과 concrete type을 사용하길 바란다는 것이었다. (원래 여러줄이 있었는데 중요한 부분만 가져옴)

> The cost of using existential types should not be hidden, and programmers should explicitly opt into these semantics.
> 

제네릭 & concrete type을 이용해 PostsDataSource를 정의해보자 !

```swift
protocol Networking {
    func fetchPosts() async throws -> [Post]
    // ...
}

struct PostsDataSource<Network: Networking> {
    let networking: Network
    // ...
}
```

위와 같이 만들어주면 PostsDataSource를 생성할 때 Networking을 채택한 concrete type을 넣어주게 되고, 그럼 컴파일러는 statically dispatch & 메모리 공간을 사전에 확보할 수 있다.

위 방식의 대안이 될 수 있는 것이 some을 이용한 것이다. `let networking: some Networking` 을 사용해주는 것이다. 또는 `let networking: any Networking` 을 사용한다.(any를 붙여줌으로써 existential type으로써의 프로토콜을 그대로 사용할 수 있다.)

어쨌든 proposal에서 언급된 동기로 인해 any가 만들어졌다. any는 어떠한 기능도 가지고 있지 않다. 개발자들에게 분명하게 전달하도록 강요한다. “this is an existential”(그러니 한번 더 생각해봐라)

****The any keyword in Swift 5.7****

Swift 5.7에서 Self requirement가 있는 프로토콜을 연관타입으로 사용할 때 발생하는 제약이 완화되었다. 

5.7부터는 `var body: some View` 에서 some이 빠지게 됐음.

# Swift 5.7에서 any와 some의 차이

우선 receivePizza() 메소드를 구현할 때 아래 두 코드의 차이에 대해 이해하고 있어야 한다.

```swift
protocol Pizza {
    var size: Int { get }
    var name: String { get }
}

/// 타입으로써 Pizza 프로토콜을 사용
func receivePizza(_ pizza: Pizza) {
    print("Omnomnom, that's a nice \(pizza.name)")
}

/// T의 제약조건으로써 Pizza 프로토콜을 사용
func receivePizza<T: Pizza>(_ pizza: T) {
    print("Omnomnom, that's a nice \(pizza.name)")
}
```