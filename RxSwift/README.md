### 📚  Article

- RxSwift 개념 파악하기(with 한국어)
    - [https://ios-development.tistory.com/95](https://ios-development.tistory.com/95)
    - [https://magi82.github.io/ios-rxswift-01/](https://magi82.github.io/ios-rxswift-01/)
- RxSwift 개념 파악하기(with 영어)
    - [RxSwift github - Why Use Rx?](https://github.com/ReactiveX/RxSwift/blob/main/Documentation/Why.md)
    - [RxSwift github - GettingStarted.md](https://github.com/ReactiveX/RxSwift/blob/main/Documentation/GettingStarted.md)

### 📖  Book

[RxSwift: Reactive Programming with Swift](https://www.raywenderlich.com/books/rxswift-reactive-programming-with-swift/v4.0)

---

### 📝  스터디 계획
1. Rx와 Observable, Subject의 기본 개념을 잡는다.
2. 예제 프로젝트를 보면서 거기에 사용되는 operator나 기타 개념들이 나올 때마다 검색한다.   
    - [예제프로젝트1](./example-project-01.md)   
    - https://github.com/DroidsOnRoids/RxSwiftExamples
    - https://github.com/marinbenc/ReactiveWeatherExample
    
3. 책은 틈틈이 읽는다.

---

[Rx란 무엇인가?](./what-is-rx.md)

*— Rx의 의미와 4가지 구성요소*

- Rx는 반응형 프로그래밍 라이브러리다.
- Rx의 4가지 요소로 Observable, Subject, Operator, Scheduler가 있다.
- 반응형은 어떤 것을 관찰하고 있다가 변화가 발생하면 그 변화에 반응해 행동한다는 의미이다.
- 무엇을 통해 관찰 할 수 있을까? Swift에 KVO나 property observer가 있는 것처럼 Rx는 Observable을 통해 관찰할 수 있다.
- Observable은 “관찰 가능한”이라는 의미를 가지고 있으며 (이해하기로는) 데이터를 observer에게 방출하는 역할을 한다.
이때 데이터를 3가지 이벤트(next, error, complete) 중 하나로 wrapping해 방출하며 observer에게 어떤 처리를 해야할지를 알려줌. unicast임.
- observer는 observable을 구독(subscribe)함으로써 관찰이 가능하다.
- Subject는 Observable이자 Observer로서 다른 Observer에 의해 구독되어 질 수도 있고, 자기자신이 다른 Observable을 구독할 수도 있다. multicast 가능.
