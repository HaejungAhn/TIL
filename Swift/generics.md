2022-02-05

---
📒  generic : 포괄적인

왜 프로토콜은 제네릭 파라미터를 사용할 수 없지?

제네릭 타입에 여러개가 들어가면?

class, enumeration에 제네릭 적용하면? 

**Generic을 사용하면 모든 타입에서 동작할 수 있는 유연하고 재사용가능한 함수와 타입을 작성**할 수 있다. 

Swift 표준 라이브러리 대부분은 제네릭 코드로 되어 있다. 대표적인게 Array와 Dictionary인데 별도로 추가작업을 하지 않더라도 서로 다른 타입의 Array나 Dictionary를 만들 수 있기 때문이다. (Int로 구성된 Array를 만들수도 있지만 String으로 구성된 Array를 만들수도 있음)

**제네릭은 왜 만들어지게 됐을까?**

특정 타입의 매개변수를 받는 메소드를 작성한다고 가정해보자. 내부적으로 동일한 기능을 하지만, 매개변수의 타입이 다르면 들어오는 매개변수의 타입만큼 메소드를 만들어줘야 한다. 

이렇게 작업을 하면 반복적인 코드가 생기고 코드량이 많아진다는 단점이 있고, 나중에 요구사항 변경으로 인해 다른 타입이 매개변수로 들어와야 할 경우 새로운 메소드를 추가해야하는 번거로움이 있다.

이런 문제를 해결한 것이 바로 제네릭이다. 

추상적으로 표현함으로써 불필요하게 반복되는 코드를 없앨 수 있고 요구사항 변경으로 인한 추가 작업도 줄일 수 있다. 

**제네릭 함수와 Type parameter**

제네릭 함수와 그렇지 않은 함수의 차이점 : 제네릭 함수는 함수명 뒤에 `<T>`가 온다. 

 `T` 에 해당하는 타입은 함수가 호출되는 시점에 결정된다. 

```swift
func swapTwoInts(_ a: inout Int, _ b: inout Int)
func swapTwoValues<T>(_ a: inout T, _ b: inout T)
```

여기서 `T` 는 “Type parameter”다. 

```swift
// Dictionary의 Key, Value / Array의 Element 타입 파라미터임.
// 파라미터 간에 유의미한 관계가 있을 경우 이 관계를 표현할 수 있는 네이밍을 해주면 되지만,
// 만약 의미있는 관계가 없을 경우 그냥 T, U, V와 같이 단일 대문자로 이름을 지정해주는게 일반적이다. 
Dictionary<Key, Value> 
Array<Element>          
```

**제네릭 타입**

모든 타입에서 동작할 수 있는 사용자 정의 클래스, 구조체, 열거형.

만약 제네릭을 사용하지 않고 커스텀 structure인 Stack을 구현한다고 가정한다면 아래와 같은 코드가 만들어진다.

items 변수에는 Int밖에 들어갈 수 없기 때문에 String이나 Float 등 Int가 아닌 다른 타입에 대해서도 Stack을 구현하고 싶다면 각각 만들어줘야 한다. 내부 동작이 모두 동일함에도 불구하고 변수에 사용되는 타입 하나 때문에 불필요한 반복 코드가 늘어나고 유연하게 대응하기 어려운 코드가 만들어지는 것이다.

```swift
struct IntStack {
    var items: [Int] = []
    mutating func push(_ item: Int) {
        items.append(item)
    }
    mutating func pop() -> Int {
        return items.removeLast()
    }
}
```

제네릭 타입으로 structure을 만들면 아래와 같이 만들 수 있다. 하나의 structure로 여러 데이터 타입에 대한 stack을 구현할 수 있게 됐다. 

제네릭 함수와 마찬가지로 structure명(혹은 class, enumeration) 뒤에 타입 파라미터 `<Element>`를 추가하여 제네릭 타입임을 명시한다.

```swift
struct Stack<Element> {
    var items: [Element] = []
    mutating func push(_ item: Element) {
        items.append(item)
    }
    mutating func pop() -> Element {
        return items.removeLast()
    }
}

// 아래와 같이 이름 뒤에 <타입>을 추가하여 사용한다.
var stackOfStrings = Stack<String>()
stackOfStrings.push("uno")
stackOfStrings.push("dos")
stackOfStrings.push("tres")
stackOfStrings.push("cuatro")
// the stack now contains 4 strings
```

**제네릭 타입 확장(Extending a Generic Type)**

`extension` 을 이용해 제네릭 타입을 확장할 때는 이름 뒤에 타입 파라미터 `<T>` 를 붙이지 않는다. 이렇게 하더라도 `extension` 의 바디에서 타입 파라미터를 활용할 수 있다.

**타입 제약(Type Constraint)과 타입 제약 구문(Syntax)**

가끔 제네릭 함수와 제네릭 타입에 사용될 수 있는 타입에 제약이 필요한 경우가 있다. 이걸 “Type Constraint”라고 부르는데 타입 파라미터가 특정 클래스를 상속하거나 특정 프로토콜 또는 프로토콜 구성을 준수해야함을 지정하는 것이다.

예를 들어 Dictionary 타입은 <Key, Value>의 타입 프로퍼티 중 “Key”에 들어올 수 있는 타입이 hashable 이어야 한다는 제한을 두고 있다. 만약 이 제약이 없다면 특정 Key에 대한 값을 삽입해야하는지, 대체해야하는지 알 수 없으며 Key에 대응하는 Value를 찾을 수도 없다.

제네릭 함수 또는 제네릭 타입의 기능을 구현함에 있어 특정 조건이 필요한 경우라면, 타입 제약을 통해 이를 제한할 수 있는 것이다.

타입 제약 구문은 아래와 같다. 

```swift
// T: SomeClass => T가 SomeClass이거나 SomeClas의 하위클래스여야 한다.
// U: SomeProtocol => U가 SomeProtocol을 준수해야한다.
func someFunction<T: SomeClass, U: SomeProtocol>(someT: T, someU: U) {
    // function body goes here
}
```

**타입 제약의 예시**

아래 함수는 컴파일 시 에러가 발생하는데 그 이유는 if value == valueToFind 부분 때문이다.

```swift
func findIndex<T>(of valueToFind: T, in array:[T]) -> Int? {
    for (index, value) in array.enumerated() {
        if value == valueToFind {
            return index
        }
    }
    return nil
}
```

Swift의 모든 타입이 동등 연산자(==)로 비교가능한 것은 아니다. 왜냐하면 복잡한 데이터 모델을 표현하기 위한 class, structure의 경우 "같음"의 기준을 Swift가 추론할 수 있는 건 아니기 때문이다. 

예를 들어 "Music"이라는 구조체를 만들고 차트를 구성한다고 가정했을 때,
차트의 목적에 따라 발행년도가 동일한 것을 "같음"이라고 볼수도 있고, 음악의 장르를 "같음"의 기준으로 잡을 수도 있다.

즉 class, struct가 만들어지고 활용되는 목적에 따라 "같음"의 기준이 상이할 수 있는 것이다.
그렇기 때문에 Swift는 이를 추론할 수 없다. 

사용자 정의 class나 structure에서 동등/비동등 연산자 사용이 필요한 경우 `Equatable` 프로토콜을 채택해야 한다.

즉 findIndex 함수를 사용하기 위해서는 “`Equatable` 프로토콜을 반드시 채택해야한다.”라는 제약이 생기는 것이다.

따라서 `findIndex<T>` 를 `findIndex<T: Equatable>` 로 변경하여 제약을 추가해야한다.

**연관된 타입(Associated Types)**

— 👀 [[ swift-evolution ] Replace `typealias` keyword with `associatedtype` for associated type declarations](https://github.com/apple/swift-evolution/blob/f55963fb0abd77aae643882ca3fc8939b1f969f2/proposals/0011-replace-typealias-associated.md)

클래스, struct, enum과 달리 protocol은 제네릭 파라미터를 허용하지 않는다. ( protocol은 인스턴스화할 수 없어서 그런건가? )

하지만 프로토콜에서도 제네릭이 필요한 경우가 있을 것이다. 이때 사용하는게 Associated Types이며, `associatedtype`키워드와 함께 정의된다. 실제 타입은 프로토콜이 채택되기 전까지 지정되지 않는다.

```swift
// 빌드시 아래 에러 발생
// error: protocols do not allow generic parameters; use associated types instead
//protocol Container<T> {
//		var item: T
//    func append(_ item: T)
//    var count: Int { get }
//    subscript(i: Int) -> T { get }
//}

// Use Associated Types
protocol Container {
    associatedtype Item
    mutating func append(_ item: Item)
    var count: Int { get }
    subscript(i: Int) -> Item { get }
}
```

프로토콜을 채택하고 구현하는 시점에 associatedtype Item의 타입을 지정한다.(아래 볼드처리된 부분)

만약 프로토콜의 모든 요구사항이 준수된 경우, 그래서 어떤 타입을 사용하는지 명확한 경우라면 Swift가 Item의 타입을 추론할 수 있기 때문에 `typealias Item = Int` 를 지워도 동작에 아무런 이상이 없다.

```swift
struct IntStack: Container {
    // original IntStack implementation
    var items: [Int] = []
    mutating func push(_ item: Int) {
        items.append(item)
    }
    mutating func pop() -> Int {
        return items.removeLast()
    }
    // conformance to the Container protocol
    **typealias Item = Int**
    mutating func append(_ item: Int) {
        self.push(item)
    }
    var count: Int {
        return items.count
    }
    subscript(i: Int) -> Int {
        return items[i]
    }
}
```

기존 타입을 확장하는 경우에도 프로토콜의 조건을 모두 충족하는 경우라면 associated type이 자동으로 추론될 수 있다.

associated type에도 제약을 추가할 수 있으며, 이는 제네릭 타입에서의 타입 제약과 유사하다.

만약 아래와 같이 `associatedtype Item: Equatable` 연관 타입에 제약이 추가된 경우라면 이 프로토콜을 채택하는 Item 타입은 Equatable도 같이 충족해야 한다.

```swift
protocol Container {
    associatedtype Item: Equatable
    mutating func append(_ item: Item)
    var count: Int { get }
    subscript(i: Int) -> Item { get }
}
```

**Using a Protocol in Its Associated Type’s Constraints**

- 아래 프로토콜 `SuffixableContainer` 는 `Container` 프로토콜을 채택(채택인가 상속인가?)한 것을 확인할 수 있다. 

- 아래 정의된 associatedtype을 보면 2개의 제약을 가진 것을 확인할 수 있다. 
  `associatedtype Suffix: SuffixableContainer where Suffix.Item == Item`
  ① 자기 자신을 제약으로 걸었음 → SuffixableContainer 프로토콜을 준수해야만 한다. 

  ② `Item`의 타입은 Container의 `Item` 타입과 동일해야한다. ( 즉 Item으로 들어오는 타입은 SuffixableContainer 프로토콜도 채택하면서 Container 프로토콜도 채택해야함)

```swift
protocol SuffixableContainer: Container {
    associatedtype Suffix: SuffixableContainer where Suffix.Item == Item
    func suffix(_ size: Int) -> Suffix
}

// Stack은 이미 Container를 채택한 상태임.
extension Stack: SuffixableContainer {
    func suffix(_ size: Int) -> Stack {
        var result = Stack()
        for index in (count-size)..<count {
            result.append(self[index])
        }
        return result
    }
    // Inferred that Suffix is Stack.
}
var stackOfInts = Stack<Int>()
stackOfInts.append(10)
stackOfInts.append(20)
stackOfInts.append(30)
let suffix = stackOfInts.suffix(2)
// suffix contains 20 and 30
```

**제네릭 Where caluse와 extension에서의 where 사용**

제네릭 Where clause란 ? 

타입 제약 이외에도 `where`를 활용해 연관된 타입이 특정 프로토콜을 준수해야 하거나, 특정 타입 파라미터와 동일해야 한다는 조건을 걸 수 있다.

어떤 형식인가?

제네릭 where 절은 `where` 뒤에 연관된 타입이 다른 연관된 타입 또는 타입 파라미터와의 동등 관계(==)에 대한 제약이 따라온다. 

어떤 위치에 올 수 있는가?

제네릭 타입 또는 제네릭 함수의 body가 시작되기 직전에 where 절을 작성한다.

```swift
func allItemsMatch<C1: Container, C2: Container>
    (_ someContainer: C1, _ anotherContainer: C2) -> Bool
    **where C1.Item == C2.Item, C1.Item: Equatable** **{**

        // Check that both containers contain the same number of items.
        if someContainer.count != anotherContainer.count {
            return false
        }

        // Check each pair of items to see if they're equivalent.
        for i in 0..<someContainer.count {
            if someContainer[i] != anotherContainer[i] {
                return false
            }
        }

        // All items match, so return true.
        return true
}
```

만약 기존 타입 또는 프로토콜의 extension에서 사용될 경우 아래와 같이 사용할 수도 있다.

```swift
extension Stack **where Element: Equatable** {
    func isTop(_ item: Element) -> Bool {
        guard let topItem = items.last else {
            return false
        }
        return topItem == item
    }
}
```

특정 타입을 요구하도록 제네릭 where절을 작성할 수도 있다. 콤마로 구분하여 여러개의 요구사항을 포함할 수도 있다.

```swift
extension Container where Item == Double {
	// some code write here...
}
```

제네릭 where절은 서브스크립트, associated type에서도 사용할 수 있다.

**상황별 Where 절**

조건에 따라 메서드의 추가가 가능하다.

아래 코드를 보면

`where` Item이 Int 타입일 경우 average() 메소드를 추가한다.

`where` Item이 Equatable을 채택한 경우 endsWith() 메소드를 추가한다.

```swift
extension Container {
    func average() -> Double where Item == Int {
        var sum = 0.0
        for index in 0..<count {
            sum += Double(self[index])
        }
        return sum / Double(count)
    }
    func endsWith(_ item: Item) -> Bool where Item: Equatable {
        return count >= 1 && self[count-1] == item
    }
}
let numbers = [1260, 1200, 98, 37]
print(numbers.average())
// Prints "648.75"
print(numbers.endsWith(37))
// Prints "true"
```