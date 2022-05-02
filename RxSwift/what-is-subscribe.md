# Subscribe
— [https://reactivex.io/documentation/ko/operators/subscribe.html](https://reactivex.io/documentation/ko/operators/subscribe.html)

> operate upon the emissions and notifications from an Observable   
Observable로부터 온 방출과 알림에 따라 작동하는 오퍼레이터.
> 
- subscribe operator는 <u>observer를 Observable에 연결시키는 glue(풀)</u>다.
- observer가 Observable에 의해 내보내진 아이템들을 보기 위해서 또는 Observable로부터 error나 completed notification을 받기 위해서, observer는 이 operator를 이용해 반드시 Observable을 구독해야한다.
- subscribe operator의 일반적인 구현은 한개 ~ 3개의 메소드를 받거나 아래 3개의 메소드를 포함한 인터페이스를 구현한 객체(문서를 읽다보면 Observer나 Subscriber라고 불리는 것들이 있는데 그걸 말함)를 받음으로써 만들어진다.
    - `onNext` : Observable이 아이템을 내보낼 때 호출됨. 이 메소드는 Observable이 내보낸 항목을 매개변수로 사용함.
    - `onError` : Observable은 예상되는 데이터를 생성하는데 실패하거나 또는 어떤 에러가 발생했다는 것을 나타내기 위해 이 메소드를 호출한다. 이 메소드는 Observable을 중지시키며 더이상 onNext나 onCompleted를 호출하지 않는다. onError 메소드는 에러의 원인이 무엇인지를 가리키는 것을 매개변수로 받는다. (구현에 따라 단순히 string일 수도 있고, Exception이나 Throwable일 수도 있다)
    - `onCompleted` : 오류가 발생하지 않은 경우 마지막으로 onNext를 호출한 후 이 메소드를 호출한다.

- 이걸 왜 별도로 뺐냐. 오늘(2022.05.02) Observable을 만드는 예제코드를 보다보니 Observable에다가 직접 subscribe를 하는게 좀 신기하다고 생각했음. 
- 원래는 Observer가 Observable을 subscribe해야 하잖아🤔 
- 근데 Observer를 명시적으로 만들어주지 않았는데, Observable만으로도 Observing이 된다고??   
    → [https://jusung.github.io/Subscribe/](https://jusung.github.io/Subscribe/)를 확인해보자.
    