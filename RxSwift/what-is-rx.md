### 목차 
- Rx란
- Rx 동작에 필요한 요소들
    - Observable 
    - Subject
    - Operator 
    - Scheduler
    - Single
- Rx를 사용하는 이유



## Rx

- Reactive eXtension의 줄임말.
- 관찰 가능한 시퀀스(또는 stream이라 표현함)와 함수 형태의 연산자를 사용하여 비동기식 프로그램 및 이벤트 기반 프로그램을 구성하기 위한 오픈소스 라이브러리
>[[reactivex.io](https://reactivex.io)]  
An API for asynchronous programming with observable streams.   
ReactiveX is a combination of the best ideas from the `Observer pattern`, the `Iterator pattern`, and `functional programming`.
- “Reactive”의 사전적 의미
    - 반응을 하는, 반응하는
    - [여기](https://magi82.github.io/ios-rxswift-01/)에서 언급한 방식이 제일 이해가 잘 갔음!
        >`~한다면, ~라면`
        버튼이 눌린다면, 텍스트 필드에 값이 들어간다면, 서버 통신을 완료했다면, 이벤트가 들어온다면 등등   

        >혹은 엑셀에서 수식을 작성해놓으면 관련된 cell이 바뀔때마다 값이 변경되는데 이런 예시도 생각해볼 수 있음.
        
- Observer pattern : 반응을 하기 위해서는 버튼이 눌리는지, 값이 들어갔는지, 서버 통신 완료가 됐는지 등을 **관찰**할 수 있어야 함. Observer pattern을 관찰한다 그 자체임.
- MS에서 만든 라이브러리
- Rx가 나오기 전까지는 DispatchQueue를 이용해 비동기 함수를 구현했는데 기능이 복잡해질 수록 코드 가독성이 떨어지고 복잡해진다는 문제가 있었다고 함.

## Rx 동작에 필요한 요소들
[reactivex.io](reactivex.io) 홈페이지를 보면 Docs가 총 5개의 항목(Observable, Operators, Single, Subject, Scheduler)으로 나뉘어져 있다. 아마도 가장 필수적인 요소들이기 때문에 Rx 공식 홈페이지의 Docs를 구성하지 않았을까 한다. 따라서 여기에서도 5개의 항목부터 공부에 들어가도록 하겠음.
- ### [Observable](./what-is-observable.md)   
    Observer가 데이터를 관찰하는 방법을 제공하는 제네릭 타입. 3 종류의 이벤트를 방출(emit)할 수 있는데 next, error, complete이 그것이다.
- ### [Subject](./what-is-observable.md)
    Observable이면서 Observer 즉, 데이터의 변화를 관찰할 수 있도록 다른 observable 구독이 가능하면서 동시에 자신을 구독한 observer에게 이벤트를 방출할 수 있다.
- ### [Operator](./what-is-operator.md)
    ObservableType과 Observable 클래스가 가지고 있는 다양한 메소드들을 operator라고 한다. 이전 값이 다음 메소드의 input으로 동작한다.
- ### [Schedulers](./what-is-schedulers.md)
    어떤 스레드에서 operator를 실행할지 결정한다.
- ### [Single](./what-is-single.md)
    *TODO*
    



## Rx를 왜 사용해야 할까?
🤛 *TODO : https://github.com/ReactiveX/RxSwift/blob/main/Documentation/Why.md*

---
**👀  참고 사이트**
- [https://daesiker.tistory.com/95](https://daesiker.tistory.com/95)
- [https://magi82.github.io/ios-rxswift-01/](https://magi82.github.io/ios-rxswift-01/)
- [https://magi82.github.io/ios-rxswift-02/](https://magi82.github.io/ios-rxswift-02/)
- [https://ios-development.tistory.com/95](https://ios-development.tistory.com/95)