2022-02-14

---
Subscript란 데이터의 수정, 조회 등과 관련된 메소드를 보조해주는 역할을 한다.

클래스, 구조체, 열거형에 subscript를 정의할 수 있다. 정의할 때는 함수처럼 매개변수와 리턴타입을 지정할 수 있고, 계산 프로퍼티처럼 getter와 setter를 지정할 수 있다. 

사용할 때는 인스턴스가 할당된 변수나 상수 명, 타입 명 바로 뒤에 대괄호를 열고 그 안에 필요한 매개변수를 넣어주면 된다. 

- 메소드를 보조해준다 : 메소드명이 없다는 특징때문인데, 메소드명이 없으면 이를 기억하거나 찾을 필요가 없기 때문에 편하지만 반대로 가독성이 떨어지기 때문에 메소드의 일부 역할들에 대해서만 제한적으로 보조해주는 것.(대체불가)
- 일반적인 개발문법 규범에서 보면 대괄호의 사용은 특정 키에 접근해서 값을 가져오거나 새로운 값을 할당할 때 사용된다. 가독성을 고려하면 데이터의 수정,조회를 제외한 나머지 용도로 사용하는 건 오히려 코드가 복잡해질 수 있기 때문에.

- 클래스, 구조체, 열거형은 collection, list, 시퀀스의 아이템에 바로 접근할 수 있는 바로가기 기능을 만들 수 있는데 이를 서브스크립트라 한다.

- array의 경우 index로, dictionary의 경우 key를 이용하여 아이템에 접근이 가능한데 이것들이 모두 Subscript를 이용한 것이다. 

- Subscript Syntax

```swift
// 계산 프로퍼티와 매우 유사한 syntax이다. 
// 입력 파라미터를 여러개 넣을 수 있으며, 어떤 타입도 들어올 수 있다. 
// 읽기-쓰기 또는 읽기 전용이 될 수 있다. 

subscript(index: Int) -> Int {
    get {
        // Return an appropriate subscript value here.
    }
    set(newValue) {
        // Perform a suitable setting action here.
    }
}

// 읽기 전용일 경우 계산 프로퍼티와 비슷하게 중괄호를 없애 단순화할 수 있다.
subscript(index: Int) -> Int {
    // Return an appropriate subscript value here.
}

// 반드시 collection type 형태일 필요는 없다.
struct TimesTable {
    let multiplier: Int
    subscript(index: Int) -> Int {
        return multiplier * index
    }
}
let threeTimesTable = TimesTable(multiplier: 3)
print("six times three is \(threeTimesTable[6])")
// Prints "six times three is 18"
```

- 함수와 유사하게 가변 파라미터와 파라미터의 기본값을 설정할 수 있음. 함수와의 차이가 있다면 `inout` 파라미터를 사용할 수 없다는 것.

- 여러개의 서브스크립트를 정의하는 것을 subscript overloading이라고 한다. 

```swift
struct Matrix {
    let rows: Int, columns: Int
    var grid: [Double]

    init(rows: Int, columns: Int) {
        self.rows = rows
        self.columns = columns
        grid = Array(repeating: 0.0, count: rows * columns)
    }

    func indexIsValid(row: Int, column: Int) -> Bool {
        return row >= 0 && row < rows && column >= 0 && column < columns
    }

    subscript(row: Int, column: Int) -> Double {
        get {
            assert(indexIsValid(row: row, column: column), "Index out of range")
            return grid[(row * **columns**) + column]
        }
        set {
            assert(indexIsValid(row: row, column: column), "Index out of range")
            grid[(row * **columns**) + column] = newValue
        }
    }
}

var matrix = Matrix(rows: 2, columns: 2)
matrix[0, 1] = 1.5
matrix[1, 0] = 3.2

// 아래는 런타임에러 발생 -> Queue.xcplaygroundpage:97: Assertion failed: Index out of range
matrix[2, 3] = 2.3 
```

assert를 저렇게 사용하는구나!

- 타입 서브스크립트

```swift
enum Planet: Int {
    case mercury = 1, venus, earth, mars, jupiter, saturn, uranus, neptune
		// 클래스인 경우라면 하위클래스가 수퍼클래스의 서브스크립트를 재정의할 수 있게 class키워드 사용 가능.
    **static** subscript(n: Int) -> Planet {
        return Planet(rawValue: n)!
    }
}

// 인스턴스화 하지 않아도 서브스크립트를 통해 열거형의 특정 항목 접근 가능.
let mars = Planet[4]
print(mars)
```

argument label을 이용하고 싶다면 이를 반드시 명시해줘야 한다. 아래와 같이 !

```swift
class SubscriptTest {
    private static var type: Int = -1
    var name: String = "SubscriptTest"
    
    subscript(name name: String, digit number: Int = 0) -> Void {
        print("subscript is called!")
        self.name = "\(name) - \(number)번째"
        print("change name:\(self.name)")
    }
    
    static subscript(type: Int..., str str: String) -> Void {
        print("type subscript is called! str is :\(str)")
        
        var total = 0
        for t in type {
            total += t
        }
        self.type = total
        print("current type is \(self.type)")
        
    }
}

SubscriptTest[1,2,3,5, str: "Heello"]
//----Print
type subscript is called! str is :Heello
current type is 11
//---------

let a = SubscriptTest()
a[name: "Rosa", digit: 100]
//----Print
subscript is called!
change name:Rosa - 100번째
//---------
```