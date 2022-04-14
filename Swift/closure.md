2022-02-22

---
> 전역함수와 중첩함수는 클로저의 특별한 케이스이다.
> 

> 모든 함수는 함수(Function)가 수행하는 작업을 설명하는 함수 이름을 가지고 있다.
> 

→ 클로저는 기능을 수행하기 위한 코드 모음이다. 함수와의 차이가 있다면 **① 함수와 달리 이름을 가질 수도 있고 이름을 가지지 않을 수도 있다 ② 주변의 값을 캡처링할 수 있다.**

옵셔널한 이름 : 클로저의 유형은 3가지가 있음(중첩, 전역, 익명) 이 중 중첩, 전역함수는 이름을 가지고 있다.

값 캡처링 : 클로저는 정의된 주변 컨텍스트의 값을 캡처할 수 있다. 정의된 스코프가 종료되더라도 그 값에 여전히 접근할 수 있다.

아래 링크를 참조해서 캡처의 의미와 캡처리스트의 필요성에 대해 알게됨.

캡처? : [https://babbab2.tistory.com/83?category=828998](https://babbab2.tistory.com/83?category=828998)

Closure의 캡처는 캡처 대상이 value type이던 reference type이던 상관없이 모두 reference capture를 한다.

- Closure 정의를 나름대로 내려봄
    
    클로저란 기능을 수행하기 위한 코드 블럭이다. 클로저의 형태는 3가지가 있는데 1) 이름이 있고 내부에 어떤 값도 참조하지 않는 전역변수 2) 이름이 있는 중첩함수 3) 익명함수. 
    
    클로저의 가장 중요한 특징은 값을 캡처한다는 것이다. “캡처"한다는 것은 값이 reference type이던 value type이던 상관없이 무조건 reference capture를 한다는 뜻이다. 이 특성으로 인해 클로저가 정의된 스코프가 종료되더라도 클로저 내부에서 클로저 외부에 정의되었던 값을 계속 사용할 수 있다. value type을 기존의 방식처럼, 값 복사로 캡처하고 싶다면 캡처 리스트를 만들고 값 캡처를 원하는 변수나 상수를 넣어주면 된다. 또한 캡처리스트는 value type으로 캡처하는 것 뿐만 아니라 reference 타입을 캡처할 때 unowned나 weak 키워드를 붙여 사용할 수 있도록 한다. 
    
- `@escaping` 클로저 정의를 나름대로 내려봄
    
    클로저에 escaping 속성이 붙은 것을 의미한다. escaping 속성은 함수나 메소드의 파라미터 타입에 붙는 속성으로 함수가 종료된 이후에도 escaping 속성이 붙은 파라미터 값은 살아있음을 의미한다. escaping 클로저는 보통 비동기 처리 후 콜백함수로 많이 사용된다. ( 후행클로저라고 다 escaping인건 아니다 헷갈리지 말자!! )
    

- reference capture의 예제코드
    
    ```swift
    class MyInfo {
        var name: String = "Unknown"
        var age: Int = 0
        
        init(name: String, age: Int) {
            self.name = name
            self.age = age
            print("MyInfo is init!")
        }
        
        deinit {
            print("MyInfo is deinit!")
        }
        
        func inner() {
            let innerName = name // 값이 그대로 복사
            
            // 중첩 타입은 클로저, 클로저는 reference capture를 진행함.
            func nested() {
                let nestedName = name // reference capture니까 주소값이 복사? 
                print("nested name is \(nestedName)") // 변경된 값이 반영되야 함. peter
            }
            
            name = "peter"
            print("name is \(innerName), age is \(age)") // 처음 값 그대로. sara
            
            nested()
        }
    }
    
    let info = MyInfo(name: "Sara", age: 21)
    info.inner()
    ```
    

제일 헷갈렸던게 함수와 클로저 둘다 주변에 있는 값을 캡처할 수 있는거 아닌가?에 대한 거였음.

메소드를 예로 들면 클래스나 구조체 등에 정의된 멤버 변수를 인스턴스 메소드에서 접근할 수 있고 사용할 수 있으니까. 이때까지만 해도 “캡처"가 단순히 “주변값에 접근할 수 있음”을 의미하는 거라고 이해했음.

하지만 위 예제코드의 inner() 메소드를 만들어보고 난 후에는 캡처의 의미가 단순히 주변값에 접근하는 것만은 아님을 이해함.

**캡처는 주변의 값이 value type이던 ref type이던 모두 reference capture를 진행한다**는 것이었음. 그래서 캡처한 값이 정의된 스코프가 종료되더라도 클로저에서는 이를 계속 참조할 수 있는 것임.

value type을 ref capture가 아닌 value 그대로 사용하고 싶은 경우도 있음. 이때는 캡처리스트를 이용함.

캡처리스트의 또다른 활용은 ref type 사용 시 순환참조를 방지하기 위해 weak나 unowned를 사용할 수 있도록 한다는 것.

<aside>
📒 Closures 
🅽 폐쇄, (힘든 일의) 종료
주변에 있는 상수나 변수에 대한 참조를 (캡처리스트를 이용해) 캡처하고 캡처된 시점의 값을 저장하는데 이를 closing over(폐쇄, 일이나 행동을 마무리짓다)라고 한다.

closing over → 어떤 값이든 캡처 & 참조 저장
캡처의 의미 → 캡처는 그 순간을 포착한다 이런 의미가 있음. 단순히 참조하는게 아니라 정의된 그 시점, 그 순간의 값을 포착하고 저장한다. 캡처한 값의 원본 값 범위가 더 이상 존재하지 않더라도 클로저 내에서는 여전히 캡처된 값을 이용할 수 있음.

~~클로저는 자기가 정의된 컨택스트 주위의 값을 캡처하고 그 참조를 저장할 수 있는 코드 블럭이다.
”캡처”라는 것에 집중할 필요가 있다. 단순히 참조하는게 아니라 정의된 그 시점, 그 순간의 값을 포착하고 저장한다는 의미를 가지고 있다.~~

</aside>

> Closures are self-contained blocks of functionality that can be passed around and used in your code.
”self-contained : body에 기능을 포함하고 있는”이라고 해석해야하지 않을까 싶다.
> 

> Functions are self-contained chunks of code that perform a specific task.
> 

> Closures can **capture and store references** to any constants and variables from **the context in which they're defined.** This is known as *closing over(~을 에워싸다)* those constants and variables.
> 

```swift
import Foundation

func delay(_ seconds: Int, closure: @escaping ()->()) {
  let time = DispatchTime.now() + .seconds(seconds)
  DispatchQueue.main.asyncAfter(deadline: time) {
    print("🕑")
    closure()
  }
}

func demo() {
    var value = 42
    print("before closure: \(value)")
    
    /// [var constValue = value] 라고 작성하면 Expected 'weak', 'unowned', or no specifier in capture list 오류 발생함.
    delay(1) { [constValue = value] in
        print("inside closure 'value' is \(value)")
        print("inside closure 'constValue' is \(constValue)")
        /// 아래와 같이 캡처리스트의 변수에 값을 할당하려고 하면 Cannot assign to value: 'constValue' is an immutable capture 오류 발생함.
        // constValue = 10
        print("inside closure 'constValue' is \(constValue)")
    }
    value = 1332
    print("after closure: \(value)")
}

demo()
/*
 before closure: 42
 after closure: 1332
 🕑
 inside closure 'value' is 1332
 inside closure 'constValue' is 42
 */
```

- 클로저의 3가지 형태

- global function(이름 있음, 값 캡처 X) 

- nested function(이름 있음, 자신의 outer에 있는 변수 및 상수 캡처 가능) 

- closure expression(이름 없음, 주변에 선언된 값 캡처 가능)이 그것이다.

- Swift document에는 클로저가 어떻게 짧아지게 되었는가에 대한 내용이 주이다(정렬메서드 ~ 연산자 메서드까지)

- trailing closure - 함수의 마지막 인자로 클로저가 전달되야 하고 클로저의 body가 길 경우 사용함.

```swift
// 만약 함수의 인자가 클로저 단 한개 뿐이라면 함수뒤에 항상 나왔던 ()를 생략하고 바로 trailing 클로저를 사용할 수 있
reversedNames = names.sorted { $0 > $1 }
```

- 클로저가 여러개일 경우

```swift
func loadPicture(from server: Server, completion: (Picture) -> Void, onFailure: () -> Void) {
    if let picture = download("photo.jpg", from: server) {
        completion(picture)
    } else {
        onFailure()
    }
}

//아래와 같이 호출 가능
loadPicture(from: someServer) { picture in
    someView.currentPicture = picture
} onFailure: {
    print("Couldn't download the next picture.")
}
```

- [function과 클로저는 reference type이다.](https://docs.swift.org/swift-book/LanguageGuide/Closures.html#ID104)

- escaping closure

> *— 👀 출처 : [[Swift] Escaping 클로저(@escaping)](https://jusung.github.io/Escaping-Closure/)*
escaping closure란 클로저가 함수의 인자로 전달됐을 때 **함수의 실행이 종료된 후 실행되는 클로저**
즉, 함수의 실행이 종료되기 전에 실행되지 않기 때문에 escaping 클로저, 다시 말해 함수 밖(escaping)에서 실행되는 클로저입니다.
다른 변수에 저장되어 나중에 실행되거나 비동기로 실행될 때 escaping 클로저가 사용됨.
> 

> — 👀 출처 : [Swift Escaping Closure 이해하기](https://hcn1519.github.io/articles/2017-09/swift_escaping_closure)
클로저가 함수로부터 `Escape`한다는 것은 해당 함수의 인자로 클로저가 전달되지만, **함수가 반환된 후 실행**되는 것을 의미합니다.
클로저의 `Escaping`은 `A 함수가 마무리된 상태에서만 B 함수가 실행되도록` 함수를 작성할 수 있다는 점에서 유용합니다.
함수의 실행 순서를 보장받을 수 있다는 것은 매우 중요하다.
> 

`@escaping` attribute

**파라미터의 값이 이후에 실행되기위해 저장될 수 있다**는 것을 나타내며, 함수나 메소드의 파라미터 타입에 적용할 수 있다. 이 말은 값이 호출 수명보다 오래 지속되도록 허용된다.

일반적인 클로저(Non-escaping closure)

아래 코드의 실행순서

1. 클로저가 `runClosure`의 인자로 전달됨

2. closure()가 실행됨

3. runClosure()가 종료됨

→ 함수가 종료되기 전에 클로저가 실행된다.

```swift
func runClosure(closure: () -> Void) {
	closure()
}
```

Escaping Closure

1. 클로저가 fetchData()의 인자로 전달됨

2. 클로저 completion이 completionhandler에 저장됨

3. fetchData()함수가 종료됨

4. 클로저 completion은 아직 실행되지 않음.

```swift
class ViewModel {
    var completionhandler: (() -> Void)? = nil
    
    func fetchData(completion: @escaping () -> Void) {
        completionhandler = completion
    }
}
```

— 내 방식대로 예제 만들어봄!

```swift
var functions: [() -> ()] = []

func escapingTest(complete: @escaping () -> ()) {
    print("escapingTest start!")
    // @escaping을 붙여주지 않은 상태에서 단순히 "complete"만 호출하면 에러 발생함
	  // expression failed to parse, unknown error
    // 그래서 반드시 complete 뒤에 ()를 붙여야 하는데 이렇게 되면 함수가 무조건 실행됨.

    // @escaping을 붙여주면 complete(인자로 들어온 클로저)을 실행시키지 않고도 함수가 종료되며, 
    // 클로저를 변수에 할당하여 추후 외부에서 실행될 수 있도록 처리가능함.
    functions.append(complete)
    print("escapingTest end!")
}

escapingTest {
    print("this is closure body")
}

functions[0]()
```

**- 자동클로저** `@autoclosure`

- 인자 값이 없으며 **특정 표현을 감싸서 다른 함수에 전달 인자로 사용할 수 있는 클로저**

- 클로저 실행 전까지 실제 실행이 되지 않는다.

```swift
var customersInLine = ["Chris", "Alex", "Ewa", "Barry", "Daniella"]
print(customersInLine.count)
// Prints "5"

// 아래 코드는 실제 실행되지 않음.
let customerProvider = { customersInLine.remove(at: 0) }
print(customersInLine.count)
// Prints "5"

// 호출해야 실행됨
print("Now serving \(customerProvider())!")
// Prints "Now serving Chris!"
print(customersInLine.count)
// Prints "4"

// customersInLine is ["Ewa", "Barry", "Daniella"]
func serve(customer customerProvider: @autoclosure () -> String) {
    print("Now serving \(customerProvider())!")
}
// customer의 타입으로 리턴값 String만 만족하는 어떤 함수라도 들어갈 수 있다.
serve(customer: customersInLine.remove(at: 0))
// Prints "Now serving Ewa!"
```

[Preventing Timing Problems When Using Closures](https://developer.apple.com/documentation/swift/preventing_timing_problems_when_using_closures)