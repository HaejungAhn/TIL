## Actor
- 참고한 아티클: [Actors in Swift: how to use and prevent data races](https://www.avanderlee.com/swift/actors/)
- 더 자세한 사항은 [Actor와 관련하여 WWDC 영상을 정리한 내용](https://github.com/HaejungAhn/TIL/blob/main/Swift%20Concurrency/WWDC/03-protect-mutable-state-with-swift-actors.md)을 참고하면 좋을 것 같다.

<br>

Swift 5.5부터 도입되었고 <u>data race를 완벽하게 해결하는 것</u>이 Actors가 만들어진 이유다. <br>
하지만 actor를 사용한다고 해서 완벽하게 data race 문제를 막을 수 있는 것은 아니기 때문에 언제 이러한 문제가 발생하는지도 함께 알아두는 것이 중요하다.

<br>

### Actor의 특징
- Actor는 <u>data race로부터 자신이 가지고 있는 state를 보호</u>한다. 또한 개발을 하는 동안 컴파일러로부터 data race 예방을 위한 유용한 피드백들을 제공받을 수 있다. 이와 더불어 <u>mutable data에 대한 concurrent한 접근을 막을 수도 있다.</u>
- class와 유사하다.
    - reference type이고 메소드, 프로퍼티, 서브스크립트, 이니셜라이저를 가질 수 있다는 점 등에서 
- class와의 차이점
    - actor type은 data 접근을 isolating(격리)시킨다.
        - Actor는 자신이 가지고 있는 isolated data에 대한 동기화된 접근을 생성함으로써 data race를 막는다.
    - actor type은 상속을 지원하지 않는다.
        - 따라서 convenience/required 이니셜라이저, overriding, class memeger, open/final 사용할 수 없다.
    
<br>

### Actor는 어떻게 Data Race 발생을 막을까?
- actor 전에는 [dispatchQueue와 barrier](https://github.com/HaejungAhn/TIL/blob/main/Swift/gcd.md)를 사용하여 쓰기 access를 핸들링했다.
    ```Swift
    final class ChickenFeederWithQueue {
        let food = "worms"
        
        /// A combination of a private backing property and a computed property allows for synchronized access.
        private var _numberOfEatingChickens: Int = 0
        var numberOfEatingChickens: Int {
            queue.sync {
                _numberOfEatingChickens
            }
        }
        
        /// A concurrent queue to allow multiple reads at once.
        private var queue = DispatchQueue(label: "chicken.feeder.queue", attributes: .concurrent)
        
        func chickenStartsEating() {
            /// Using a barrier to stop reads while writing
            queue.sync(flags: .barrier) {
                _numberOfEatingChickens += 1
            }
        }
        
        func chickenStopsEating() {
            /// Using a barrier to stop reads while writing
            queue.sync(flags: .barrier) {
                _numberOfEatingChickens -= 1
            }
        }

    }
    ```
<br>

- actor의 경우 매우 심플해짐.
    ```Swift
    actor ChickenFeeder {
        let food = "worms"
        var numberOfEatingChickens: Int = 0
        
        func chickenStartsEating() {
            numberOfEatingChickens += 1
        }
        
        func chickenStopsEating() {
            numberOfEatingChickens -= 1
        }
    }
    ```
- 그냥 클래스를 선언하듯 actor를 만들어주면 된다. 다만, actor 외부에서 프로퍼티나 메소드를 사용할 때는 일반 클래스의 그것과는 조금 다르게 사용해야 한다.
- 우선 immutable한 프로퍼티의 경우 actor 외부에서 접근이 자유롭다. (위 코드의 food 프로퍼티)<br>
왜냐하면 data race를 일으키지 않기 때문이다. 따라서 접근할 때 아래와 같이 사용이 가능하다.
    ```Swift
    let feeder = ChickenFeeder()
    print(feeder.food)
    ```
- 그러나 mutable한 프로퍼티 또는 이를 핸들링하는 메소드는 actor 외부에서의 접근이 자유롭지 못하다. data race를 발생시킬 여지가 있기 때문이다. 외부에서 mutable한 데이터에 접근하기 위해서는 sequential하게 접근하는게 필요하다.<br>
언제 mutable data에 접근할 수 있을지 확실하지 않기 때문에 async/await을 이용해 비동기적으로 접근해야한다.
    ```Swift
    let feeder = ChickenFeeder()
    await feeder.chickenStartsEating()
    print(await feeder.numberOfEatingChickens)
    ```

- 만약 actor 내에 정의되어 있는 프로퍼티나 메소드인데 isolated하게 접근할 필요가 없다면 `nonisolated` 키워드를 붙여주면 된다. 해당 키워드는 메소드, computed property에 붙일 수 있다. (단, immutable한 프로퍼티에는 붙일 필요가 없음.)

<br>

### Actor를 사용하더라도 data race가 발생하는 이유
```Swift
queueOne.async {
    await feeder.chickenStartsEating()
}
queueTwo.async {
    print(await feeder.numberOfEatingChickens)
} 
```
위와 같은 코드에서 발생할 수 있는 race condition : "둘 중 어떤 스레드가 먼저 isolated 접근을 먼저 시작할 것인가?"<br>
위 상황에서는 두가지 결과가 나올 수 있다.<br>

- `queueOne`이 먼저 시작되어 `numberOfEatingChickens`가 1 증가한다. 이후 `queueTwo`가 1을 print한다.
- `queueTwo`가 먼저 시작되어 0을 print한다.

