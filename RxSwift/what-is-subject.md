# Subject
— [https://rhammer.tistory.com/289](https://rhammer.tistory.com/289)

— [https://ntomios.tistory.com/12](https://ntomios.tistory.com/12)

- 사전적 의미 : 주제, 받게 하다, 종속시키다, 받아야 할, ~을 조건으로 하여 등등 매우 많음
- Observable과 Observer 역할을 동시에 수행한다.
    
    `Observable`은 ~~next, complete, error 이벤트를 emit(방출)하는 것~~ 비동기 데이터 스트림이고, `Observer`는 관찰자
    
    **🤔 데이터 스트림이면서 동시에 관찰자 역할(subscriber)을 수행한다? 왜 이런걸 만들었을까?**
    
- 사용하는 이유
    
    [여기](https://ntomios.tistory.com/12)를 보면 아래와 같은 설명이 나와있다.
    
    > 쉽게 말하면, 이벤트를 발행(emit)할 수도 있으며 구독(subscribe)할 수도 있다는 의미입니다.
    이와 같은 특성을 이용하여, view의 User Interaction을 viewModel의 Subject가 구독하고, 구독을 통해 발행된 값을 바탕으로 viewModel에서 비즈니스 로직을 거쳐 가공된 데이터를 View가 구독함으로써 연결다리 역할을 할 수 있습니다.
    > 
    
    😲 
    
    아직 완벽히 이해했다고 할 순 없지만 왜 쓰는지는 조금 알게 되었음. 하지만 정리 필요함.
    
- cold observable과 hot observable?

**Subject의 종류**

종류를 나누는 기준은 subscriber가 새롭게 등록됐을 때 '어떤 시점에서부터 이벤트를 받을 것인가'라고 이해함.

- `PublishSubject`   
    - subscriber(observer랑 subscriber는 서로 다른건가?)는 구독한 시점 이후에 방출된 데이터만 전달받는다. 
    - 초기값 없이 생성 가능하다. (단, complete, error 이벤트는 emit함?)

- `BehaviorSubject`   
    - publishSubject와 유사하나, 다른점이 있다면 publish는 구독한 이후의 이벤트만 전달받을 수 있으나 behavior은 구독 전 가장 최신의 next 이벤트를 하나 전달받을 수 있다.
    - subscription이 생성됨과 동시에 반드시 최신 이벤트를 emit해야하기 때문에 반드시 초기값을 설정해야한다.

- `ReplaySubject`
    - behaviorSubject와 유사하나 차이점이 하나 있다면, behavior는 딱 한개의 최신 이벤트를 받지만, replay는 내가 받고 싶은 만큼 설정할 수 있다. 최신 이벤트 여러개를 캐싱하고 있다가 새로운 subscriber에게 한번에 전달한다.

- `Variable`
    - behavior subject를 wrapping함. 
    - value 프로퍼티를 통해 subject의 현재 값을 얻을 수 있고, 새로운 값도 추가할 수 있다. 
    - onNext()메소드를 사용하지 않음.
    - variable에 `asObservable()`을 사용하면 subject 그 자체에 접근할 수 있다.(variable 자체는 subject가 아니므로 이렇게 접근해서 subscribe해야함) 
    - subject와의 추가적인 차이가 있다면 subject와 달리 error를 emit하지 않는 것 그리고 메모리에서 해제될 때 자동으로 complete 이벤트를 발생시킨다.
    - deprecated될 예정이라고..

- `AsyncSubject`
    - 여기에 추가적으로 공식문서에서는 AsyncSubject도 소개하고 있다. 
    - 이는 구독한 시점과 상관없이 complete되기 직전 값 딱 하나만을 방출받게 되는 것을 의미한다. 만약 error로 종료될 경우 completed가 호출되지 않기 때문에 어떠한 값도 받을 수 없다.

<br>

**RxRelay에 대해 잠깐 살펴보도록 하자.**
- Relay는 Subject의 wrapper 클래스이며 dispose되기 전까지 종료되지 않는다.(UI 이벤트 방출 시 용이하게 사용할 수 있음)
    - Subject는 complete, error를 통해 dispose되기 전에 종료가 가능하다.
- onNext() 대신 accept() 메소드로 element를 전달받아 subscriber에게 방출한다.
- PublishRelay와 BehaviorRelay가 있으며 BehaviorRelay가 value를 통해 가장 마지막에 방출된 값에 접근할 수 있다.

<br>

**Subject와 Observable의 차이**

- 가장 큰 차이는 unicast냐 multicast냐의 차이다.
    
    Subject는 여러 observer에 의해 구독되어질 수 있기 때문에 한번에 여러 observer에게 이벤트를 emit할 수 있다.
    
    하지만 Single을 포함한 일반적인 Observable은 unicast라 딱 하나의 Observer에게만 이벤트를 emit 할 수 있다.
    
- observable도 여러번 구독되어질 수 있다. 다만, 구독되어질 때마다 새롭게 Observable이 생성됨.
- 둘을 코드상으로 비교해보면 사실 별다른 차이가 없다.
    
    ```swift
    // Observable
    let random1 = Observable<Int>.create{ observer in
    	observer.onNext(Int.random(in: 0 ..< 100))
    	return Disposables.create()
    }
        
    random1.subscribe(onNext: { (element) in
    	print("observer 1 : \(element)")       // observer 1 : 23
    })
    random1.subscribe(onNext: { (element) in
    	print("observer 2 : \(element)")       // observer 2 : 56
    })
    
    // Subject
    let random2 = PublishSubject<Int>()
    random2.on(.next(Int.random(in: 0..<100)))
        
    random2.subscribe(onNext: { (element) in
    	print("observer subejct 1 : \(element)") // observer subject 1 : 92
    })
    random2.subscribe(onNext: { (element) in
    	print("observer subject 2 : \(element)") // observer subject 1 : 92
    })
    ```

# Relay

[https://nacho.tistory.com/21](https://nacho.tistory.com/21)

- RxCocoa 안에 있다. Subject의 wrapper 클래스로 두가지 종류(PublishRelay, BehaviorRelay)가 있다. 각각 PublishSubject, BehaviorSubject에 대응됨.
- Subject와의 차이는 두가지가 있는데 1) completed되지 않음 2) error를 방출하지 않음 이다. Dispose 되기 전까지는 계속 작동한다.
- 그래서 Relay는 UI작업을 하기 적합하다고 함
    - RxCocoa 안에 있다고 하니 UI작업과 관련해 만들어졌다는 것은 알겠음 ㅇㅇ
    - [여기](https://jinshine.github.io/2019/01/05/RxSwift/3.Subject%EB%9E%80/) → completed, error 발생 시 바로 subscribe가 종료되는 반면 relay는 error, completed를 발생시키지 않고 dispose되기 전까지 계속 동작하기 때문에 UI event에서 적절하다고 하는 것 같다.)
- relay는 이벤트를 방출할 때 onNext 대신 accept를 사용한다.