사이드 프로젝트에 Swinject를 사용하게 되어 알아보게 됨.

## [Swinject](https://github.com/Swinject/Swinject)

- DI(Dependency Injection) 프레임워크
    - DI란 디펜던시를 해결하기 위해 Inversion of Control을 구현하는 소프트웨어 디자인패턴이다.
- 🤔 *DI는 개념만 놓고 보면 그렇게 복잡해보이진 않은 것 같은데, 왜 프레임워크가 만들어졌을까?*
- Swinject의 역할 : 앱이 느슨하게 결합된 컴포넌트로 분리되도록 도와준다.
- Swinject는 앱의 디펜던시를 쉽게, 유창하게(fluently) 정의하기 위해 Swift의 Generic 타입 시스템과 first class function(함수의 first class citizen으로서의 특성을 말하는 것 같다.)의 도움을 받았다.

---

<br>

### **[DI Container](https://github.com/Swinject/Swinject/blob/master/Documentation/DIContainer.md)**

Swinject를 구성하는 요소들(serivce, component, factory, container)과 주요 메소드(register, resolve), 내부 동작(key 등록)에 대해 간략히 소개한다.

- Swinject를 사용하는 방법
    1. 의존성을 해결할 타입을 등록한다(어디에?)
    2. 그런 다음 DI Container를 이용해 1번에서 등록한 타입들의 인스턴스를 얻어온다. 그러면 타입들의 디펜던시는 DI container에 의해 자동으로 해결된다.
- Swinject에서 사용하는 common name들의 역할은 아래와 같다.<br>
    - `Service` 의존하는 타입(dependent type)을 위한 인터페이스를 정의한 프로토콜
    - `Component` service를 구현한 concrete type
    - `Factory` component를 인스턴스화 하는 함수 또는 클로저
    - `Container` component 인스턴스들의 컬렉션. Swinject에서 DI Container를 의미한다.

### Registration in a DI Container

- service와 각 service를 채택한 컴포넌트는 Container의 `register` 라는 메소드를 통해 등록되어질 수 있다.
    
    ```swift
    @discardableResult
    public func register<Service>(
    	_ serviceType: Service.Type,
      name: String? = nil,
      factory: @escaping (**Resolver**) -> Service
    ) -> ServiceEntry<Service> {
    	return _register(serviceType, factory: factory, name: name)
    }
    ```
    
    - 🤔 *등록되어진다는 것은 무슨 의미이지?*
- `register` 메소드는 컴포넌트에 대한 Service 타입과 Factory 메소드를 매개변수로 받는다.
- 만약에 컴포넌트가 다른 service를 의존하고 있다면, factory 메소드는 전달된 Resolver의 `resolve` 메소드를 호출할 수 있다. 이를 통해 의존성을 주입할 수 있다.
    - Reslove라는 단어의 의미를 이해하는게 조금 힘들었는데 DI Container에 있는 내용을 보니 “의존성을 해결한다”는 의미에서 resolve, resolver라는 네이밍을 사용한 것으로 보임.
- dependency의 실제 underlying type은 component 인스턴스가 만들어지고 난 이후 알 수 있다.

<br>

**service registration code snippet**

```swift
let container = Container()
container.register(Animal.self) { _ in Cat(name: "Mimi") }
container.register(Person.self) { r in
    PetOwner(name: "Stephen", pet: r.resolve(Animal.self)!)
}

let animal = container.resolve(Animal.self)!
let person = container.resolve(Person.self)!
let pet = (person as! PetOwner).pet
```

컴포넌트를 등록(register)하고 난 이후, container의 `resolve` 메소드를 통해 service 인스턴스를 받을 수 있다.

만약 container에 등록되지 않은 service type을 호출하면 container는 nil을 리턴한다.

<br>

### Named Registration in a DI Container

만약 동일한 service type에 대해 두개 이상의 컴포넌트를 등록하고 싶다면, 구분을 위해 registration에 대한 이름을 지을 수 있다.

특정 이름을 가진 컴포넌트를 가져오고 싶다면 이름을 사용하면 됨.

```swift
let container = Container()
container.register(Animal.self, name: "cat") { _ in Cat(name: "Mimi") }
container.register(Animal.self, name: "dog") { _ in Dog(name: "Hachi") }

let cat = container.resolve(Animal.self, name:"cat")!
let dog = container.resolve(Animal.self, name:"dog")!
```

<br>

### Registration with Arguments in a DI Container

`register` 메소드에 의해 전달되어진 factory closure는 service가 resolved될 때 전달되는 argument를 가질 수 있다. 

```swift
// argument를 지정할 때 별도 타입을 명시하지 않아도 됨(아래 코드의 name에 타입이 지정되지 않은 걸 볼 수 있음)
// 왜냐하면 추론이 되기 때문임. Horse(name:) 이니셜라이저에서 name은 String 타입이기 때문에 문제가 없는 것.
// 실제 들어온 매개변수의 타입과 추론된 타입이 일치하지 않으면 Registration key가 존재하지 않다고 판단하고 nil을 리턴.
container.register(Animal.self) { _, name in
    Horse(name: name)
}
container.register(Animal.self) { _, name, running in
    Horse(name: name, running: running)
}
```

위와 같이 register를 해주고, 실제 사용할 때 (런타임에) 필요한 argument들을 넘겨줄 수 있다.

```swift
// argument 한개만 넘겨줄 때는 resolve(_:argument:)
let animal1 = container.resolve(Animal.self, argument: "Spirit")!

// argument를 두개 이상 넘겨줄 때는 resolve(_:argument:,_:)
let animal2 = container.resolve(Animal.self, arguments: "Lucky", true)!
```

<br>

### Registration Keys

주어진 service 타입에 대한 componenet의 등록은 내부적으로 생성되어진 키와 함께 container에 저장된다. container는 이 키를 사용해서 service dependency를 resolve하려 한다.

키는 아래와 같이 구성되어 있다.(약간 함수같기도?)

- The type of the service
- The name of the registration
- The number and types of the arguments

만약에 동일한 키가 이미 존재하는데 또 만들었다 → 그럼 덮어쓰기함.

---

<br>

## [Injection Patterns](https://github.com/Swinject/Swinject/blob/master/Documentation/InjectionPatterns.md)

injection 패턴에 어떠한 것들이 있는지에 대한 설명을 해주는 것 같음.

### Initializer Injection

이니셜라이저로 주입하는 것 아닐까? 아래 코드처럼. ㅇㅇ 맞음.

Initializer Injection은 의존하는 인스턴스의 이니셜라이저를 통해 디펜던시를 전달하는 패턴이다. 의존하는 인스턴스가 디펜던시 없이 작동할 수 없을 때 사용하기 적절한 방법이다.

```swift
final class PetOwner {
	
	private let pet: Animal

	init(pet: Animal) {
		self.pet = pet
	}
	
}
```

<br>

### Property Injection

~~메소드의 매개변수로 디펜던시를 전달해서 내부에 할당해주는 것 같음. 아님.~~

property injection은 setter property를 통해 의존하는 인스턴스에 디펜던시를 전달함. 의존하는 인스턴스에 해당 의존성이 옵셔널할 때 사용하기 적절한 방법이다. 

```swift
final class PetOwner2 {
	var pet: Animal?
	init() {}
}

let container = Container()
container.register(Animal.self) { _ in Cat() }
container.register(Person.self) { r in
    let owner = PetOwner2()
    owner.pet = r.resolve(Animal.self)
    return owner
}

// 위와 같이 하거나 혹은 initCompleted 콜백을 사용할 수 있다.
container.register(Person.self) { _ in PetOwner2() }
    **.initCompleted** { r, p in
        let owner = p as! PetOwner2
        owner.pet = r.resolve(Animal.self)
    }
```

<br>

### Method Injection

property injection에서 내가 설명한(취소선 그은) 내용이 여기에 해당되는 듯.

---

<br>

### ****[Circular Dependencies](https://github.com/Swinject/Swinject/blob/master/Documentation/CircularDependencies.md)****

- 서로가 서로를 의존하는 디펜던시를 circular dependencies라고 함.
- Swinject에서 순환 종속성을 정의하려면 종속성 중 하나를 프로퍼티를 통해 주입해야 한다.(Property Injection)

**Initializer/Property Dependencies**

- 한쪽은 이니셜라이저를 통해 의존성이 생기고, 다른 한쪽은 프로퍼티 injection을 통해 의존성을 주입할 수 있음.
- 이때 무한 recursion을 피하기 위해 반드시 initCompleted 콜백을 통해 다른 하나를 주입해줘야 한다.

**Property/Property Dependencies**

- 위와 마찬가지로 initCompleted 콜백을 통해 다른 하나를 주입해줘야 함.

**Initializer/Initializer Dependencies**

- 지원하지 않음. 무한 recursion 발생.

순환 종속성을 resoloving할 때 둘 중 하나의 factory method가 두번 호출될 수도 있다. 결과로 만들어진 인스턴스 중 하나만 사용이 되지만 이런 특성은 때로 문제가 될 수 있다. 만들어진 인스턴스를 이용해 side effect가 필요한 경우 등..

이런 것을 피하기 위해 양쪽 모두 initCompleted 콜백을 사용하면 된다.

---

<br>

### [Object Scope](https://github.com/Swinject/Swinject/blob/master/Documentation/ObjectScopes.md)

object scope란 DI Container를 통해 제공된 인스턴스가 어떻게 시스템 안에서 공유되어야 할지를 결정하는 config 옵셔이다. 

해당 설정 값은 register를 할 때, `inObjectScope` 메소드를 통해 지정할 수 있다. factory 클로저가 value type을 리턴하게 되면 해당 메소드가 있어도 무시하게 된다. 왜냐하면 value type은 share 되어질 수 없기 때문에.

```swift
container.register(Animal.self) { _ in Cat() }
    .inObjectScope(.container)
```

<br>


### Built-in scopes

Swinject에서 제공하는 기본 스코프

**Transient**

- 📒  일시적인, 잠깐 머무르는
- container가 제공하는 인스턴스는 share 되지 않는다. 다시말해 type이 resolve될때마다 container는 항상 새로운 인스턴스를 만든다.

**Graph (the default scope)**

- container의 resolve 메소드를 직접적으로 호출하면 transient처럼 항상 인스턴스를 새롭게 만들지만, factory 클로저에서 resolve된 인스턴스는 object graph를 구성하는 동안 공유된다.
    - A 뷰컨이 있고 B 뷰모델이 있음. A 뷰컨에서 factory 클로저로 B 뷰모델을 주입받음.
    - C 뷰컨에서도 factory 클로저에서 B 뷰모델을 주입받음
    - 만약 C 뷰컨에서 사용하는데 B 뷰모델이 초기화됨을 가정하고 개발을 진행한다면 오류가 발생할 가능성도 있음. 이걸 찾을 수 있을까? 메모리 관리는 어떻게..?

**Container**

- container를 통해 만들어진 인스턴스는 container와 그 container의 child 안에서 공유되어질 수 있다.
- DI Framework에서 해당 스코프 타입은 Singleton으로도 알려져있다.

**Weak**

- Container 스코프와 비슷하긴 한데 인스턴스에 대한 모든 strong reference가 존재하지 않게 되면 더이상 share되지 않고 새로운 인스턴스가 만들어짐.

Value type은 기본적으로 이 스코프에 해당하지 않음.

스코프를 커스텀할 수도 있는데 이것까지 지금 볼 필요는 없을 것 같아 [링크](https://github.com/Swinject/Swinject/blob/master/Documentation/ObjectScopes.md#custom-scopes)만 남겨둠.

---
### [Mics](https://github.com/Swinject/Swinject/blob/master/Documentation/Misc.md)

- value type도 component로 사용할 수 있다는 내용, Service와 Component가 동일한 타입일 수도 있다는 내용(Self-registration 또는 Self-binding)이 있다.
- Resolution failure logging에 대한 내용도 나옴.

---

### [Container Hierarchy](https://github.com/Swinject/Swinject/blob/master/Documentation/ContainerHierarchy.md)

container 계층구조는 DI의 registration을 공유하기 위한 container의 tree이다.

parent container에 등록된 service type은 child containers에서도 resolved가 가능하다. (근데 child 타입에서 등록한 service가 parent에서 resovled될 순 없음)

`init(parent:)` 이니셜라이저를 통해 parent-child 관계를 맺을 수 있다.

```swift
let parentContainer = Container()
parentContainer.register(Animal.self) { _ in Cat() }
let childContainer = Container(parent: parentContainer)

let cat = childContainer.resolve(Animal.self)
print(cat != nil) // prints "true"
```

---

## [Modularizing Service Registration](https://github.com/Swinject/Swinject/blob/master/Documentation/Assembler.md)

service 등록 모듈화하기

이 feature는 `Assembly` 에서 서로 관련된 Service 정의를 그룹핑할 수 있도록 도와준다. 이 feature를 사용하면…

- 관련있는 Service들을 한군데 모아 관리할 수 있다.
- 공유된 Container를 제공한다.
- Allow registering different assembly configurations, which is useful for swapping in mock implementations. ?
- container가 완전히 구성되면 notify한다.

### Assembly

service 정의가 등록되어질 수 있는 공유된 container를 제공하는 프로토콜이다. 공유된 container는 Assembler에 등록된 모든 Assembly의 service definition을 포함하게 된다. (아래 bold 처리한 부분 확인)

```swift
class ServiceAssembly: Assembly {
    func assemble(container: Container) {
        container.register(FooServiceProtocol.self) { r in
           return FooService()
        }
        container.register(BarServiceProtocol.self) { r in
           return BarService()
        }
    }
}

class ManagerAssembly: Assembly {
    func assemble(container: Container) {
        container.register(FooManagerProtocol.self) { r in
           return FooManager(service: r.resolve(**FooServiceProtocol**.self)!)
        }
        container.register(BarManagerProtocol.self) { r in
           return BarManager(service: r.resolve(**BarServiceProtocol**.self)!)
        }
    }
}
```

### Load aware

`Assembly` 를 사용하면 Assembly에 의해 container가 완전히 로드되었음을 알 수 있다.

### Assembler

Assembly 인스턴스와 Container를 관리할 책임을 가진다. Assembler를 사용하면 Container는 오직 Assembler에 등록된 Assembly들에게 노출된다. 그리고 Assembly에 대한 registration을 엄격하게 제한하는 `Resolver` 프로토콜을 통해서만 애플리케이션 액세스를 제공한다.

```swift
let assembler = Assembler([
    ServiceAssembly(),
    ManagerAssembly()
])

// Now you can resolve any components from either assembly:
let fooManager = assembler.resolver.resolve(FooManagerProtocol.self)!

// Assembler는 약간 AnyPublisher 같은 느낌이라고 해야하나? 구체적인 Assembly를 클라이언트 코드로부터 가리는 그런 역할을 하는 듯?

// You can also lazy load assemblies(나중에 Assembly를 추가할 수도 있음)
assembler.applyAssembly(LoggerAssembly())
```

- Assembler에 대한 strong reference를 가져야한다. 그렇지 않으면 Container가 deallocated될 수 있음.
- You **MUST** hold a strong reference to the `Assembler` otherwise the `Container` will be deallocated along with your assembler
- If you are lazy loading your properties and assemblies you must load your properties **first** if you want your properties to be available to load aware assemblies when `loaded` is called
- If you are lazy loading assemblies and you want your load aware assemblies to be invoked after all assemblies have been loaded then you must use `addAssemblies` and pass all lazy loaded assemblies at once