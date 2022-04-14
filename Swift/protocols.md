2022-02-23

---
protocol Ⓝ 통신규약

**protocol이란?**

특정 기능을 수행하기 위해 필요한 필수 요소들을 정의한 청사진. 필수 요소에는 메소드와 프로퍼티, 이니셜라이저가 들어간다.

- 클래스, 구조체, 열거형에서 이를 conform함으로써 사용 가능

- 여러 프로토콜을 콤마로 구분해서 추가할 수 있다.

- 프로퍼티와 메소드를 정의해둘 수 있다. 

   **프로퍼티를 정의할 때는 이게 계산 프로퍼티인지 저장프로퍼티인지를 명시하지 않아도 됨.**

   필요한 건 변수(필수 프로퍼티는 항상 `var`로 선언해야 한다고 함) 프로퍼티 이름과 타입, `{ get }` 또는 `{ get set }` 여부(이걸 작성해주지 않으면 컴파일 타임에 에러 발생함, 또한 setter는 반드시 getter와 함께여야 하기 때문에 Settable만 지정해줄 수는 없음)

- 🤔  왜 var만 사용해야할까?
    
    - 프로토콜에 정의된 프로퍼티에 gettable인지 혹은 gettable & settable 한지에 대한 정의를 하지 않고 아래와 같이 작성하면 오류가 발생함 : immutable property requirement must be declared as 'var' with a '{ get }' specifier
    
    - 왜냐하면 프로토콜에 정의될 수 있는 프로퍼티는 위에서 언급했듯이 gettable/settable을 표시해줘야 하는데 이는 계산 프로퍼티를 나타냄. **계산 프로퍼티는 계산하는 과정에서 값이 변할 수 있기 때문에 let이 아니라 var 로 선언**을 해줘야함. 
    
    ```swift
    protocol Test {
    // immutable property requirement must be declared as 'var' with a '{ get }' specifier
        let name: String
    }
    ```
    

- protocol 프로퍼티의 구현

— 👀 참고 : [SwiftProtocols: Properties distinction(get,get set)](https://chetan-aggarwal.medium.com/swift-protocols-properties-distinction-get-get-set-32a34a7f16e9)

- 단, var로 선언된 protocol 프로퍼티가 gettable & settable일 경우에는 상수 저장프로퍼티나 read-only 계산 프로퍼티로 선언할 수 없다. 이렇게 할 경우 프로토콜을 준수하지 않았다는 오류 발생함.
- gettable로 선언된 프로토콜 프로퍼티의 경우 어떠한 형태로든 준수할 수 있음.
    - 예제코드
        
        ```swift
        protocol FullyNamed {
            var fullName: String { get }
        }
        
        struct Person: FullyNamed {
            var fullName: String
        }
        
        // protocol에서 var라고 정의되어도 구현부에서 let으로 설정하여 사용 가능
        struct Person2: FullyNamed {
            let fullName: String = "Doe"
        }
        
        struct Person3: FullyNamed {
            var fullName: String {
        			return "Ahn"
        		}
        }
        
        var john = Person(fullName: "John Appleseed")
        print(john.fullName)
        
        // 근데 fullName은 gettable인데 왜 값 설정도 되지?
        // 구현부에서 저장프로퍼티로 선언했기 때문에 그런것 같다. 
        // ? 계산 프로퍼티로 만들어서 사용하면 set 만들었을 때 오류가 발생할줄 알았는데 잘됨. ??
        john.fullName = "Apple"
        print(john.fullName)
        
        var jane = Person2()
        print(jane.fullName)
        
        ```
        
- 프로토콜을 채택해서 구현하는 쪽에서는 gettable 프로퍼티를 어떤 형태로든 구현할 수 있음. (read-write 계산 프로퍼티로 선언할 수도 있음. 따라서 값도 새로 할당할 수 있음) 하지만 구현된 타입을 프로토콜로 type casting 하게 되면 get-only 프로퍼티에 값을 할당할 수 없음.
    - 예제코드
        
        ```swift
        protocol FullName {
            var familyName: String { get }
            var firstName: String { get set }
        }
        
        struct Person: FullName {
            var familyName: String = "Hann"
            
            fileprivate var innerName: String = ""
            var firstName: String {
                get {
                    return innerName
                }
                set {
                    innerName = newValue   
                }
            }
        }
        
        var a = Person()
        a.firstName = "Tomas"
        a.familyName = "Doe"
        print(a)
        
        var casting = a as FullName
        casting.firstName = "Anna"
        casting.familyName = "Maria"
        print(casting)
        ```
        

- 프로토콜의 타입 프로퍼티는 `static` 사용하면 됨.

- 프로토콜에서 필수 인스턴스 메소드와 타입 메소드를 정의할 수 있음. 하지만 **기본값은 메소드에서 설정할 수 없음.**

- mutating 키워드도 사용가능. 단 채택한 타입이 값 타입일 경우에만 mutating 붙여줌. 클래스일 경우 안붙여줘도 됨.

- 클래스에서 프로토콜 이니셜라이저 구현 시 `required` 키워드로 표시해줘야함.

- **프로토콜도 하나의 타입으로서 사용된다.**

- **위임(Delegate)**

클래스 혹은 구조체 인스턴스에 **특정 행위에 대한 책임을 넘길 수 있게 해주는 디자인 패턴** 중 하나. 이를 구현하는데 있어 protocol을 사용한다.

- 클래스 전용 프로토콜은 클래스에서만 사용할 수 있는 프로토콜로 `protocol SomeClassOnlyProtocol: AnyObject` 를 추가해준다.

여기서 궁금한것 : AnyObject 대신 class 키워드를 추가하는 경우도 있는데 뭐가 다름??

- [StackOverflow] [What’s the difference between a protocol extended from AnyObject and a clsss-only protocol?](https://stackoverflow.com/questions/30176814/whats-the-difference-between-a-protocol-extended-from-anyobject-and-a-class-onl)
    
    - `AnyObject`와 `class` 키워드는 동일하다, 차이가 없다.
    
    - 하지만 `class` 키워드는 결국 deprecated될 것이기 때문에 `AnyObject`를 써야한다.
    

- is, as?, as!를 이용해 특정 인스턴스가 특정 프로토콜을 따르는지 확인 가능

- **선택적 프로토콜**

프로토콜에 정의된 메소드나 프로퍼티는 프로토콜을 채택한 쪽에서 필수로 구현해야함. 하지만 간혹 선택적으로 구현해도 되는 요소들이 있을 수 있음. 이 경우 `@objc` 키워드를 이용해 선택적으로 구현하도록 만들 수 있다. 클래스 타입에서만 채택되어 사용할 수 있음.

1. protocol 정의 앞에 `@objc` 키워드를 붙임.

2. 선택적 구현이 필요한 메소드, 요소 앞에 `@objc optional`을 붙임

```swift
@objc protocol CounterDataSource {
    @objc optional func increment(forCount count: Int) -> Int
    @objc optional var fixedIncrement: Int { get }
}
```