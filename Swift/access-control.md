2022-02-25

---
Access Control이란 ~~클래스나 열거형, 구조체, 프로토콜과 같은 타입 혹은 메소드, 서브스크립트, 프로퍼티 등의 요소에 대해 외부에서 접근할 수 있는 범위를 지정하는 것.~~ 

> — 👀 출처 : [Access Specifier in swift](https://medium.com/analytics-vidhya/access-specifier-in-swift-4c6698eb3577)
*Access control* restricts access to the parts of your code from code in other source files and modules.
다른 소스파일이나 모듈에서 어떤 코드에 접근하는 것을 제한하는 것이다.
> 

Access Control에서 접근 제한이 이루어지는 단위는 소스파일과 모듈이다. 

소스파일은 하나의 swift 파일을 의미하고 모듈은 하나의 코드 묶음을 의미한다. 라이브러리나 프레임워크, 어플리케이션이 여기에 속하며 Xcode는 하나의 target을 하나의 모듈로 취급한다.

Swift에서 접근 수준은 총 5단계로 나뉜다. open, public, internal, fileprivate, private

계산 프로퍼티의 getter와 setter에도 각기 별도로 access level을 지정할 수 있다. 이때 setter의 접근지정자는 getter와 동일하거나 더 제한적일 수 있다. 

---

**Access Control : 접근 제어** 

- 모듈과 소스파일에서 코드에 대한 접근을 제어한다. 클래스, 구조체, 열거형 뿐만 아니라 여기에 속하는 메소드, 프로퍼티, 초기화, 서브스크립트에도 접근 제어를 지정할 수 있다. ( 아래부터는 접근제어를 적용할 수 있는 위의 요소들을 모두 “엔티티”라고 명시하겠음 )

- 프로토콜은 특정 컨텍스트로 제한될 수 있다??? 무슨말임

**모듈과 소스파일, 무엇인가?**

- access control 모델은 모듈과 소스파일의 개념을 기초로 한다.(즉, 접근 수준을 나누는 단위가 모듈 및 소스파일)

- 모듈 : 하나의 코드 묶음. 하나의 단위로 빌드되고 제공되는 프레임워크 또는 애플리케이션과 같은 코드 배포의 한 단위, `import` 키워드로 가져다 쓸 수 있음. Xcode에서 각 빌드 타겟(앱 번들 또는 프레임워크 등)은 Swift에서 별도의 모듈로 처리됨. 

- 🤔   빌드 타겟, 앱 번들, 프레임워크
- 👀   코드의 캡슐화란

- 소스파일 : 모듈 안에 있는 하나의 .swift 파일을 의미함.

**Access Level**

5개의 접근 수준이 있다. 엔티티가 정의된 소스파일 및 소스파일이 속한 모듈과 관련된다. (소스파일, 모듈의 접근수준에 따라 엔티티의 접근수준이 영향을 받는다.)

- `open` 모듈 내부 뿐만 아니라 import를 통해 모듈을 사용하는 외부에서도 엔티티 접근 가능. 클래스와 클래스 멤버에게만 사용할 수 있으며, 모듈 밖에 있는 코드가 해당 클래스를 서브클래싱하고 오버라이딩 가능하도록 만든다.
- `public` 모듈 내부 뿐만 아니라 모듈 외부에서도 접근할 수 있다. 서브클래싱의 경우 모듈 내부에서만 가능하다.
- `internal` 모듈 내부에서는 접근 가능하지만 모듈 외부에서는 접근 불가
- `fileprivate` 동일한 소스파일 내에서만 접근 가능. 같은 모듈이더라도 소스파일이 다르면 접근 불가
- `private` 엔티티를 둘러싸고 있는 타입 및 그 타입의 확장 내에서만 접근 가능. 같은 파일이더라도 속해있는 타입이 다르면 확장 불가. 같은 타입이더라도 다른 파일에서 확장되었다면 접근 불가하며 subclass에서도 접근 불가하다.

**Access Level의 기본원칙**

- 엔티티는 현재 지정된 것보다 더 제한된 수준으로 정의될 수 없다.
    
    예를 들어 `public` 변수는 `public`보다 더 제한된 수준(`internal`, `fileprivate`, `private`)의 타입으로 정의될 수 없다.
    
    함수는 파리미터 타입 및 리턴 타입의 접근 수준보다 더 높은(덜 제한적인) 엑세스 레벨로 정의될 수 없다. 이렇게 되면 함수가  파라미터 타입 및 리턴타입의 접근수준보다 더 개방적인 곳에서 사용될 수 있기 때문이다.
    

**Default Access Level**

명시적으로 지정하지 않으면 `internal`이 기본 접근 수준이다.

단일 타겟 앱이라면 `public`이나 `open`의 access level을 지정할 필요가 없을 것이다. 경우에 따라 `fileprivate`이나 `private`을 사용할 수는 있음.

프레임워크를 개발하는 경우라면 외부에서 사용되길 원하는 API에 대해 open, public을 사용해야함.

기본적으로 `open`이나 `public`이 아니라면 다른 타겟에서 엔티티에 접근할 수 없다. 하지만 유닛테스트 타겟일 경우라면 모듈을 가져올 때 `@testable` 속성으로 표시하고 테스트를 진행할 수 있다. 이렇게 하면 모든 엔티티에 접근할 수 있음.

(예시 `@testable import PHPicker`)

**커스텀 타입에 Access Level 지정하기**

- 커스텀 타입을 선언하는 시점에 access level을 지정하면 된다. 이 설정은 타입의 멤버(프로퍼티, 메소드, 이니셜라이저, 서브스크립트)에도 영향을 미친다.
- 타입의 접근수준을 `private` 또는 `fileprivate`으로 지정한다면 멤버의 기본 접근수준도 `internal`이 아닌 `private`이나 `fileprivate`이 된다.
- **단, `public`으로 타입의 접근수준을 지정한 경우 멤버의 기본접근 수준은 `internal`이다.** 멤버도 `public`으로 만들고 싶다면 명시적으로 표시해줘야함.
- 만약 커스텀 타입의 접근수준을 private으로 선언한다면 이를 활용해 변수나 상수를 만들때에도 private으로 선언해줘야한다. 그렇지 않으면 런타임 에러 발생 → property must be declared private because its type 'OuterClass.SomePrivateClass' uses a private type
- 엔티티를 private으로 선언하면 이를 인스턴스화한 후 접근하는 것도 불가하다.

**튜플 타입의 접근수준**

튜플 타입의 접근수준은 명시적으로 지정할 수 없다. 튜플 타입 중 가장 제한적인 접근수준이 튜플 타입의 접근수준이 된다. 예를 들어 3개의 값을 가진 튜플인데 2개는 `open`이고 한개는 `private`이라면 해당 튜플의 접근수준은 `private`임. 

**함수 타입의 접근수준**

함수의 파라미터 타입과 리턴 타입 중 가장 제한적인 접근수준이 함수의 접근수준이 된다. 만약 이 접근수준이 컨텍스트의 접근수준과 일치하지 않는다면 func 키워드 앞에 명시적으로 적어줘야함. 

아래 코드에서 리턴 타입 중 두번째에 있는 SomePrivateClass가 private 이므로 func 앞에 private을 작성해줘야함.

```swift
private func someFunction() -> (SomeInternalClass, SomePrivateClass) {
    // function implementation goes here
}
```

**열거형의 접근수준**

열거형의 개별 케이스는 타입의 접근수준을 자동으로 받게된다. 케이스별로 다른 접근수준을 설정할 수 없음. 원시값 또는 연관된 값에 사용되는 타입은 열거형의 접근수준보다 높은(덜 제한적인) 접근수준을 가져야 함. 예를 들어 열거형이 Internal이라면 타입으로 private을 사용할 수 없음.

**중첩된 타입의 접근수준**

포함한 타입이 Public이 아닌 경우 포함한 타입과 동일함. public일 경우 중첩된 타입은 internal이다.

**Subclassing**

현재의 액세스 컨텍스트에서 접근할 수 있고 하위클래스와 동일한 모듈에 정의된 모든 클래스는 서브클래싱 가능하다. 또한 다른 모듈에 정의되어 있다하더라도 `open` 으로 접근제어가 되어 있는 거라면 이 또한 서브클래싱 가능함. 

서브클래스는 슈퍼클래스보다 높은 접근제어(덜 제한적인 것)를 가질 수 없다. 슈퍼클래스가 internal인데 서브클래스를 public으로 지정하는거 안됨.

**오버라이딩을 하면 상속받은 클래스 멤버의 접근 수준도 수정이 가능**하다.

```swift
public class A {
    **fileprivate** func someMethod() {}
}

internal class B: A {
    override **internal** func someMethod() {}
}
```

getter와 setter의 경우 속해있는 상수,변수 혹은 프로퍼티, 서브스크립트의 접근수준과 동일한 수준으로 제한된다. 

setter의 경우 getter와 동일하거나 더 제한적인 접근 수준을 제공할 수도 있다. `fileprivate(set)`, `private(set)` 등과 같은 방법으로.

필수 이니셜라이저(required)의 경우 타입과 동일한 접근수준을 가져야 한다. 그 이외의 이니셜라이저는 같거나 더 제한적인 접근수준 가능

**프로토콜**

프로토콜의 접근수준을 지정하면 그 안에 있는 멤버들의 접근수준도 모두 동일하게 지정된다. public 지정 시 멤버들은 internal이 되는 클래스나 기타 타입과 달리 프로토콜은 public 지정 시 멤버들도 모두 public이 된다. 

## 헷갈리는 private과 fileprivate에 대한 테스트

| 프로퍼티의 access level | private | fileprivate |
| --- | --- | --- |
| 같은파일, 확장 | ✅  접근가능 | ✅  접근가능 |
| 같은파일, 상속관계 | ❌ 접근불가 | ✅  접근가능 |
| 같은파일, 인스턴스로 접근
(다른 클래스의 인스턴스 메소드에서 인스턴스 만들고 접근) | ❌ 접근불가 | ✅  접근가능 |
| 다른파일, 확장 | ❌ 접근불가 | ❌ 접근불가 |
| 다른파일, 상속관계 | ❌ 접근불가 | ❌ 접근불가 |
| 다른파일, 인스턴스로 접근
(다른 클래스의 인스턴스 메소드에서 인스턴스 만들고 접근) | ❌ 접근불가 | ❌ 접근불가 |

| 타입의 access level | private fileprivate (동일한 결과임) |
| --- | --- |
| 같은파일, 확장 | ✅  가능 |
| 같은파일, 상속관계 | ✅  가능, 하지만 서브클래스의 접근수준이 private이나 fileprivate 이어야 함
| 같은파일, 인스턴스로 접근 (다른 클래스의 인스턴스 메소드에서 인스턴스 만들고 접근) | ✅  가능 |
| 다른파일, 확장 | ❌ 불가(Error메세지 : Cannot find type in scope) |
| 다른파일, 상속관계 | ❌ 불가(Error메세지 : Cannot find type in scope) |
| 다른파일, 인스턴스 접근 (다른 클래스의 인스턴스 메소드에서 인스턴스 만들고 접근) | ❌ 불가(Error메세지 : Cannot find type in scope) |