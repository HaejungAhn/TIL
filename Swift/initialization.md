2022-02-24

---
- **초기화**

- 클래스, 구조체, 열거형 인스턴스를 사용하기 위해 준비하는 단계
- 무엇을 할 수 있나?
    
    ① 각 인스턴스의 프로퍼티에 초기값을 설정함 
    
    ② 새로운 인스턴스가 사용될 준비가 되기 전 다른 설정이나 초기화를 수행함(예시?)
    

- **초기화 하기**

- 저장 프로퍼티 : 인스턴스의 모든 프로퍼티에 적절한 값이 설정되어 있거나 이니셜라이저에서 반드시 초기화되어야 한다.
- 저장 프로퍼티가 초기화될때 프로퍼티 옵저버는 실행되지 않는다!
    - 테스트 코드
        
        ```swift
        class Test {
            var name: String {
                willSet {
                    print("willSet : \(newValue)")
                }
                didSet {
                    print("didSet : \(oldValue)")
                }
            }
            
            init(name: String) {
                self.name = name
            }
        }
        
        let a = Test(name: "Grace") // nothing to print
        a.name = "Tom" // willSet : Tom // didSet : Grace
        
        ```
        
- 이니셜라이저에서도 메소드와 마찬가지로 argument label과 parameter name을 가질 수 있다. 이니셜라이저의 함수 명은 `init` 으로 모두 동일하기 때문에 argument label과 paramger name이 어떤 이니셜라이저를 호출할지 식별하는데 아주 중요한 역할을 한다.
- 클래스 인스턴스에서 상수 프로퍼티의 초기화 값 할당은 해당 프로퍼티를 소유하고 있는 원래 클래스에서만 가능하다. 서브클래스에서는 수정 불가능함.
- 명시적으로 이니셜라이저를 추가하지 않더라도 가지고 있는 프로퍼티의 초기값이 모두 설정되어 있는 상태라면 default initializer가 제공된다. ( 더불어 클래스라면 superclass가 없는 basic class여야함. superclass가 있더라도 superclass의 모든 프로퍼티가 초기화되어 있는 상태라면 default initializer만으로도 초기화 가능 )
- memberwise initializer - 구조체타입은 자동적으로 자기가 가지고 있는 프로퍼티 전체를 초기화할 수 있는 memberwise initializer를 가지고 있음. 인스턴스 생성 시점에 각 프로퍼티의 값을 할당해줄 수 있음. 만약 기본값이 있는 프로퍼티라면 생략 가능

- **초기화 위임하기(Initializer Delegation)**

- 이니셜라이저 내에서 다른 이니셜라이저를 호출하여 초기화 작업을 위임함. 이를 통해 코드의 중복을 방지할 수 있음.
- 값 타입(Struct, Enum)의 경우 상속이 불가함. 따라서 자기 자신의 이니셜라이저만 호출가능. `self.init` 으로 호출하며 해당 코드는 이니셜라이저 내에서만 호출할 수 있다.

- **클래스 상속과 초기화**

- 클래스는 상속이 가능함. 따라서 클래스의 경우 superclass의 모든 저장 프로퍼티가 초기화 중 반드시 초기값을 가지고 있어야 한다.
- 이를 위해 2가지 종류의 초기화 구문을 제공함
    - designated initializer ⇒ 지정된 이니셜라이저
        
        **지정된 이니셜라이저**의 경우 **현재 클래스의 모든 sotred property를 적절히 초기화**하고 **상위 클래스의 지정된 이니셜라이저를 호출**하여 모든 프로퍼티가 초기값을 가질 수 있도록 설정해야함. 지정된 이니셜라이저는 상위 클래스의 이니셜라이저까지 연속적으로 호출하는 funel 지점이다.
        
    - convenience initializer ⇒ 편의 이니셜라이저
        
        `convenience` 키워드 사용. 지정된 이니셜라이저의 파라미터를 기본값으로 설정하여 호출한다. 편의를 제공함.
        
- 클래스 타입 이니셜라이저의 초기화 위임하기 - 규칙
    
    규칙 1 : 지정된 이니셜라이저는 자신의 바로 상위 클래스의 지정된 이니셜라이저를 호출해야 한다. (지정된 초기화는 funel역할을 한다는 것과 연결시키면 쉽게 기억할 수 있다 )
    
    규칙 2 : convenience 이니셜라이저는 반드시 동일 클래스에 있는 다른 이니셜라이저를 호출해야 한다.
    
    규칙 3 : convenience 이니셜라이저는 반드시 궁극적으로 지정된 이니셜라이저를 호출해야 한다.
    
    지정된 이니셜라이저는 항상 위로 위임해야 하고 / convenience 이니셜라이저는 반드시 옆으로 위임해야 한다.
    

[Swift - Init과 convenience init, required init](https://www.zehye.kr/swift/2020/07/25/swift_init_convenience_init/)

엄밀히 말하면 convenience init은 **보조 이니셜라이저** 이다.클래스의 원래 이니셜라이저인 init을 도와주는 역할을 하는 것이다.

designated init의 파라미터 중 일부를 기본값으로 설정해 이 convenience init안에 designated init을 호출하여 초기화를 진행할 수 있다. 그렇기 때문에 **convenience init을 사용하기 위해서는 designated init이 반드시 먼저 선언되어야 한다.**

**- 2단계 초기화**

Swift의 클래스 초기화는 2단계로 이루어진다. (위로 delegation 됐다가 다시 아래로 내려오는 모양새임)

1단계 - **stored property가 해당 프로퍼티를 소유하고 있는 클래스에 의해 초기값 할당**됨. 모든 초기값이 할당되면 2단계 시작

편의 혹은 지정된 이니셜라이저가 호출됨. 메모리가 할당되지만 아직 초기화된 상태는 아님. 상위 클래스의 이니셜라이저가 delegation 되고 **모든 저장프로퍼티의 값이 초기화되면 메모리가 지정된 값으로 초기화됨**.

2단계 - 각 클래스는 자신이 소유한 stored property를 커스터마이징할 기회가 주어진다.

self로 접근해서 프로퍼티 수정 가능, 인스턴스 메소드 호출 가능

마지막 - 새 인스턴스 사용할 준비 완료!

- [2단계 초기화를 위한 안전점검] 전체 내용
    - **안전 점검 1**
        
        지정된 초기화 구문은 상위 클래스 초기화 구문에 위임되기 전에 클래스에 의해 도입된 모든 프로퍼티가 초기화 되었는지 확인합니다.
        
    
    위에서 언급했듯이 객체의 메모리는 모든 저장된 프로퍼티의 초기상태가 알려지면 완전히 초기화 된것으로 간주합니다. 이 규칙이 충족하려면 지정된 초기화 구문은 체인을 넘기기 전에 모든 프로퍼티가 초기화 되었는지 확인해야 합니다.
    
    - 
        
        **안전 점검 2**
        
        지정된 초기화 구문은 상속된 프로퍼티에 값을 할당하기 전에 상위 클래스 초기화 구문에 위임해야 합니다. 그렇지 않으면 지정된 초기화 구문에 할당한 새로운 값은 자체 초기화 부분으로 상위 클래스에 의해 덮어 쓰여집니다.
        
    - 
        
        **안전 점검 3**
        
        편의 초기화 구문은 *모든*프로퍼티 (같은 클래스에 정의한 프로퍼티 포함)에 값을 할당하기 전에 다른 초기화 구문에 위임해야 합니다. 그렇지 않으면 편의 초기화 구문에 할당한 새로운 값은 자체 클래스의 지정된 초기화 구문에 의해 덮어 쓰여집니다.
        
    - 
        
        **안전 점검 4**
        
        초기화 구문은 첫번째 초기화가 완료될 때까지 인스턴스 메서드를 호출하거나 인스턴스 프로퍼티의 값을 읽거나 `self` 를 값으로 참조할 수 없습니다.
        
- 지정된 이니셜라이저는 상속받은 프로퍼티에 값을 할당하기 전에 상위 클래스의 이니셜라이저를 호출해야 한다. (그렇지 않으면 상속받은 프로퍼티의 값이 덮어씌워질 수 있음)
- convenience 이니셜라이저는 해당 클래스가 소유한 모든 프로퍼티에 값을 할당하기 전에 다른 이니셜라이저를 호출해야 한다. (이렇게 하지 않으면 convenience 이니셜라이저에서 설정한 값이 지정된 이니셜라이저에 의해 덮어쓰여짐)

- **클래스의 이니셜라이저 오버라이딩**

- `override` 키워드를 이용한다.
- 단, convenience 이니셜라이저는 자기 자신의 다른 이니셜라이저만 호출할 수 있기 때문에 하위클래스에서 overriding을 할 수 없다.
    - 근데 링크의 다른 예제를 보니까 convenience init인데 override 키워드를 붙여 쓰고 있음?!
        
        하위 클래스 입장에서 보면 상위 클래스의 `init(name: String)` 이니셜라이저를 convenience init으로 오버라이딩 함.(**즉, 상위 클래스의 지정 이니셜라이저를 convenience init으로 구현한 것**) → Food 클래스의 지정 이니셜라이저를 구현함. 따라서 Food 클래스의 convenience 이니셜라이저도 호출할 수 있게 됨.
        
        ```swift
        class Food {
            var name: String
            init(name: String) {
                self.name = name
            }
            convenience init() {
                self.init(name: "[Unnamed]")
            }
        }
        
        class RecipeIngredient: Food {
            var quantity: Int
            init(name: String, quantity: Int) {
                self.quantity = quantity
                super.init(name: name)
            }
            **override convenience init(name: String)** {
                self.init(name: name, quantity: 1)
            }
        }
        ```
        
    
- 예제 코드
    
    ```swift
    class Vehicle {
        var numberOfWheels = 0
        var description: String {
            return "\(numberOfWheels) wheel(s)"
        }
        
        convenience init(number: Int) {
            self.init()
            /// self를 호출하기 전에 반드시 self.init()으로 초기화가 되어 있어야 함.
            self.numberOfWheels = number
        }
    }
    
    class Bicycle: Vehicle {
        override init() {
            super.init()
            // 상위 클래스의 값을 수정하기 전에는 반드시 super.init()이 먼저 호출되야 한다. 
    				// 그렇지 않으면 컴파일 에러 발생
            numberOfWheels = 2
        }
        
        convenience init(number: Int) {
            print("convenience Bicycle")
            self.init()
        }
    }
    
    class Hoverboard: Vehicle {
        var color: String
        init(color: String) {
            self.color = color
            // 만약 상위클래스의 값을 수정하지 않는다면 하위클래스는 명시적으로 super.init()을 호출하지 않아도 된다.
            // super.init() implicitly called here
        }
        override var description: String {
            return "\(super.description) in a beautiful \(color)"
        }
    }
    
    var b = Bicycle(number: 3)
    print(b.numberOfWheels)
    ```
    

- **자동으로 상속되는 이니셜라이저**

기본적으로 하위 클래스는 상위 클래스의 이니셜라이저를 자동으로 상속받지 않는다. 다만 하위 클래스에서 새롭게 추가된 프로퍼티에 기본값을 제공하면 아래 2가지의 규칙이 적용되고 자동으로 상위 클래스의 이니셜라이저를 상속받을 수 있다.

default : 새롭게 추가된 프로퍼티에 대한 기본값이 모두 설정됨

규칙 1; 하위 클래스가 지정된 이니셜라이저를 정의하지 않았다면, 자동으로 상위 클래스의 지정된 이니셜라이저를 모두 상속받음

규칙 2; 상위 클래스의 지정 이니셜라이저를 모두 구현했거나 override 하면, 상위 클래스의 convenience 이니셜라이저는 자동으로 상속된다. 

이니셜라이저 오버라이딩, 자동상속 관련 예제코드

```swift
class Food {
    var name: String
    init(name: String) {
        print("Food : designated")
        self.name = name
    }
    convenience init() {
        print("Food : convenience")
        print(type(of: self))
        self.init(name: "[Unnamed]")
    }
}

class RecipeIngredient: Food {
    var quantity: Int
    init(name: String, quantity: Int) {
        print("RecipeIngredient : designated")
        self.quantity = quantity
        super.init(name: name)
    }
    override convenience init(name: String) {
        print("RecipeIngredient : convenience")
        self.init(name: name, quantity: 1)
    }
    
    // convenience 이니셜라이저를 자동으로 상속받게 됨(Food의 지정이니셜라이저를 모두 구현했기 때문에)
}

let b = RecipeIngredient()
print("ingredient name is '\(b.name)'")
print("ingredient quantity is \(b.quantity)")

/*
-- Print --
Food : convenience
RecipeIngredient
RecipeIngredient : convenience
RecipeIngredient : designated
Food : designated
ingredient name is '[Unnamed]'
ingredient quantity is 1
*/
```

`let b = RecipeIngredient()` 실행되면

1. Food의 `convenience init()` 호출
2. self의 타입은 RecipeIngredient 이기 때문에 argument label과 매개변수 타입이 일치하는 RecipeIngredient 클래스의 `override convenience init(name: String)`을 호출하게 되는거임.

- **실패 가능한 초기화 구문(Failable Initializers) : `init?`**

실패 가능한 초기화 구문과 실패불가능한 초기화 구문은 동일한 파라미터 타입과 이름을 가져서는 안된다.

failiable 이니셜라이저로 초기화면 옵셔널 값을 생성한다. (실패할 수 있는 부분에서 return nil을 작성해야 함)

대표적인 예시가 숫자 변환임. 

```swift
let successNumber = Int("123") //Optional(123)
let failNumber = Int("가나다")   //nil

// init(exactly:) : 숫자 타입간 변환을 확실하게 해준다.
let x = Int(exactly: 21.0)
// x == Optional(21)
let y = Int(exactly: 21.5)
// y == nil

struct Animal {
    let species: String
    **init?**(species: String) {
        if species.isEmpty { **return nil** }
        self.species = species
    }
		// 아래처럼 동일한 파라미터 타입과 이름을 가져서는 안된다. 
    // 컴파일 에러 발생함 : invalid redeclaration of 'init(species:)'
		//init(species: String) {
    //    self.species = species
    //}

		init(animalName: String) {
        self.species = animalName
    }
}

let a = Animal(species: "Rabbit") // Optional<Animal>
print(type(of: a))
let b = Animal(animalName: "Lion") // Animal
print(type(of: b))
```

- **열거형의 Failable Initializer**

열거형도 이니셜라이저가 있었나,,? `init?` 을 활용하고 self의 값을 직접 지정하는 형태임.

**① 원시값이 없다면**

```swift
// Failable init이 없다면
enum TempUnit {
    case kelvin, celsius, fahrenheit
}

// 리턴값 : kelvin
let c = TempUnit.kelvin 
// runtime error
let d = TempUnit.test 
```

```swift
enum TempUnit {
    case kelvin, celsius, fahrenheit
    init?(**symbol**: Character) {
        switch symbol {
        case "K":
            self = .kelvin
        case "C":
            self = .celsius
        case "F":
            self = .fahrenheit
        default:
            return nil
        }
    }
}

// 리턴값 : Optional(TempUnit.kelvin)
let c = TempUnit(symbol: "K")
// nil
let d = TempUnit(symbol: "T")

```

**② 원시값이 있다면**

별도의 처리를 해주지 않아도 해당하는 case가 없을 경우 nil을 리턴한다.

- **Propagation of Initialization Failure (초기화 실패 전파)**

상위 클래스의 프로퍼티에 대한 failure 체크는 상위 클래스에 위임.

하위 클래스에서는 자기가 가지고 있는 프로퍼티에 대한 failure 체크만 진행하고 처리하면 됨.

- **Failable Initializer 오버라이딩**

상위 클래스의 failable한 이니셜라이저를 하위 클래스에서 failable 혹은 un-failable 이니셜라이저로 오버라이딩 할 수 있음. 단, 상위 클래스의 un-failable한 이니셜라이저를 failable 이니셜라이저로 오버라이딩 할 수 없다.

- 실패가능한 초기화 구문은 옵셔널이기 때문에 경우에 따라 강제언래핑으로 호출할 수도 있다. 단, nil이 리턴될 경우 런타임 에러가 발생할 수 있음.

```swift
class UntitledDocument: Document {
    override init() {
        **super.init(name: "[Untitled]")!**
    }
}
```

- **필수 초기화 구문 : `required`**

`required` 를 이용해 이니셜라이저를 작성하면 모든 하위 클래스가 반드시 이를 구현해야 함을 나타낸다. 필수 이니셜라이저의 경우 하위클래스에서 `override` 를 함께 작성하지 않음.

- **클로저 또는 함수를 사용하여 기본 프로퍼티 값 설정**

stored property의 기본값으로 클로저 또는 함수를 사용할 수 있음. 프로퍼티가 속한 타입(클래스, 구조체, 열거형)의 인스턴스가 초기화될 때마다 클로저나 함수가 호출되고 리턴값이 기본값으로 할당됨.

아래는 클로저를 사용해서 stored proeprty를 생성하는 예시이다. 

클로저 끝에 `()` 이 붙음을 알 수 있는데 이는 Swift가 클로저를 즉시 실행하도록 만든다. 만약 이게 없다면 프로퍼티에 클로저의 반환값을 저장하는게 아니라 클로저 자체를 저장하는게 된다.

❗️  주의할 점 - 클로저가 실행될 때 클래스에 있는 다른 프로퍼티는 아직 초기화되지 않았다는 것을 기억해야 한다. 또한 `self`를 이용하거나 인스턴스 메소드를 사용하는 것도 불가하니 참고하자!

```swift
class SomeClass {
    let someProperty: SomeType = {
        // create a default value for someProperty inside this closure
        // someValue must be of the same type as SomeType
        return someValue
    }**()**
}
```