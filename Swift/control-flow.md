2022-02-21

---
## **반복문**

키워드 - while, for-in

### for-in

- 루프를 진행할 **범위를 지정함**
- Sequence 프로토콜을 준수하면 이 구문을 사용할 수 있음.
- 사용 가능한 타입 - 배열, 딕셔너리, set, 숫자 범위 연산자(ex. 1<..4), 문자열 등

```swift
// 숫자범위 연산자
for i in 1...4 {
	print(i) 
}
// 1, 2, 3, 4

// set : 순서 없이 무작위로 나옴
let numbers: Set<Int> = [1, 2, 5, 7, 10]
for num in numbers {
	print(num)
}

// 딕셔너리 : 순서 없이 무작위로 나옴
let numbers: [String: Int] = ["one": 1, "two": 2, "three": 3]
for (k, v) in numbers {
    print("\(k) - \(v)")
}
```

- 범위에 사용가능한 함수;

`stride(from:to:by:)` 함수는 `by`로 지정된 값만큼 점진적으로 값을 증가시키면서 `from`값부터 `to`값까지의 시퀀스를 리턴한다.  (단, **마지막 값은 포함하지 않음**)

`stride(from:through:by:)` 함수는 `by`로 지정된 값만큼 점진적으로 값을 증가시키면서 `from`값부터 `through`값까지의 시퀀스를 리턴한다. (단, **마지막 값 포함함**)

```swift
let minutes = 60
let minuteInterval = 5

for tickMark in stride(from: 0, to: minutes, by: minuteInterval) {
	// render the tick mark every 5 minutes (0, 5, 10, 15 ... 45, 50, 55)
}

let hours = 12
let hourInterval = 3
for tickMark in stride(from: 3, through: hours, by: hourInterval) {
    // render the tick mark every 3 hours (3, 6, 9, 12)
}
```

### While

- **조건을 지정하고 이 조건이 false가 될 때까지 루프를 진행함**
- 반복문의 범위를 특정하기 어려울 경우 사용하면 좋음
- 조건을 어느 시점에 체크하느냐를 기준으로 두가지 종류가 있다.
    
    `while` 반복 시작할 때 조건 비교
    
    `repeat-while` 반복 종료될 때 조건 비교
    

```swift
let maximumNum = 20
var num = 0

while num < maximumNum { /// 시작할 때 조건문 체킹, true일 경우 while문 body 진행
    num += 5
    print(num)
}

num = 0
repeat { /// 일단 body 진행
    num += 20
    print(num)
} while num < 100 /// while문 종료할 때 조건문 체킹, true일 경우 다시 while문 body 진행
```

# 조건문

### if

- 조건이 적을 때 적합함

### switch

- **패턴이 일치할 때**를 기준으로 조건을 나눌 수 있음(단, 첫번째로 일치하는 패턴을 탐)
    
    조건이 아니라 “패턴"을 case에 넣어야 한다!!
    
- 검사할 값의 타입과 조건으로 사용될 값의 타입이 일치해야한다.
- 명시적으로 break를 기입하지 않더라도 처음 일치하는 조건을 만나면 바로 switch문이 종료된다. 실수로 switch 문이 중복으로 실행되는 경우도 있는데(break를 명시적으로 기입하지 않아서) 이런 문제를 방지하고자 이렇게 만들었다고 한다.
- 만약 첫번째 케이스를 실행하고 난 뒤 다음 케이스도 실행시키고 싶을 경우 `fallthrough` 키워드로 표시해줘야함.
- switch의 케이스에는 범위연산자를 지정할 수도 있다.

```swift
let char = "a"

switch char {
// 아래 케이스는 오류 발생함. 왜냐하면 결과값의 타입과 char의 타입이 일치하지 않기 때문
// Expression pattern of type 'Bool' cannot match values of type 'String'
//case char == "a":
//    print("char is a")
case "a":
    print("char is 'a'")
    // 이게 없으면 "char is 'A'" 프린트안됨. 근데 fallthrough는 무조건 다음 case 실행하는듯?
    // 패턴이 일치하던 일치하지 않던? ㅇㅇ 맞음. 다음 case에서 조건 확인하지 않음.
    fallthrough 
case "A":
    print("char is 'A'")
case "a"..."z":
		print("char is between 'a' and 'z'")
default:
    print("there is no match case")
}
//char is 'a'
//char is 'A'
```

- switch의 case로 튜플을 사용할 수도 있음. 단, 검사할 값도 튜플형태여야 함.

튜플에는 값, 범위연산자를 사용할 수 있고 언더바를 사용할 수도 있음.

```swift
let somePoint = (1, 1)

switch somePoint {
case (0, 0):
    print("\(somePoint) is at the origin")
case (_, 0):
    print("\(somePoint) is on the x-axis")
case (0, _):
    print("\(somePoint) is on the y-axis")
case (-2...2, -2...2):
    print("\(somePoint) is inside the box")
default:
    print("\(somePoint) is outside of the box")
}
// Prints "(1, 1) is inside the box"
```

- switch의 value binding(케이스에 일치하는 값들을 임시 상수/변수로 만들어 사용 가능)
- 아래를 보니까 "패턴이 일치할때"라는 말이 무슨 말인지 더 와닿는다.

만약 anotherPoint의 값이 (2, 100)이었다면 3번째 케이스가 실행됨. 왜냐하면 첫번째, 두번째 케이스의 패턴이 anotherPoint의 패턴과 일치하지 않기 때문.

```swift
let anotherPoint = (2, 0)
switch anotherPoint {
case (let x, 0):
    print("on the x-axis with an x value of \(x)")
case (0, let y):
    print("on the y-axis with a y value of \(y)")
case let (x, y):
    print("somewhere else at (\(x), \(y))")
}
// Prints "on the x-axis with an x value of 2"

```

- case 조건이 모든 상황을 받아들이기에 적절하다면 default 구문을 굳이 넣을 필요는 없다.

위 코드를 보면 default 케이스는 없지만 `case let (x, y)`를 통해 첫번째,두번째 조건에 해당하지 않는 값을 처리할 수 있기 때문에 default를 넣지 않은 걸 볼 수 있음.

- switch는 where를 이용해 추가조건을 사용할 수 있다.

```swift
let anotherPoint = (91, 0)
switch anotherPoint {
case (let x, 0) where x > 90:
    print("y is 0, x is \(x) which bigger than 90")
case (0, let y) where y < 100:
    print("x is 0, y is \(y) which smaller than 100")
case let (x, y) where x == y:
    print("x(\(x)) and y(\(y)) are equal.")
case let (x, y):
    print("somewhere else at (\(x), \(y))")
}
// Prints "y is 0, x is 91 which bigger than 90"
```

- case에 여러개의 조건을 추가할 수 있다. 이때 , 를 기준으로 조건을 나눈다. 이를 compound case라고 부른다.
- compound case는 값 바인딩 역시 가능하다. 단, comound case의 **바인딩할 임시변수나 상수의 이름과 타입은 동일**해야한다.

**🤔  와일드카드 패턴**

와일드카드 패턴은 _로 표현되며, 값을 무시하는 패턴이다. 이외에도 Swift에서는 다양한 종류의 패턴을 사용할 수 있는데 이는 [Swift Programming Guide - Patterns](https://bbiguduk.gitbook.io/swift/language-reference/patterns)에서 볼 수 있음.

# 제어 변경하기

- 코드가 실행되는 순서를 변경함
- 키워드 - continue, break, fallthrough, return(함수에 나옴), throw(에러 핸들링에 나옴)
- continue : 루프안에서 사용되며 루프를 벗어나지 않고도 이 루프는 종료되었으니 다음 루프를 실행하라는 것을 나타낼 수 있음.
- break : 루프를 그 즉시 종료한다. break가 실행되면 루프의 닫기 중괄호(`}`) 다음부터 코드 실행됨. 루프 안에 있는 if에서 break를 호출하면 if의 닫기 중괄호가 아니라 루프의 닫기 중괄호 다음부터 코드 실행됨.
- fallthrough : switch문에서 사용되며 일치하는 첫번째 케이스의 구문을 실행한 후 다음으로 일치하는 케이스의 구문도 실행시키기 위해 사용됨.
    
    ❗️ fallthrough 키워드는 다음 케이스로 넘어갈 때 조건을 확인하지 않는다.
    

# 라벨이 있는 구문(Labeled Statements)

- 복잡한 제어구문을 만들기 위해 조건문/반복문 안에 다른 조건문/반복문을 넣을 수 있음.(중첩 가능하다)

- 중첩된 반복문/조건문에서 바깥쪽에 있는 반복문/조건문을 컨트롤하려고 할 때 라벨이 있으면 유용하다.

- 라벨을 붙일 때에는 루프, if 키워드 앞에 `라벨이름 :`  을 붙인다. 

# 이른 종료 (Early Exit)

- guard 구문임

- 조건이 모두 참일 때 다음을 실행함. guard는 항상 else가 같이 와야함(조건이 false일 때 실행할)

- else에는 guard구문이 존재하는 코드블럭을 종료하기 위해 제어를 이동시켜야 한다.

   따라서 return, break, continue, throw, fatalError(_:file:line:) 과 같이 되돌아가지 않는 함수 또는 메서드를 사용해야함.

# Sequence Protocol

- Sequence : 한번에 하나씩 진행되는 값의 리스트

- 요소들에 대한 순차적이고 반복적인 접근을 제공하는 타입입니다. 

   Sequence protocol을 채택하면 for-in루프나 contains, map, filter, reduce, lazy, foreach, enumerated, sorted 등과 같은 다양한 메소드들을 제공합니다.

- Sequence의 특징은 크게 두가지로 다음과 같은데요.

   ①유한하거나 무한하다 ② 딱 한번만 iterate할 수 있다 

🤔  여기서 특이한 건 딱 한번만 iterate할 수 있다는 내용입니다. 왜 그럴까요?

*— 출처 👀 : [[Apple Document] Swift - Sequence](https://developer.apple.com/documentation/swift/sequence)*

Sequence 프로토콜은 iteration에 의해 요소들이 파괴적으로 소비되는 것과 관련해, Sequence 프로토콜을 채택한 타입에게 어떠한 요구도 하지 않는다. 따라서 sequence에 대한 여러번의 for-in 루프를 실행하는 것이 반복을 재개하거나 처음부터 다시 시작할 것이라고 가정하지 마라.

collection이 아닌 sequence는 두번째 for-in loop에서 임의의 시퀀스를 생성할 수 있다? 

커스텀하는 타입이 nondestructive iteration을 지원하게 만들고 싶다면, Collection protocol을 채택해라.

- Sequence protocol을 채택하기 위해서는 `makeIterator()` 메소드를 구현하면 된다.