2022-

---
— 👀 출처 : [Opaque Type 살펴보기](https://jcsoohwancho.github.io/2019-08-24-Opaque-Type-%EC%82%B4%ED%8E%B4%EB%B3%B4%EA%B8%B0/)

[https://velog.io/@wekdnjs/Opaque-Type](https://velog.io/@wekdnjs/Opaque-Type)

진짜 이해안간다 ㅎㅎ

SwiftUI를 보면 계산 프로퍼티의 타입에 `some`이라는 키워드가 붙는 것을 볼 수 있음.

iOS 13부터 적용되며, swift 5.1에 추가되었음.

opaque : 불투명한

구체적인 타입을 숨기고, 해당 타입이 채택하고 있는 프로토콜의 관점에서 함수의 반환 값이나 프로퍼티를 사용하게 해준다.

→ opaque type을 사용하면 특정 프로토콜을 채택한 타입을 사용할 수 있다? 프로토콜이 약간 타입의 역할을 하는 것 같은데.

```swift
import SwiftUI

// View는 protocol임.
struct ContentView: View {
    var body: **some View** {
        Text("Hello, world!")
            .padding()
    }
}
```

```swift
protocol Shape {
    func draw() -> String
}

struct Triangle: Shape {
    var size: Int
    func draw() -> String {
        var result: [String] = []
        for length in 1...size {
            result.append(String(repeating: "*", count: length))
        }
        return result.joined(separator: "\n")
    }
}
let smallTriangle = Triangle(size: 3)
print(type(of: smallTriangle)) //타입 -> Triangle
print(smallTriangle.draw())

struct FlippedShape<T: Shape>: Shape {
    var shape: T
    func draw() -> String {
        let lines = shape.draw().split(separator: "\n")
        return lines.reversed().joined(separator: "\n")
    }
}
let flippedTriangle = FlippedShape(shape: smallTriangle)
print(type(of: flippedTriangle)) //타입 -> FlippedShape<Triangle> <- generic의 타입이 노출됨.
print(flippedTriangle.draw())

struct JoinedShape<T: Shape, U: Shape>: Shape {
    var top: T
    var bottom: U
    func draw() -> String {
        return top.draw() + "\n" + bottom.draw()
    }
}

let joinedTriangles = JoinedShape(top: smallTriangle, bottom: flippedTriangle) 
// JoinedShape<Triangle, FlippedShape<Triangle>>
print(type(of: joinedTriangles))
print(joinedTriangles.draw())
```

> Exposing detailed information about the creation of a shape allows types that aren’t meant to be part of the ASCII art module’s public interface to leak out because of the need to state the full return type.

Shape의 생성과 관련한 디테일한 정보를 노출하는 것은 ASCII art 모듈(위 예제의 모듈)의 공개 인터페이스가 아닌 타입이 누출되는 것을 허용하게 되는 것이다. 왜냐하면 제네릭을 사용했을 때 전체 리턴타입 유형이 명시되기 때문이다.
> 

- Opaque type은 generic type의 정 반대라고 이해할 수 있다.

generic type : 함수를 호출하는 쪽이 매개변수와 리턴값의 타입을 정한다.

아래는 Generic type을 사용한 함수의 예시이다. 이를 호출하는 쪽에서는 x와 y에 들어가는 타입(Comparable을 채택했다면 무엇이든 넣을 수 있음)을 정할 수 있다. 함수 body는 어떤 값이 들어와도 처리할 수 있도록 범용적으로 구성되어있을 것이다.

```swift
func max<T>(_ x: T, _ y: T) -> T where T: Comparable { ... }
```

Opaque return type에서는 이게 정반대다. 함수의 구현부에서 그 함수가 리턴하고자 하는 타입을 지정할 수 있다.