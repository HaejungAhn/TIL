2022-03?

---
- NotificationCenter에 대해 공부

- KVO에 대해 공부

- Delegate, NotificationCenter, KVO를 어떻게 사용하는지 공부

- 각각의 장/단점을 나름대로 정리하기

*— 🗞 읽을거리*

[패턴이 나오게된 배경](https://medium.com/@Alpaca_iOSStudy/delegation-notification-%EA%B7%B8%EB%A6%AC%EA%B3%A0-kvo-82de909bd29)

# NotificationCenter

*— 🔗 참고 : [[Apple Document] NotificationCenter](https://developer.apple.com/documentation/foundation/notificationcenter)*

*— 🔗 참고 : [[Apple Document] NSNotification.Name list](https://developer.apple.com/documentation/foundation/nsnotification/name)*

> A notification dispatch mechanism that enables the broadcast of information to registered observers.

등록된 옵저버”들”에게 정보를 전파할 수 있는 알림 dispatch 매커니즘
> 

Apple은 “Dispatch”라는 단어를 상당히 많이 쓰는것 같다. 메소드 디스패치, DispatchQueue, notification dispatch 등등

~~이제 “dispatch”에 대한 사전적 정의 말고 나만의 정의를 내릴때가 된 것 같다.~~

~~dispatch를 기준으로 앞에 단어가 오느냐, 뒤에 단어가 오느냐에 따라 의미가 조금 다른 것 같다.~~

NotificationCenter를 통해 알림을 받는 과정

1. 객체가 notification을 받기 위해 자기자신을 알림센터(NotificationCenter)에 등록한다. (addObserver)
    
    “실행중인” 앱은 default 알림센터가 있다. 별도로 알림센터를 만들어 사용해도 된다. NotificationCenter는 단일 프로그램안에서만 알림을 전달할 수 있다. 만약 다른 프로세스로부터 알림을 받거나 알림을 게시하고 싶다면 DistributedNotificationCenter를 사용해라.
    
2. 옵저버로 자신을 등록할 때 어떤 알림([NSNotification.Name](https://developer.apple.com/documentation/foundation/nsnotification/name) 옵셔널 타입)을 받을지 정한다.
    
    서로 다른 알림을 받기 위해 자기자신을 여러번 등록할 수 있다.
    
3. 이벤트가 발생하면 이벤트를 전파하고 싶은 곳에서 Notification(`post` 메소드 사용)을 게시할 수 있다. NotificationCenter를 통해 앱 안에 있는 옵저버들에게 전달된다. 
    
    `post` 메소드에 들어가는 파라미터
    
    - `name: NSNotification.Name` 
    
    - `object: Any?` notification을 post한 인스턴스를 전달
    
    - `userInfo: [AnyHashable: Any]?` Notification의 정보 전달 가능
    

4. 옵저버들은 Notification을 받아 알맞은 처리를 한다.

5. 만약 더이상 필요하지 않다면 NotificationCenter에서 옵저버로 등록된 자기자신을 제거해야한다. 제거하지 않으면 옵저버로 등록된 인스턴스(예를 들어 ViewController 등)가 계속 남아있어 메모리 누수가 발생할 수 있으며 예기치 못한 오류가 발생할 수도 있다. (removeObserver)

# KVO : Key-Value Observing

*— 🔗 [[Tistory, zeddios] Key-Value Observing(KVO) in Swift](https://zeddios.tistory.com/1220)*

*— key에 매핑된 value를 관찰한다.*

- **인스턴스 프로퍼티의 변경을 다른 인스턴스에 알리기 위해** 사용하는 Cocoa Programming 패턴

KVO를 이용해 프로퍼티의 변경 감지하기

1. NSObject를 상속
    
    — 상속이 필요하기 때문에 클래스에서만 KVO를 사용할 수 있다.
    
    — NSObject가 모든 object에 대해 자동 옵저빙 기능을 제공하는 NSKeyValueObserving 프로토콜을 구현하고 있기 때문
    
2. 옵저빙하려는 프로퍼티에 `@objc` 속성과 `dynamic` modifier를 추가
3. 옵저빙할 프로퍼티를 가지고 있는 인스턴스의 `observe()` 메소드를 통해 옵저빙할 키패스와 options(old, new, initial, prior이 있음), 옵저빙 핸들러를 정의

**KeyPath**

*— 🔗 [[Tistory, zeddios] Key-Value Coding(KVC) / KeyPath in Swift](https://zeddios.tistory.com/1218)*

```swift
let address = Address(town: "unknown")
let zedd = Person(address: address)

let zeddAddress = zedd[keyPath: \.address]
let zeddTown = zedd[keyPath: \.address.town]
```

- 중복되는 keypath를 아래와 같이 만들어서 사용할 수도 있다.

```swift
\{타입}.{프로퍼티 이름}

// 위 예제에서 address.town을 keyPath 타입으로 만들고싶다면
let townKeyPath = \Person.address.town
zedd[keyPath: townKeyPath]
```

keyPath에는 3가지 타입이 있다.

KeyPath - Read-only, 상수로 선언된 프로퍼티 접근 시 자동으로.

WritableKeyPath : value type 인스턴스에 사용 가능. **변경 가능한**(var) 모든 프로퍼티에 대한 read & write access 제공

ReferenceWritableKeyPath : 참조타입 인스턴스에 사용 가능. 변경 가능한 모든 프로퍼티에 대한 read & write access 제공

**Property observer(willSet, didSet)과의 차이가 있다면?**

property observer는 프로퍼티의 값 변화를 캐치하고 이를 인스턴스 내부에만 알릴 수 있는 반면 KVO는 (프로그래밍하기에 따라) 인스턴스 외부에 있는 다른 인스턴스에도 변경됐을때에 대한 처리를 할 수 있도록 한다.

# Delegate, NotificationCenter, KVO 비교

— 3가지 모두 어떤 이벤트가 발생했을 때 이벤트를 캐치하는 곳과 이를 처리하는 곳을 **분리**시킬 수 있는 방법들이다.

— Delegate와 NotificationCenter는 서로 다른 역할을 가진 두 인스턴스, 예를 들어 하나는 이벤트를 캐치하는 역할이고 다른 하나는 이벤트에 대한 처리를 하는 역할을 가진 두 인스턴스가 통신할 수 있는 방법을 제시한다.

목적, 구현하는 방법과 데이터에 응답할 수 있는 인스턴스의 개수차이

Delegate 

- 프로토콜을 이용해 필요한 메소드 정의, delegate에 인스턴스를 할당하고 필요한 메소드를 정의함으로써 통신 가능하며 1:1 통신이다.
- ~~메소드의  매개변수를 이용해 데이터 전달 가능하며 타입이 명확하다.~~ → Delegate도 매개변수를 Any로 정의할 수 있기 때문에 차이라고 보긴 어려울 것 같다.

NotificationCenter

- NotificationCenter라는 중간 매개체를 통해 통신하며 처리하고자 하는 이벤트를 String으로 설정할 수 있다. 하나의 이벤트를 여러 인스턴스가 처리할 수 있다.
- NSNotification.Name을 이용해 post, subscribe 할 수 있음.(이미 정의된 걸 사용할 수도 있고, String을 이용해 새롭게 정의할 수도 있다)
- 시스템 이벤트를 쉽게 받을 수 있지 않나 이거? Keyboard show,hide 혹은 앱이 백그라운드로 이동, 포그라운드로 올라왔을 때의 Notification 구독이 쉽다고 생각함.

KVO

- NSObject를 상속받아야하기 때문에 클래스에서만 사용 가능
- 프로퍼티의 값 변화를 관찰하는게 주 목적(Delegate, NotificationCenter와는 목적이 조금 다름)
- 프로퍼티에 `@objc dynamic` 키워드를 붙여야함.