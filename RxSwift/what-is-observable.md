# Observable
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
- Observable의 3가지 행동규칙
    
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