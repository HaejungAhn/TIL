## 객체지향: 캡슐화

#### <b>들어가며</b>
- 객체지향의 특징으로 보통 4가지(추상화, 캡슐화, 다형성, 상속)를 꼽는다.
- 각각 대략적으로 어떤 것인지는 알것도 같지만, 막상 이를 설명해보라고 하면 못할 것 같다.🥲 그래서 시작하는 OO 특징 정리!

#### <b>캡슐화(Encapsulation)</b>
- 📒 Encapsulation 🄽 캡슐화
- 캡슐은 안에 들어있는 것들을 보호하는 껍데기 정도로 정의내릴 수 있으며 캡슐화는 어떤 것들을 캡슐로 감싸 외부로부터 보호하는 것을 말한다.
- 프로그래밍에서도 **데이터와 데이터를 처리하는 메소드**를 캡슐에 감쌀 수 있다. 여기서 캡슐 역할을 하는 것이 class, struct 등이다.
- 추상화(Abstraction)는 관련 세부 정보만 end-user에게 노출하는데 사용되는 반면, 캡슐화(encapsulation)는 주로 data security를 다룬다.
- 이를 위해 캡슐화는 access control(private, public, internal 등)을 지정함으로써 <u>원치 않는 접근으로부터 데이터를 보호</u>하고 <u>하나의 unit에 데이터와 이를 처리하는 메소드들을 묶는다.</u>

#### <b>캡슐화와 data hiding</b>
- 캡슐화는 외부로부터 안에 있는 데이터를 보호할 수 있기 때문에 data hiding과 혼용되어 쓰이지만, 이 둘은 동일한 개념이 아니다.
- (Java 기준) 캡슐화는 단일 unit에 연관된 데이터를 그룹핑함으로써 데이터를 더욱 쉽게, 안전하게 관리하기 위함이 목적이다.
- 이와 달리 data hiding은 디테일한 구현을 숨김으로써 데이터 멤버에 대한 접근을 제한한다.(access control을 이용해 data hiding 가능)
- 비록 캡슐화가 data hiding이라고 말할 순 없지만, 캡슐화는 data hiding할 수 있는 방법을 제공한다.

---
- 뭔가 부족한데.. 겉핥기만 하는 느낌이 든다. 왜지???

---
📚 참고 사이트
- [Encapsulation In Java: Complete Tutorial With Example](https://www.softwaretestinghelp.com/encapsulation-in-java/)