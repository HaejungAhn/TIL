## 4. Factory 패턴
### 👀 이런 코드 한번쯤은 봤을듯.
- 조건에 따라 적절한 구상(concrete) 클래스를 생성해야하는 경우가 있다.  
```Swift
enum PizzaType {
    case cheese
    case clam
    case pepperoni
}

class PizzaStore {
    func orderPizza(type: PizzaType) -> Pizza {
        let pizza: Pizza

        switch type {
            case .cheese:
                pizza = CheesePizza()
            case .clam:
                pizza = ClamPizza()
            case .pepperoni:
                pizza = PepperoniPizza()
        }

        pizza.prepare()
        pizza.bake()
        pizza.cut()
        pizza.box()

        return pizza
    }
}

```
- 만약 이 상태에서 조건이 변경되거나, Pizza 클래스 및 PizzaType enum이 변경될 경우 코드의 변경은 불가피하다.   
- 이런 코드가 프로젝트 이곳저곳 흩어져 있다면 어떨까? 일일이 찾아가서 수정을 할 수밖에 없다.
- 위 코드에서는 새로운 객체를 생성하는 것이 바뀌는 부분이라고 볼 수 있는데, 이 책에 나오는 객체지향 디자인의 첫번째 원칙, "바뀌는 부분을 찾아내어 바뀌지 않는 부분과 분리(캡슐화) 해야한다."가 지켜지지 않고 있다.
- 이 장에서 새로운 객체를 생성할 때 이를 캡슐화할 수 있는 방법과 관련된 패턴(팩토리 패턴)을 알아본다.
    >193P  
    객체 생성을 캡슐화해서 어플리케이션의 결합을 느슨하게 만들고, 특정 구현에 덜 의존하도록 만들 수 있다.

- 😮 *책에 나오는 객체지향 디자인 원칙을 외우는게 좋을 것 같다. 그래야 설계를 할 때 생각의 물꼬를 틀 수 있을 것 같음!*

#### <b>📍 이 장의 예제</b>
- 🍕 피자 가게를 프로그래밍해보자!
- 피자 가게에는 여러 종류의 피자가 있다. 종류별로 알맞은 피자 객체를 생성해서 사용해야 한다.(위의 코드와 같이)
- 자그마하던 피자 가게는 어느새 유명해져서 미국 각지의 특색에 맞는 피자를 판매하는 프랜차이즈 사업으로 확장하게 된다.
    - 도우가 두껍고 치즈가 많이 들어간 시카고 피자
    - 도우가 얇고 치즈도 조금 들어간 뉴욕 피자
    - 기타 각지의 특색에 맞는 피자들

#### <b>변하는 것 파악하기</b>
- 위에서도 언급했다시피 타입에 따라 적절한 피자 객체를 생성해야 하는 부분이 변하는 부분이다. 그 이외에 것들은 상대적으로 잘 변하지 않는 부분들이기 때문에 이를 서로 분리시켜야 한다.
- 위 코드의 switch문을 똑 떼어서 새로운 객체에 넣도록 하겠음! 그럼 일단 분리(캡슐화)는 된 것이잖아?

#### <b>간단한 팩토리(Simple Factory)</b>
- 분리를 위해 새롭게 만든 객체를 **팩토리**라고 부른다. 즉, 조건에 맞는 적절한 객체를 생성해주는 역할을 하는 것이라고 정의할 수 있을 것 같다.
- 간단한 팩토리는 객체 생성 메소드를 하나의 클래스에 모아놓고, 사용하는 곳에서 해당 객체의 레퍼런스를 가지고 있는 형태이다. 심플.
- 팩토리를 사용하면 아래와 같이 코드를 만들 수 있다.
    ```Swift
    class SimplePizzaFactory {
        func createPizza(type: PizzaType) -> Pizza {
            let pizza: Pizza
            // 위에서 switch문으로 작성된 코드를 여기로 옮긴다.
            // ..생략..
            return pizza
        }
    }    
    ```
    ```Swift
    //사용하는 곳에서는(팩토리의 클라이언트)
    class PizzaStore {
        func orderPizza(type: PizzaType) -> Pizza {
            let pizza: Pizza
            let factory = SimplePizzaFactory()

            pizza = factory.createPizza(type: type)

            pizza.prepare()
            pizza.bake()
            pizza.cut()
            pizza.box()

            return pizza
        }
    }
    
    ```
- 위 코드에서 PizzaType이 새롭게 추가되거나 삭제되더라도 orderPizza 메소드는 코드를 수정해야할 것이 전혀 없다.
- `SimplePizzaFactory`를 여러곳에서 사용한다 하더라도 변경 발생 시 `SimplePizzaFactory`만 수정하면 전체에 적용이 된다.
- 😮 이것이 팩토리 패턴!!은 아니다. 디자인 패턴이라기 보다는 프로그래밍 시 자주 사용하는 관용구 같은 것이라 한다.
    - 🤔 근데 왜 패턴이 아닐까? 패턴이다 아니다의 기준은 뭘까?

#### ✋ <b>팩토리 정적 메소드</b>
- 팩토리 정적 메소드는 팩토리 역할(객체를 조건에 따라 적절하게 생성하는)을 하는 static 메소드를 말한다.
- 코드로 표현하자면 아래와 같다.
    ```Swift
    class SimplePizzaStaticFactory {
        static createPizza() -> Pizza {
            let pizza: Pizza
            // 생략
            return pizza
        }
    }
    ```
    ```Swift
    // 사용할 때는..
    let pizza = SimplePizzaStaticFactory.createPizza()
    ```
- 결과적으로 보면 간단한 팩토리(Simple Factory)와 큰 차이는 없다.
- 정적 메소드인만큼 팩토리 메소드를 실행하기 위해 인스턴스를 별도로 생성할 필요가 없다는 점이 장점일 수 있으나 서브클래스를 만들어서 팩토리 메소드의 행동을 변경할 수는 없다는 단점이 있다.(간단한 팩토리는 그 반대)

#### <b>피자 프랜차이즈 사업의 시작</b>
- 피자 가게가 성공해서 뉴욕과 시카고에 지점을 내게 됐다. 뉴욕에서는 기본적으로 도우가 얇아야 하고 시카고는 기본적으로 도우가 두꺼워야 한다.
- 그래서 뉴욕에서 판매할 피자 객체와 시카고에서 판매할 피자 객체를 각각 생성했다.
- 적절한 객체를 생성하기 위해, 기존에 사용하던 `SimplePizzaFactory`는 삭제하고 `NYPizzaFactory`, `ChicagoPizzaFactory`를 만들어 사용하기로 했다.
    ```Swift
    let nyFactory = NYPizzaFactory()
    let nyPizzaStore = PizzaStore(nyFactory)
    nyPizzaStore.orderPizza(type: .clam)

    let chicagoFactory = ChicagoPizzaFactory()
    let chicagoStore = PizzaStore(chicagoFactory)
    chicagoStore.orderPizza(type: .clam)
    ```
- 위 방법의 문제점(책에 나온 문제점과 그 해결책이 있는데 이게 잘 이해가 안됨.)
    > 지점에서 우리가 만든 팩토리로 피자를 만들긴 하지만, 굽는 방식이 달라진다거나 피자를 자르는 것을 까먹거나 이상한 피자 박스를 사용하는 일이 발생했다.

    > 이 문제를 해결하기 위해서는 `PizzaStore`와 `피자 생성 코드` 전체를 하나로 묶어주는 프레임워크를 만들어야 한다.
- 팩토리가 외부에서 생성된 후 `PizzaStore`에 주입되는 구조인데 이때 엉뚱한 `Factory`를 주입하게 되면서 문제가 되는 것이 아닐까?  
- 시카고 지점은 뉴욕 지점보다 빵이 두껍기 때문에 굽는 방식이 달라야 함. 그래서 이런 특성이  적용된 `ChicagoPizzaFactory`를 만들었음. 근데 종종 뉴욕 지점에 `ChicagoPizzaFactory`가 들어가면서 문제가 됐다.   
-> 그래서 `PizzaStore`와 `피자 생성 코드` 전체를 하나로 묶어주는 프레임워크를 만들어야 한다.

### PizzaStore와 피자 생성코드 합치기
#### <b>팩토리 메소드 패턴</b>
- 팩토리 메소드 패턴
    - 객체를 생성할 때 필요한 인터페이스를 만든다.
    - 어떤 클래스의 인스턴스를 만들지는 서브클래스에서 결정한다.
    - 해당 패턴을 사용하면 클래스 인스턴스를 생성하는 일을 서브클래스에 위임하게 된다.
    - 상속을 활용한다.
- `PizzaStore` 안에 피자 생성 메소드를 위치시킨다.
- 이때 피자 생성 메소드는 ~~추상화 된 상태(이게 맞는 말인건가?)~~ 인터페이스 역할을 하도록 만들어야 한다. 즉, `PizzaStore`의 서브클래스에서 피자 생성메소드를 구현하도록 만들어야 한다.
- 만들 수 있는 방법은 두가지가 있다.
    - Protocol 활용
        ```Swift
        Protocol PizzaStore {
            func createPizza(type: PizzaType) -> Pizza
            func orderPizza(type: PizzaType) -> Pizza
        }
        extension PizzaStore {
            func orderPizza(type: PizzaType) -> Pizza {
                // orderPizza의 기본 로직을 여기에 작성
                let pizza = createPizza(type)
                ..생략..
            }
        }

        class NYPizzaStore: PizzaStore {
            func createPizza(type: PizzaType) -> Pizza {
                switch type {
                    case .cheese: 
                    return NYCheesePizza()
                    ..생략..
                }
            }
        }
        ```

    - Class로 PizzaStore를 만들고, 피자 생성 메소드는 오버라이딩 할 수 있게 만든다.
        ```Swift
        class PizzaStore {
            func createPizza(type: PizzaType) -> Pizza {
                return Pizza()
            }
            final func orderPizza(type: PizzaType) -> Pizza {
                // orderPizza의 기본 로직을 여기에 작성
                let pizza = createPizza(type)
                ..생략..
            }
        }

        class NYPizzaStore: PizzaStore {
            override func createPizza(type: PizzaType) -> Pizza {
                switch type {
                    case .cheese: 
                    return NYCheesePizza()
                    ..생략..
                }
            }
        }
        ```
    - 이렇게 놓고 비교해보니 class보다는 Protocol을 사용하는게 더 적절해보인다. 왜냐하면 createPizza() 메소드를 서브클래스에서 반드시 오버라이딩 해야하는데 이를 강제하는게 아니기 때문임.
- 아래와 같이 사용할 수 있음. 팩토리 메소드 패턴은 런타임 시 동적으로 피자 종류를 결정하는게 아니다. 피자의 종류는 어떤 서브클래스를 선택했느냐에 의해 결정되기 때문에 컴파일 시점부터 생성되야할 서브클래스가 무엇인지 이미 알고 있다.
    ```Swift
    let nyPizzaStore = NYPizzaStore()
    nyPizzaStore.orderPizza()
    ```
- <b>팩토리 메소드 패턴의 구성요소 : Creator 클래스와 Product 클래스</b>
    - 위 예제에서는 PizzaStore가 Creator 클래스가 되고, Pizza가 Product 클래스가 된다.
    - `Creator 클래스`는 팩토리 메소드를 가지고 있다. 팩토리 메소드에 의해 생성된 Product를 사용하는 클래스이기도 하다.
    - `Product 클래스`는 팩토리 메소드에 의해 만들어지는 것들을 말한다.

- concrete Product 클래스가 하나밖에 없다하더라도 팩토리 메소드 패턴을 활용하면, 추후 Product 클래스가 추가되거나 구성을 변경하더라도 사용하는 쪽에서 영향을 받지 않는다. 변하는 것으로부터 변하지 않는 것을 분리시킨다는 디자인 패턴 원칙에 적합하기 때문에 concrete Product 클래스가 하나이더라도 의미가 있다.

- Simple Factory와 팩토리 메소드 패턴의 차이는 무엇?
    > 간단한 팩토리는 일회용 처방에 불과한 반면, 팩토리 메소드 패턴을 사용하면 여러 번 재사용이 가능한 프레임워크를 만들 수 있다.

    > 간단한 팩토리는 Product를 마음대로 변경할 수 없기 때문이다.
    - 팩토리 메소드 패턴은 상속을 이용, 서브클래스별로 다양한 Product를 생산해낼 수 있다.(확장성이 좋음) 하지만 간단한 팩토리는 객체 생성과 관련된 인터페이스가 있는게 아니기 때문에(원래 상속을 위해 만들어졌던 것도 아니었고) Product가 하나로 고정되어 있다.

### 의존성 역전 원칙(DIP)
> 디자인 원칙   
추상화된 것에 의존하게 만들고 구상 클래스에 의존하지 않게 만든다.

> 고수준 구성 요소가 저수준 구성 요소에 의존하면 안되며, 항상 추상화에 의존하게 만들어야 한다. 

> 고수준 구성요소란 다른 저수준 구성요소에 의해 정의되는 행동이 들어있는 구성요소를 뜻한다.

- 예를 들면 텀블러 스트로우나 뚜껑, 음료를 담는 컵 객체 등이 저수준 구성요소이고 이걸 하나로 합치는 메소드를 가지고 있는 객체가 고수준 구성요소라고 이해함.
- 위에서 계속 보고 있는 피자 가게를 예로 들면, `Pizza` 객체 덕분에 `PizzaStore`의 `orderPizza()`가 제대로 동작할 수 있다. 따라서 `Pizza` 객체가 저수준, `PizzaStore`가 고수준임.
- 의존성 역전은 클린아키텍처 책에서도 잠깐 봤지만, 인터페이스를 이용해 프로그램의 제어흐름 방향과 의존성 흐름 방향이 반대가 되도록 만드는 것을 의미한다.
- 의존성 역전 원칙을 지키는 방법
    - 변수에 concrete class의 레퍼런스를 저장하지 말기
    - concrete class에서 유도된 클래스 만들지 말기???
    - 베이스 클래스에 이미 구현되어 있는 메소드를 오버라이드하지 말기
        -> 베이스 클래스에 이미 구현되어 있다는 것은 모든 서브클래스에서 해당 메소드의 로직을 사용하겠다는 의미이기도 하다. 근데 이걸 오버라이드 한다는 것은 서브클래스 중 예외가 생긴다는 의미이고, 사용하는 쪽에서 구체적인 서브클래스를 참조할 수 밖에 없는 상황이 생긴다는 의미임. 이렇게 되면 베이스 클래스가 제대로 추상화되지 않음.

### 지점마다 다른 원재료를 사용해요!
#### <b>추상 팩토리</b>
- 추상 팩토리 패턴
    - 서로 연관되거나 의존적인 객체로 이루어진 제품군을 생산하는 인터페이스를 제공한다.
    - 클라이언트에서는 추상 인터페이스로 객체를 공급받을 수 있다.
    - 객체 composition(구성)을 활용한다.
- Pizza에 들어가는 기본적인 구성은 지점마다 모두 동일하다: 도우, 소스, 치즈, 야채, 페퍼로니, 조개
- 하지만 지점마다 구체적인 재료(야채와 페퍼로니를 제외하고)가 모두 다르다.
    - 뉴욕: 얇은 도우, 마리나라 소스, 레지아노 치즈, 야채(마늘, 양파, 버섯), 슬라이스 페퍼로니, 신선한 조개
    - 시카고: 두꺼운 도우, 플럼토마토 소스, 모짜렐라 치즈, 야채(마늘, 양파, 버섯), 슬라이스 페퍼로니, 냉동 조개
- 원재료 팩토리를 만들어 동일한 구성이지만 지점별로 적절한 재료를 공급해보자!
    ```Swift
    protocol PizzaIngredientFactory {
        func createDough() -> Dough
        func createSauce() -> Sauce
        func createCheese() -> Cheese
        func createVeggies() -> [Veggies]
        func createPepperoni() -> Pepperoni
        func createClam() -> Clam
    }

    class NYPizzaIngredientFactory: PizzaIngredientFactory {
        func createDough()     -> Dough  { return ThinCrustDough() }
        func createSauce()     -> Sauce  { return MarinaraSouce() }
        func createCheese()    -> Cheese { return ReggianoCheese() }
        func createVeggies()   -> [Veggies] { return [Garlic(), Onion(), Mushroom()] }
        func createPepperoni() -> Pepperoni { return SlicedPepperoni() }
        func createClam()      -> Clam { return FreshClam() }
    }
    ```
- 지점이 확장되더라도 프로토콜 채택 및 구현을 통해 해당 지점에 맞는 재료를 공급해줄 수 있다. 또한 재료가 추가되거나 변경/삭제 되더라도 적절한 인터페이스만 채택하면(Dough, Sauce 등 원재료 인터페이스) 원재료 팩토리를 사용하는 쪽에서 어떠한 수정도 없이 변경할 수 있다.
- 원래는 피자 종류 및 지점마다 클래스가 만들어져 있었다. 예를 들면 NYCheesePizza와 ChicagoCheesePizza 클래스가 필요했던 것이다. 그런데 이제 원재료 팩토리가 있으니 이렇게 만들 필요가 없다.
    ```Swift
    class CheesePizza: Pizza {
        ingredientFactory: PizzaIngredientFactory

        init(ingredientFactory: PizzaIngredientFactory) {
            self.ingredientFactory = ingredientFactory
        }

        func prepare() {
            dough = ingredientFactory.createDough()
            sauce = ingredientFactory.createSauce()
            cheese = ingredientFactory.createCheese()
        }
    }
    ```
    사용하는 곳에서는 CheesePizza 인스턴스 생성 시 적절한 원재료 팩토리를 넣어주면 되므로 이전에 비해 조금 더 추상화가 이루어졌다. 이제 한국 지점의 치즈피자가 필요해도 CheesePizza 클래스는 재사용할 수 있다. 원재료 팩토리만 추가해서 생성자로 넣어주면 됨!
    ```Swift
    // 수정 전
    let nyCheesePizza = NYCheesePizza()
    let chicagoCheesePizza = ChicagoCheesePizza()

    // 수정 후
    let nyCheesePizza = CheesePizza(ingredientFactory: NYPizzaIngredientFactory())
    let chicagoCheesePizza = CheesePizza(ingredientFactory: ChicagoPizzaIngredientFactory())
    ```
- 추상 팩토리 패턴에서 메소드가 팩토리 메소드로 구현되는 경우가 종종 있다.
    - 추상 팩토리의 서브클래스에서 어떤 객체를 리턴할지 결정하는 것. 
    - 어떤 서브클래스를 쓰느냐에 따라 concrete 타입이 결정됨.

### 팩토리 메소드와 추상 팩토리의 차이
- Product를 만들 때 클래스를 쓰느냐(상속) 객체를 쓰느냐(구성)
    - 클래스와 객체의 차이? : 클래스는 코드로 보는 그것. 구체적인 값이 들어가있지 않음. 객체는 클래스에 구체적인 값을 넣어 만든 것.
- 팩토리 메소드는 단일 객체를, 추상 팩토리는 여러 객체를 만들 때 유용(193P)

