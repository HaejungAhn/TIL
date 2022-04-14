2022-03?

---
기술면접 준비 중 질문에 OOP와 POP를 비교해서 설명해보라는 질문이 있어 찾아보게 되었음.

<aside>
🤔 프로그래밍 패러다임에 자주 나오는 **“Oriented”**는 어떤 의미를 가지고 있을까? 지향하다는 구체적으로 무엇을 지향하는걸까? 
⇒ **프로그램을 디자인(설계)할 때 무엇을 기반으로 디자인(설계)하느냐**가 “Oriented”의 목적

</aside>

— 👀 [Protocol-Oriented Programming](https://scotteg.github.io/protocol-oriented-programming)

# POP: Protocol Oriented Programming

✨ OOP는 객체를 기반으로 설계를 한다면, POP는 프로토콜을 기반으로 설계를 한다. value type도 프로토콜을 채택할 수 있기 때문에 OOP에서와는 달리 POP에서는 reference type인 클래스 뿐만 아니라 value type인 struct와 enum도 first class citizen이 된다.

<aside>
❓ **First class citizen
”**일급객체”라는 의미이다. 아래의 조건을 만족해야한다.
1. 변수 및 상수에 저장, 할당될 수 있어야 함
2. 파라미터로 사용될 수 있어야 함
3. 리턴 타입으로 사용될 수 있어야 함

</aside>

### **POP가 해결하는 문제와 장점**

- 상속의 한계 

![https://scotteg.github.io/Assets/SingleInheritanceObjectOrientedProgramming.png](https://scotteg.github.io/Assets/SingleInheritanceObjectOrientedProgramming.png)

전통적인 OOP 방식의 데이터 모델링이다. superclass를 만들고 그 특성을 하위클래스가 이어받아 뻗어나가는 모양이다.
클래스를 만들어나가다 보면 분명 두개 이상의 클래스 특성이 모두 필요한 클래스가 있을것이다. 예를 들어 위 이미지의 HybridCar(좌측하단) 클래스는 ElecVehicle과 GasVehicle 클래스의 특성이 모두 필요할 것이다. Swift에서 상속은 단일 상속만 가능하기 때문에 HybridCar를 위한 새로운 클래스를 또 추가해야한다. 수륙양용차(AmphiCar)는 또 어떤 클래스를 상속받아야 할까? 물에서도 다니고 땅에서도 다니는데. 여기에다가 hybrid의 특성을 지닌다면? 
케이스가 늘어날수록 클래스는 어마어마해질 것이다. 그것도 중복된채로!!

정리하자면 상속의 한계점은

① 어떤 클래스가 서로 다른 superclass를 상속한 클래스들의 특성을 모두 필요로 할 때 새로운 클래스를 만들어야 한다. 

② 그리고 이렇게 되면 중복되는 코드가 많아질 것이다.

③ class만으로 프로그램 설계를 해야한다.

프로토콜은 모델 계층구조를 좀 더 flat 하게 만들어준다.

또한 상속과 달리 **value type에도 사용**할 수 있고 **여러개의 protocol을 채택**할 수도 있다.

프로토콜은 body를 구현하지 않는다. 필요한 메소드, 프로퍼티에 대한 시그니처만 정의해둔다. 이런 특징은 unit test에도 좋다.(동일한 프로토콜을 채택한 mock object를 이용해 유닛 테스트 가능함)

또한 프로토콜은 **extension을 통해 default implementation을 정의**할 수도 있다.

### **POP의 한계**

POP는 OOP를 대체하는 방식이 아니다. 서로 함께 사용해야하는 경우도 있다. 왜냐하면 프로토콜은 상태를 저장할 수 없기 때문이다. ( 프로퍼티에 기본값 설정 불가, 메소드 파라미터에 기본값 설정 불가 )

> One reason why you may do this is that **protocol extensions cannot implement stored state**, i.e., it is not possible to set a property to a default stored value in a protocol extension. Therefore, a class may be subclassed to inherit a stored property, and then use protocols to add new methods to provide the behavior required for instances of that class.
> 

Protocol 만들기

- 네이밍 시 프로토콜의 요구사항 또는 이를 준수하는 type의 동작을 나타내야 한다.

   `BidirectionalCollection`, `Equatable`

- 프로토콜의 dispatch

![https://scotteg.github.io/Assets/DispatchTypeFlowchart.png](https://scotteg.github.io/Assets/DispatchTypeFlowchart.png)