2022-03-03

---
Attribute는 클래스, 프로퍼티, 함수나 파라미터 앞에 위치. 특정한 동작을 하도록 만든다. @로 시작함.

크게 3가지로 나뉠 수 있다.

Declaration Attribute, Type Attribute, Switch Case Attribute

# @frozen

*— 👀 [[Tistory] @frozen은 무슨 의미일까?](https://chibest.tistory.com/99)*

*— 👀 [[Apple Swift Docs] LibraryEveolution.rst](https://github.com/apple/swift/blob/main/docs/LibraryEvolution.rst)*

`@frozen` 은 enum 및 struct와 함께 사용될 수 있다.

“얼어붙은”이란 의미를 가지고 있는데, 단어 뜻 그대로 더이상 변경사항이 없음을 나타낸다. enum과 함께 사용되면 더이상 추가되는 case가 없고 순서 역시 변하지 않음을 보장하며, struct와 함께 사용되면 더이상 추가되거나 삭제, 수정되는 stored property가 없을 것을 보장한다. 

struct의 경우 `@frozen`이 붙으면 stored property의 순서를 바꾸거나 저장 → 계산 혹은 계산 → 저장프로퍼티로 바꾸거나 옵저버 추가 불가함.

# @unknown

보통 열거형의 각 케이스에 대응하는 코드를 작성할 때 switch문을 사용하게 된다. switch문은 모든 케이스를 커버해야만 한다. 그런데 만약 열거형에 새로운 값이 추가된다면 어떨까? switch 문에서 모든 케이스를 처리하지 못하기 때문에 에러가 발생할 수 있고, default가 있는 경우 새로운 케이스가 추가된지도 모르고 이에 대한 처리를 정확하게 할 수 없다.

이때 switch에 default앞에 `@unknown`을 붙여주면 나중에 케이스가 추가됐을 때 컴파일이 “Switch must be exhaustive”라는 warning을 줄 수 있다.

non-frozen enum(예시 - UILabel의 textAlignment 속성인 NSTextAlignment)의 경우 추후 case값이 추가될 수도 있다.

# @objc

— [Structure and Classes](https://www.notion.so/Structures-and-Classes-7fa8feb3031b4fa389ab5e9cf523ef35)

> 기본적으로 swift에서 만든 코드는 swift 코드에서만 사용이 가능하다. 하지만 `@objc`속성을 사용하면 Objective-C에서도 Swift의 코드 접근이 가능하다.
> 

Objective-C에서 Swift의 코드를 사용할 수 있게끔 하기 위해 필요함.

컴파일러는 암시적으로 Objective-C에 정의된 모든 클래스의 하위클래스에 objc 속성을 추가한다. 단, 하위클래스는 제네릭이 아니어야 한다.

프로토콜에서의 @objc는 옵셔널한 프로토콜 메소드나 프로퍼티를 정의하기 위함임.

클래스의 경우 target-action이나  NotificationCenter의 observer 추가시 selector로 전달하는 메소드를 정의할 때 @objc를 사용한다. UIKit(IBOutlet, IBAction등을 사용할 경우에도 포함됨.)

사용하는 이유 - 전체 코드를 Swift로 작성했다 하더라도 많은 framework(대표적으로 UIKit)가 objective-c 런타임을 이용하기 때문이다. 또한 KVO, method swizzling, dynamic

# @PropertyWrapper

- 프로퍼티의 getter, setter와 관련된 반복적인 로직이 있을 경우 → 필요할 때마다 프로퍼티 작성 시 로직을 작성할 수 있음. 하지만 그만큼 코드 반복이 많을 것이다. 

- @propertyWrapper를 사용하면 반복되는 로직을 별도로 정의해두고, 프로퍼티 선언 시 커스텀한 @propertyWrapper 속성을 부여할 수 있음.