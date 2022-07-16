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
#### <b>Factory Method 패턴</b>
- PizzaStore 안에 피자 생성 메소드를 위치시킨다.
- 이때 피자 생성 메소드는 추상화 된 상태여야 한다. `PizzaStore`의 서브클래스에서 피자 생성 메소드를 구현하도록 만들어야 한다.
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
            }
        }

        class NYPizzaStore: PizzaStore {
            func createPizza(type: PizzaType) -> Pizza {
                switch type {
                    case .cheese: 
                    return NYCheesePizza()
                    //..생략..
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
            func orderPizza(type: PizzaType) -> Pizza {
                // orderPizza의 기본 로직을 여기에 작성
            }
        }

        class NYPizzaStore: PizzaStore {

            override func createPizza(type: PizzaType) -> Pizza {
                switch type {
                    case .cheese: 
                    return NYCheesePizza()
                    //..생략..
                }
            }
        }
        ```
    - 이렇게 놓고 비교해보니 class보다는 Protocol을 사용하는게 더 적절해보인다. 왜냐하면 상위타입에서 Pizza 객체를 리턴하는게 꼭 필요하기 때문에.