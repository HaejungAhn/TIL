

### **Receiving and Handling Events with Combine**

asynchronous sources(이벤트 소스 말하는 것 같음.)로부터 이벤트를 받고 커스텀하기

**Connect a Publisher to a Subscriber**

- 컴바인으로 textfield의 notification을 받기 위해서는 NotificationCenter의 `default` instance에 접근한 뒤 publisher(for:object:) 메소드를 호출해야 한다. 이 호출은 notification 이름과 source object(noti를 받고 싶은 object)를 받고 notification element를 expose하는 publisher를 리턴한다.

publisher와 subscriber가 설정해야 하는 타입

- publisher로부터 element를 받고 싶다면 Subscriber를 사용해야 한다. subscriber는 Input(associated type)타입(publisher로부터 받을 타입)을 지정해야 한다.
- publisher는 Output(associated type)타입(만들어낼, 내보낼 타입)을 지정해야 한다.
- publisher와 subscriber 모두 Failure(Error 타입)를 정의해야 한다. 이는 그들이 생산하거나 받을 수 있는 오류 타입을 나타낸다.

publiser와 subscriber를 연결하고 싶다면 

`publisher의 output 타입 == subscriber의 Input 타입 && publisher의 failure 타입 == subscriber의 failure 타입`

subscriber를 publisher에 좀더 쉽게 연결하기 위해 Swift에서 제공하는 빌트인 Subscriber가 있다. 연결할 publisher의 output과 failure type을 자동으로 매칭시켜준다.

1. sink(receiveCompletion:receiveValue:)
    - 두개의 클로저를 받는다. 첫번째 클로저는 Subscibers.Completion을 받을 때 실행된다.
    - Subscribers.Completion은 enumeration으로 publisher가 정상적으로 종료됐는지 error와 함께 실패했는지를 나타낸다. 내부적으로 두개의 enum 값을 가지고 있다. (case finished, case failure(Failure) )
    `@frozen enum Completion<Failure> where Failure : [Error](https://developer.apple.com/documentation/Swift/Error)`
    - 두번째 클로저는 publisher로부터 element를 받았을 때 실행된다.
    - 리턴 값이 있다. AnyCancellable타입의.
        
        store()를 활용하면 해당 함수의 리턴값을 별도 변수/상수에 할당하지 않아도 에러가 뜨지 않음.
        
2. assign(to:on:)
    - 프로퍼티를 가리키는 key path를 이용해 전달받은 Element를 주어진 object의 프로퍼티에 즉시 할당한다.

sink와 assign 모두 publisher로부터 element를 무제한으로 요청할 수 있다. element를 받는 속도를 제어하려면 subscriber 프로토콜로 커스텀해라.

****Change the Output Type with Operators****

연산자를 이용해 output type을 변경하기

이전 섹션의 sink subscriber는 receiveValue 클로저에서 모든 작업을 수행했다. 만약 전달받은 element로 많은 커스텀 작업을 수행하거나 invocation간 상태를 유지할 필요가 있다면 부담이 될 수 있다.

컴바인의 이점은 event delivery를 커스터마이즈하기 위해 연산자를 결합하는데서 온다.

예를 들어 만약 당신한테 필요한게 textField의 string value 뿐이라면 callback에서 받는 NotificationCenter.Publisher.Output은 편리한 타입이 아니다.

publisher의 아웃풋은 기본적으로 시간이 지남에 따른 일련의 element이기 때문에 컴바인은 시퀀스(아웃풋 element)를 수정할 수 있는 연산자(map, flatmap, reduce 등)를 제공한다. 이런 operator의 행동들은 Swift standard 라이브러리와 유사하다.

```swift
let sub = NotificationCenter.default
    .publisher(for: NSControl.textDidChangeNotification, object: filterField)
    .map( { ($0.object as! NSTextField).stringValue } )
    .sink(receiveCompletion: { print ($0) },
          receiveValue: { print ($0) })
```

publisher 체인이 원하는 타입을 생성해내면 sink를 assign subscriber로 바꿔 바로 값을 할당할 수 있다.

```swift
let sub = NotificationCenter.default
    .publisher(for: NSControl.textDidChangeNotification, object: filterField)
    .map( { ($0.object as! NSTextField).stringValue } )
    .assign(to: \MyViewModel.filterString, on: myViewModel)
```

**Customize Publishers with Operators**

연산자로 publisher 커스터마이징 하기

수동으로 코딩해야 하는 작업을 수행하는 연산자를 사용하여 publisher 인스턴스를 확장할 수 있다. 다음은 event-processing chain을 개선하기 위해 연산자를 사용할 수 있는 3가지 방법이다.

- TextField에 입력된 문자열로 뷰 모델을 업데이트하는 대신 filter(_:) 연산자를 사용해 특정 길이 미만의 입력을 무시하거나 영숫자가 아닌 문자를 거부할 수 있다.
- 만약 filtering 연산이 expensive하다면, user가 타이핑을 stop할때까지 기다리는 것을 원할 수도 있다(타이핑할 때마다 filtering 하는 것이 아니라 타이핑 멈출때까지 기다렸다가 filtering 하고 싶을 수도 있음) 그렇다면, debounce(for:scheduler:options:) 연산자를 이용해 publisher가 event를 emit하기 전에 반드시 지연되야 할 최소 시간을 설정할 수 있다. RunLoop 클래스는 초 단위 혹은 밀리초 단위로 지연 시간을 지정하는 편리한 방법을 제공한다.
    - debounce ([https://webclub.tistory.com/607](https://webclub.tistory.com/607))
- 만약 결과로 UI를 업데이트해야한다면, receive(on:options:)를 호출함으로써 콜백을 메인스레드에 전달할 수 있다. RunLoop 클래스에 의해 제공되는 Scheduler 인스턴스를 첫번째 파라미터로 지정함으로써 컴바인에게 subscriber를 메인 런 루프에서 호출하도록 만들 수 있다.

위 예시 케이스들의 결과는 아래와 같다.

```swift
let sub = NotificationCenter.default
    .publisher(for: NSControl.textDidChangeNotification, object: filterField)
    .map( { ($0.object as! NSTextField).stringValue } )
		/// filter에서 조건 충족 못하면 어떻게 되나?
    .filter( { $0.unicodeScalars.allSatisfy({CharacterSet.alphanumerics.contains($0)}) } )
    .debounce(for: .milliseconds(500), scheduler: RunLoop.main)
    .receive(on: RunLoop.main)
    .assign(to:\MyViewModel.filterString, on: myViewModel)
```

### Cancel Publishing when Desired

publisher는 정상적으로 완료되거나 실패할때까지 Element를 계속해서 emit한다. 더이상 publisher를 구독하고 싶지 않다면, 구독을 취소할 수 있다. sink, assign subscriber는 모두 cancel() 메소드를 제공하는 Cancellabe 프로토콜을 채택했기 때문에 구독 취소가 가능하다.

만약 커스텀 Subscriber를 만들었다면, publisher는 처음 구독할 때 Subscription 객체를 보내게 되는데 이걸 저장하고 필요할 때  Subscription 객체의 cancel() 메소드를 호출하면 된다.

커스텀 Subscriber를 만들 때, Cancellable 프로토콜을 채택해야하며, 구현한 프로토콜 메소드(cancel)가 취소 요청을 저장된 subscription으로 전달하도록 만들어야 한다.

![스크린샷 2022-09-11 오후 7.12.13.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/d402a757-48ff-4532-9110-f75c8b9a2a44/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2022-09-11_%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE_7.12.13.png)

## Publisher

### `protocol Publisher<Output, Failure>`

시간의 흐름에 따른 일련의 값을 전송할 수 있는 타입을 지정한다.

Publisher는 한개 이상의 Subscriber에게 요소를 전달할 수 있다. Publisher는 Subscriber를 수용하기 위해 `recieve(subscriber:)` 메소드를 구현해야 한다.

이후 publisher는 (매개변수로 들어온) subscriber의 아래 메소드를 호출할 수 있다:

이게 무슨말인가 했더니, Publisher를 채택해서 구현할 때 아래와 같이 subscriber의 특정 메소드를 활용해 작업을 할 수 있다는 말이었다.

![스크린샷 2022-09-11 오후 3.21.31.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/97a6a4db-3ce5-4d49-8512-dd2e9bf949af/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2022-09-11_%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE_3.21.31.png)

- `[receive(subscription:)](https://developer.apple.com/documentation/combine/subscriber/receive(subscription:))`
    
    구독 요청을 승인하고 Subscription 인스턴스를 리턴한다. subscriber는 subscription 인스턴스를 이용해 Publisher에게 element를 요구하거나 publishing을 취소할 수 있다.
    
- `[receive(_ input:)](https://developer.apple.com/documentation/combine/subscriber/receive(_:))`
    
    한 개의 element를 publisher에서 subscriber로 전달할 수 있다.
    
- `[receive(completion:)](https://developer.apple.com/documentation/combine/subscriber/receive(completion:))`
    
    subscriber에게 publishing이 종료(정상적으로 종료 또는 에러로 종료)됐다는 것을 알려줄 수 있다.
    

Publisher가 Subscriber와 함께 할 수 있는 작업은 크게 두 종류로 receive와 subscribe이다.

🤔  receive(subscriber:) 메소드는 어떤 역할을 하길래 필수로 구현해야 하는걸까?

“Attaches the specified subscriber to this publisher.”

특정 subscriber를 publisher에게 부착(attach)한다.

🤔 subscribe 메소드가 Publisher에 있는 이유는 뭘까?

receive(subscriber:) 메소드 대신 항상 subscribe() 메소드를 호출하라고 한다. 

[여기](https://icksw.tistory.com/272) 흐름도를 참고

- Publisher에는 많은 오퍼레이터가 존재한다.
    
    **mapping**
    
    map, tryMap, replaceNil, scan 등
    
    **filtering**
    
    filter, compactMap, removeDuplicates, replaceEmpty 등
    
    **reducing**
    
    collect, reduce, ignoreOutput 등
    
    **applying mathmetical**
    
    count, max, tryMax, min 등
    
    **applying matching criteria**
    
    contains, allSatisfy 등
    
    **applying sequence opration**
    
    drop, append, prepend, prfix 등
    
    **selecting specific**
    
    first, last, tryLast, output 등
    
    **Collecting and Republishing the Latest Element from multiple publishers**
    
    combineLatest 등
    
    **Republishing elements from multiple publishers as an interleaved stream**
    
    merge
    
    **Collecting and Republishing the oldest unconsumed elements from multiple publishers**
    
    zip
    
    **Republishing elements by subscribing to new Publishers**
    
    flatMap, switchToLatest
    
    **error handling**
    
    assertNoFailure, `catch`, retry 등
    
    **Controlling Timing**
    
    measureInterval, debounce, throttle, timeout 등
    
    **Encoding and Decoding**
    
    **Identifying Properties with key Paths**
    
    map
    
    기타 등등 매우 많음.
    

### `enum Publishers`

Publishers enum도 있는데 이는 namespace를 정의하기 위해 존재함.

ex. contains(_:) 오퍼레이터는 Publishers.Contains 인스턴스를 리턴함.

### `@frozen struct AnyPublisher<Output, Failure> where Failure : [Error](https://developer.apple.com/documentation/Swift/Error)`

- 다른 publisher를 래핑함으로써 타입을 지우는(type erasure) 역할을 하는 publisher이다.
- publisher의 detail한 것들을 외부에 노출하고 싶지 않을 경우 publisher를 AnyPublisher로 감싸라.
- Subject를 AnyPublisher로 래핑한 것 역시 caller라 send(_:)메소드에 접근하는 것을 막는다.
- 이런 식으로 타입을 erasure하면, 다른 클라이언트에 영향을 미치지 않고도 underlying publisher의 구현을 변경할 수 있다.
- 래핑할 때는 `eraseToAnyPublisher()` 를 사용한다.

### `@propertyWrapper struct Published<Value>`

- 속성으로 표시된 property를 publish하는 타입
- `@Published`
이걸로 프로퍼티를 publishing 할 수 있다.
    
    해당 publisher에 접근할 때는 `$` 오퍼레이터를 사용할 수 있다.
    
    class에서만 사용 가능하다.
    
    ```swift
    class SomeClass {
    	@Published var someValue: Int = 0
    }
    
    let someClass = SomeClass()
    
    //$someValue의 willSet 블록에 메소드를 추가하는 거랑 똑같다고 한다.
    //값이 setting되기 전에 해당 블록이 호출되는 것이다.
    let cancellable = someClass.$someValue
    		              .sink(receiveValue: {
    		                  print("someValue is changed:\($0)")
    		              })
    
    someClass.someValue = 10 // someValue is changed:10
    
    cancellable.cancle() // cancle() 이후에는 someVlaue 값이 변경되어도 publishing 되지 않음.
    ```
    

```swift
// 만약 아래와 같이 subscriber가 붙기 전에 여러개의 값을 입력한다면, 
// 가장 마지막에 있는 값만 subscriber에 전달된다.
someClass = SomeClass()
someClass.someValue = 15
someClass.someValue = 20
someClass.someValue = 25

someClass.$someValue
	.sink(receiveValue: {
					print("someValue is changed:\($0)")
	})

// someValue is changed:25

```

### `protocol Cancellable`

activity 또는 action이 cancellation을 지원할 수 있음을 나타내는 프로토콜

cancel()을 호출하면 할당된 리소스를 모두 해제한다. 또한 timers, 네트워크 접근, disk I/O 등의 사이드 이펙트를 모두 중지시킨다.

🤔  여기서 side effect를 어떻게 해석해야 할까?

### `final class AnyCancellable`

취소됐을 때 제공된 클로저를 실행하는 type-erasing cancellable 객체다.

Subscriber 프로토콜을 구현한 것들은 caller가 publisher를 취소할 수 있지만 Subscription 객체를 사용해 element를 요청할 수는 없도록 하는 “cancellation token”을 제공함으로써 AnyCancellable을 사용할 수 있다. 

AnyCancellable 인스턴스는 deinitialized될 때 자동으로 cancel() 메소드를 호출한다.

### `final class Future<Output, Failure> where Failure : [Error](https://developer.apple.com/documentation/Swift/Error)`

- convenience publishers
- 단일 값을 생성한 뒤 complete(finish 또는 fail) 되는 publisher
- 어떤 작업을 수행한뒤 **비동기적으로** **단일 element를 publishing하고자 할 때** future를 사용할 수 있다.
- Result 타입의 값을 받는 클로저를 이용해 초기화할 수 있는데 만약 성공했을 경우 future의 다운스트림 subscriber는 publishing stream이 정상적으로 종료되기 전에 element를 받는다. 결과가 에러일 경우 해당 에러와 함께 publishing stream이 종료된다.

### `struct Just<Output>`

subscriber에게 단 한번만 output을 방출하고 종료되는 publisher이다. fail할 수 없고 항상 값을 생산해야 함.

### `struct Deferred<DeferredPublisher> where DeferredPublisher : [Publisher](https://developer.apple.com/documentation/combine/publisher)`

제공된 클로저를 실행하여 새로운 publisher를 만들기 전에 구독(subscription)을 await한다.

### `struct Empty<Output, Failure> where Failure : [Error](https://developer.apple.com/documentation/Swift/Error)`

어떠한 값도 게시하지 않고 선택적으로 즉시 완료되는 publisher

Empty(completeImmediately: false) 를 이용해 “Never” publisher를 만들 수 있음(값을 보내지 않고 절대 종료 또는 실패하지 않는 publisher) 

🤔 왜 필요한거야?

값이 필요하지는 않은데 종료되지도 않는 경우?

### `struct Fail<Output, Failure> where Failure : [Error](https://developer.apple.com/documentation/Swift/Error)`

특정 에러와 함께 바로 종료되는 publisher

🤔 왜 필요한거야?

### `struct Record<Output, Failure> where Failure : [Error](https://developer.apple.com/documentation/Swift/Error)`

각각의 subscriber가 나중에 playback할 수 있도록 일련의 입력 및 완료를 기록할 수 있는 publisher

## ****Processing Published Elements with Subscribers****

컴바인에서 publisher는 값을 생산할 수 있고 subscriber는 자신이 받은 값에 대한 행동을 할 수 있다. 

그러나 publisher는 subscriber가 연결되고 요청하기 전까지 값을 보낼 수 없다.

subscriber는 또한 받을 수 있는 element의 양이 얼마인지를 나타내는 Subscribers.Demand 타입으로 publisher가 전달하는 element의 속도를 조절할 수 있다.

다음 두가지 방법 중 하나로 수요를 나타낼 수 있다.

- subscriber가 처음 구독했을 때 publisher가 보내주는 Subscription 인스턴스의 request(_:)를 호출함으로써.
- publisher가 element를 전달하기 위해 subscriber의 receive(_:) 메소드를 호출했을 때 새로운 demand를 리턴함으로써.

Demand는 첨가물이다 : 만약 subscriber가 2개의 element를 요구한 다음 `Subscribers.Demand(.max(3))` 를 요청한 경우 publisher가 충족하지 못한 수요는 5가 된다. 이 상태에서 publisher가 element를 보내면 충족되지 못한 수요는 4가 된다.

sink와 assign은 unlimited 수요를 가지며, publisher가 일단 unlimited 수요를 가지게 되면, subscriber와 publisher간 수요와 관련한 추가적인 협상은 없다.

🤔 수요가 있는 상태에서 unlimited 수요로 변경이 가능한건가?

****Consume Elements as the Publisher Produces Them****

publisher가 생산하는 element 사용하기

publisher가 많거나 제한이 없는 demand를 갖게 되면, subscriber가 처리할 수 있는 속도보다 훨씬 빠른 속도로 요소를 보낼 수 있다. 이러한 시나리오는 (처리하지 못해서) 삭제되는 요소가 발생할 수 있고 element가 처리를 기다리는 동안 buffer를 채우기 때문에 memory 사용량을 급속히 증가시킬 수 있다. 특히나 sink, assign과 같은 unlimited demand subscriber를 사용할 경우 발생할 가능성이 높다.

assign의 사이드 이펙트와 sink는 아래 특성들을 따라야 한다:

- publisher를 블록시키지 마라.
- element를 버퍼링하여 과도하게 memory 사용하지 마라.
- Don’t get overwhelmed and fail to process elements.
overwhelemed? 요소 처리에 실패하지 마라.

****Apply Back Pressure with a Custom Subscriber****

커스텀 subscriber로 back pressure 적용하기

publisher가 subscriber로 보내는 element의 속도를 제어하기 위해 Subscriber 프로토콜을 채택한 커스텀 subscriber를 만들어라. 위에서 언급한 “수요를 나타내는 두가지 방법"을 통해 속도를 조정할 수 있다.

subscriber가 element를 수신할 준비가 되었다는 신호를 보내 흐름을 제어하는 개념을 `back pressure` 라고 부른다.

(원래는 게시자가 게시를 자유자재로 할 수 있어야 하는데 그게 아니고, 구독자가 준비가 되었다는 것을 알렸을 때 게시를 할 수 있으므로, 제어흐름의 뒤쪽으로 요청을 보낸다는 의미에서 back pressure라고 부른게 아닐까 한다.)

각각의 publisher는 자신이 충족하지 못한 수요를 계속 트래킹하고 있다.(얼마만큼의 element가 더 필요한지를 체크)

****Manage Unlimited Demand by Using Back-Pressure Operators****

커스텀 subscriber 없이도 back-pressure를 적용할 수 있다.

- `[buffer(size:prefetch:whenFull:)](https://developer.apple.com/documentation/combine/publisher/buffer(size:prefetch:whenfull:))`
    
    업스트림 게시자로부터 전달받은 고정된 수의 아이템을 hold해둔다. 만약 다 차버리면, element를 드롭하거나 error를 던진다.
    
- `[debounce(for:scheduler:options:)](https://developer.apple.com/documentation/combine/publisher/debounce(for:scheduler:options:))`
업스트림 게시자가 지정된 시간 간격 동안 게시를 중지하는 경우에만 publishing한다.
- `[throttle(for:scheduler:latest:)](https://developer.apple.com/documentation/combine/publisher/throttle(for:scheduler:latest:))`
    
    주어진 최대 속도로? element를 생산한다. 주어진 간격 동안 여러개의 element를 수신한 경우 가장 최신 또는 가장 오래된 element만 보낸다.
    

debounce와 throttle을 자주 사용하는데 무슨 차이인지 잘 모르겠다.