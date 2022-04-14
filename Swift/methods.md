2022-02-21

---
**Method**

- 특정 타입(클래스, 구조체, 열거형)과 연관된 함수

✓ 인스턴스 메소드 

  - 특정 클래스, 구조체, 열거형의 인스턴스에 속하는 함수. 즉, 인스턴스화 해야지만 사용할 수 있는 메소드임.

  - 구조체, 열거형은 값 타입이다. 인스턴스 메소드에서 기본적으로 구조체/열거형의 프로퍼티 수정이 불가함. 하지만 mutating 키워드를 사용하면 수정 가능함. mutating을 사용하면 self프로퍼티에 새로운 인스턴스를 할당하고 새로운 인스턴스는 메소드가 종료되면 기존에 존재하던 인스턴스를 대체하게 된다. 

- mutating function 안에서 self를 새롭게 할당할수도 있다.

- 열거형에서의 mutating func는 아래와 같이 사용된다. (self를 다시 새롭게 설정함)

```swift
enum Colors {
	case red, blue, orange
	mutating func next() {
		switch self {
			case .red: self = .blue
			case .blue: self = .orange
			case .orange: self = .red
		}
	}
}
```

✓ 타입 메소드(Obj-c의 클래스 메소드와 유사함)

  - 타입 자체에서 호출되는 메소드(인스턴스화하지 않아도 호출할 수 있음)

  - `static` 을 이용해 타입 메소드를 나타낼 수 있다.

  - class의 경우 `class` 키워드를 사용할 수 있음. `static` 대신 `class`를 사용하면 서브 클래스가 이 메소드를 오버라이딩 할 수 있도록 만듬. (static을 사용하면 오버라이딩 불가)

  - 타입 메소드 안에서 `self`는 인스턴스를 가리키는게 아니라 타입 그 자체를 가리킨다.

```swift
struct Test {
    var name: String
    mutating func changeName(name: String) {
        self.name = name
    }
    
    func instanceFunction() {
        print(self)
    }
    
    static func typeFunction() {
        print(self)
    }
}

var a = Test(name: "tom")
a.instanceFunction() // Test(name: "tom")
Test.typeFunction()  // Test
```