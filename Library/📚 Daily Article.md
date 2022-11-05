### [스위프트로 다시보는 객체지향 프로그래밍: 피해야 할 코딩습관](https://soojin.ro/blog/solid-principles-in-swift)
- 2022년 10월 31일
- OCP를 지키기 위해서는 같은 타입(특히 enum)에 대해 if/switch문의 사용을 줄여야 한다. 여기에는 두가지 방법이 있는데 하나는 protocol을 사용하는 것(OCP를 직접적으로 지키는 방법, 다형성을 이용함, POP)이고 다른 하나는 딕셔너리를 사용하는 것(default 절이 있는 switch문과 동일한 약점이 있음, case가 추가되면 dictionary 변수 수정 불가피)이다. 
- protocol을 사용해 enum을 리팩토링 하는 방법은 [Refactoring: Replace Enum with Polymorphism](https://www.jessesquires.com/blog/2016/07/31/enums-as-configs/)을 참고
    - 프로토콜을 이용하는 방법은 다형성을 이용한 방법이며, enum으로 구현된 부분 중에서도 자주 바뀌는 것과 그렇지 않은 것을 구분하여 자주 바뀌는 것은 protocol 선언 및 채택할 수 있도록 하고 그렇지 않은 것은 그대로 enum을 사용하도록 만들 수 있다. enum은 case가 추가될 가능성이 적거나 [ADT](https://en.wikipedia.org/wiki/Abstract_data_type) 를 만들때 적절하다고 필자는 말하고 있다.
    - Further reading : [Enums as configuration: the anti-pattern](https://www.jessesquires.com/blog/2016/07/31/enums-as-configs/) -> 이건 아직 안 봄.

<br>

### 비즈니스 로직에 관하여
- 📚 Article
    - [비즈니스 로직, 도메인 로직이 도대체 뭐지?](https://velog.io/@eddy_song/domain-logic#%EC%9D%B4-%EC%BD%94%EB%93%9C%EB%8A%94-%ED%98%84%EC%8B%A4-%EB%AC%B8%EC%A0%9C%EC%97%90-%EB%8C%80%ED%95%9C-%EC%9D%98%EC%82%AC%EA%B2%B0%EC%A0%95%EC%9D%84-%ED%95%98%EA%B3%A0-%EC%9E%88%EB%8A%94%EA%B0%80)
    - [WIKI: Business Logic](https://en.wikipedia.org/wiki/Business_logic)
- 2022년 11월 5일

[도메인 로직과 비즈니스 로직은 동의어라고 볼 수 있다.](https://enterprisecraftsmanship.com/posts/what-is-domain-logic/) 비즈니스 로직에 대한 개념을 잡는데 가장 큰 도움이 됐던 것은 wiki에 정의된 내용이었다.

> In computer [software](https://en.wikipedia.org/wiki/Software), **business logic** or **domain logic** is the part of the program that encodes the real-world [business rules](https://en.wikipedia.org/wiki/Business_rule) that determine how data can be [created, stored, and changed](https://en.wikipedia.org/wiki/Create,_read,_update_and_delete).
CS에서, 비즈니스 로직 또는 도메인 로직은 **데이터가 생성되고 저장되고 변경되는 방법을 결정하는 현실세계 Business Rule**을 인코딩 한 프로그램의 일부이다.
> 

예전에 읽었던 클린 아키텍처에서 봤던 내용도 생각났다.

> *클린 아키텍처, 20장 업무규칙*<br>
컴퓨터상으로 구현했는지와 상관없이, 업무규칙은 사업적으로 수익을 얻거나 비용을 줄일 수 있어야 한다.
사업 자체에 핵심적이며, 규칙을 자동화하는 시스템이 없더라도 업무 규칙은 그대로 존재하기 때문이다.
> 

클린 아키텍처 책을 볼 당시 위 내용을 이렇게 이해했다. <br>
“어플리케이션이 존재하지 않더라도, 사람이 직접 손을 써서라도 반드시 수행되어야 하는 로직”. 

사업적 관점에서 이렇게까지 해서라도 반드시 수행되어야 하는 것은 수익을 얻거나 비용을 줄일 수 있는(즉, 사업에 이익이 되는) 것들이기 때문에 위 설명에 수익/비용과 관련된 내용이 나온 것 같다.

<br>

어떤 로직을 추상화했을 때에도 남아있는 단계가 비즈니스 로직이라고 볼 수 있지 않을까.. 아니면 이게 없다면 서비스가 제대로 기능을 하지 못하고 결국 손해로 이어지게 된다.. 하는 것들을 비즈니스 로직이라고 볼 수도..?

<br>

🤔  왜 하필 데이터의 생성,저장,변경 방법을 결정하는 것을 비즈니스 로직이라고 봤을까?<br>
데이터가 없다면 어플리케이션은 그냥 빈 깡통이나 마찬가지이기 때문이다. 음악 앱인데 음악이 없다면 아무도 정기결제를 하려 하지 않을 것이고, 이용하는 사용자가 없다면 아무도 음악 앱을 통해 홍보를 하려 하지 않을 것이다. 데이터가 없다면 어떠한 사업적 가치도 만들어낼 수 없다.

<br>

🤔  데이터를 생성,저장,변경하는 방법과 관련되어 있다면 모두 비즈니스 로직인가?<br>
그렇지 않다. wiki 정의에 나오는 “현실세계 Business Rule(어플리케이션이 없더라도 반드시 존재하고 수행되야 하는 규칙)”에 해당하는가를 생각해봐야 한다.
예를 들어 사용자의 활동을 통해 수익이 발생되는 서비스라면 로그를 위한 데이터 저장은 비즈니스 로직이라고 볼 수 있다. 이와 달리 단순히 에러(비즈니스에 치명적인 손해를 일으키지는 않는)를 트래킹하기 위한 로그 데이터 저장이라면 이는 비즈니스 로직에 해당되지 않는 것.(이라고 이해했는데 나중에 다시 보면 잘못 이애하고 있을수도…)