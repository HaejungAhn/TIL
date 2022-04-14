2022-02-16

---
- 예제코드
    
    ```swift
    class MediaItem {
        var name: String
        init(name: String) {
            self.name = name
        }
    }
    class Song: MediaItem {
        var artist: String
        init(name: String, artist: String) {
            self.artist = artist
            super.init(name: name)
        }
    }
    
    let song = Song(name: "", artist: "")
    
    // 자기 자신의 타입을 확인하는 것은 결과가 항상 true이기 때문에 아래와 같은 경고 문구가 뜬다.
    // 'is' test is always true
    if song is Song {
    	print("song is Song")
    } else {
    	print("song isn't Song") // Will never be executed
    }
    ```
    

**타입 확인(Type Checking)이란?**

- `is` 타입 검사 연산자 

  인스턴스가 특정 클래스나 structure와 동일하거나 특정 클래스/structure의 **“하위”** 타입인지를 확인한다. 이 경우 true, 나머지는 false

  인스턴스가 특정 프로토콜을 준수하는지도 확인 가능

  ( 인스턴스가 특정 클래스의 하위 클래스 타입이면 true / 그렇지 않으면 false )

**타입 캐스팅(Type Casting)이란?**

- 인스턴스를 상위 클래스 또는 하위 클래스로 **취급**할 수 있도록 함

   📒  casting :  주물(녹인 금속을 틀에 넣고 응고시켜 원하는 모양의 제품을 만들어내는 것)

  “casting”이라는 단어의 뜻을 보면 외양만 변할 뿐 금속이라는 본래의 성질이 바뀌는 것은 아니다. 타입 캐스팅 역시 특정 타입으로 보이도록 만드는 것일 뿐 그 성질까지 바꿔놓는 것은 아니다. 따라서 “변환" 이라는 단어보다는 “취급"이라는 단어가 더 적절하다고 생각함.

*다운캐스팅 한다고 하여 원래 인스턴스나 값을 실제로 변경하는 것은 아니다. 기존 인스턴스는 그대로 유지된다. 그저 다운캐스팅 된 타입으로 접근하고 처리될 수 있을 뿐이다.*

- `as` 타입 캐스트 연산자. 특정 클래스를 하위 클래스 타입으로 **downcast**한다.

- 다운캐스팅은 실패할 가능성도 있기 때문에 타입 캐스트 연산자는 두가지 형태로 제공된다.  `as?`와 `as!`가 그 형태다.

as? → 다운 캐스팅 할 때 타입의 옵셔널 값을 반환. 다운캐스팅 실패할 경우 nil 반환

as! → 다운 캐스팅 할 때 강제 언래핑함. 다운캐스팅 실패할 경우 런타임 에러 발생

**Any와 AnyObject에 대한 타입 캐스팅**

Swift는 특정하지 않은 타입에 대한 작업을 위해 2개의 특별한 타입을 제공한다.

Swift는 변수나 상수에 담을 타입이 명확하게 지정되어야 함. 근데 Any와 AnyObject는 좀 다르다. 어떤게 들어올지 명확하게 지정하지 않아도 다 담을 수 있는 주머니 같은 것.

- Swift에서 타입이란?
    - *출처 : **[Swift Language Guide: Types](https://docs.swift.org/swift-book/ReferenceManual/Types.html)***
    
    Swift는 두가지의 타입이 있다.
    
    ①  named type : 정의할 때 이름을 부여하는 것. class, structure, enumeration, protocol이 여기에 해당됨.
    
    Data type(Int, Float와 같은 숫자, characters, string) 역시 named type이다. extension을 활용해 기능을 확장할 수 있다.
    
    ②  compound type(복합 타입) : Swift 언어 자체에 정의된, 이름이 없는 타입으로 function type과 tuple type이 있다.
    

- `Any` 함수 타입을 포함하여 모든 타입(옵셔널 타입도 포함)의 인스턴스를 나타낼 수 있음. reference type과 value type 둘다 다룰 수 있다.

- `AnyObject` 클래스 타입 인스턴스만 나타낼 수 있음(즉, reference type만 다룰 수 있음)

애플 공식문서에서는 필요한 경우가 아니라면 Any와 AnyObject 대신 구체적인 타입을 지정할 것을 권장한다. 

그 이유는 코드의 가독성과 유지보수성을 떨어뜨리기 때문이다.

→ 관련된 예제는 [Any vs AnyObject](https://medium.com/swiftcommmunity/any-vs-anyobject-2accaba7ea9c) 에서 자세히 볼 수 있다.

Any와 AnyObject는 어떤 목적으로 만들어졌을까? Swift2로 거슬러올라간다.

*− 👀 참고 : [Any vs. AnyObject in Swift 3.0](https://medium.com/@mimicatcodes/any-vs-anyobject-in-swift-3-b1a8d3a02e00)*

- Swift2에서는 objective-c의 “id”가 Swift의 AnyObject(클래스 타입의 값만 담을 수 있는)와 매핑되었다. 

- Objective-C의 id란?
    
    Objecitve-C의 object에 대한 포인터다.
    

- 또한 Swfit2는 편의를 위해 암묵적으로 일부 bridg된 value type(Array, String, Dictionary, Set, Number 타입 등)을 AnyObject로 변환하는 기능을 제공했다. 이로 인해 네이티브 Swift 타입들(String, Array, Dictionary 등등)은 NSString, NSArray 또는 Foundation의 다른 컨테이너 클래스를 예상하는(주로 사용하는) Cocoa API와 함께 쉽게 사용될 수 있었다.

- 이런 변환은 언어의 나머지 부분과 일관성 있는 정책은 아니었다. (아마 Reference type만 담을 수 있는 AnyObject에 Value Type이 들어갈 수 있어 그런 것 아닐까 한다..) 이로 인해 정확히 어떤 것들이 AnyObject로써 사용되어야 하는지 이해하는 것을 어렵게 만들었고 이는 곧 버그로 이어졌다.

- 그래서 reference type과 value type을 모두 담을 수 있는 Any가 도입되게 되었고, Swift 3.0에서부터는 Objective-C의 id가 Swift의 Any로 임포팅됨.