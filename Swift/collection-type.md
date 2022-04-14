2022-02-14

---
Swift는 배열(Array), 집합(Set), 딕셔너리(Dictionary) 3개의 Collection Type을 제공한다. 

배열 - 컬렉션 값에 순서를 가지고 있으며, 같은 타입의 값을 저장한다. 동일한 값이더라도 다른 index에 존재할 수 있다.

집합 - 순서와 상관없이 같은 타입의 서로 중복되지 않는 값을 저장한다.

딕셔너리 - 순서와 상관없이 같은 타입의 키와 같은 타입의 value를 저장한다.

## Mutability of Collections

collection의 가변성 →  collection 생성 후 아이템을 추가 / 수정 / 삭제 할 수 있다.

만약 collection을 상수에 할당하면 해당 collection의 사이즈와 아이템은 변경 불가하다.

## Array

- 동일한 타입의 배열 두개를 + 연산자로 합쳐 새로운 배열을 만들 수 있다.

```swift
var firstArray = Array(repeating: 0.0, count: 3)
var secondArray = [2.5, 2.5, 3.0] 

// 서로 같은 타입(Int는 Int와, Double은 Double과) 연산 가능
var resultArray = firstArray + secondArray  // [0.0, 0.0, 0.0, 2.5, 2.5, 3.0]
```

- 덧셈 대입연산자(+=)를 사용하여 새로운 아이템, 배열을 추가할 수도 있다.

```swift
var numbers = [1, 2, 3]
numbers += [4, 5, 6, 7]
```

- 서브스크립트

```swift
// 인덱스를 통해 배열의 아이템에 접근할 수 있다. 이때 인덱스는 유효해야한다. 그렇지 않으면 런타임 에러 발생함.
// 서브스크립트로 특정 범위를 변경할 수도 있다. 
// 난 처음에 서브스크립트를 통해 지정된 범위와 들어가야할 값이 1:1로 대응되야 하는 건 줄 알았는데 그게 아님.
numbers[0..<2] = [445, 222, 555] // [445, 222, 555, 3, 4, 5, 6, 7]
numbers[0...2] = [-1]            // [-1, 3, 4, 5, 6, 7]
```

- insert 메소드를 이용해 특정 인덱스에 아이템을 끼워 넣을수도 있다.

- remove 메소드를 이용하면 특정 인덱스의 값을 삭제할 수 있다.

- 배열의 마지막 값 삭제 : removeLast() 혹은 popLast()

- 배열 반복 : for - in

   인덱스도 필요하다면? `for (index, value) in numbers.enumerated()` → 인덱스와 아이템의 조합을 튜플 형태로 반환함.

## Sets

- Set에 저장할 타입은 반드시 hashable이어야 한다. (즉, `Hashable` 프로토콜을 채택해야한다는 의미다.)

- 🤔 hashable - 자료구조(Hash Table) 할 때 다시 자세히 보자!
    
    - 프로토콜이다.
    
    - 정수 hash value를 생성하기 위해 Hasher에 의해 hash되어질 수 있는 타입.
    
    - 아니 도대체 hash value가 뭔데? ( 출처는 [이곳](https://applecider2020.tistory.com/14) )
    
       ✓ hash function을 이용하여 원본 데이터(객체)를 64bit의 Int 값으로 변환한 것. 즉, 데이터를 간단한 숫자로 변환한 것 
    
       ✓ 두개의 데이터를 비교할 때 데이터가 동일하다면 각 변수의 hash value도 동일하다.
    
       ✓ 단, 2개의 서로 다른 데이터가 동일한 hash value를 가질 수도 있다.
    
       ✓ 코드를 컴파일 및 실행할 때마다 모든 hash value는 변한다.
    
       ✓ Set에 저장될 타입, Dictionary의 Key는 반드시 Hashable 해야한다. 또한 Swift의 기본타입인 String, Int 등은 자동으로 Hashable 하다. 열거형이 associated value를 가지지 않으면 자동으로 Hashable 하다.
    
       ✓ Hash Value를 사용하는 이유 : Hash Value를 인덱스로 사용하여 값의 위치를 빠르게 찾을 수 있다. 즉, 값의 검색 속도가 빠르기 때문에 이를 사용한다. (O(1)의 시간복잡도를 가진다)
    
       ✓  [https://developer.apple.com/documentation/swift/hashable](https://developer.apple.com/documentation/swift/hashable)
    

- Set 생성과 초기화

```swift
// 배열과 달리 Set은 shorhand form이 없다. 
// 즉, var letters = ["a", "b"] 와 같이 생성/초기화할 수 없다는 뜻이다.
var letters = Set<Character>()
// 하지만 생성 및 초기화되고 난 이후에는 아래와 같이 사용할 수 있다.
letters = []

// 배열 리터럴과 함께 아래와 같이 초기화할 수도 있다. 
// 반드시 Set 타입을 명시해줘야 하는데 그 이유는 배열 리터럴만으로는 Set인지 여부를 추론할 수 없기 때문이다.
var favoriteGenres: Set<String> = ["Rock", "Classical", "Hip hop"]
var favoriteGenres2: Set = ["City Pop"]
```

- insert(_:)를 활용하여 새로운 아이템 추가 가능 `letters.insert(”c”)`

- remove(_:)를 활용하여 아이템 삭제 가능, 이때 삭제된 값을 반환한다. 아이템 없으면 nil 반환함. `letters.remove(”c”)`

   removeAll()로 전체 아이템 삭제 가능

- contain(_:)을 활용하여 Set에 특정 아이템 있는지 확인 가능  `letters.contains(”D”)` // false

- for-in으로 반복 사용 가능

- Set은 순서를 가지고 있지 않다. 만약 특정 순서로 반복을 돌리고 싶다면 sorted()메소드를 활용하라

```swift
for genre in favoriteGenres.sorted() {
    print("\(genre)")
}
// Classical
// Hip hop
// Jazz
```

- 이름이 “집합”이니만큼 집합 연산을 수행할 수 있다. 연산 수행 결과 새로운 집합을 생성한다.

![스크린샷 2022-02-14 오후 3.13.38.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/3b596832-4938-44c9-9cce-f7bc36b5ae19/스크린샷_2022-02-14_오후_3.13.38.png)

📒  각 연산의 뜻

intersection - 교차점

symmetricDifference - 대칭차이 ( 합집합 - 교집합 )

union - 연합

subtracting - 뺄셈

- 집합 멤버십과 동등성

```swift
let a: Set<Int> = [1, 2, 3, 4, 5, 6, 7, 8, 9]
let b: Set<Int> = [1, 2]
let c: Set<Int> = [3, 5, 7, 9]
```

- `a`는 `b`의 모든 요소를 포함한다. 이때 `a`는 `b`의 superset이다.
    
    a.isSuperset(of: b) // true
    
- `b`의 모든 요소는 `a`에 포함된다. 이때 `b`는 `a`의 subset이다.
    
    b.isSubset(of: a) // true
    
- `b`와 `c`는 공통요소가 없다. 분리집합(disjoint)이다.
    
    b.isDisjoint(with: c) // true
    

- 두 집합이 같은지 비교할 때는 `==` 연산자를 사용한다.

## Dictionaries

- Dictionary의 Key타입은 반드시 Hasahble 프로토콜을 채택해야한다. 

- Dictionary 생성 및 초기화

```swift
var namesOfIntegers = [Int: String]()
var namesOfChracters: Dictionary<String, Character> = [:]

// 이미 타입정보를 제공한다면 아래와 같이 초기화할 수도 있다.
namesOfIntegers = [:]
namesOfIntegers = [16: "sixteen", 1: "one"]
```

- Dictionary 값에 접근,수정,삭제

```swift
// [서브스크립트]
// Key 값으로 접근하여 수정 가능
namesOfIntegers[16] = "Sixteen"
namesOfIntegers[16] // Optional("Sixteen") Key가 없을 수 있기 때문에 옵셔널로 반환됨
// 삭제는 Key에 nil을 할당하면 됨.
namesOfIntegers[16] = nil
 

// updateValue()메소드로 특정 키에 대한 값 수정 가능. 만약 key 없다면 값 추가됨.
// 메소드 수행 이후 "이전값"을 옵셔널타입으로 반환한다.
namesOfIntegers.updateValue("One", forKey: 1)
// removeVale(forKey:) 사용
// 메소드 수행 이후 삭제된 값을 옵셔널타입으로 반환한다.
namesOfIntegers.removeVale(forKey: 1)
```

- Dictionary의 반복

   ✓ for-in루프 사용 가능하며, 딕셔너리의 각 아이템은 (key, value)의 튜플 형태로 반환된다.

   ✓ 혹은 딕셔너리의 key만 반복을 돌리거나 `namesOfIntegers.keys`  

       value만 반복을 돌릴 수 있다. `namesOfIntegers.values`

   ✓ 딕셔너리의 Key 또는 값을 Array에 넣어 새로운 배열을 만들수도 있다.

```swift
// key를 배열로 만들기
let integers = [Int](namesOfIntegers.keys)

// value를 배열로 만들기
let values = [Int](namesOfIntegers.values)
```

   ✓ 특정 순서로 key나 value의 반복을 돌리고 싶다면 sorted()를 활용해라!

COW

Swift에서는 배열뿐만 아니라 standard library에 있는 모든 variable-size collection들(Dictionary, set 포함, String도 포함인데 사이즈에 따라 다름.)은 **copy-on-write** 최적화를 사용한다.

🤔  copy-on-write?

쓰여질 때 복사가 된다. 즉, 배열의 원본이나 여러 복사본 중 하나가 수정(write)되기 전까지는 모두 동일한 storage(메모리)를 공유한다.

수정이 발생하면 ① 수정을 야기하는 배열이 단독으로 사용하는 storage를 복사한 후 ② 수정작업이 이루어진다.

![[https://developerbee.tistory.com/26](https://developerbee.tistory.com/26)](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/37d545bb-6b41-47d4-a61c-20214748917e/스크린샷_2022-04-13_오후_4.46.47.png)

[https://developerbee.tistory.com/26](https://developerbee.tistory.com/26)