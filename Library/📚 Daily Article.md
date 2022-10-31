## Swift
#### [스위프트로 다시보는 객체지향 프로그래밍: 피해야 할 코딩습관](https://soojin.ro/blog/solid-principles-in-swift)
- 2022년 10월 31일
- OCP를 지키기 위해서는 같은 타입(특히 enum)에 대해 if/switch문의 사용을 줄여야 한다. 여기에는 두가지 방법이 있는데 하나는 protocol을 사용하는 것(OCP를 직접적으로 지키는 방법, 다형성을 이용함, POP)이고 다른 하나는 딕셔너리를 사용하는 것(default 절이 있는 switch문과 동일한 약점이 있음, case가 추가되면 dictionary 변수 수정 불가피)이다. 
- protocol을 사용해 enum을 리팩토링 하는 방법은 [Refactoring: Replace Enum with Polymorphism](https://www.jessesquires.com/blog/2016/07/31/enums-as-configs/)을 참고
    - 프로토콜을 이용하는 방법은 다형성을 이용한 방법이며, enum으로 구현된 부분 중에서도 자주 바뀌는 것과 그렇지 않은 것을 구분하여 자주 바뀌는 것은 protocol 선언 및 채택할 수 있도록 하고 그렇지 않은 것은 그대로 enum을 사용하도록 만들 수 있다. enum은 case가 추가될 가능성이 적거나 [ADT](https://en.wikipedia.org/wiki/Abstract_data_type) 를 만들때 적절하다고 필자는 말하고 있다.
    - Further reading : [Enums as configuration: the anti-pattern](https://www.jessesquires.com/blog/2016/07/31/enums-as-configs/) -> 이건 아직 안 봄.