📚  참고 사이트

- [Swift Language Guide: Property Wrappers](https://docs.swift.org/swift-book/LanguageGuide/Properties.html#ID617)

---

**What is Property Wrappers?**

> A property wrapper adds a layer of separation between code that manages how a property is stored and the code that defines a property.

프로퍼티 래퍼는 프로퍼티가 저장되는 방법을 관리하는 코드와 프로퍼티를 정의하는 코드 사이를 분리하는 레이어를 추가한다.
> 

예를 들어 thread-safety 체크를 제공하는 프로퍼티 또는 자신이 가지고 있는 데이터를 database에 저장하는  프로퍼티를 가지고 있는 경우, 이런 기능을 제공하는 코드를 모든 프로퍼티에 작성해야 한다.

property wrapper를 사용하면 wrapper를 정의할 때 이런 관리성 코드를 한번만 작성해두고 여러 프로퍼티에 이를 적용함으로써 코드를 재사용할 수 있다.

**How can I make the Property Wrapper?**

`wrappedValue` 프로퍼티를 정의한 structure, enum, 클래스를 만들고 타입 선언부 상단에 `@propertyWrapper` 를 추가해주면 된다. 아래 코드를 참고.

```swift
@propertyWrapper
struct TwelveOrLess {
    private var number = 0
    var wrappedValue: Int {
        get { return number }
        set { number = min(newValue, 12) }
    }
}

// 위 래퍼를 사용하는 곳에서는...
/// TwelveOrLess에서 number가 0으로 초기값 설정이 되어 있기 때문에 struct 생성 시 
/// height, width에 아무런 값을 주지 않더라도 get을 하면 0이 나온다.
struct SmallRectangle {
    **@TwelveOrLess** var height: Int 
    **@TwelveOrLess** var width: Int
}
```

wrapper를 프로퍼티에 적용할 때, 컴파일러는 해당 프로퍼티의 코드를 다음과 같은 코드를 합성한다.

- wrapper에게 storage를 제공하는 코드
- wrapper를 통해 프로퍼티에 접근하는 코드

property wrapper가 wrapped value를 저장하는 책임을 가지고 있기 때문에 이를 위한 코드는 합성하지 않는다. 아래는 예시 코드이다.

```swift
struct SmallRectangle {
    private var _height = TwelveOrLess()
    private var _width = TwelveOrLess()
    var height: Int {
        get { return _height.wrappedValue }
        set { _height.wrappedValue = newValue }
    }
    var width: Int {
        get { return _width.wrappedValue }
        set { _width.wrappedValue = newValue }
    }
}
```

**Setting Initial Values for Wrapped Properties**

위에서 봤던 @TwelveOrLess 프로퍼티 래퍼를 사용하면 초기값을 0 이외의 다른 값으로 지정할 수 없다. 만약 초기값 지정이 필요한 경우 TwelveOrLess 프로퍼티 래퍼에 이니셜라이저를 추가해야 한다. 이니셜라이저 추가는 일반적인 클래스, struct, enum에서의 사용과 동일하다. 
(자세한 코드는 [링크](https://docs.swift.org/swift-book/LanguageGuide/Properties.html#ID617)의 “Setting Initial Values for Wrapped Properties” 참고)

이니셜라이저를 통해 아래와 같이 프로퍼티 래퍼의 초기값을 할당할 수 있다.

```swift
/// 프로퍼티에 초기값 할당하는 것과 동일하게 사용 가능
/// 이렇게 할당해주면 init(wrappedValue:) 이니셜라이저를 사용하는 것과 동일하다.
struct UnitRectangle {
    @SmallNumber var height: Int = 1
    @SmallNumber var width: Int = 1
}

/// 아래와 같이 프로퍼티 래퍼에 argument을 추가함으로써 초기화할 수도 있다. 가장 일반적인 방법이다.
struct NarrowRectangle {
    @SmallNumber(wrappedValue: 2, maximum: 5) var height: Int
    @SmallNumber(wrappedValue: 3, maximum: 4) var width: Int
}

/// 아래와 같이 혼합된 방식도 사용 가능하다.
struct MixedRectangle {
    @SmallNumber var height: Int = 1
    @SmallNumber(maximum: 9) var width: Int = 2
}
```

이쯤에서 드는 궁금증 하나. wrappedValue를 매개변수로 가지는 이니셜라이저에서 parameter name은 항상 wrappedValue여야 하는가? ㄴㄴ 꼭 그럴 필요는 없다. 그냥 이니셜라이저의 특성이랑 똑같다고 생각하면 됨.

**Projecting a Value From a Property Wrapper**

property wrapper로부터 value 투영하기(?)

wrapped value 이외에도 프로퍼티 래퍼는 **projected value**를 정의함으로써 추가적인 기능을 노출시킬 수 있다. projected value의 이름은 wrapped value와 동일한데, 외부에서 접근할 때 `$` 사인으로 시작되어야 한다.

위에서 나온 예제를 이어서 계속 사용해보자. 

number 값이 maximum보다 큰 경우 property wrapper는 값을 저장하기 전에 이를 조정하게 된다. 아래 나오는 코드는 property wrapper가 새로운 값을 저장하기 전에 이를 수정했는지 아닌지를 나타내는 projectedValue 프로퍼티를 추가한 것이다.

```swift
@propertyWrapper
struct SmallNumber {
    private var number: Int
    private(set) var projectedValue: Bool

    var wrappedValue: Int {
        get { return number }
        set {
            if newValue > 12 {
                number = 12
                projectedValue = true
            } else {
                number = newValue
                projectedValue = false
            }
        }
    }

    init() {
        self.number = 0
        self.projectedValue = false
    }
}

struct SomeStructure {
    @SmallNumber var someNumber: Int
}
var someStructure = SomeStructure()

someStructure.someNumber = 4
print(someStructure.$someNumber) /// 프로퍼티 래퍼의 proejctedValue에 접근
// Prints "false"

someStructure.someNumber = 55
print(someStructure.$someNumber)
// Prints "true"
```

프로퍼티 래퍼는 자신의 proejctedValue로써 어떠한 타입의 값이든 리턴할 수 있다.

근데 이렇게 다른 값을 투영하게 되면 오히려 헷갈릴 수도 있을 것 같기도…?

실사례는 아래 코드를 참고

```swift
@available(iOS 13.0, macOS 10.15, tvOS 13.0, watchOS 6.0, *)
@frozen @propertyWrapper public struct State<Value> : DynamicProperty {

    public init(wrappedValue value: Value)

    public init(initialValue value: Value)

    public var wrappedValue: Value { get nonmutating set }

    public var projectedValue: Binding<Value> { get }
}

```