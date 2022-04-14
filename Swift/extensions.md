2022-02-23

---
Extension : 확장

*−  👀  참고 : [https://blog.yagom.net/529/](https://blog.yagom.net/529/)*

- 클래스, 구조체, 열거형, 프로토콜에 **새로운 기능을 추가할 수 있음**
  (제네릭 타입 포함. 다 이름 있는 타입인듯)

- **Extension을 사용하는 이유** : 기능을 추가하려는 클래스, 구조체, 열거형, 프로토콜의 원래 코드에 접근이 불가하더라도( 혹은 코드를 알지 못하더라도? ) 타입만 알고 있다면 그 타입의 기능을 Extension을 통해 확장할 수 있다. 

✓ 추가할 수 있는 기능은 아래와 같다. 

- **computed 타입 및 인스턴스 프로퍼티** - 저장 프로퍼티 추가 못함, 기존 프로퍼티에 observer 추가 못함

- 🤔  왜 저장 프로퍼티는 추가하지 못할까?
    
    extension은 열거형도 사용할 수 있는데 여기에는 stored property를 추가할 수 없다. 그래서 computed property만 추가 가능하도록 만들지 않았을까..?
    
    Stored property가 모두 초기화되어야 인스턴스가 생성될 수 있기 때문이다? 근데 어떻게든 만들려면 
    
    왜 추가할 수 없나?
    
    Stored Property는 모두 초기화가 되어야 하기 때문에? 이니셜라이저와 연관있는 것으로 보임.
    
    extension에서 stored property를 사용하고 싶으면 어떻게 사용할 수 있는가?
    
    ① Swift : protocol + extension에 protocol 채택
    
    ② Objective-C : associated value
    

✓ **타입 메서드 / 인스턴스 메서드** 

- 단, 기능 추가는 가능해도 상속에서 사용할 수 있는 **오버라딩은 불가능**하다.    

- 자기자신을 변형하는 mutating instance method를 만들 수도 있다.

**✓ 이니셜라이저**

- 지정된 이니셜라이저나 deinit을 extension에 작성할 수는 없다. 
   (지정된 이니셜라이저, designated initializer; 모든 프로퍼티를 초기화하는 이니셜라이저를 말함)

   단, value type(struct, enum)에 커스텀 이니셜라이저가 만들어지지 않은 경우 extension에서 지정된 이니셜라이저를 생성할 수 있다. 

- 지정된 이니셜라이저를 작성할 수 없다는 것은 extension에서 전체 프로퍼티를 초기화하는 이니셜라이저를 작성할 수 없다는 것 ⇒ 그래서 프로퍼티 중 일부만 초기화될 수 있도록  `conveniece`  이니셜라이저만 추가할 수 있나보다. (initializer에 대한 공부 필요함)

- [여기](https://stackoverflow.com/questions/26877314/why-cant-we-add-designated-initialisers-in-extensions-in-swift) 답변 내용을 보면 접근지정자로 인해 원래 클래스, 구조체, 열거형 등에 선언된 프로퍼티에 접근할 수 없으면 문제가 발생할 수 있기 때문에 지정된 이니셜라이저를 사용하지 못한다는 내용도 있음.

- extension에 구현된 이니셜라이저에서 `self` 를 사용하고 싶다면 → 기존에 정의된 이니셜라이저를 호출하기 전까지 `self`에 접근할 수 없다.

✓ 서브스크립트

✓ 중첩 타입

    중첩 타입은 특정 타입 안에 선언된 타입을 말함.

✓ 특정 프로토콜을 준수할 수 있도록 기능 추가