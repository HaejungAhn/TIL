2022-02-05

---
๐ย  generic : ํฌ๊ด์ ์ธ

์ ํ๋กํ ์ฝ์ ์ ๋ค๋ฆญ ํ๋ผ๋ฏธํฐ๋ฅผ ์ฌ์ฉํ  ์ ์์ง?

์ ๋ค๋ฆญ ํ์์ ์ฌ๋ฌ๊ฐ๊ฐ ๋ค์ด๊ฐ๋ฉด?

class, enumeration์ ์ ๋ค๋ฆญ ์ ์ฉํ๋ฉด? 

**Generic์ ์ฌ์ฉํ๋ฉด ๋ชจ๋  ํ์์์ ๋์ํ  ์ ์๋ ์ ์ฐํ๊ณ  ์ฌ์ฌ์ฉ๊ฐ๋ฅํ ํจ์์ ํ์์ ์์ฑ**ํ  ์ ์๋ค. 

Swift ํ์ค ๋ผ์ด๋ธ๋ฌ๋ฆฌ ๋๋ถ๋ถ์ ์ ๋ค๋ฆญ ์ฝ๋๋ก ๋์ด ์๋ค. ๋ํ์ ์ธ๊ฒ Array์ Dictionary์ธ๋ฐ ๋ณ๋๋ก ์ถ๊ฐ์์์ ํ์ง ์๋๋ผ๋ ์๋ก ๋ค๋ฅธ ํ์์ Array๋ Dictionary๋ฅผ ๋ง๋ค ์ ์๊ธฐ ๋๋ฌธ์ด๋ค. (Int๋ก ๊ตฌ์ฑ๋ Array๋ฅผ ๋ง๋ค์๋ ์์ง๋ง String์ผ๋ก ๊ตฌ์ฑ๋ Array๋ฅผ ๋ง๋ค์๋ ์์)

**์ ๋ค๋ฆญ์ ์ ๋ง๋ค์ด์ง๊ฒ ๋์๊น?**

ํน์  ํ์์ ๋งค๊ฐ๋ณ์๋ฅผ ๋ฐ๋ ๋ฉ์๋๋ฅผ ์์ฑํ๋ค๊ณ  ๊ฐ์ ํด๋ณด์. ๋ด๋ถ์ ์ผ๋ก ๋์ผํ ๊ธฐ๋ฅ์ ํ์ง๋ง, ๋งค๊ฐ๋ณ์์ ํ์์ด ๋ค๋ฅด๋ฉด ๋ค์ด์ค๋ ๋งค๊ฐ๋ณ์์ ํ์๋งํผ ๋ฉ์๋๋ฅผ ๋ง๋ค์ด์ค์ผ ํ๋ค. 

์ด๋ ๊ฒ ์์์ ํ๋ฉด ๋ฐ๋ณต์ ์ธ ์ฝ๋๊ฐ ์๊ธฐ๊ณ  ์ฝ๋๋์ด ๋ง์์ง๋ค๋ ๋จ์ ์ด ์๊ณ , ๋์ค์ ์๊ตฌ์ฌํญ ๋ณ๊ฒฝ์ผ๋ก ์ธํด ๋ค๋ฅธ ํ์์ด ๋งค๊ฐ๋ณ์๋ก ๋ค์ด์์ผ ํ  ๊ฒฝ์ฐ ์๋ก์ด ๋ฉ์๋๋ฅผ ์ถ๊ฐํด์ผํ๋ ๋ฒ๊ฑฐ๋ก์์ด ์๋ค.

์ด๋ฐ ๋ฌธ์ ๋ฅผ ํด๊ฒฐํ ๊ฒ์ด ๋ฐ๋ก ์ ๋ค๋ฆญ์ด๋ค. 

์ถ์์ ์ผ๋ก ํํํจ์ผ๋ก์จ ๋ถํ์ํ๊ฒ ๋ฐ๋ณต๋๋ ์ฝ๋๋ฅผ ์์จ ์ ์๊ณ  ์๊ตฌ์ฌํญ ๋ณ๊ฒฝ์ผ๋ก ์ธํ ์ถ๊ฐ ์์๋ ์ค์ผ ์ ์๋ค. 

**์ ๋ค๋ฆญ ํจ์์ Type parameter**

์ ๋ค๋ฆญ ํจ์์ ๊ทธ๋ ์ง ์์ ํจ์์ ์ฐจ์ด์  : ์ ๋ค๋ฆญ ํจ์๋ ํจ์๋ช ๋ค์ `<T>`๊ฐ ์จ๋ค. 

 `T` ์ ํด๋นํ๋ ํ์์ ํจ์๊ฐ ํธ์ถ๋๋ ์์ ์ ๊ฒฐ์ ๋๋ค. 

```swift
func swapTwoInts(_ a: inout Int, _ b: inout Int)
func swapTwoValues<T>(_ a: inout T, _ b: inout T)
```

์ฌ๊ธฐ์ `T` ๋ โType parameterโ๋ค. 

```swift
// Dictionary์ Key, Value / Array์ Element ํ์ ํ๋ผ๋ฏธํฐ์.
// ํ๋ผ๋ฏธํฐ ๊ฐ์ ์ ์๋ฏธํ ๊ด๊ณ๊ฐ ์์ ๊ฒฝ์ฐ ์ด ๊ด๊ณ๋ฅผ ํํํ  ์ ์๋ ๋ค์ด๋ฐ์ ํด์ฃผ๋ฉด ๋์ง๋ง,
// ๋ง์ฝ ์๋ฏธ์๋ ๊ด๊ณ๊ฐ ์์ ๊ฒฝ์ฐ ๊ทธ๋ฅ T, U, V์ ๊ฐ์ด ๋จ์ผ ๋๋ฌธ์๋ก ์ด๋ฆ์ ์ง์ ํด์ฃผ๋๊ฒ ์ผ๋ฐ์ ์ด๋ค. 
Dictionary<Key, Value> 
Array<Element>          
```

**์ ๋ค๋ฆญ ํ์**

๋ชจ๋  ํ์์์ ๋์ํ  ์ ์๋ ์ฌ์ฉ์ ์ ์ ํด๋์ค, ๊ตฌ์กฐ์ฒด, ์ด๊ฑฐํ.

๋ง์ฝ ์ ๋ค๋ฆญ์ ์ฌ์ฉํ์ง ์๊ณ  ์ปค์คํ structure์ธ Stack์ ๊ตฌํํ๋ค๊ณ  ๊ฐ์ ํ๋ค๋ฉด ์๋์ ๊ฐ์ ์ฝ๋๊ฐ ๋ง๋ค์ด์ง๋ค.

items ๋ณ์์๋ Int๋ฐ์ ๋ค์ด๊ฐ ์ ์๊ธฐ ๋๋ฌธ์ String์ด๋ Float ๋ฑ Int๊ฐ ์๋ ๋ค๋ฅธ ํ์์ ๋ํด์๋ Stack์ ๊ตฌํํ๊ณ  ์ถ๋ค๋ฉด ๊ฐ๊ฐ ๋ง๋ค์ด์ค์ผ ํ๋ค. ๋ด๋ถ ๋์์ด ๋ชจ๋ ๋์ผํจ์๋ ๋ถ๊ตฌํ๊ณ  ๋ณ์์ ์ฌ์ฉ๋๋ ํ์ ํ๋ ๋๋ฌธ์ ๋ถํ์ํ ๋ฐ๋ณต ์ฝ๋๊ฐ ๋์ด๋๊ณ  ์ ์ฐํ๊ฒ ๋์ํ๊ธฐ ์ด๋ ค์ด ์ฝ๋๊ฐ ๋ง๋ค์ด์ง๋ ๊ฒ์ด๋ค.

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

์ ๋ค๋ฆญ ํ์์ผ๋ก structure์ ๋ง๋ค๋ฉด ์๋์ ๊ฐ์ด ๋ง๋ค ์ ์๋ค. ํ๋์ structure๋ก ์ฌ๋ฌ ๋ฐ์ดํฐ ํ์์ ๋ํ stack์ ๊ตฌํํ  ์ ์๊ฒ ๋๋ค. 

์ ๋ค๋ฆญ ํจ์์ ๋ง์ฐฌ๊ฐ์ง๋ก structure๋ช(ํน์ class, enumeration) ๋ค์ ํ์ ํ๋ผ๋ฏธํฐ `<Element>`๋ฅผ ์ถ๊ฐํ์ฌ ์ ๋ค๋ฆญ ํ์์์ ๋ช์ํ๋ค.

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

// ์๋์ ๊ฐ์ด ์ด๋ฆ ๋ค์ <ํ์>์ ์ถ๊ฐํ์ฌ ์ฌ์ฉํ๋ค.
var stackOfStrings = Stack<String>()
stackOfStrings.push("uno")
stackOfStrings.push("dos")
stackOfStrings.push("tres")
stackOfStrings.push("cuatro")
// the stack now contains 4 strings
```

**์ ๋ค๋ฆญ ํ์ ํ์ฅ(Extending a Generic Type)**

`extension` ์ ์ด์ฉํด ์ ๋ค๋ฆญ ํ์์ ํ์ฅํ  ๋๋ ์ด๋ฆ ๋ค์ ํ์ ํ๋ผ๋ฏธํฐ `<T>` ๋ฅผ ๋ถ์ด์ง ์๋๋ค. ์ด๋ ๊ฒ ํ๋๋ผ๋ `extension` ์ ๋ฐ๋์์ ํ์ ํ๋ผ๋ฏธํฐ๋ฅผ ํ์ฉํ  ์ ์๋ค.

**ํ์ ์ ์ฝ(Type Constraint)๊ณผ ํ์ ์ ์ฝ ๊ตฌ๋ฌธ(Syntax)**

๊ฐ๋ ์ ๋ค๋ฆญ ํจ์์ ์ ๋ค๋ฆญ ํ์์ ์ฌ์ฉ๋  ์ ์๋ ํ์์ ์ ์ฝ์ด ํ์ํ ๊ฒฝ์ฐ๊ฐ ์๋ค. ์ด๊ฑธ โType Constraintโ๋ผ๊ณ  ๋ถ๋ฅด๋๋ฐ ํ์ ํ๋ผ๋ฏธํฐ๊ฐ ํน์  ํด๋์ค๋ฅผ ์์ํ๊ฑฐ๋ ํน์  ํ๋กํ ์ฝ ๋๋ ํ๋กํ ์ฝ ๊ตฌ์ฑ์ ์ค์ํด์ผํจ์ ์ง์ ํ๋ ๊ฒ์ด๋ค.

์๋ฅผ ๋ค์ด Dictionary ํ์์ <Key, Value>์ ํ์ ํ๋กํผํฐ ์ค โKeyโ์ ๋ค์ด์ฌ ์ ์๋ ํ์์ด hashable ์ด์ด์ผ ํ๋ค๋ ์ ํ์ ๋๊ณ  ์๋ค. ๋ง์ฝ ์ด ์ ์ฝ์ด ์๋ค๋ฉด ํน์  Key์ ๋ํ ๊ฐ์ ์ฝ์ํด์ผํ๋์ง, ๋์ฒดํด์ผํ๋์ง ์ ์ ์์ผ๋ฉฐ Key์ ๋์ํ๋ Value๋ฅผ ์ฐพ์ ์๋ ์๋ค.

์ ๋ค๋ฆญ ํจ์ ๋๋ ์ ๋ค๋ฆญ ํ์์ ๊ธฐ๋ฅ์ ๊ตฌํํจ์ ์์ด ํน์  ์กฐ๊ฑด์ด ํ์ํ ๊ฒฝ์ฐ๋ผ๋ฉด, ํ์ ์ ์ฝ์ ํตํด ์ด๋ฅผ ์ ํํ  ์ ์๋ ๊ฒ์ด๋ค.

ํ์ ์ ์ฝ ๊ตฌ๋ฌธ์ ์๋์ ๊ฐ๋ค. 

```swift
// T: SomeClass => T๊ฐ SomeClass์ด๊ฑฐ๋ SomeClas์ ํ์ํด๋์ค์ฌ์ผ ํ๋ค.
// U: SomeProtocol => U๊ฐ SomeProtocol์ ์ค์ํด์ผํ๋ค.
func someFunction<T: SomeClass, U: SomeProtocol>(someT: T, someU: U) {
    // function body goes here
}
```

**ํ์ ์ ์ฝ์ ์์**

์๋ ํจ์๋ ์ปดํ์ผ ์ ์๋ฌ๊ฐ ๋ฐ์ํ๋๋ฐ ๊ทธ ์ด์ ๋ if value == valueToFind ๋ถ๋ถ ๋๋ฌธ์ด๋ค.

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

Swift์ ๋ชจ๋  ํ์์ด ๋๋ฑ ์ฐ์ฐ์(==)๋ก ๋น๊ต๊ฐ๋ฅํ ๊ฒ์ ์๋๋ค. ์๋ํ๋ฉด ๋ณต์กํ ๋ฐ์ดํฐ ๋ชจ๋ธ์ ํํํ๊ธฐ ์ํ class, structure์ ๊ฒฝ์ฐ "๊ฐ์"์ ๊ธฐ์ค์ Swift๊ฐ ์ถ๋ก ํ  ์ ์๋ ๊ฑด ์๋๊ธฐ ๋๋ฌธ์ด๋ค. 

์๋ฅผ ๋ค์ด "Music"์ด๋ผ๋ ๊ตฌ์กฐ์ฒด๋ฅผ ๋ง๋ค๊ณ  ์ฐจํธ๋ฅผ ๊ตฌ์ฑํ๋ค๊ณ  ๊ฐ์ ํ์ ๋,
์ฐจํธ์ ๋ชฉ์ ์ ๋ฐ๋ผ ๋ฐํ๋๋๊ฐ ๋์ผํ ๊ฒ์ "๊ฐ์"์ด๋ผ๊ณ  ๋ณผ์๋ ์๊ณ , ์์์ ์ฅ๋ฅด๋ฅผ "๊ฐ์"์ ๊ธฐ์ค์ผ๋ก ์ก์ ์๋ ์๋ค.

์ฆ class, struct๊ฐ ๋ง๋ค์ด์ง๊ณ  ํ์ฉ๋๋ ๋ชฉ์ ์ ๋ฐ๋ผ "๊ฐ์"์ ๊ธฐ์ค์ด ์์ดํ  ์ ์๋ ๊ฒ์ด๋ค.
๊ทธ๋ ๊ธฐ ๋๋ฌธ์ Swift๋ ์ด๋ฅผ ์ถ๋ก ํ  ์ ์๋ค. 

์ฌ์ฉ์ ์ ์ class๋ structure์์ ๋๋ฑ/๋น๋๋ฑ ์ฐ์ฐ์ ์ฌ์ฉ์ด ํ์ํ ๊ฒฝ์ฐ `Equatable` ํ๋กํ ์ฝ์ ์ฑํํด์ผ ํ๋ค.

์ฆ findIndex ํจ์๋ฅผ ์ฌ์ฉํ๊ธฐ ์ํด์๋ โ`Equatable` ํ๋กํ ์ฝ์ ๋ฐ๋์ ์ฑํํด์ผํ๋ค.โ๋ผ๋ ์ ์ฝ์ด ์๊ธฐ๋ ๊ฒ์ด๋ค.

๋ฐ๋ผ์ `findIndex<T>` ๋ฅผ `findIndex<T: Equatable>` ๋ก ๋ณ๊ฒฝํ์ฌ ์ ์ฝ์ ์ถ๊ฐํด์ผํ๋ค.

**์ฐ๊ด๋ ํ์(Associated Types)**

โ ๐ย [[ swift-evolution ] Replace `typealias` keyword with `associatedtype` for associated type declarations](https://github.com/apple/swift-evolution/blob/f55963fb0abd77aae643882ca3fc8939b1f969f2/proposals/0011-replace-typealias-associated.md)

ํด๋์ค, struct, enum๊ณผ ๋ฌ๋ฆฌ protocol์ ์ ๋ค๋ฆญ ํ๋ผ๋ฏธํฐ๋ฅผ ํ์ฉํ์ง ์๋๋ค. ( protocol์ ์ธ์คํด์คํํ  ์ ์์ด์ ๊ทธ๋ฐ๊ฑด๊ฐ? )

ํ์ง๋ง ํ๋กํ ์ฝ์์๋ ์ ๋ค๋ฆญ์ด ํ์ํ ๊ฒฝ์ฐ๊ฐ ์์ ๊ฒ์ด๋ค. ์ด๋ ์ฌ์ฉํ๋๊ฒ Associated Types์ด๋ฉฐ, `associatedtype`ํค์๋์ ํจ๊ป ์ ์๋๋ค. ์ค์  ํ์์ ํ๋กํ ์ฝ์ด ์ฑํ๋๊ธฐ ์ ๊น์ง ์ง์ ๋์ง ์๋๋ค.

```swift
// ๋น๋์ ์๋ ์๋ฌ ๋ฐ์
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

ํ๋กํ ์ฝ์ ์ฑํํ๊ณ  ๊ตฌํํ๋ ์์ ์ associatedtype Item์ ํ์์ ์ง์ ํ๋ค.(์๋ ๋ณผ๋์ฒ๋ฆฌ๋ ๋ถ๋ถ)

๋ง์ฝ ํ๋กํ ์ฝ์ ๋ชจ๋  ์๊ตฌ์ฌํญ์ด ์ค์๋ ๊ฒฝ์ฐ, ๊ทธ๋์ ์ด๋ค ํ์์ ์ฌ์ฉํ๋์ง ๋ชํํ ๊ฒฝ์ฐ๋ผ๋ฉด Swift๊ฐ Item์ ํ์์ ์ถ๋ก ํ  ์ ์๊ธฐ ๋๋ฌธ์ `typealias Item = Int` ๋ฅผ ์ง์๋ ๋์์ ์๋ฌด๋ฐ ์ด์์ด ์๋ค.

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

๊ธฐ์กด ํ์์ ํ์ฅํ๋ ๊ฒฝ์ฐ์๋ ํ๋กํ ์ฝ์ ์กฐ๊ฑด์ ๋ชจ๋ ์ถฉ์กฑํ๋ ๊ฒฝ์ฐ๋ผ๋ฉด associated type์ด ์๋์ผ๋ก ์ถ๋ก ๋  ์ ์๋ค.

associated type์๋ ์ ์ฝ์ ์ถ๊ฐํ  ์ ์์ผ๋ฉฐ, ์ด๋ ์ ๋ค๋ฆญ ํ์์์์ ํ์ ์ ์ฝ๊ณผ ์ ์ฌํ๋ค.

๋ง์ฝ ์๋์ ๊ฐ์ด `associatedtype Item: Equatable` ์ฐ๊ด ํ์์ ์ ์ฝ์ด ์ถ๊ฐ๋ ๊ฒฝ์ฐ๋ผ๋ฉด ์ด ํ๋กํ ์ฝ์ ์ฑํํ๋ Item ํ์์ Equatable๋ ๊ฐ์ด ์ถฉ์กฑํด์ผ ํ๋ค.

```swift
protocol Container {
    associatedtype Item: Equatable
    mutating func append(_ item: Item)
    var count: Int { get }
    subscript(i: Int) -> Item { get }
}
```

**Using a Protocol in Its Associated Typeโs Constraints**

- ์๋ ํ๋กํ ์ฝ `SuffixableContainer` ๋ `Container` ํ๋กํ ์ฝ์ ์ฑํ(์ฑํ์ธ๊ฐ ์์์ธ๊ฐ?)ํ ๊ฒ์ ํ์ธํ  ์ ์๋ค. 

- ์๋ ์ ์๋ associatedtype์ ๋ณด๋ฉด 2๊ฐ์ ์ ์ฝ์ ๊ฐ์ง ๊ฒ์ ํ์ธํ  ์ ์๋ค. 
  `associatedtype Suffix: SuffixableContainer where Suffix.Item == Item`
  โ  ์๊ธฐ ์์ ์ ์ ์ฝ์ผ๋ก ๊ฑธ์์ โ SuffixableContainer ํ๋กํ ์ฝ์ ์ค์ํด์ผ๋ง ํ๋ค. 

  โก `Item`์ ํ์์ Container์ `Item` ํ์๊ณผ ๋์ผํด์ผํ๋ค. ( ์ฆ Item์ผ๋ก ๋ค์ด์ค๋ ํ์์ SuffixableContainer ํ๋กํ ์ฝ๋ ์ฑํํ๋ฉด์ Container ํ๋กํ ์ฝ๋ ์ฑํํด์ผํจ)

```swift
protocol SuffixableContainer: Container {
    associatedtype Suffix: SuffixableContainer where Suffix.Item == Item
    func suffix(_ size: Int) -> Suffix
}

// Stack์ ์ด๋ฏธ Container๋ฅผ ์ฑํํ ์ํ์.
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

**์ ๋ค๋ฆญ Where caluse์ extension์์์ where ์ฌ์ฉ**

์ ๋ค๋ฆญ Where clause๋ ? 

ํ์ ์ ์ฝ ์ด์ธ์๋ `where`๋ฅผ ํ์ฉํด ์ฐ๊ด๋ ํ์์ด ํน์  ํ๋กํ ์ฝ์ ์ค์ํด์ผ ํ๊ฑฐ๋, ํน์  ํ์ ํ๋ผ๋ฏธํฐ์ ๋์ผํด์ผ ํ๋ค๋ ์กฐ๊ฑด์ ๊ฑธ ์ ์๋ค.

์ด๋ค ํ์์ธ๊ฐ?

์ ๋ค๋ฆญ where ์ ์ `where` ๋ค์ ์ฐ๊ด๋ ํ์์ด ๋ค๋ฅธ ์ฐ๊ด๋ ํ์ ๋๋ ํ์ ํ๋ผ๋ฏธํฐ์์ ๋๋ฑ ๊ด๊ณ(==)์ ๋ํ ์ ์ฝ์ด ๋ฐ๋ผ์จ๋ค. 

์ด๋ค ์์น์ ์ฌ ์ ์๋๊ฐ?

์ ๋ค๋ฆญ ํ์ ๋๋ ์ ๋ค๋ฆญ ํจ์์ body๊ฐ ์์๋๊ธฐ ์ง์ ์ where ์ ์ ์์ฑํ๋ค.

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

๋ง์ฝ ๊ธฐ์กด ํ์ ๋๋ ํ๋กํ ์ฝ์ extension์์ ์ฌ์ฉ๋  ๊ฒฝ์ฐ ์๋์ ๊ฐ์ด ์ฌ์ฉํ  ์๋ ์๋ค.

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

ํน์  ํ์์ ์๊ตฌํ๋๋ก ์ ๋ค๋ฆญ where์ ์ ์์ฑํ  ์๋ ์๋ค. ์ฝค๋ง๋ก ๊ตฌ๋ถํ์ฌ ์ฌ๋ฌ๊ฐ์ ์๊ตฌ์ฌํญ์ ํฌํจํ  ์๋ ์๋ค.

```swift
extension Container where Item == Double {
	// some code write here...
}
```

์ ๋ค๋ฆญ where์ ์ ์๋ธ์คํฌ๋ฆฝํธ, associated type์์๋ ์ฌ์ฉํ  ์ ์๋ค.

**์ํฉ๋ณ Where ์ **

์กฐ๊ฑด์ ๋ฐ๋ผ ๋ฉ์๋์ ์ถ๊ฐ๊ฐ ๊ฐ๋ฅํ๋ค.

์๋ ์ฝ๋๋ฅผ ๋ณด๋ฉด

`where` Item์ด Int ํ์์ผ ๊ฒฝ์ฐ average() ๋ฉ์๋๋ฅผ ์ถ๊ฐํ๋ค.

`where` Item์ด Equatable์ ์ฑํํ ๊ฒฝ์ฐ endsWith() ๋ฉ์๋๋ฅผ ์ถ๊ฐํ๋ค.

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