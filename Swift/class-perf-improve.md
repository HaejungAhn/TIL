2022-03-04

---
heap allocation이나 reference counting 관련해서는 class가 아니라 struct와 같은 value type의 성능최적화 같다. 

**클래스 성능향상의 핵심은 “불필요한 동적 디스패치를 얼마만큼 줄일 수 있냐"**라고 생각함.

- 동적 디스패치는 무엇이고 왜 성능에 영향을 미치는가(성능 = 동적 디스패치의 과정을 얘기하면 됨)

- 불필요한 동적 디스패치를 줄이는 방법은 무엇인가

*— [👀 Swift 최적화 팁](https://makeeyaf.postype.com/post/9427533)*

*— ✨ [아직 안읽은] 👀 [Method Dispatch in Swift](https://www.rightpoint.com/rplabs/switch-method-dispatch-table)*

— 👀 [Dynamic Dispatch와 성능 최적화](https://jcsoohwancho.github.io/2019-10-11-Dynamic-Dispatch%EC%99%80-%EC%84%B1%EB%8A%A5-%EC%B5%9C%EC%A0%81%ED%99%94/)

- WMO(Whole Module Optimization) : Swift는 컴파일 시 각 파일을 별도로 컴파일하는데 WMO를 활성화하면 전체 파일을 한 뭉텅이로 보고 컴파일함. 따라서 특정 클래스의 메소드가 코드 전체를 통틀어 어디서 오버라이딩 되는지, 안되는지를 알 수 있고 오버라이딩 되지 않는 메소드라면 final 키워드를 자동으로 붙여줄 수 있음. (따라서 명시적으로 final을 추가하지 않아도, private이 아니라도 최적화 가능)

- 동적 디스패치 줄이기
    
    클래스는 기본적으로 메소드와 프로퍼티 접근에 동적 디스패치를 사용한다고 함.
    
    동적 디스패치는 vtable(vtable은 메소드의 위치 주소를 저장한 배열임)을 통한 간접 호출로 이뤄짐.
    
    **① `final` 키워드 명시적으로 붙여주기**
    
    **② private 혹은 fileprivate 이용하기** 
    
    - 컴파일 시 상속이 안된다면 자동으로 final 키워드를 붙여줌. 
    
    - private은 private 또는 fileprivate 접근제어를 가진 클래스에서 상속할 수 있기 때문
    
    **③ WMO 활성화**
    
    - Whole Module Optimization : 전체 모듈 최적화
    
    - Swift 컴파일러는 원래 컴파일 할 때 소스파일 하나하나를 컴파일 함. WMO를 활성화하면 모듈 안에 있는 전체 파일을 통으로 컴파일 한다. 이를 통해 internal이더라도 override가 이뤄지지 않으면 final 키워드를 자동으로 붙여줄 수 있음.
    
- class에서만 사용하는 프로토콜은 AnyObject로 표시하기 - 예를 들어, ARC 메모리 관리 시스템은 클래스를 다루고 있을 때 쉽게 할당할 수 있습니다(객체의 참조 횟수 증가). 이러한 정보가 없으면 컴파일러는 구조체가 프로토콜을 따를 경우를 대비해 할당과 해제를 고려해야하므로 비용이 많이들 수 있습니다.

---

출처 : [https://corykim0829.github.io/swift/Understanding-Swift-Performance/#](https://corykim0829.github.io/swift/Understanding-Swift-Performance/#) - WWDC 정리

출처 : [Method static dispatch & dynamic dispatch](https://babbab2.tistory.com/143)

# Allocation

할당은 Stack 또는 Heap에서 이루어진다. 

Stack은 LIFO의 단순한 구조로 이루어져있기 때문에 메모리의 할당과 해제가 쉽다.

Heap은 구조가 조금 더 복잡하다. 할당이 좀 더 dynamic한데 사용하지 않는 블록을 찾아서 메모리 할당을 진행한다. 해제하기 위해서는 해당 메모리를 적절한 위치로 다시 삽입한다???  여러 thread가 동시에 Heap에 메모리 할당을 할 수 있기 때문에 locking 또는 기타 동기화 매커니즘을 사용하여 무결성을 보호해야 한다.

stack이 Heap보다 비용이 적고 속도가 빠른 할당방법이다.

메모리 할당 시 Stack과 Heap에 저장되는 기준은 **`semantics`**

> semantics - 어떤 타입이나 기호가 내부적으로 어떤 의미인지를 뜻한다.???
value semantics와 reference semantics가 있다.
> 

value semantics - struct, tuple, enum 등

reference semantics - class, function, closure 

stack에는 reference 주소를, heap에는 진짜 데이터를 할당함.

### **성능올리기 : Heap allocation 피하기**

String은 value 타입이지만 heap에 character타입으로 문자들이 간접 저장되기 때문에 String을 사용하게 되면 heap allocation이 발생한다. (따라서 reference counting이 발생함)

**String이외에 value 타입이지만 heap allocation이 발생하는 요소들이 있을텐데?**

# Reference Counting Overhead

> 오버헤드 : 어떤 처리를 하기 위해 들어가는 간접적인 처리 시간, 메모리 등을 말함
> 

### Reference Counting in Struct

sturct는 value type이기 때문에 reference counting이 발생하지 않을거라고 생각할 수 있지만 그렇지 않다.

만약 struct의 프로퍼티 중 reference type이 있다면 reference counting이 발생한다.

String에 reference counting이 증가한다고? ㅇㅇ 왜냐하면 string을 구성하는 character들은 heap안에 할당되기 때문에 reference count가 필요함.

struct안에 프로퍼티가 있으면 성능상 class보다 더 안좋다고 한 이유는 struct가 다른 변수,상수에 할당될 때마다 가지고 있는 프로퍼티 별로 retain이 발생함. (release도 가지고 있는 프로퍼티 개수별 발생함) 

만약 class 타입이었으면 해당 클래스에 대한 retain, 딱 한번만 진행하면 되기 때문에 retain, release 카운트가 훨씬 적음.

따라서 struct안의 프로퍼티 중 한개를 초과해서 referenece type을 가지게 되면 성능이 떨어짐.

```swift
struct Label {
    var text: String
    var font: UIFont
    func draw() { }
}

let label1 = Label(text: "Hi", font: font)
let label2 = label1
retain(label2.text._storage)
retain(label2.font)
// finished using label1
release(label1.text._storage)
release(label1.font)
// finished using label2
release(label2.text._storage)
release(label2.font)

// Label이 class라면
class Label {
        var text: String
        var font: UIFont
        func draw() { }
}

let label1 = Label(text: "Hi", font: font)
let label2 = label1
retain(label2)
// finished using label1
release(label1)
// finished using label2
release(label2)
```

# Method Dispatch

dispatch : 발송하다, 신속히 일을 처리하다

Method dispatch : 프로그램이 어떤 메소드를 호출할 것인지 결정하여 그 메소드를 호출하는 과정

어떤 메소드를 호출할 건지 결정되는 시점에 따라 static method dispatch와 dynamic method dispatch로 나뉜다.

**Static Method Dispatch (Direct Call)**

- 컴파일 시점에 컴파일러가 메소드의 실제 코드 위치를 파악할 수 있음

- 런타임 시 찾는 과정 없이 바로 해당 코드 실행

- 구현된 코드들이 어디서 실행되는지 알 수 있기 때문에 메소드 인라이닝과 같은 코드 최적화를 적극적으로 시행함

> 메소드 인라이닝 ; 메소드를 호출할 때 해당 메소드로 이동하지 않고 메소드의 결과값을 바로 반환하여 성능 향상시킴.
> 

**Dynamic Method Dispatch (Indirect Call )**

- 컴파일 타임에 판단불가 → 런타임에 `V-Table`의 구현을 참조하여 해당 메소드에 대한 정보를 가져와서 코드를 실행시킴.

🤔  V-Table이란?

virtual method table : 함수 포인터들의 배열(클래스마다 가지고 있음)

vtable에서 함수를 찾아 메모리 주소를 읽고, 그 주소로 점프해야하기 때문에 상대적으로 성능이 조금 떨어질 수 있다.

- static dispatch보다 많은 비용을 필요로 하지는 않지만 컴파일러에서 해주는 최적화 작업 적용이 안된다. 

- 단, 여러개의 method dispatch가 발생하는 dispatch chain에서는 차이가 있다.

static method dispatch의 경우 컴파일러가 어떤 메소드를 호출해야할지 이미 알고 있기 때문에 컴파일러는 static method dispatch chain을 메소드 인라이닝으로 붕괴시켜서 호출 스택 오버헤드 없이 단일 구현 형태, 즉 하나의 코드 덩어리로 바꿀 수 있다.

별다른 성능상의 차이도 없고, 여기까지만 봤을 때는 굳이 dynamic method dispatch가 필요하나? 하겠지만 필요한 이유가 따로 있음.

바로 **다형성**때문임.

🤔 다형성이란?

보통 상속이 이뤄지고, 메소드 오버라이딩이 발생하는 경우가 다형성에 속함.

컴파일러가 컴파일 타임 시 상속관계에 있는 어떤 (오버라이딩이 되었건 안되었건 상관않고)메소드를 호출해야할지 알기 어려울거임. 그래서 런타임에 어떤 메소드를 호출해야할지 알아야 하는데 이때 Dynamic dispatch를 사용한다.

final 키워드를 이용하면 서브클래싱이 안됨. 이는 팀원들에게도 서브클래싱이 안된다는걸 알려줄 수 있지만 컴파일러에게도 알려줄 수 있음. 따라서 static 하게 method dispatch를 할 수 있음.

private 키워드가 붙으면(상속받은 타입의 인스턴스를 통해 접근 불가) 오버라이딩이 되는지 안되는지를 컴파일러가 판단하고 추론해서 static dispatch로 동작한다고 함.

Xcode Build Settings → Whole Model Optimization

**Protocol에서의 Dispatch**

프로토콜은 기본적으로 메소드의 선언부만 제공함. 실제 메소드를 호출할 때 프로토콜 타입인 변수/상수의 프로토콜 메소드를 호출할 경우, 어떤 인스턴스에서 메소드를 가져와야할지 컴파일타임에는 알 수 없기 때문에 이땐 dynamic disaptch가 발생한다. 이와 달리 프로토콜을 채택한 인스턴스 타입의 변수/상수를 이용하면 구체적으로 어디서 method를 가져와야 할지 알 수 있기 때문에 static dispatch가 발생함.

이때의 v Table을 특별히 witness table이라고 한다함.

**value, reference type, protocol 확장 시 Dispatch**

- value type은 상속 가능성이 없기 때문에 확장해도 static dispatch임

- reference type에서의 확장

extension에서는 메소드 오버라이딩이 불가함(물론 @objc 속성을 가지고 있다면 오버라이딩 가능) 따라서 extension에 정의된 메소드를 가져올 때는 static dispatch임

- protocol 확장에서의 dispatch

1. protocol에 선언만 되어 있는 메소드를 해당 protocol의 Extension에서 default 메소드 구현함

아래와 같이 구현할 경우 해당 프로토콜을 채택해도 default 메소드에 대한 구현이 필수적이지 않음. 단, 채택한 곳에서 이를 구현하면 채택한 곳의 메소드가 우선순위가 더 높음.

컴파일 타임에 어떤 메소드를 호출할지 확정지을 수 없으므로 이때는 Dynamic Dispatch로 동작한다.

```swift
protocol Human {
	func sayHello()
}
extension Human {
	func sayHello() { print("Hello Human!") }
}
```

2. protocol에 선언되어 있지 않은 메소드를 추가로 구현함

이 경우 인스턴스를 할당할 변수/상수의 타입이 무엇이냐에 따라 다름.

만약 프로토콜 타입으로 변수/상수를 만들었다면 채택한 곳에서 sayHello() 메소드를 구현하더라도 protocol의 extension에 구현된 메소드가 호출됨. 이때는 컴파일러는 어떤걸 불러야할지 명확히 알기 때문에 static dispatch로 동작함. 

만약 프로토콜 타입이 아닌 본래 인스턴스 타입으로 변수/상수를 만들었다면 당연히 자기가 구현한 메소드가 호출됨.

```swift
protocol Human { }
 
extension Human {
    func sayHello() {
        print("Hello Human!")
    }
}
```