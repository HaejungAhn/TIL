2022-

---
β πΒ μΆμ² : [Opaque Type μ΄ν΄λ³΄κΈ°](https://jcsoohwancho.github.io/2019-08-24-Opaque-Type-%EC%82%B4%ED%8E%B4%EB%B3%B4%EA%B8%B0/)

[https://velog.io/@wekdnjs/Opaque-Type](https://velog.io/@wekdnjs/Opaque-Type)

μ§μ§ μ΄ν΄μκ°λ€ γγ

SwiftUIλ₯Ό λ³΄λ©΄ κ³μ° νλ‘νΌν°μ νμμ `some`μ΄λΌλ ν€μλκ° λΆλ κ²μ λ³Ό μ μμ.

iOS 13λΆν° μ μ©λλ©°, swift 5.1μ μΆκ°λμμ.

opaque : λΆν¬λͺν

κ΅¬μ²΄μ μΈ νμμ μ¨κΈ°κ³ , ν΄λΉ νμμ΄ μ±ννκ³  μλ νλ‘ν μ½μ κ΄μ μμ ν¨μμ λ°ν κ°μ΄λ νλ‘νΌν°λ₯Ό μ¬μ©νκ² ν΄μ€λ€.

β opaque typeμ μ¬μ©νλ©΄ νΉμ  νλ‘ν μ½μ μ±νν νμμ μ¬μ©ν  μ μλ€? νλ‘ν μ½μ΄ μ½κ° νμμ μ­ν μ νλ κ² κ°μλ°.

```swift
import SwiftUI

// Viewλ protocolμ.
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
print(type(of: smallTriangle)) //νμ -> Triangle
print(smallTriangle.draw())

struct FlippedShape<T: Shape>: Shape {
    var shape: T
    func draw() -> String {
        let lines = shape.draw().split(separator: "\n")
        return lines.reversed().joined(separator: "\n")
    }
}
let flippedTriangle = FlippedShape(shape: smallTriangle)
print(type(of: flippedTriangle)) //νμ -> FlippedShape<Triangle> <- genericμ νμμ΄ λΈμΆλ¨.
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

> Exposing detailed information about the creation of a shape allows types that arenβt meant to be part of the ASCII art moduleβs public interface to leak out because of the need to state the full return type.

Shapeμ μμ±κ³Ό κ΄λ ¨ν λνμΌν μ λ³΄λ₯Ό λΈμΆνλ κ²μ ASCII art λͺ¨λ(μ μμ μ λͺ¨λ)μ κ³΅κ° μΈν°νμ΄μ€κ° μλ νμμ΄ λμΆλλ κ²μ νμ©νκ² λλ κ²μ΄λ€. μλνλ©΄ μ λ€λ¦­μ μ¬μ©νμ λ μ μ²΄ λ¦¬ν΄νμ μ νμ΄ λͺμλκΈ° λλ¬Έμ΄λ€.
> 

- Opaque typeμ generic typeμ μ  λ°λλΌκ³  μ΄ν΄ν  μ μλ€.

generic type : ν¨μλ₯Ό νΈμΆνλ μͺ½μ΄ λ§€κ°λ³μμ λ¦¬ν΄κ°μ νμμ μ νλ€.

μλλ Generic typeμ μ¬μ©ν ν¨μμ μμμ΄λ€. μ΄λ₯Ό νΈμΆνλ μͺ½μμλ xμ yμ λ€μ΄κ°λ νμ(Comparableμ μ±ννλ€λ©΄ λ¬΄μμ΄λ  λ£μ μ μμ)μ μ ν  μ μλ€. ν¨μ bodyλ μ΄λ€ κ°μ΄ λ€μ΄μλ μ²λ¦¬ν  μ μλλ‘ λ²μ©μ μΌλ‘ κ΅¬μ±λμ΄μμ κ²μ΄λ€.

```swift
func max<T>(_ x: T, _ y: T) -> T where T: Comparable { ... }
```

Opaque return typeμμλ μ΄κ² μ λ°λλ€. ν¨μμ κ΅¬νλΆμμ κ·Έ ν¨μκ° λ¦¬ν΄νκ³ μ νλ νμμ μ§μ ν  μ μλ€.