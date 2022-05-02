# Observable
<details>
<summary>처음 공부한 내용</summary>
<div markdown="1">
- 사전적 정의 : 관찰 가능한
- 제네릭 "타입"이다.
- Swift에서 어떤 정수형 프로퍼티를 관찰하겠다고 한다면, Property Observer를 활용할 수 있을 것이다.
    
    ```swift
    var number: Int = 0 {
    	willSet { /* number에 값이 세팅되기 직전 */ }
    	didSet {  /* number에 값이 세팅되고 난 이후 */ }
    }
    ```
    
    Rx에서도 별도로 “관찰하는 방법"이 만들어져 있는데 그게 바로 `Observable`이다.
    
    > *ReactiveX에서 Observer는 Observable을 구독한다. Observable이 방출(emit)하는 하나 또는 연속된 항목에 Observer는 반응한다.*
    > 
- ~~Observable의 3가지 행동규칙~~ 아래 내용은 Observable의 행동 규칙이라고 보긴 어려운 것 같다. ObserverType 프로토콜이 가지고 있는 메소드들임.
    
    `next` : 하나 또는 연속된 항목을 방출하는 행동
    
    `complete` : Observable이 더이상 next를 방출하지 않으면, 즉 모든 값을 다 방출하면 complete이 된다.
    
    `error` : 값을 방출하다 error가 발생하는 경우임. error를 방출한 후 Observable은 스트림을 멈추게 된다. error가 발생하면 complete이 호출되는게 아님. 바로 그 자리에서 중단되는 것임.
    

![https://magi82.github.io/images/2018-4-6-ios-rxswift-02/1.png](https://magi82.github.io/images/2018-4-6-ios-rxswift-02/1.png)

```swift
func checkArrayObservable(items: [Int]) -> Observable<Int> {
// Observable의 create : observer를 매개변수로 받고 Disposable 객체를 리턴하는 클로저를 받음.
	return Observable<Int>.create { observer -> Disposable in
		for item in items {

			if item == .zero {
				observer.onError(NSError(domain: "Error!", code: 0, userInfo : nil))
				break
			}
			
			observer.onNext(item)			
			
			sleep(1)
		}
	
		observer.onCompleted()
		return Disposables.create()
	}
}

checkArrayObservable(items: [4, 3, 1, 2, 0, 5])
	.subscribe { event in
		switch event {
		case .next(let value):
			print(value)
		case .error(let error):
			print(error)
		case .completed:
			print("completed")
		}
	}
	.disposed(by: self.disposeBag)

```
</div>
</details>

**2022.05.01 ~ 2022.05.02 내용 추가**   

[예제 프로젝트 1](https://github.com/NohEunTae/RxMVVMSample)에 사용된 RxSwift 함수들에 대해 보고 있다. 두개의 Observable을 합치는 `combineLatest`에 대해 보면서 내가 아직 Observable에 대해 이해가 되지 않았다는 것을 깨달음.   
왜냐하면 내가 이해한 Observable은 "**Rx에서 값의 변화를 관찰할 수 있는 방법이며 이벤트를 내보낸다.**"인데 이걸 `Observable.combineLatest(a, b) { $0 + $1 }` 이렇게 사용한다는 개념이 너무너무 와닿지가 않았다. Observable은 메소드가 아닌건가..? 어떻게 이걸 더해..? 라는 의문.   
그리고 위의 정의대로라면 이미 이벤트를 내보내고 있기 때문에 구독을 할 필요가 없는 것 같은데 왜 subscribe를 또 하는거지?? 라는 의문도...ㅎㅎ   
그래서 다시 보게 된 Observable!   

👀 도움이 됐던 링크 ✨ 
- [RxSwift 기본 익히기 (1) — 10 분짜리 (updated)](https://trilliwon.medium.com/rxswift-기본-익히기-1-d4d77ce63ca8)   
- [Reactive Programming? Functional Reactive Programming? Reactive Extensions?](https://brunch.co.kr/@oemilk/79)

---
## Observables aka Sequences
- 우선 Rx와 Reactive Programming에 대해 다시 짚고 넘어가자면...
	- Rx는 Reactive Programming을 하기 위한 라이브러리
	- Reactive Programming이라는 것은 <u>비동기 데이터 스트림</u>을 이용한 프로그래밍을 말한다.
	- Rx에서는 <u>비동기 데이터 스트림을 Observable</u>이라는 용어로 표현한다.
	- 아니 그럼 비동기 데이터 스트림은 도대체 뭐임??
		>🔗 [[oreilly] Asynchronous data streams](https://www.oreilly.com/library/view/architecting-angular-applications/9781787122406/8ac189ca-a3e4-4376-8a9c-aae8a60cf5df.xhtml)   
		Asynchronous data streams is a stream of data where values are emitted, one after another, with a delay between them.   
		The word asynchronous means that the data emitted can appear anywhere in time, after one second or even after two minutes, for example.
		>
		- 값이 차례로 방출되는 데이터 스트림인데 방출되는 값 사이에 약간의 delay가 있다. 비동기라는 단어의 의미는 예를 들어 1초 후 또는 (심지어) 2분 후에도 방출된 데이터가 나타날 수 있다는 의미다.
		- 데이터 스트림이란 계속 입력되는 작은 데이터 조각의 연속을 의미한다. 데이터가 연속적으로 들어온다는 의미임.

- Sequence는 순차적이고 반복적으로 각각의 element에 접근가능하도록 디자인된 데이터 타입이다.
- RxSwift의 Observable은 Sequence와 동등한 개념이다. 다만 "비동기"라는 점이 다름.
- Array, String과 같은 Sequence는 RxSwift에서 Observable이 된다. Swift의 [Sequence Protocol](https://developer.apple.com/documentation/swift/sequence)을 따르는 모든 Object들은 Observable로 만들 수 있다.

	```swift
	let stringSequence = Observable.just("this is string")
	let oddSequence = Observable.from([1, 3, 5, 7, 9])
	let dictSequence = Observable.from([1: "Rx", 2: "Swift"])
	```
- 위에 사용된 just, from은 모두 새로운 Observable을 생성(creating)할 수 있는 operator다.   
just : 하나의 element를 Observable로 만들고 리턴   
from : array, dictionary, set을 Observable sequence로 만들고 리턴   

---

## Observable 사용해보기
```Swift
import UIKit
import RxSwift

let stringSeq = Observable.just("this is string")
let oddSeq = Observable.from([1, 3, 5, 7, 9])
let dictSeq = Observable.from([1: "Rx", 2: "Swift"])

var disposeBag = DisposeBag()

// subscribe : Subscribes an event handler to an observable sequence.
// func subscribe(_ on: @escaping (Event<Element>) -> Void) -> Disposable
// 아직 escaping 클로저에 대해 잘 모르는 것 같다.
// subscribe 함수의 이스케이핑 클로저 매개변수로 들어가는 Event에 대해 : 제네릭 열거형 타입으로 next(Element), error(Swift.Error), completed case를 가지고 있다.
let subscription = stringSeq.subscribe { event in
    print(event)
}

let subscription2 = oddSeq.subscribe { event in
    print(event)
}

// 열거형 switch의 let에 대해서도 공부하자.
let subscription3 = dictSeq.subscribe { event in
    switch event {
    case let .next(element):
        print(element.key)
        print(element.value)
    case let .error(err):
        print(err)
    case .completed:
        print("completed")
    }
    
}

// subscription 상수 정의하면서 `.dispose()`를 함께 붙이면 subscription 상수의 타입은 ()
// .dispose()를 안붙이면 subscription 상수의 타입은 Disposable
// .disposeBag() 붙이면 상수의 타입은 ()
// 무슨차이?
//print(subscription)

// create 함수를 통해 Observable을 새롭게 만들 수도 있다.
// [https://reactivex.io/documentation/operators/create.html#collapseRxSwift]
// static func create(_ subscribe: @escaping (AnyObserver<Element>) -> Disposable) -> Observable<Element>
func myJust<E>(element: E) -> Observable<E> {
    return Observable.create { observer in
        observer.onNext(element)
        observer.onCompleted()
        return Disposables.create()
    }
}

myJust(element: [1, 2, 3, 4, 5])
    .subscribe { event in
        print(event)
    }

let source: Observable = Observable<Int>.create { observer in
    for i in 1...5 {
        observer.on(.next(i))
    }
    observer.on(.completed)
    
    return Disposables.create {
        print("disposed")
    }
}
source.subscribe {
    print($0)
}



// generate : 다음 내보내어질 값을 생성(generate)하고 이전에 방출된 값을 토대로 종료조건을 설정할 수 있는 Observable sequence를 만들 수 있다.
// 매개변수는 3가지임.
//    initialState : 방출할 첫번째 값
//    condition : 방출할지 혹은 종료할지를 결정할 함수
//    iterate : 이전에 방출된 값을 토대로 다음에 방출할 값을 만드는 함수
// https://reactivex.io/documentation/operators/create.html#collapseRxSwift
Observable.generate(
    initialState: 1,
    condition: { $0 < 10 },
    iterate: { $0 + 3 }
)
.subscribe(onNext: { print($0) })
.disposed(by: disposeBag)


/*
----- 
empty, never, error는 매우 구체적이고 제한된 행동을 하는 Observable을 생성하는 Operator들이다.
테스팅의 목적으로 혹은 다른 Observable과의 combine, 다른 Observable을 매개변수로 기대하는 연산자에 대한 매개변수로도 사용될 수 있음.

https://reactivex.io/documentation/operators/empty-never-throw.html
-----
*/

// never : 어떤 아이템도 내보내지 않고 종료되지도 않는 Observable을 만든다.
let neverSeq = Observable<String>.never()
let neverSeqSubscription = neverSeq
    .subscribe { event in
        print(event)
        print("This will never be printed")
}
neverSeqSubscription.disposed(by: disposeBag)

// empty : 어떤 아이템도 내보내지 않지만 정상적으로 종료됨. 따라서 onCompleted는 호출됨.
Observable<Int>.empty()
    .subscribe { event in
        print(event)
    }
    .disposed(by: disposeBag)

// error : 어떤 아이템도 내보내지 않고 error로 인해 종료되는 Observable을 만듬.
enum TestError: Error {
    case test
}
Observable<Int>.error(TestError.test)
    .subscribe { print($0) }
    .disposed(by: disposeBag)


// of : 가변 길이의 element를 이용해 새로운 Observable 인스턴스를 생성하는 메소드
// This method creates a new Observable instance with a variable number of elements.
Observable.of("🐶", "🐱", "🐭", "🐹")
    .subscribe { event in
        print(event)
    }
    .disposed(by: disposeBag)

// range : 정해진 range의 정수에 해당하는 Observable sequence를 만듬.
// Generates an observable sequence of integral numbers within a specified range, using the specified scheduler to generate and send out observer messages.
Observable.range(start: 1, count: 3)
    .subscribe { event in
        print(event)
    }
    .disposed(by: disposeBag)

// repeatElement : 주어진 element를 무한대로 반복하는 Observable sequence를 만듬.
Observable.repeatElement("✨")
    .take(3) // 이걸 추가해주지 않으면 무한대로 Element가 호출됨!!
    .subscribe(onNext: { print($0) })
    .disposed(by: disposeBag)



/*
https://reactivex.io/documentation/operators/defer.html
deferred
- Observer가 구독하기 전까지 Observable을 생성하지 않는다. 각 Observer별로 새로운 Observable을 생성한다.(? 원래 기본은 각 Observer별로 동일한 Observable을 생성하는 건가?)
- Observer가 구독을 하면 "Observable factory function"을 이용해 새로운 Observable을 만든다.
- 각 Observer별로 새로 작업을 수행하기 때문에 각각의 Observer가 동일한 Observable을 구독하고 있다고 생각할 수 있지만 실제로는 각각이 고유한 개별 sequence를 가지고 있게 된다.
- Returns an observable sequence that invokes the specified factory function whenever a new observer subscribes.
*/
var count = 1
let deferredSeq = Observable<String>.deferred {
    print("Creating \(count)")
    count += 1
    
    return Observable.create { observer in
        print("Emitting...")
        observer.onNext("🐶")
        observer.onNext("🐱")
        observer.onNext("🐵")
        return Disposables.create()
    }
}

//Create 1
deferredSeq
    .subscribe(onNext: { print($0) })
    .disposed(by: disposeBag)

//Create 2
deferredSeq
    .subscribe(onNext: { print($0) })
    .disposed(by: disposeBag)

/*
https://reactivex.io/documentation/operators/do.html

do : 다양한 Observable 라이프사이클 이벤트에 대해 수행할 작업을 등록한다.
Invokes an action for each event in the observable sequence, and propagates all observer messages through the result sequence.

콘솔에 찍힌것을 확인해보면,
do->onSubscribe 호출
do->onSubscribed 호출
do->onNext 호출
subscribe->onNext 호출
do->afterNext 호출
*/
Observable.of("🍎", "🍐", "🍊", "🍋")
    .do(onNext: 		{ print("onNext : \($0)") },
        afterNext: 		{ print("after next : \($0)") },
        onError: 		{ print($0) },
        afterError: 	{ print("after error : \($0)") },
        onCompleted:	{ print("completed!") },
        afterCompleted: { print("after completed!") },
        onSubscribe: 	{ print("onSubscribe!") },
        onSubscribed: 	{ print("onSubscribed?? 구독이후?") },
        onDispose: 		{ print("onDispose") }
	)
    .subscribe(
		onNext: 	 { print("subscribe:\($0)") }, 
		onCompleted: { print("subscribe completed!") }
	)
    .disposed(by: disposeBag)


```