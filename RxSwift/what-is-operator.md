# Operator

- 사전적 정의 : 운영자, 연산자
- ObservableType과 Observable 클래스에는 복잡한 논리를 구현하기 위해 많은 메소드가 포함되어 있는데 이것들을 Operator라고 부른다.
- 비동기 입력을 받아 출력만 생성한다. 따라서 Operator들끼리 혼합해서 사용할 수 있다.
- 이전 연산자에서 나온 출력을 다음 연산자의 입력으로 활용할 수 있다. → 이런 부분에서 Side Effect를 발생시킨다고 언급했나봄(아래에서)
- 여러개가 있는데 그 중 하나인 `subscribe`를 알아보자!
    - `subscribe`는 operator 중 하나로, Observable의 stream 변화에 대한 알림을 받기 위해 해당 Observable을 구독하는 행위임.
    - `subscribe`는 `disposable`이라는 객체를 리턴해야함.
    - disposable이란?
        
        disposable은 “일회용의, 사용 후 버릴 수 있는, 처분할 수 있는” 이라는 의미를 가지고 있음.
        
        (내 추측) 아무래도 계속 관찰을 하고 있어야 하기 때문에 참조타입인 것 같은데 이 경우 메모리 관리에 어려움이 있을 수 있음. 이때 dispose메소드를 호출하거나 혹은 disposeBag을 활용해 편리하게 메모리 해제를 할 수 있다.
        
    
- subscribe이외에도 interval, just, take 등 다양한 operator가 있다.