## 3. Decorator 패턴
#### 👋 <b>들어가며</b>
>• 3장에서는 상속을 남용하는 사례를 살펴봅니다.   
>• 데코레이터 패턴을 배우면 기존 클래스 코드를 바꾸지 않고도 객체에 새로운 임무를 추가할 수 있습니다.   

#### <b>들어가며 했던 생각</b>  
- 인터페이스(Swift에서는 프로토콜)를 활용하는건가? 싶었다. 난 아직 인터페이스밖에 몰라...🌝
- 상속 남용 사례는 1장에서도 봤었던 것 같다! 고무오리🐥
---
#### <b>📍 이 장의 예제</b>
- 초대형 커피 전문점, 스타버즈의 주문 시스템 클래스 만들기
- 스타버즈에서 판매하는 음료들의 super class인 `Beverage`(추상 클래스)가 있음.
    - 인스턴스 멤버변수 : 음료를 설명하는 `description`
    - 인스턴스 메소드
        - 음료의 가격(Double)을 리턴하는 `cost()`
        - 음료의 설명을 리턴하는 `getDescription()`
- 기본적인 커피(하우스블렌드, 다크로스트, 디카페인, 에스프레소)를 기반으로 모카를 추가하거나, 우유/두유를 추가하거나, 휘핑크림을 추가함으로써 다양한 종류의 음료를 만들어낸다.

#### <b>처음에는 이렇게 만들었다.</b>
- 스타버즈에서 판매하는 모든 음료들이 `Beverage`(추상 클래스)를 상속받는다.
    - ex. 다크로스트, 아이스아메리카노 with 다크로스트, 아이스라떼 with 다크로스트, 아이스모카라떼 with 다크로스트, 아이스모카라떼(휘핑) with 다크로스트 ...

- 이렇게 만들면 Beverage 클래스를 상속받은 서브클래스들이 너무 많아진다! 일단 클래스가 많아진다는 것부터가 관리에 있어 부담이 될 수 있다.(예를 들면 어떤 부담?)


#### <b>두번째에는 이렇게 만들었다.</b>
- 슈퍼클래스가 커피에 들어갈 첨가물의 flag값을 소유하고 있는 형태로 만든다. 그리고 서브클래스는 이를 상속받는다.   
```Swift
    class Beverage {
        // ...생략...
        var milk:  Bool = false
        var soy:   Bool = false
        var mocha: Bool = false
        var whip:  Bool = false
        // ...생략...

        // 그리고 cost 메소드의 기본 구현은 아래와 같을 것이다.
        func cost() -> Double {
            var cost: Double = .zero
            if milk  { cost += 0.9 }
            if soy   { cost += 0.8 }
            if mocha { cost += 1.2 }
            if whip  { cost += 1.5 }

            return cost
        }
    }
```
- 클래스의 양이 매우 줄어들었다. 기본이 되는 커피(하우스블렌드, 다크로스트, 디카페인, 에스프레소)에 대한 클래스만 만들면 됨!! 나머지 커피들(아이스아메리카노, 카페라떼, 모카라떼 등)은 flag값으로 조정하면 된다!!
- 그런데 만약 들어가야 할 첨가물의 종류가 많아진다면 어떨까? 코드의 수정 없이는 그 변화를 적용시킬 수 없을 것이다.
- 이 외에도 커피 이외에 아이스 티 종류의 음료가 추가된다면, 이 음료들은 milk나 soy 등과 관련된 멤버변수, 메소드가 필요없어도 어쩔 수 없이 가지고 있어야 한다. (<-이게 왜 안좋은지?)
- 이처럼 상속을 사용한다고 하여 무조건 좋다, 유연하다 라고 말할 수 없다.

#### <b>🤔 그럼 상속말고 뭐가 있는데?</b>
- 상속 이외에도 <u>구성과 위임으로 실행중에 행동을 '상속'하는 방법</u>이 있다.  
😮 *행동을 상속한다는 관점이 새로웠다.*
- 코드 관리에 구성이 미치는 영향 : 객체를 동적으로 구성하면 기존 코드를 고치는 대신 새로운 코드를 만들어서 기능을 추가할 수 있다. 기존 코드는 건드리지 않으므로 코드 수정에 따른 버그나 의도하지 않은 부작용을 원천봉쇄할 수 있다.
- OCP(개방폐쇄원칙)로 새로운 행동을 추가해야하는 것이다.
- 코드에서 확장해야 할 부분을 선택할 때는 세심한 주의를 기울어야 한다. 무조건 OCP를 적용한다면 괜히 쓸데없는 일을 하며 시간을 낭비할 수 있으며, 필요 이상으로 복잡하고 이해하기 힘든 코드를 만들게 되는 부작용이 발생할 수 있다.

#### <b>데코레이터 패턴이란</b>
- 처음에는 인터페이스로 Mocha, Whip 클래스를 감싸는 것인가했다. 
- 근데 그게 아니었음!! 클래스를 클래스로 wrapping하는 것이었음. 😮 상상도 못했다.
- 꾸며줄 객체와 동일한 상위타입을 갖는 데코레이터 객체들로 꾸밀 객체를 감싸는 것이다. 마치 마트료시카 인형처럼!
- 데코레이터 패턴의 특징
    - ✨ 데코레이터의 ~~슈퍼 클래스~~ 상위타입은 자신이 장식하고 있는 객체의 ~~슈퍼클래스~~ 상위타입과 같다.  
        - 이렇게 해야하는 이유는, 여러개의 데코레이터를 이용해 어떤 객체를 꾸며줄 수 있는데 "꾸밈을 당하는" 객체가 데코레이터 자기 자신일 수도 있기 때문이다. 
        - 다형성을 위해 이렇게 한 것임. 따라서 클래스를 써도 되고, 프로토콜을 사용해도 된다(고 이해함).
    - 데코레이터 안에는 슈퍼클래스 타입의 멤버변수를 가진다.
        - 이 멤버변수에 해당 데코레이터가 꾸며줄 객체가 들어감.
        - 객체**구성**(인스턴스 변수로 다른 객체를 저장하는 방식)을 이용.
    - 한 객체를 여러 개의 데코레이터로 감쌀 수 있다.
    - 데코레이터는 자신이 장식하고 있는 객체와 같은 슈퍼클래스를 가지고 있기 때문에 원래 객체가 들어갈 자리에 데코레이터 객체를 넣어도 상관없다.(LSP)
    - 데코레이터는 자신이 장식하고 있는 객체에게 어떤 행동을 위임하는 일 말고도 추가작업을 수행할 수 있다.
    - 객체는 언제든지 감쌀 수 있으므로 실행중에 필요한 데코레이터를 마음대로 적용할 수 있다.
- 이걸 코드로 작성해보면 아래와 같다.
    ```Swift
    protocol Beverage {
        var description: String { get set }
        func getDescription() -> String
        func cost() -> Double
    }

    class DarkRoast: Beverage {
        var description = "다크 로스트"
        func getDescription() -> String { return description }
        func cost() -> Double { 
            return 2.0
        }
    }

    //condimentDecorator를 프로토콜로 만드는 것도 가능할까?
    class CondimentDecorator: Beverage {
        var description = ""
        func getDescription() -> String { return description }
        func cost() -> Double { 
            return 2.0
        }
    }

    class Milk: CondimentDecorator {
        let beverage: Beverage

        init(beverage: Beverage) {
            self.beverage = beverage
        }

        func getDescription() -> String { 
            return beverage.getDescription() + ", 우유"
        }

        func cost() -> Double {
            //0.5는 milk 가격
            //beverage.cost() 부분이 위임하는 부분이다.
            return beverage.cost() + 0.5
        }
    }
    //카페라떼 with 다크로스트
    let beverage = Milk(beverage: DarkRoast())
    ```
#### <b>데코레이터 패턴은 언제 사용해야하는가?</b>
- 구체적인 객체로 특정 작업을 처리해야한다면, 데코레이터 패턴은 적정한 패턴이 아닐 수 있다.
    - 예를들어, "아이스모카라떼(휘핑) with 다크로스트"가 특별할인에 들어간다고 했을때, 데코레이터 패턴을 활용하면 다크로스트가 데코레이터로(Milk 또는 Whip 등) wrapping되기 때문에 특정 구체(다크로스트)에 의존하여 어떤 처리를 할 수 없다.
- <u>데코레이터는 감싸고 있는 객체에 행동을 추가하는 용도</u>로 만들어진다. 만약 여러 단계의 데코레이터를 파고 들어가서 어떤 작업을 해야한다면 원래 데코레이터 패턴이 만들어진 의도에 어긋나는 것이다.
- 데코레이터를 끼워넣어도 클라이언트는 데코레이터를 사용하고 있다는 사실을 전혀 알 수 없다는 것이 장점

#### <b>데코레이터 패턴의 단점</b>
- 자잘한 클래스가 많이 추가될 수 있다. 그래서 다른 사람이 봤을 때 이해하기 힘든 디자인이 만들어진다.
    - 옛날옛적에 Java로 AOS 앱을 개발한 적이 있는데 FileInputStream은 정말정말 이해가 안가긴 했다.
- 특정 형식에 의존하는 코드에 데코레이터를 적용하면 안된다.
- 데코레이터를 이용하면 초기화 코드가 복잡해진다. -> 이는 팩토리, 빌드패턴으로 보완할 수 있는 단점임.

#### <b>궁금한 점</b>
- 아니 도대체 구성이 뭐임??? 아직도 잘 모르겠음..!   
👉 "구성요소"의 구성을 생각해보면 됨.  
    - 두 클래스를 합치는 것, "A에는 B가 있다"의 관계
    - A라는 객체의 멤버변수로 B라는 객체가 사용되어있다면 A와 B는 구성관계라고 할 수 있다.(B가 A의 구성요소가 되는 것)

- 이해가 안갔던 문장들
    > 119p  
    "구성으로 객체의 행동을 확장하면 실행 중에 동적으로 행동을 설정할 수 있습니다."
    - <b>*"구성으로 객체의 행동을 확장한다."*</b>
        - 상속으로 행동(메소드)을 추가하는게 아니라, 객체가 가지고 있는 구성요소의 메소드를 호출함으로써 기능을 확장한다.
        - 예를 들면 버튼이 있는 화면을 개발해야 하는 경우, UIViewController(이하 뷰컨) 안에 UIScrollView를 멤버변수로 선언해놓고 이를 활용해 스크롤에 대한 사용자 인터렉션에 반응할 수 있음.
        - 원래 뷰컨은 스크롤에 반응하는 행동이 없었으나, 구성(UIScrollView를 멤벼변수로 가져감)을 통해 뷰컨의 행동이 확장된 것이지.  
        (난 이렇게 이해함.)
    

    > 127P  
    "어떤 구성요소를 가지고 데코레이터를 만들때 새로운 행동을 추가하지. 새로운 행동은 객체를 구성해서 얻는 거잖아. 슈퍼클래스로부터 행동을 상속받아서 얻는게 아니라."   
    "행동은 기본 구성 요소와는 다른 데코레이터 등을 인스턴스 변수에 저장하는 식으로 연결하는 거고."

    - 슈퍼클래스의 메소드를 호출한 것이 아니고, decorator가 가지고 있는 멤버변수의 메소드 호출을 통해 행동을 확장할 수 있는 것.

#### <b>📚 더 읽어보기</b>
- [Decorator in Swift](https://refactoring.guru/design-patterns/decorator/swift/example)
- [Decorator](https://refactoring.guru/design-patterns/decorator)
- Decorator는 객체들을 decorator라고 하는 특별한 wrapper object 안에 둠으로써 동적으로 객체에 새로운 행동을 추가하는 구조적 패턴이다.
- Decorator는 Swift code, 특히 stram과 관련된 코드에서 표준적으로 사용되고 있다.
- 현재 클래스와 동일한 클래스나 또는 인터페이스를 constructor나 creation method에서 받는다면 Decorator 패턴을 사용한 것이라고 볼 수 있다.