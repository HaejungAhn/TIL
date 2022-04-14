2022-02-24

---
- 상속은 클래스만 가능하다.

- subclass는 superclass의 메소드, 프로퍼티, 서브스크립트에 접근할 수 있으며 오버라이딩도 가능하다.

- 기본 클래스(base class) : 어떠한 상속도 받지 않은 클래스

- 하위 클래스(**subclassing**) : 기존 클래스를 상속받아 새로운 클래스를 만드는 작업

- 재정의(overriding) : superclass의 인스턴스 및 타입 메소드, 인스턴스 및 타입프로퍼티, 서브스크립트를 subclass에서 새롭게 정의할 수 있음. `override` 키워드 사용

- **프로퍼티 재정의**

- 상속받은 프로퍼티가 저장프로퍼티던 계산 프로퍼티던 상관없이 재정의를 위해 getter와 setter를 지정할 수 있다. 상속받은 프로퍼티가 stored인지 computed인지는 서브클래스에서 알 수 없다. 상속받은 프로퍼티의 타입과 이름만 알고 있음.
- 서브클래스에서는 상속받은 read-only 프로퍼티를 read-write 프로퍼티로 재정의할 수 있다. 하지만 read-wrtie 프로퍼티를 read-only 프로퍼티로 바꿀 수는 없다. ( 다음과 같은 에러 발생 : `cannot override mutable property with read-only property '프로퍼티명'` )

- **프로퍼티 옵저버 재정의**

- 상속받은 stored property의 상수, 상속받은 read-only computed property는 프로퍼티 옵저버 추가할 수 없음.
    
    → 상수로 선언된 저장프로퍼티는 값 변경이 안되기 때문에 옵저버를 추가하여 계산 프로퍼티로 변경하는게 불가함.
    
    → 읽기 전용 계산 프로퍼티의 경우 setter가 없음. 프로퍼티 옵저버는 값이 세팅되는 시점을 감지하고 호출되는건데 값 세팅자체가 안되니까 옵저버가 필요 없는거지.
    

- **`final`**

오버라이딩을 막는다. extension 안에서도 final 사용 가능