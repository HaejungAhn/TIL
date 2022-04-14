2022-02-24

---
**구조체와 클래스**

- 서로 연관있는 프로퍼티와 메소드를 하나로 묶어 사용할 수 있도록 한다.

- 데이터를 저장할 수 있고 이에 대해 처리할 수 있는 메소드를 가질 수 있다.

- 프로퍼티와 메소드를 가지고 있다. → 이런 관점에서 보니 열거형에는 어떤 것들이 들어갈 수 있는지 잘 모르겠다. 

**구조체와 클래스의 비교 : 공통점**

- 프로퍼티와 메소드를 가지고 있음.

- initializer 설정 가능 (deinit은 클래스만 가능함)

- 서브스크립트, 프로토콜, extension 사용 가능

**구조체와 클래스의 비교 : 차이점, 클래스의 더 많은 기능들**

- 구조체는 value type, 클래스는 reference type

- 클래스는 상속 가능~~(그래서 타입 캐스팅 가능)~~, deinitializer 정의 가능

**구조체 : Memberwise initializer**

구조체는 인스턴스화될 때, 가지고 있는 프로퍼티를 초기화할 수 있는 자동 initializer를 가지고 있다.이를 memberwise initializer라고 한다.

아래 struct에는 초기화 메소드가 없지만 각 프로퍼티를 초기화할 수 있는 이니셜라이저가 디폴트로 제공됨.

```swift
struct Resolution {
    var width = 0
    var height = 0
}

let vga = Resolution(width: 640, height: 640)
```

**구조체와 열거형은 value type이다.**

- 변수 또는 상수에 할당될 때 혹은 함수에 전달될 때 복사된다. 

- swift 표준 라이브러리에 정의된 콜렉션은 COW이다. (복사된 것처럼 보이지만 실은 동일한 저장소를 공유하며, 값이 수정되기 직전에 복사되는 것)

**클래스는 reference type이다.**

- 변수 또는 상수에 할당될 때 혹은 함수에 전달될 때 값 복사가 아닌 인스턴스 참조가 이루어진다.

**식별연산자**

2개의 상수 또는 변수가 **동일한 인스턴스를 참조하는지를 확인할 때** 식별 연산자를 이용하면 된다. → 즉, 클래스 타입의 변수/상수 비교할 때만 사용할 수 있다. 

(함수/클로저도 reference type이니까 함수에 대한 비교를 해봤는데 오류남. **cannot check reference equality of functions; operands here have types '(Int) -> ()' and '(Int) -> ()'**)

동일 연산자(==)와는 다른 것이다.

- `===` 동일 인스턴스

- `!==` 동일하지 않은 인스턴스

**포인터**

Wiki - 프로그래밍 언어에서 다른 변수 혹은 그 변수의 메모리상 주소를 가리키는 변수를 말한다.

reference type의 변수나 상수를 선언할 때, 그 변수 혹은 상수가 포인터아님? 난 그렇게 이해했음!

* 상속과 프로토콜의 차이는 무엇인가

[**Choosing Between Structures and Classes**](https://developer.apple.com/documentation/swift/choosing_between_structures_and_classes)

**🤔  왜 structure를 디폴트로 선택하라고 하는가**

- 다른 언어와 달리 여러 기능을 제공함. 저장 및 계산 프로퍼티와 메소드 사용가능

- Objective-C의 structure
    
    Objective-C에서의 struct는 Swift의 프로토콜과 비슷한 모양이다. 틀만 만들 수 있고 struct 안에서 데이터의 초기화나 메소드 구현 등을 할 수 없음. 
    
    ```swift
    // − *👀출처 [[tutorialspoint] structures](https://www.tutorialspoint.com/objective_c/objective_c_structures.htm)*
    
    #import <Foundation/Foundation.h>
    
    struct Books {
       NSString *title;
       NSString *author;
       NSString *subject;
       int   book_id;
    };
     
    int main() {
       struct Books Book1;        /* Declare Book1 of type Book */
     
       /* book 1 specification */
       Book1.title = @"Objective-C Programming";
       Book1.author = @"Nuha Ali"; 
       Book1.subject = @"Objective-C Programming Tutorial";
       Book1.book_id = 6495407;
    
    	 /* print Book1 info */
       NSLog(@"Book 1 title : %@\n", Book1.title);
       NSLog(@"Book 1 author : %@\n", Book1.author);
       NSLog(@"Book 1 subject : %@\n", Book1.subject);
       NSLog(@"Book 1 book_id : %d\n", Book1.book_id);
    
    	 return 0;
    }
    ```
    
    ```swift
    // *- 👀출처 [[Stack overflow] Use C Struct in Objective-C](https://stackoverflow.com/questions/2172887/use-c-struct-in-objective-c)*
    
    //Typedef 2 function pointers, first takes and returns int,
    // second takes and returns double
    typedef int    (*FuncPtrInt)   (int);
    typedef double (*FuncPtrDouble)(double);
    
    // create structure to store function pointers
    struct ABC
    {
        FuncPtrInt    applyA;
        FuncPtrDouble applyB;
    };
    
    // create some functions to use with structure
    int incrFuncA(int num) { return ++num; }
    double decrFuncB(double num) { return --num; }
    double multiplyFuncB(double num) { return num*num; }
    
    // try it out
    void testStruct()
    {
        struct ABC abc;
        abc.applyA = incrFuncA;
        abc.applyB = decrFuncB;
    
        NSLog(@"increment: %d",abc.applyA(3));
        NSLog(@"decrement: %f",abc.applyB(3.5));
    
        abc.applyB = multiplyFuncB;
    
        NSLog(@"multiply: %f",abc.applyB(3.5));
    }
    
    //-- output
    2010-02-01 10:36:22.335 x[11847] increment: 4
    2010-02-01 10:36:22.336 x[11847] decrement: 2.500000
    2010-02-01 10:36:22.336 x[11847] multiply: 12.250000
    ```
    

- protocol을 이용할 수 있음

- protocol 이용의 장점?
    
    

- Value type이라는 특징으로 인해 얻을 수 있는 장점 : 코드를 쉽게 추론할 수 있음(왜냐하면 값이 복사되는 것이기 때문에 structure 타입의 변수가 코드의 이곳저곳에서 수정되어도 다른 곳에 영향을 미치지 못함)

**⚠️  클래스는 Objective-C의 상호운용성이 필요할 때 써라**

- 데이터를 처리하기 위해 Objective-C API가 필요한 경우

- 어떤 예시가 있을까? → `@objc` attribute를 사용해야 하는 경우
    
    ✓ 프로토콜에 `@objc` 키워드가 붙은, 선택적 프로토콜은 struct가 채택해서 사용할 수 없음. 
    
    ```swift
    @objc protocol MyProtocol {
       @objc func myFunction1()
       @objc optional func myFunction2()
    }
    
    // error:  Non-class type 'MyStructure' cannot conform to class protocol 'MyProtocol’
    struct MyStructure: MyProtocol { 
        var myName: String = "wai"
    }
    
    let my = MyStructure()
    ```
    
    struct에 NotificationCenter의 옵저버를 등록하는 경우도 있나?
    
    struct에서 UIKit에 있는 것들은 사용 못하겠네. 
    
- `@objc` attribute는 무엇인가?
    
    — *👀출처 : [[Swift Language Guide] Attributes: objc](https://docs.swift.org/swift-book/ReferenceManual/Attributes.html)*
    
    > 기본적으로 swift에서 만든 코드는 swift 코드에서만 사용이 가능하다. 하지만 `@objc`속성을 사용하면 Objective-C에서도 Swift의 코드 접근이 가능하다.
    > 
    
    Objective-C로 표현될 수 있는 곳에 이 속성을 적용한다. 중첩되지 않은 클래스, 프로토콜, 정수 원시값 타입의 열거형, 클래스의 프로퍼티, 메소드, 이니셜라이저, 서브스크립트에 `@objc`를 적용할 수 있다.
    
    **Objective-C에 코드를 왜 노출시켜야 하는가?**
    
    — *👀출처 : [[Medium] To the depth of objc and dynamic in swift](https://varun04tomar.medium.com/to-the-depth-of-objc-and-dynamic-in-swift-b5472800b85d)*
    
    왜냐하면 전체 코드를 Swift로 작성했다 하더라도 많은 framework(대표적으로 UIKit)가 objective-c 런타임을 이용하기 때문이다.
    
    `#selector(function)` → 버튼에 타겟을 추가하거나 NotificationCenter의 옵저버에 사용될 메소드를 정의할 때는 반드시 @objc가 붙은 메소드만 사용할 수 있음. 왜냐하면 selector는 런타임시 메소드의 이름을 표현하는 C string이기 때문이다. C String의 경우 objective-c 런타임을 호출해야 한다.
    
    Swift에서 “`dynamic`”을 사용하려면 Objective-C의 dynamic dispatch를 사용해야 한다. KVO 혹은 method swizzling을 할 때 이게 필요할거임. `dynamic`이 objective-c의 특징이니만큼, `dynamic` 키워드를 사용할 때 `@objc`도 같이 사용해야 한다.
    
    **Objective-C와 Swift의 차이**
    
    - objective-c pros
    
    - dynamic and adaptable
    
    obj-c의 가장 큰 특징은 dynamic 하다는 것임. 무엇이 dynamic 하냐? 런타임에서 메소드를 변경하는게 가능하도록 만든다? (obj-c가 스몰토크 패러다임을 차용했기 때문)
    
    - objective-c cons
    
    - 문법이 어렵다.
    
    - Objective-C의 dynamic 한 속성때문에 디버깅이 어렵다.
    
    - Objective-C와 비교했을 때 Swift의 pros
    
    - 안정성, 속도 빠름, Cross platform, 오픈소스
    
    [https://joycestudios.tistory.com/3](https://joycestudios.tistory.com/3)
    
    - swift는 .swift 파일 하나만 있으면 됨. obj-c는 .h(header), .m(implementation)파일로 나뉘어져 있다. 
    
    -  siwft는 멀티 프로그래밍 패러다임(객체지향, 함수형, 프로토콜 지향 언어), objective-c는 객체지향 언어
    
       swift에서는 함수를 값으로 이용하고, 함수 안에 함수를 넣을수도 있고 클로저와 기타 다른 함수 속성들이 많다. 
    
    - Swift는 지속적으로 버전업되고 있기 때문에 기존 코드에 대한 마이그레이션 작업이 필요할 수 있음. Obj-c는 버전업 없음.
    
    - 코딩 스타일 : Swift는 java와 유사한형태. Obj-c는 블럭코딩[]과 C 스타일
    
    - Swift는 컴파일 단계에서 nil 포인트 에러, 타입 체크 등을 진행함. obj-c는 사용자가 직접 관리해야함. → 런타임 에러가 발생할 가능성이 상대적으로 높다.
    
    - Swift는 전체 API에 대한 ARC 지원, obj-c는 Cocoa API 내에서만 ARC 사용 가능
    
    - obj-c의 문법은 다른 개발언어들과 많이 다르기 때문에 러닝커브가 높다. 이에 비해 swift는 문법이 단순하다.
    
    - obj-c는 C, C++을 사용할 수 있지만, Swift는 C나 C++을 직접적으로 사용할 수 없다.
    
    - swift는 변수,상수 선언 시 타입추론이 가능하지만 objective-c는 타입을 명확히 작성해야한다.
    
    - Swift는 optional로 nil 관리가 용이하지만 objective-c는 null 포인터를 사용할 수 있다.?
    
    - Objective-C는 동적라이브러리 미지원하지만 swift는 지원한다.
    
    Objective-C 런타임
    
    [https://zeddios.tistory.com/296](https://zeddios.tistory.com/296)
    
    > dynamic dispatch : objective-c 런타임이 호출해야하는 특정 메소드나 함수의 구현을 런타임에 결정하겠다.
    
    Objective-C는 클래스의 메소드나 프로퍼티를 호출할 때, 해당 객체에 "메세지"를 보내는 방식(message sending)으로 구현되어있어요.
    그럼 메세지를 받은 객체는 이제 진짜 그 메소드가 구현되어있는 곳으로 가서 비로소 그 메소드를 실행하는거죠.
    가장 중요한 것은 이러한 과정이 “런타임"에 일어난다는 것이죠.
    > 
    
    > static dispatch : 컴파일 타임에 해당 메소드의 실제 코드위치를 파악하는 것?
    > 
    

- Objective-C 프레임워크에 정의된 기존 클래스 계층구조에 맞게 data model을 만들어야 하는 경우

**🆔  Control Identity가 필요할 경우 클래스를 써라**

- Swift의 클래스는 reference type이기 때문에 identity의 개념이 내장되어 있다. 따라서 서로 다른 두 클래스가 동일한 저장프로퍼티를 가지고 있다 하더라도 식별연산자로 두 인스턴스를 비교해보면 서로 다름을 알 수 있다.

- 인스턴스의 변화는 그를 참조하고 있는 다른 인스턴스에게도 영향을 미친다. 따라서 이런 동작이 필요한 경우라면 클래스를 써라?

**structure와 class 모두 상속의 형태를 제공한다.**

- struct의 경우 protocol을 활용하면 이러한 형태로 만들 수 있다.