2022-02-16

---
열거형이란

어떤 주제와 관련된 다양한 경우의 수 중 코드에서 처리가능 한 케이스들을 하나로 묶어 그룹핑한 타입이다. 예를 들면 UIViewController에서 화면 전환과 관련된 `modalPresentationStyle` 이라는 프로퍼티는 열거형 타입의 프로퍼티로, 해당 속성에 들어갈 수 있는 값이 coverVerticla, popover, fullScreen 등으로 한정되어 있다. 또 다른 예는 UILabel의 `textAlignment` 속성으로 역시 열거형 타입이고 여기에 left, right, center 등의 한정된 값이 들어갈 수 있다. 

열거형을 사용했을 때의 가장 큰 장점은 코드의 가독성이다. 아무 의미도 없는 원시값에 의미를 담아 네이밍 할 수 있기 때문에 코드를 읽는데 많은 도움이 된다.

열거형의 각 케이스는 기본적으로 원시 값을 설정하지 않아도 되지만 필요에 따라 설정할 수 있다. 예를 들어 서버에서 들어오는 어떤 모델의 상태값이 숫자로 들어오는 경우라면 열거형 케이스별로 원시값을 설정해 활용할 수 있음.

원시값에는 String, Character, Number 타입이 들어올 수 있다. 

원시값을 설정하면 모든 케이스가 동일한 타입에 딱 하나의 값만을 가질 수 있다. 만약 케이스와 연관된 값의 타입이 다양하고 종류도 많을 경우 연관값을 활용할 수 있다. 연관값은 케이스 옆에 튜플로 연관있는 값을 정의하면 된다.

---

- Enumeration(열거형)이란

   *서로 관련있는 값들을 하나의 그룹으로 묶어 타입을 정의할 수 있다.* 

   *이 값들을 thread-safe한 방식(여러 스레드에서 접근해도 문제가 발생하지 않음)으로 코드 안에서 사용할 수 있다.* 

    *→ ~~근데 이건 너무나도 당연한게, 열거형은 자신이 가지고 있는 값을 수정할 수 없다. 그냥 읽어들일수만 있지.~~*

    → thread-safe가 이 의미가 아닌데

   **서로 관련 있는 값들에 이름을 부여하고 하나의 그룹으로 묶어 코드에서 사용할 수 있도록 하는 것**

   열거형에서는 이 “관련 있는 값들"을 “열거형 case” 라고 부른다. 

예를 들어 “차가운 색”과 관련된 열거형을 만들고 싶다면 관련 있는 값들(파란색, 보라색, 흰색 등)에 이름을 부여하여 그룹으로 묶을 수 있다. 

blue - purple - white 이렇게.

만약 노트나 펜과 같은 문구류를 생산하는 회사에서 이 “차가운 색” 열거형을 사용한다면 제품별로 “차가운 색"을 그룹핑할 수도 있다. 별도의 이름을 만들어서.

noteBlue - notePurple - noteWhite 이렇게. 

이름 뿐만 아니라 각 값에 고유한 숫자나 문자를 부여할 수도 있다.

예를 들어 각 값에 컬러 코드를 16진수나 RGB 형태로 부여할 수도 있다.

- 열거형은 확장 가능하고 프로토콜을 채택할 수도 있다. 

- 열거형은 기본적으로 케이스의 raw value를 할당하지 않아도 된다. 필요한 경우에만 사용해도 된다. String, Character, Number Type으로 제한된다.

```swift
enum ColdColors {
	case blue, purple, white
}

// 원시값
// 아래와 같이 raw value를 직접 부여해주지 않아도 된다. 이 경우 기본값이 할당된다.
// 단, Character의 경우에는 항상 raw value를 직접 부여해줘야 한다.
enum ColdColors: Int {
	case blue    // 0
  case purple  // 1
  case white   // 2
}

enum ColdColors: String {
	case blue    // "blue"
  case purple  // "purple"
  case white   // "white"
}

// 각 색상에 16진수 코드 컬러를 주고 싶다면 아래와 같이 작성한다.
enum ColdColors: String {
	case blue = "#3300ff"
	case purple = "#6633cc"
  case white = "#ffffff"
}

// raw value로 초기화
// 아래 상수는 ColdColors? 타입이고 ColdColrs.white와 동일
// 옵셔널 타입인 이유는 rawValue에 정의되지 않은 케이스가 들어올 수 있기 때문.
let whiteColor = ColdColors(rawValue: "#ffffff") 
```

- 열거형에 `CaseIterable` 프로토콜을 채택하면 모든 케이스를 대상으로 반복을 돌 수 있다.

❗️ 단, [CaseIterable](https://developer.apple.com/documentation/swift/caseiterable) 프로토콜을 채택하기 위해서는 associated value가 없어야 한다.

```swift
enum Beverage: CaseIterable {
	case coffee, tea, juice
}

let numberOfChoice = Beverage.allCases.count
print("\(numberOfChoice) beverage available")

for beverage in Beverage.allCases {
	print(beverage)
}
```

- Raw value의 한계

- 모든 케이스가 동일한 형식을 사용해야함
- 케이스당 값을 하나밖에 저장할 수 없음

*— 출처 : [[Swift] Enum 열거형 정복하기 1편](https://onelife2live.tistory.com/13)*

- Associated Values

열거형의 케이스와 함께 다른 타입의 값을 저장할 수 있는데 이 추가적인 정보를 연관된 값이라고 한다.

연관된 값을 사용하려는 열거형은 아래와 같이 정의할 수 있다. 정의하는 시점에는 구체적인 값을 지정하지 않는다. 다만, 들어갈 타입만 지정한다.

```swift
/// (Int, Int, Int, Int) 타입의 연관된 값을 가진 upc
/// String 타입의 연관된 값을 가진 qrCode
/// 반드시 모든 케이스가 연관된 값을 가져야 하는 것은 아니다. 참고!
enum Barcode {
	case upc(Int, Int, Int, Int)
  case qrCode(String)
}

var productBarcode = Barcode.upc(8, 85909, 51226, 3)
productBarcode = .qrCode("ABCDEFGHIJKLMNOP")

switch productBarcode {
/// switch 문에서는 연관된 값에 이름을 지정할 수 있다는 게 신기했다.
/// let 대신 var로 선언하여 값을 변경할 수도 있다.
case .upc(let numberSystem, let manufacturer, let product, _):
    print("UPC: \(numberSystem), \(manufacturer), \(product).")
case .qrCode(let productCode):
    print("QR code: \(productCode).")
}
// Prints "QR code: ABCDEFGHIJKLMNOP."

// 이렇게 사용할 수도 있다.
if case let .qrCode(product) = productBarcode {
    print("QR코드 형태이다. :\(product.name)")
}

// 연관된 값에는 Struct, class 타입도 사용할 수 있다.
//struct ProductStructure {
//    var name: String = "Structure Product"
//}

//class ProductClass {
//    var name: String
//    init(name: String) {
//        self.name = name
//    }
//}

//var qrBarcode: Barcode = .qrCode(ProductStructure(name: "Test"))
//var qrBarcode2: Barcode = .qrCode(ProductClass(name: "Test2"))

```

- Enum Case Pattern

```swift
enum Transportation {
    case bus(number: Int)
    case taxi(company: String, number: Int)
    case subway(line: Int, express: Bool)
}

var commute = Transportation.bus(number: 7)
var commute2 = Transportation.subway(line: 2, express: true)

switch commute {
    case .bus(let number):
        print("\(number)번 버스를 타고 통근한다.")
    case .taxi(let company, _):
        print("\(company) 회사의 택시를 타고 통근한다.")
    case .subway(let line, let express):
        let expressType = (express) ? "급행" : "일반"
        print("\(line)호선(\(expressType))을 타고 통근한다.")
}

if case let .subway(2, express) = commute2 {
    if express {
        print("2호선(급행)을 이용한다.")
    } else {
        print("2호선(일반)을 이용한다.")
    }

} else {
    print("지하철을 이용하지 않거나, 이용하더라도 2호선이 아니다.")
}

// 만약 아래와 같이 아무런 변수가 없는데 let을 쓰면 이런 경고를 띄워준다.
// 'let' pattern has no effect; sub-pattern didn't bind any variables
// 이때는 if case let .subway(_, true) -> if case .subway(_, true)로 변경해준다.
if case .subway(_, true) = commute2 {
    print("급행 지하철만 이용한다.")
}

let commuters: [Transportation] = [
    .subway(line: 2, express: false),
    .bus(number: 4419),
    .subway(line: 7, express: true),
    .taxi(company: "Kakao", number: 134)
]

for case let .subway(n, _) in commuters {
    print("subway:\(n)")
}

print("=======")
for case .subway(_, true) in commuters {
    print("subway-express")
}
print("=======")
for case let .subway(n, _) in commuters where n == 2 {
    print("subway only 2")
}
```

*— 출처 : [[Swift] Enum 열거형 정복하기2](https://onelife2live.tistory.com/14?category=741790)*

- Recursive Enumeration(재귀 열거형)

또 다른 열거형 인스턴스를 연관 값으로 가지고 있는 열거형을 말한다.

[https://www.tothenew.com/blog/recursive-enumerations-in-swift/](https://www.tothenew.com/blog/recursive-enumerations-in-swift/)

🤔 Swift에서 타입이란 무엇인가?

Swift에서는 두가지의 타입이 있다. 

1) named type : 정의할 때 이름을 부여하는 것. class, structure, enumeration, protocol이 여기에 해당됨. 

   Data type(Int, Float와 같은 숫자, characters, string) 역시 named type이다. extension을 활용해 기능을 확장할 수 있다.

2) compound type(복합 타입) : Swift 언어 자체에 정의된, 이름이 없는 타입이다. 

   - function type

   - tuple type

— 출처 : [Swift Language Guide : Types](https://docs.swift.org/swift-book/ReferenceManual/Types.html)

🤔  thread-safe한 방식이란 무엇을 의미하는가? 

  Swift는 멀티 스레딩 방식을 채택하고 있다. 멀티 스레딩 방식에서는 메모리의 Heap, 데이터 영역 등을 공유하고 있기 때문에 한 Thread에서 사용 중인 데이터 등을 다른 Thread가 접근해서 사용한다면 문제가 될 수 있다. 

  Swift의 대부분의 것들은 본질적으로 threadsafe 하지 않다. 

— 출처 : [Thread Safety In iOS - Swift](https://mohit-bhalla.medium.com/thread-safety-in-ios-swift-7b75df1d2ba6)

  

  *“ 멀티 스레드 프로그래밍에서 어떤 함수나, 변수 혹은 객체가 어떤 스레드로부터 동시에 접근이 이루어져도 문제가 발생하지 않는다는 것을 의미한다. 즉, 여러곳에서 동시에 접근하더라도 그 결과가 올바르다! “*

— 출처 : [iOS - Thread-safe란?](https://minosaekki.tistory.com/50)

**스레드 안전**(thread 安全, [영어](https://ko.wikipedia.org/wiki/%EC%98%81%EC%96%B4): thread safety)은 [**멀티 스레드](https://ko.wikipedia.org/wiki/%EC%8A%A4%EB%A0%88%EB%93%9C) 프로그래밍**에서 일반적으로 어떤 [함수](https://ko.wikipedia.org/wiki/%ED%95%A8%EC%88%98_(%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8D))나 [변수](https://ko.wikipedia.org/wiki/%EB%B3%80%EC%88%98_(%EC%BB%B4%ED%93%A8%ED%84%B0_%EA%B3%BC%ED%95%99)), 혹은 [객체](https://ko.wikipedia.org/wiki/%EA%B0%9D%EC%B2%B4_(%EC%BB%B4%ED%93%A8%ED%84%B0_%EA%B3%BC%ED%95%99))가 **여러 [스레드](https://ko.wikipedia.org/wiki/%EC%8A%A4%EB%A0%88%EB%93%9C)로부터 동시에 접근이 이루어져도 [프로그램](https://ko.wikipedia.org/wiki/%EC%BB%B4%ED%93%A8%ED%84%B0_%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%A8)의 실행에 문제가 없음**을 뜻한다. 보다 엄밀하게는 **하나의 함수가 한 스레드로부터 호출되어 실행 중일 때, 다른 스레드가 그 함수를 호출하여 동시에 함께 실행되더라도 각 스레드에서의 함수의 수행 결과가 올바로 나오는 것**으로 정의한다.

— 출처 : Wiki
