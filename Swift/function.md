2022-02-21

---
**Function**

- 특정 작업을 수행하기 위한 코드 모음

- 함수에 이름을 지정할 수 있으며 이 이름은 호출할 때 사용한다.

- 함수를 매개변수로 전달할 수 있고 리턴 타입으로 사용할 수도 있다. 

- 중첩된 함수를 만듦으로써 기능을 캡슐화할 수 있다.

- **함수 파라미터는 기본적으로 상수다.**

- 함수의 파라미터에는 이름을 지정할 수 있다. (이 이름을 라벨이라고 함) 라벨이 다르면 같은 함수이름, 파라미터 타입, 리턴타입을 가졌다 하더라도 다른 함수로 인식한다.

```swift
func greet(person: String) -> String {
    return "\(person), hello~"
}

func greet(name: String) -> String {
    return "My name is \(name)"
}

print(greet(person: "Man"))
print(greet(name: "Tom"))
```

- 함수는 반환값을 반드시 정의하지 않아도 된다. 하지만 이게 실제로 리턴 값이 없음을 나타내는 것은 아니다. `Void`라는 특수한 타입의 반환값을 정의한 것과 동일하다. 해당 타입은 빈 튜플을 리턴한다.

- 함수의 반환값을 반드시 사용해야하는 것은 아니다. 와일드카드 패턴(_)으로 무시할 수도 있다.

- **여러개의 반환값**을 가질 수도 있다. 이 경우 튜플 패턴을 사용한다. 

- 옵셔널 튜플은 다음과 같은 형태다. 

  `(Int, Int)?` 이는 (Int?, Int?)와는 다른 것임을 명심하자!

```swift
func minMax(array: [Int]) -> (min: Int, max: Int)? {
		var currentMin = array[0]
    var currentMax = array[0]

    for value in array[1..<array.count] {
        if value < currentMin {
            currentMin = value
        } else if value > currentMax {
            currentMax = value
        }
    }
    return (currentMin, currentMax)
}

if let bounds = minMax(array: [8, -6, 2, 100, 3, 71]) {
	print("min:\(bounds.min), max:\(bounds.max)")
}
```

- 리턴할 때 암시적으로 반환할 수도 있다(즉, 리턴할 때 `return` 키워드를 안쓴다는 말이다)

  이는 함수의 body가 딱 한줄인 경우 유효하다. 

- argument label과 parameter name

  argument label 은 함수를 호출하는 쪽에서 인자를 전달하기 위해 사용하는 이름

  parameter name은 함수 내에서 해당 인자를 사용하기 위한 이름

 인수와 매개변수의 차이가 궁금해짐.

인수 - 함수가 호출될 때 함수로 전달하는 값

매개변수 - 함수의 정의에서 인수를 함수 내부로 전달하기 위해 사용하는 변수

```swift
// 여기서 personName이 parameter name
func greeting(personName: String) -> String {
	"Hello, \(personName)! Good Morning👋"
}

// 호출하는 시점의 personName은 argument lable
let str = greeting(personName: "Tom")
```

- **가변 파라미터(Variadic Parameters)** : 매개변수의 개수가 변할 수 있다(단, 타입은 동일해야함)

함수는 여러개의 가변 파라미터를 가질 수 있다. 가변파라미터 뒤에 오는 파라미터는 반드시 argument label을 가지고 있어야 한다. 없으면 “A parameter following a variadic parameter requires a label” 오류 표시됨. 그 뒤에 오는 파라미터가 argument label을 가지고 있지 않으면 구분을 할 수가 없음. 어디까지가 가변파라미터인지. 그래서 필수로 있어야함.

```swift
func arithmaticMean(_ numbers: Double...) -> Double {
	var total: Double = 0
  for number in numbers {
	  total += number
  }
  return total / Double(numbers.count)
}

arithmeticMean(1, 2, 3, 4, 5)  // 5개
arithmeticMean(3, 8.25, 18.75) // 3개
```

- **In-Out 파라미터** : 함수의 파라미터는 기본적으로 상수이기 때문에 함수 내에서 이를 수정하려고 하면 오류가 발생한다.

```swift
func greet(person: String) -> String {
		// Cannot assign to value: 'person' is a 'let' constant
    person = "Sunny"
    return "\(person), hello~"
}
```

`inout` 을 사용하면 ①파라미터 값을 변경할 수 있고 ②호출이 종료된 후에도 그 값이 유지됨 

함수가 함수 바디를 벗어나 영향을 미치는 여러 방법 중 하나이다.

사용법 - 파라미터 타입 전에 “inout” 키워드 붙임

          - 호출하는 쪽에서는 인자 앞에 &를 붙임

[동작](https://bbiguduk.gitbook.io/swift/language-reference/declarations#in-out-in-out-parameters) (copy-in copy-out, call by value 결과)

   - 함수가 호출될 때 인자 값이 복사된다.

   - 함수의 body 내에서 복사본이 수정된다.

   - 함수가 반환될 때 복사본의 값이 기존 인자에 할당된다.

제한 

  - inout의 인자로 변수만 전달 가능(상수 X, 리터럴로 정의된 것도 X, 가변파라미터 X)

  - 기본값 X

```swift
func greet(person: inout String) -> String {
    person = "Sunny"
    return "\(person), hello~"
}

// 아래는 오류 발생함 :Cannot pass immutable value as inout argument: **literals are not mutable**
print(greet(person: &"Man"))

```

- **Function Type** : 파라미터 타입과 반환 타입으로 구성됨.

  다른 타입처럼 이 함수 타입을 사용할 수 있음. 

아래 두 함수에서의 함수 타입은 (2개의 정수를 파라미터로 가지고 1개의 정수를 반환하는 타입)으로 볼 수 있다.

함수에도 타입이란 개념을 도입함으로써 변수/상수에 함수가 할당될 수 있게 했나보다/

```swift
func addTwoInts(_ a: Int, _ b: Int) -> Int {
    return a + b
}
func multiplyTwoInts(_ a: Int, _ b: Int) -> Int {
    return a * b
}

// 타입이 일치하면 아래와 같이 변수에 함수를 할당할 수 있음
var mathFunction: (Int, Int) -> Int = addTwoInts
print("Result: \(mathFunction(2, 3))")
```

- 함수 타입을 다른 함수의 파라미터 타입으로 사용할 수도 있다.

- 함수 타입을 리턴 타입으로써 사용할 수도 있다.

아래와 같이 사용할 수 있다. 신기하다.

```swift
func stepForward(_ input: Int) -> Int {
    return input + 1
}
func stepBackward(_ input: Int) -> Int {
    return input - 1
}

func chooseStepFunction(backward: Bool) -> (Int) -> Int {
    return backward ? stepBackward : stepForward
}

var currentValue = 3
// 어떤 함수를 사용할지(stepBackward() or stepForward()) 할당하는 것.
let moveNearerToZero = chooseStepFunction(backward: currentValue > 0)
// moveNearerToZero now refers to the stepBackward() function

print("Counting to zero:")
// Counting to zero:
while currentValue != 0 {
    print("\(currentValue)... ")
    currentValue = moveNearerToZero(currentValue)
}
print("zero!")
// 3...
// 2...
// 1...
// zero!
```

- **Nested Function**

중첩 함수. 함수 안에 다른 함수가 포함되어 있는 것.

중첩 함수로 만들어지면 외부에서 접근할 수 없으나 중첩함수를 포함하고 있는 함수에서는 이를 사용할 수 있음. 또한 리턴 타입으로 중첩함수를 사용하면 외부에서도 중첩함수를 사용할 수 있음.

왜 쓰는가?

> — [Function in Function](http://www.apeth.com/swiftBook/ch02.html#_function_in_function)
This feature is an elegant architecture for functions whose sole purpose is to assist another function.
다른 함수를 보조하기 위한 목적으로만 만들어진 함수일때 nested function으로 정의하면 좋다.
> 

```swift

func chooseStepFunction(backward: Bool) -> (Int) -> Int {
	func stepForward(_ input: Int) -> Int { return input + 1 }
	func stepBackward(_ input: Int) -> Int { return input - 1 }
  return backward ? stepBackward : stepForward
}

var currentValue = 3
// 어떤 함수를 사용할지(stepBackward() or stepForward()) 할당하는 것.
let moveNearerToZero = chooseStepFunction(backward: currentValue > 0)
// moveNearerToZero now refers to the stepBackward() function

print("Counting to zero:")
// Counting to zero:
while currentValue != 0 {
    print("\(currentValue)... ")
    currentValue = moveNearerToZero(currentValue)
}
print("zero!")
// 3...
// 2...
// 1...
// zero!
```