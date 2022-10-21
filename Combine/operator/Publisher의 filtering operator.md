```Swift
//: [Previous](@previous)

import Foundation
import Combine

var cancellables = Set<AnyCancellable>()
/*
 filter(_:) : 주어진 클로저와 일치하는 모든 element를 다시 publish(republish)한다.
 `func filter(_ isIncluded: @escaping (Self.Output) -> Bool) -> Publishers.Filter<Self>`
 isIncluded - 하나의 element를 받아 이를 다시 downstream에 게시할지 말지 여부를 나타내는 bool값을 리턴한다.
 
 combine의 filter는 Swift stnadard library에 있는 필터와 유사하다.
 */

let numbers = [1, 2, 3, 4, 5]
numbers.publisher
    .filter { $0 % 2 == 0 } /// 들어온 element를 2로 나눴을 때 나머지가 0인 것만 downstream으로 repulish한다.
    .sink { print("\($0)", terminator: " ") }
    .store(in: &cancellables)

print("\n========")

/*
 tryFilter(_:) : filter랑 동일한데 클로저가 에러를 throw할 수 있음.
 만약 클로저가 error를 throw한다면, publisher는 해당 에러로 인해 fail됨.
 */
struct ZeroError: Error {}
let numbers2 = [1, 2, 0, 4, 5, 6, 8]
numbers2.publisher
    .tryFilter {
        if $0 == 0 {
            throw ZeroError()
        } else {
            return $0 % 2 == 0
        }
    }
    .sink(
        receiveCompletion: { print("\($0)") },
        receiveValue: { print("value:\($0)") })
    .store(in: &cancellables)

print("\n========")

/*
 compactMap(_:) : element를 전달받으면 클로저를 호출하고 ~~값이 있는 반환된 optional을 게시한다.~~ 값이 있는 것을 선택적으로 리턴한다 아닐까..?! ?? Swift standard library에서는 compactMap을 하면 nil이 제거되지 않았나,,? 해석을 잘못했나?
 > Calls a closure with each received element and publishes any returned optional that has a value.
 
 근데 정의된 시그니처를 보면 옵셔널 풀어서 리턴해주는 것 같긴한데..
 `func compactMap<T>(_ transform: @escaping (Self.Output) -> T?) -> Publishers.CompactMap<Self, T>`
 result는 클로저 호출 후 nil이 아닌 값을 선택적으로 리턴함.
 
 컴바인의 compactMap 역시 swift standard library의 compactMap과 유사하다. 컴바인의 compactMap 오퍼레이터는 publisher 스트림의 nil 요소를 제거하고 downstream subscriber에게 nil이 아닌 요소만 republish한다.
 */

let numbers3 = (0...5)
let romanNumeralDict: [Int: String] = [1: "I", 2: "II", 3: "III", 5: "V"]
numbers3.publisher
    .compactMap { romanNumeralDict[$0] }
    .sink{ print("\($0)", terminator: " ") }
    .store(in: &cancellables)

print("\n========")

/*
 tryCompactMap(_:) : compactMap이랑 똑같은데 클로저에서 error를 throw할 수 있음.
 `func tryCompactMap<T>(_ transform: @escaping (Self.Output) throws -> T?) -> Publishers.TryCompactMap<Self, T>`
 */
struct ParseError: Error{}
func romanNumeral(from: Int) throws -> String? {
    let romanNumeralDict: [Int: String] = [1: "I", 2: "II", 3: "III", 4: "IV", 5: "V"]
    guard from != 0 else { throw ParseError() }
    return romanNumeralDict[from]
}

let numbers4 = [6, 5, 4, 3, 2, 1, 0]
numbers4.publisher
    .tryCompactMap { try romanNumeral(from:$0) }
    .sink(
        receiveCompletion: { print("completion:\($0)") },
        receiveValue: { print("value:\($0)", terminator: ", ") })
    .store(in: &cancellables)

print("\n========")
/*
 removeDuplicates() : 이전 element와 일치하지 않는 element만 게시한다.
 `func removeDuplicates() -> Publishers.RemoveDuplicates<Self>`
 publisher의 Output 타입이 Equatable을 채택한 경우에만 사용할 수 있음.
 중복요소를 게시하는 대신 소비(consume)하는 publisher다.
 이 오퍼레이터는 두개의 element를 저장할 수 있으며 따라서 오퍼레이터는 현재, 그리고 이전에 게시된 element를 비교할 때 사용한다.
 */
let numbers5 = [0, 1, 2, 2, 3, 3, 3, 4, 4, 4, 4, 0]
numbers5.publisher
    .removeDuplicates()
    .sink { print("\($0)", terminator: " ") }
    .store(in: &cancellables)

print("\n========")

/*
 removeDuplicates(by:) : 제공된 클로저로 연산된 결과와 이전 element가 일치하지 않는 경우에만 게시한다.
 `func removeDuplicates(by predicate: @escaping (Self.Output, Self.Output) -> Bool) -> Publishers.RemoveDuplicates<Self>`
 클로저의 매개변수로 두개의 output이 들어가네?
 일단 removeDuplicates()와 return value는 동일함.
 predicate - 두 element가 동일한지를 검증?확인?하는 클로저로 필터링의 목적으로 사용된다. 만약 true가 리턴된다면, 두번째 element가 첫번째 element의 중복이라는 것을 나타낸다.
 
 Equatable을 채택하지 않은 element의 중복을 제거하고 싶거나, Equatable 프로토콜에서 제공하는 방식과는 다른 방식으로 "같음"을 비교하고 싶을 때 사용할 수 있다.
 */
struct Point {
    let x: Int
    let y: Int
}

let points = [Point(x: 0, y: 0), Point(x: 0, y: 1),
              Point(x: 1, y: 1), Point(x: 2, y: 1)]
points.publisher
    .removeDuplicates(by: { $0.x == $1.x }) // $0이 이전값, $1이 현재 값. return true이면 element consume됨.
    .sink { print("value:\($0)", terminator: ", ") }
    .store(in: &cancellables)


print("\n========")

/*
 tryRemoveDuplicates(by:) : 위에서 본 오퍼레이터와 동일한데 클로저가 error를 throw함.
 `func tryRemoveDuplicates(by predicate: @escaping (Self.Output, Self.Output) throws -> Bool) -> Publishers.TryRemoveDuplicates<Self>`
 */
struct BadValuesError: Error {}
let numbers6 = [0, 0, 0, 0, 1, 2, 2, 3, 3, 3, 4, 4, 4, 4]
numbers6.publisher
    .tryRemoveDuplicates { first, second -> Bool in
        if first == 4 && second == 4 {
            throw BadValuesError()
        }
        return first == second
    }
    .sink(
        receiveCompletion: { print("\($0)") },
        receiveValue: { print("\($0)", terminator: " ") }
    )
    .store(in: &cancellables)


print("\n========")

/*
 replaceEmpty(with:) : 빈 stream이 내려올 때 이를 제공된 element로 대체한다.
 `func replaceEmpty(with output: Self.Output) -> Publishers.ReplaceEmpty<Self>`
 output - 업스트림 publisher가 어떠한 element의 방출도 없이 종료됐을 때 방출되는 element
 빈 값이 publish되면 이를 적절한 값으로 대체해서 "finish normally"하게 만들어준다.
 🤔 그냥 빈 값이 내려오면 어떻게 되는거임? -> 아무 동작 X
 
 이거 언제 유용하게 쓸 수 있을까?
 예를 들어서 원래는 좋아요 기능이 없었는데, 이번에 새로 만들어졌음. 근데 불행히도 사용자의 액션이 없다면, 좋아요 기능의 default 값을 클라이언트에서 채워넣어줘야 하는 상황. 있다면 그대로 그 값을 노출. 이런 케이스에서..
 let postLists = Network(apiType: 포스트목록 가져오기).value // 이렇게 해서 데이터를 가져왔어. 타입:[PostList]
 postLists.publisher
    .map { $0.like }
    .replaceEmpty(with: false) // 근데 upstream 자체가 replace 가능한 타입의 배열이어야 하네.. 유용할까?
    .sink { do something }
 */
let numbers7 = [Double]()
numbers7.publisher
    .replaceEmpty(with: .nan)
    .sink { print("\($0)", terminator: " ") }
    .store(in: &cancellables)

print("\n")

numbers7.publisher
    .sink{ print("\($0)", terminator: " ") } // 아무것도 안찍힘.
    .store(in: &cancellables)

print("\n")

let numbers8 = [1.0, 2.0, 3.0]
numbers8.publisher
    .replaceEmpty(with: .nan)
    .sink { print("\($0)", terminator: " ") } // 그대로 나옴. 값이 있는채로 내려오면 아무런 동작하지 않음.
    .store(in: &cancellables)

print("\n========")

/*
 replaceError(with:) : stream에 있는 error를 주어진 element로 대체함.
 `func replaceError(with output: Self.Output) -> Publishers.ReplaceError<Self>`
 output - 업스트림 publisher가 fail됐을 때 방출되는 element.
 만약 업스터림 publisher가 error로 fail됐을 때, 이 publisher는 주어진 값을 방출하고 "finishes normally"하게 해준다.
 */
struct MyError: Error {}
let fail = Fail<String, MyError>(error: MyError())
fail.replaceError(with: "(replacement element)")
    .sink(
        receiveCompletion: { print("\($0)") },
        receiveValue: { print("\($0)", terminator: " ") })
    .store(in: &cancellables)


let numbers9 = [1, 2, 3, 4, 0, 2, 5]
numbers9.publisher
    .tryMap {
        if $0 == 0 { throw MyError() }
        return $0
    }
    .replaceError(with: -1000)// 에러가 발생했을 때 이를 대체할 수 있는데 이때 upstream의 Output 타입과 대체할 타입이 일치해야함. 위에서는 Int를 사용했기 때문에 대체값으로 Int를 제외한 다른 값을 넣을 수 없음.
    .sink(
        receiveCompletion: { print("\($0)") },
        receiveValue: { print("\($0)", terminator: " ") })
    .store(in: &cancellables)

```