## LSP(Liskov Substitution Principle) : 리스코프 치환 원칙
- 여기서 `치환`이란 상위타입을 하위타입으로 치환하는 것을 의미한다.
- 하위타입에 대한 리스코프의 정의는 다음과 같다.
    > 여기에서 필요한 것은 다음과 같은 치환(substitution)원칙이다. S 타입의 객체 o1 각각에 대응하는 T 타입 객체 o2가 있고, T 타입을 이용해서 정의한 모든 프로그램 P에서 o2의 자리에 o1을 치환하더라도 P의 행위가 변하지 않는다면, S는 T의 하위타입이다.
- 간단히 말해 **소스코드 상에서 상위 타입을 하위 타입으로 치환하더라도 완벽하게 동작해야한다**가 리스코프 치환 원칙이다. 
- 근데 의아할 수 있다. 하위 타입으로 치환했을 때 문제가 생기는 경우가 있나? 당연히 치환 되야하는거 아님?!
    ```Swift
    enum Drink {
        case water
        case milk
        case greenTea
    }

    protocol Cup {
        func fill(drink: Drink)
        func putInMicrowave()
        func heat(min: Int)
        func pull()
    }

    struct GlassCup: Cup {
        func fill(drink: Drink) { }
        func putInMicrowave() { }
        func heat(min: Int) { }
        func pull() { }
    }

    struct PlasticCup: Cup {
        func fill(drink: Drink) { }
        func putInMicrowave() { }
        func heat(min: Int) { }
        func pull() { }
    }

    let cup = Cup()
    cup.fill()
    cup.putInMicrowave()
    cup.heat(min: 2)
    cup.pull()
    
    ```
    위 예제 코드에서 Cup을 GlassCup으로 치환하면 정상적으로 동작할 것이다. 유리컵은 전자레인지에 돌려도 안에 있는 음료와 함께 따뜻해질뿐이니까.   
    그렇지만 PlasticCup으로 치환하면 정상적으로 동작하지 않는다. 플라스틱 컵은 전자레인지에 돌렸을 때 녹거든!   
    이런 케이스가 상위타입을 하위타입으로 치환했을 때 문제가 생기는 경우다.(이해를 돕기위한 예시임!! LSP를 위반한 형태가 구체적으로 저런 형태다? ㄴㄴ)
- 리스코프 치환원칙은 단순히 "상위 타입을 하위타입으로 치환할 수 있어야 한다"로 끝나는 것이 아니다. **사전에 약속한 기획대로 구현해야 하기 때문에 하위 타입을 생성할 때 상위 타입에서 구현한 원칙을 따라야 한다**가 핵심이다.
- 이어지는 내용들은 모두 [여기](https://pizzasheepsdev.tistory.com/9)를 참고한 것이다. 리스코프 치환원칙을 이해하는데 많은 도움이 됐다.
- 하위타입이 만족해야 하는 행동 조건들이 있다.
    - **하위 타입에서 선행 조건은 강화될 수 없다.**   
    선행 조건은 사전 조건이라고도 하는데 사전적 의미로 함수가 오류없이 실행되기 위한 모든 조건을 정의한 것이다.
        ```Swift
        enum someError: Error {
            case negative
            case zero
        }
        func someMethod(number: Int) throws {
            if number < 0 {
                throw someError.negative
            }
        }
        ```
        위 코드에서 매개변수로 들어온 값이 의도한대로 올바르게 들어왔는지 체크하는 것을 선행 조건이라고 한다.   
        `선행 조건이 강화됐다.` 라는 것은 선행 조건을 추가하는 걸 말하는데 리스코프 원칙은 이렇게 코드를 짜서는 안된다는 것을 말한다.   
        왜냐하면 상위 타입과 동일한 수준의 선행조건을 기대하고 코드를 사용하기 때문임.
        ```Swift
        // 위 코드의 someError enum 그대로 사용

        func someMethod(number: Int) throws {
            if number < 0 {
                throw someError.negative
            }
            if number == 0 {
                throw someError.zero
            }
        }
        ```
    - **하위 타입에서 후행 조건은 약화될 수 없다.**   
    후행 조건은 사후 조건이라고도 하는데 사전적 의미로 함수 호출 후 객체가 유효한 상태로 존재하는지를 검사하는 것이다.
        ```Swift
        func subtract(number: Int) {
            var result = number

            for i in 0...3 {
                result -= 5
            }

            // 아래 코드가 후행 조건에 해당된다. 함수 처리 후 리턴값이나 매개변수로 전달된 참조형 값이 제대로 된 값인지 검사하는 것.
            if result < 0 {
                result == 0
            }

            return result
        }
        ```
        후행 조건이 약화된다는 것은 값을 리턴할 때 조건을 완화시킨다는 것이다. 즉, 위 코드에서 `result < 0`을 검사하지 않고 리턴하는거임. 이렇게 짜면 안된다!
    - **하위 타입에서 상위타입의 불변조건은 반드시 유지되야 한다.**   
        ```Swift
        fileprivate protocol SomeProtocol {
            var num: Int { get set }
        }
        class Parent: SomeProtocol {
            fileprivate var num: Int = 10
            var number: Int {
                get { 
                    return num
                }
                set {
                    if newValue < 0 {
                        num = 0
                    }
                }
            }
        }
        ```
        위의 Parent 클래스의 `number`는 계산 프로퍼티로 private인 `num`의 getter/setter 역할을 한다.   
        만약 `number`에 음수가 들어올 경우 이를 체크해서 `num`에는 항상 0 혹은 양수가 들어가도록 처리되어 있다.
        ```Swift
        class Child: Parent {
            override var number: Int {
                get { 
                    return num
                }
                set {
                    num = newValue
                }
            }
        }
        ```
        하지만 Child 클래스에서 `number` 계산 프로퍼티의 음수 체크 조건을 제거하고 오버라이딩할 경우 `num`에는 항상 0 혹은 양수가 들어가야 한다는 상위 타입의 조건이 깨지는 문제가 발생한다.(음수 넣으면 음수도 할당됨)   
        이런 경우가 불변 조건이 깨지는 경우이며 LSP가 깨지는 경우이다.

- 하위타입이 지켜야하는 메소드 시그니처 조건들도 있다.
    - 하위타입 메소드는 상위타입 메소드에서 던져진 예외의 하위타입을 제외하고 새로운 예외를 던지면 안된다.   
    왜냐면 상위타입을 하위타입으로 치환했을 때 예상치 못한 에러를 던질 경우 받는 쪽에서 적절한 처리를 할 수 없기 때문이다.
    - 공변성과 반공변성
        변성 : Child가 Parent의 서브타입일때 Child의 컬렉션타입과 Parent의 컬렉션 타입 간 관계 변화를 의미한다.

- 👀 더 봐야할 것(출처 : [이 글](https://pizzasheepsdev.tistory.com/9))
    - 리스코프의 원칙은 새로운 객체 지향 프로그래밍 언어에 채용된 시그니처에 관한 몇 가지 표준적인 요구사항을 강제한다.
        - 하위형에서 메서드 인수의 반공변성 
        - 하위형에서 반환형의 공변성
        - 하위형에서 메서드는 상위형 메서드에서 던져진 예외의 하위형을 제외하고 새로운 예외를 던지면 안 된다.