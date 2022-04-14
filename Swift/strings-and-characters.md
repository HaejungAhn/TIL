*— 🔗 [[Swift Language Guide] Strings and Characters](https://docs.swift.org/swift-book/LanguageGuide/StringsAndCharacters.html)*

### String

- Swift의 String은 **Character 타입의 컬렉션**이다.

- **빠른 유니코드 호환방법을 제공**한다.

- \0은 null문자를 나타낸다.

   \u{n}은 유니코드 스칼라 값을 표현한다. 여기서 n은 1 - 8자리의 16진수임.

- 문자열은 값타입이다. 따라서 할당되거나 전달될 때 복사가 발생한다.(**COW 최적화 적용**됨)

- String을 Character 배열로 만들 때 `Array(string)`

  Character를 String으로 만들 때 `String(character array)`

- 문자열 앞뒤로 #을 넣으면 해당 문자열 전체를 String으로 인식한다. (내부적으로 문자열 보간 `\()`이 있다 하더라도 이를 문자 그대로 인식함) 만약 확장된 구분기호(#)를 사용하는 문자열 안에서 문자열 보간을 사용하고 싶다면 
`\#()`을 사용하면 됨.

### 유니코드

- 유니코드(unicode)

> *— 🔗 [유니코드란 무엇일까? (UTF8과 EUC-KR 비교)](https://norux.me/31)*

유니코드란, **숫자와 글자, 즉 키와 값이 1:1로 매핑된 형태의 코드** 인것이다.
다시말해 아스키코드로 0x41 = A 로 매핑된 것 처럼, 아스키코드로 표현할 수 없는 문자들을 유니코드라는 이름 아래 전 세계의 모든 문자를 특정 숫자(키)와 1:1로 매핑한 것이다.

UTF-8, UTF-16 이런것들은 모두 인코딩 방식을 의미한다. 숫자에 매핑된 것들을 어떻게 표현할 것이냐에 대한 것.
> 

- Swift의 기본 String타입은 “유니코드 스칼라 값(문자를 위한 유니크한 21-bit 숫자 또는 modifier)"으로부터 생성된다.

- Character 타입의 모든 인스턴스는 확장된 자소 클러스터(무리)로 표현된다. 즉, **한개 이상의 자소가 모여서 하나의 character를 구성한다.**

  자소 : 문자체계에서 의미한 구별할 수 있는 가장 작은 단위

  ex: `é` 는 두가지 방식으로 표현 가능함.

① U+00E9 (단일 자소 한개)

② U+0065와 U+0301 ( 영소문자 e와 액센트를 나타내는 ‘를 합성)

  ex: 한글의 표기도 위와 같이 두가지로 표현 가능함.

① U+D55C (”한" 이라는 단일 자소 한개)

② U+112와 U+1161와 U+11AB ( “ㅎ", “ㅏ", “ㄴ”이라는 여러 자소의 합성)

- **Swift에서 Character는 여러개의 unicode scalar로 구성할 수 있기 때문에 문자 하나하나마다 차지하는 메모리 양이 다를 수 있다.**

- 따라서 문자열의 count를 계산할 때는 순회를 하면서 하나하나 문자를 인식해야한다.

### 문자열 접근과 수정

- 메소드, 프로퍼티, 서브스크립트로 문자열에 접근, 수정할 수 있다.

- **문자열 인덱스**

String은 각 Character 위치에 해당하는 `String.Index`라는 인덱스 타입을 가지고 있다.

문자마다 저장할 메모리의 양이 다를 수 있으므로 특정 위치에 있는 Character를 확인하기 위해서는 해당 String의 시작 또는 끝에서 각각의 유니코드 스칼라를 반복하며 확인해야 한다. 따라서 Swift 문자열은 정수값으로 인덱스를 생성할 수 없다.

- **프로퍼티**

`startIndex` 프로퍼티는 String의 가장 첫번째 Character에 접근할 수 있다.

`endIndex` 프로퍼티는 String의 가장 마지막 Character 바로 다음 위치를 나타낸다.

문자열이 비어있다면 `startIndex` == `endIndex`

`indices` for-in 문에서 활용 가능하며 개별 character에 접근 가능함.

- **메소드**

String의 `index(before:)`와 `index(after:)`를 사용하면 주어진 인덱스의 앞, 뒤에 접근할 수 있다.

String은 RandomAccessCollection 프로토콜을 채택하지 않았다. BidirectionalCollection 프로토콜만 채택했기 때문에 주어진 원소를 기준으로 앞,뒤만 접근할 수 있다. 따라서 멀리 떨어진 Character에 접근해야 한다면 순회를 돌면서 확인이 필요하다. 주어진 인덱스에서 거리가 먼 인덱스에 접근하고 싶다면 `index(_:offsetBy:)`를 이용해라

```swift
func index(_ i: String.Index, offsetBy n: String.IndexDistance) -> String.Index
-- i : String의 적합한 인덱스
-- n : i와의 거리, i 다음부터 시작해서 n번째에 위치한 character가 리턴됨.

let s = "Swift"
let i = s.index(s.startIndex, offsetBy: 4)
print(s[i])
// Prints "t"

Complexity: O(n), where n is the absolute value of n.
```

- **서브스크립트**

String의 Character에 접근하기 위해 서브스크립트 구문을 사용할 수 있고, 여기에는 위에서 언급한 문자열 인덱스, 프로퍼티, 메소드 등을 활용할 수 있다.

```swift
let greeting = "Guten Tag!"
greeting[greeting.startIndex]
// G
greeting[greeting.index(before: greeting.endIndex)]
// !
greeting[greeting.index(after: greeting.startIndex)]
// u

// String.Index 타입을 만들어서 서브스크립트로 활용
let index = greeting.index(greeting.startIndex, offsetBy: 7)
greeting[index]
// a
```

### 부분 문자열(Substrings)

서브스크립트나 prefix(_:) - 첫번째 character 포함-, suffix(_:) - 마지막 character 포함-와 같은 메소드를 사용해 부분 문자열(문자 개수가 여러개일때)을 얻었을 때 그 문자열의 타입은 String이 아닌 `Substring` 타입이다. String과 거의 대부분의 것이 비슷하다. 차이가 있다면 성능최적화를 위해 **substring이 원래 문자열의 저장공간 또는 다른 substring을 저장하는데 사용되는 메모리의 일부를 재사용**한다는 것이다. ( 문자열도 COW로 최적화하므로 비슷하긴 하지만 두 문자열이 같은 메모리 공간을 공유하면 서로 값이 같은 것과는 차이가 있다 )

substring의 목적은 임시로 문자열을 저장하는 것에 있다. 이걸 계속 사용하면 원본 문자열을 계속 가지고 있어야 하기 때문에 장기 목적으로 사용하는 것은 목적에 맞지 않다.

### 문자열의 동등비교

여러 유니코드 스칼라로 구성되어 있다 하더라도 의미적으로 동일하면 같다고 본다.

hasPrefix(:)를 사용하면 특정 문자열로 시작되는지 아닌지를 체크할 수 있다. haSuffix(_:)를 사용하면 특정 문자로 끝나는지 체크 가능.

### 문자열의 유니코드 표현

Swift의 String, Character는 여러 인코딩 형식을 아주 쉽게 제공한다.

```swift
let dogString = "Dog‼🐶"

let utf8 = dogString.utf8
let utf16 = dogString.utf16
let unicodeScalar = dogString.unicodeScalars // UInt32 값 안에 표현된 스칼라의 21-bit 리턴
```

---

## **Accessing String Elements**

*— 🔗 [[Apple Document] String](https://developer.apple.com/documentation/swift/string)*

*— 🔗 [[Apple Document] Character](https://developer.apple.com/documentation/swift/character)*

- String은 Character 타입이 모여진 컬렉션이다.
- “김", “✨” 등과 같은 각 **문자들은 사실** **여러개의 unicode scalar 값으로 이루어진 것**이다.

> Because each character in a string can be made up of one or more Unicode scalar values, the number of characters in a string may not match the length of the Unicode scalar value representation or the length of the string in a particular binary representation.

string은 여러개의 character로 이루어져있기 때문에 string의 문자열 길이는 유니코드 스칼라 값의 길이 또는 특정 바이너리로 표현된 string의 길이와 일치하지 않을 것이다.
> 

```swift
let greeting = "Hello! 🐥"
print("Length: \(greeting.count)")
// Prints "Length: 8"

print("Unicode scalar value count: \(greeting.unicodeScalars.count)")
// Prints "Unicode scalar value count: 8"

print("UTF-8 representation count: \(greeting.utf8.count)")
// Prints "UTF-8 representation count: 11"

// 우리에겐 한개로 이루어진 문자열을 character로 구성하면 여러개가 될 수 있다.
let usFlag: Character = "\u{1F1FA}\u{1F1F8}"
print(usFlag)
// Prints "🇺🇸"
```

### **Substring 타입**

아래 `firstName` 의 타입은 Substring 타입이다. 

substring 타입은 **원본 문자열의 저장공간을 공유**하면서 문자열의 일부분을 표시하는 타입이다.

```swift
let name = "Marie Curie"

let firstSpace = name.firstIndex(of: " ") ?? name.endIndex 
let firstName = name[..<firstSpace]
// firstName == "Marie"
```

아니 근데 내가 이해가 안가는건!

String이 Character의 모음이라고 했잖아. 그럼 배열 형태일거아니야. 배열형태면 0번째 인덱스, 1번째 인덱스 이렇게 접근할 수 있지않나?

아래 예제를 보면 그게 안돼는 이유를 명확히 알 수 있음.

출력된 cafe 상수의 카운트를 출력해보면 길이는 9라는 걸 쉽게 알 수 있음.

```swift
let cafe = "Cafe\u{301} du 🌍"
print(cafe)
// Prints "Café du 🌍"
print(cafe.count)
// Prints "9"
print(Array(cafe))
// Prints "["C", "a", "f", "é", " ", "d", "u", " ", "🌍"]"
```

String은 유니코드 스칼라의 모음이라고 했잖아. 그럼 유니코드 스칼라가 어떻게 나오는지 보도록 하자.

문자의 count와 달리 unicode 스칼라의 실제 개수는 10개임.

```swift
print(cafe.unicodeScalars.count)
// Prints "10"
print(Array(cafe.unicodeScalars))
// Prints "["C", "a", "f", "e", "\u{0301}", " ", "d", "u", " ", "\u{0001F30D}"]"
```

UTF-16으로 인코딩했을때(NSString으로 문자열을  표현했을 때 UTF-16과 동일하게 나온다.)

```swift
print(cafe.utf16.count)
// Prints "11"
print(Array(cafe.utf16))
// Prints "[67, 97, 102, 101, 769, 32, 100, 117, 32, 55356, 57101]"

let nscafe = cafe as NSString
print(nscafe.length)
// Prints "11"
print(nscafe.character(at: 3))
// Prints "101"
```

UTF-8으로 인코딩했을때(C API를 이용했을 때 UTF-8로 인코딩됨)

```swift
print(cafe.utf8.count)
// Prints "14"
print(Array(cafe.utf8))
// Prints "[67, 97, 102, 101, 204, 129, 32, 100, 117, 32, 240, 159, 140, 141]"

let cLength = strlen(cafe)
print(cLength)
// Prints "14"
```

어떤 방식으로 인코딩하냐(어떤 API를 사용하느냐에 따라 다름)에 따라 같은 문자더라도 컬렉션의 개수가 달라지는 것을 확인할 수 있다. 그렇기 때문에 단순히 배열의 인덱스를 이용해 접근할 수가 없는거임.

문자열의 길이를 측정하기 위해 제일 먼저 해야할 것은 목적이 무엇인지를 정하는 것이다.

단지 화면에 디스플레이되는 길이를 알고싶은건지 혹은 특정 인코딩을 사용했을 때 문자열이 사용할 저장공간의 양을 측정하고 싶은것인지를 결정해야한다.

디스플레이 상으로는 단 한개의 문자일지라도 어떤 인코딩을 하느냐에 따라 그 길이가 달라질 수 있기 때문이다. 

만약 “A”라는 문자의 길이를 보자면, 해당 문자의 유니코드 스칼라 값은 65인데 이는 UTF-8에서도, UTF-16에서도 단 하나의 공간만으로 충분히 표현할 수 있는 범위이기 때문에 길이는 1이 된다.

하지만 \u{1F1F5}와 \u{1F1F7}로 구성된 emoji flag를 표현하려고 한다면 UTF-16 이나 UTF-8 딱 한개만으로는 표현할 수 없다. 너무 크기때문에. 따라서 각 인코딩 방식으로 해당 문자의 길이를 표현해보면 아래와 같이 인코딩별로 상이한 걸 알 수  있다.

```swift
let flag = "🇵🇷"
print(flag.count)
// Prints "1"
print(flag.unicodeScalars.count)
// Prints "2"
print(flag.utf16.count)
// Prints "4"
print(flag.utf8.count)
// Prints "8"
```

이런 과정으로 유추해볼 때 string의 count 프로퍼티는 내부적으로 string을 순회하면서 count를 구하게 되는 걸 알 수 있다. 

**Accessing String View Elements**

문자열의 개별요소를 찾기 위해선, 목적에 맞는 적합한 view를 선택해야한다.(여기서 view는 인코딩 방식을 의미하는 것 같다)

String은 COW 최적화가 적용됨. 만약 string과 인접한 저장소가 모두 차면, 새로운 버퍼가 할당되고 기존 데이터들이 새로운 저장소로 이동하게 된다. string buffer는 Array랑 비슷하게 기하급수적으로 저장소가 늘어나는 전략을 취한다.

— *🔗 [[velog, @hansangjin96] Swift) String은 왜 subscript[Int]로 접근이 안될까?](https://velog.io/@hansangjin96/Swift-String%EC%9D%80-%EC%99%9C-subscriptInt%EB%A1%9C-%EC%A0%91%EA%B7%BC%EC%9D%B4-%EC%95%88%EB%90%A0%EA%B9%8C)*

String은 가장 앞,뒤 원소만 참조할 수 있는 BidirectionalCollection 프로토콜을 준수한다. (왜일까?)

따라서 String.count의 시간복잡도는 O(n)이 된다. 또한 String.Index는 +, - 연산을 지원하지 않기때문에 현재 인덱스에서 n만큼 떨어진 글자를 참조하기 위해서는 O(n)의 시간복잡도가 필요함. 따라서 알고리즘 문제를 풀 때와 같은 경우 Array(문자열)을 통해 명시적으로 [Character] 형태로 반환하는 것이 효율적이다.

— Swift language guide

문자마다 저장할 메모리 양이 다를 수 있으므로 특정 위치에 있는 `Character` 를 확인하려면 해당 `String` 의 시작 또는 끝에서 각 유니코드 스칼라를 반복해야 합니다. 이러한 이유로 Swift 문자열은 정수값으로 인덱스를 생성할 수 없습니다.

string의 endIndex는 문자열의 “past the end” 위치다. 즉, 가장 마지막 인덱스보다 하나 큰 위치임.

---

[https://jcsoohwancho.github.io/2019-11-19-Swift-String-효율적으로-쓰기/](https://jcsoohwancho.github.io/2019-11-19-Swift-String-%ED%9A%A8%EC%9C%A8%EC%A0%81%EC%9C%BC%EB%A1%9C-%EC%93%B0%EA%B8%B0/)

String은 Character로 이루어진 컬렉션이다. 

Swift에서 string은 기본적으로 다양한 뷰를 제공한다. 여기서 view는 인코딩 방식을 의미함. 따라서 정수형으로 접근할 때 어떤 view를 기준으로 접근해서 데이터를 가져올지가 애매하기 때문에 정수형 인덱스 참조 X