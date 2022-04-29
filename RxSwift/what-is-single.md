# Single

— [https://reactivex.io/documentation/ko/single.html](https://reactivex.io/documentation/ko/single.html)

- Trait의 한 종류로, Single뿐만 아니라 Driver, Maybe, Completable 등 여러가지가 있다.
- Obesrvable의 한 형태이지만, Observable처럼 존재하지 않는 값부터 무한대까지 임의의 연속된 값을 방출하는 것과 달리 항상 한가지의 값 또는 오류 둘 중 하나만 방출한다.
- 이런 이유로 Observable이 가지고 있는 3개의 행동(next, complete, error) 대신 단 2개의 행동만 할 수 있다. (next가 없고 성공/실패 두가지만 존재함)
    
    `onSuccess` : 자신이 방출하는 하나의 값을 이 메소드를 통해 전달한다.
    
    `onError` : 항목을 방출할 수 없을 때 이 메소드를 통해 Throwable 객체를 전달한다.
    
- 위 메소드가 호출되면 Single의 생명주기는 끝나고 구독도 종료된다.