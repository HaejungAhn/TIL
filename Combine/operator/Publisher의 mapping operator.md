```Swift
// SwiftPlayground에서 작성됨.
import Foundation
import Combine

// Mapping Element
// Mapping : 모든 요소를 순회하면서 각 요소를 변형하여 새로운 요소를 리턴하는 작업이 이루어짐. 순회할 때마다(순회라는 단어가 적절한 것인지 잘 모르겠다. 왜냐하면 이벤트라고 생각했을 때, 이벤트는 순회 하는게 아니고 들어올 때마다 stream으로 내려오니까) 값이 게시됨.

var cancellables = Set<AnyCancellable>()
/*
 map(_:) : 제공된 클로저를 사용하여 upstream publisher로부터 받은 모든 element를 변형한다.
 `func map<T>(_ transform: @escaping (Self.Output) -> T) -> Publishers.Map<Self, T>`
 map을 사용하면 publisher를 리턴하게 된다.
 컴바인의 map(_:) 오퍼레이터는 Swift standard library의 map(_:)과 유사하다.
 */

let numbers = [5, 4, 3, 2, 1, 0]
let romanNumeralDict: [Int: String] = [1:"I", 2:"II", 3:"III", 4:"IV", 5:"V"]
numbers.publisher
    .map { romanNumeralDict[$0] ?? "unknown" }
    .sink { print("\($0)") }
    .store(in: &cancellables)

print("=======")
/*
 만약 클로저가 error를 throw할 수 있다면, 컴바인의 tryMap(_:) 오퍼레이터를 사용해야 한다.
 tryMap(_:) : 제공된 error-throwing 클로저를 사용하여 upstream publisher로부터 받은 모든 element를 변형한다.
 `func tryMap<T>(_ transform: @escaping (Self.Output) throws -> T) -> Publishers.TryMap<Self, T>`
 map과 마찬가지로 publisher를 리턴한다. map의 클로저가 error를 throw하면 publishing이 종료된다.
 */
struct ParseError: Error {}
func romanNumeral(from: Int) throws -> String {
    guard let numeral = romanNumeralDict[from] else {
        throw ParseError()
    }
    return numeral
}

numbers.publisher
    .tryMap { try romanNumeral(from: $0) }
    .sink(
        receiveCompletion: { print("completion: \($0)") },
        receiveValue: { print ("\($0)", terminator: " ") })
    .store(in: &cancellables)


print("=======")
/*
 mapError(_:) : upstream으로부터 받은 failure를 새로운 error로 converting 한다.
 `func mapError<E>(_ transform: @escaping (Self.Failure) -> E) -> Publishers.MapError<Self, E> where E : Error`
 어떤 에러 타입을 다른 에러타입으로 교체해야할 필요가 있는 경우 사용한다. 또는 downstream 오퍼레이터 input의 에러 타입이 일치해야 하는 경우(?) 사용할 수 있다.
 */
struct DivisionByZeroError: Error {}
struct MyGenericError: Error { var wrappedError: Error }

func myDivide(_ dividend: Double, _ divisor: Double) throws -> Double {
    guard divisor != 0 else { throw DivisionByZeroError() }
    return dividend / divisor
}
numbers.publisher
    .map { Double($0) }
    .tryMap { try myDivide(1, $0) }
    .mapError { MyGenericError(wrappedError: $0) }
    .sink(
        receiveCompletion: { print("💥completion: \($0)") },
        receiveValue: { print("\($0)", terminator: " ") })
    .store(in: &cancellables)

print("=======")
/*
 replaceNil(with:) : stream 안에 있는 nil element를 주어진 element로 대체한다.
 `func replaceNil<T>(with output: T) -> Publishers.Map<Self, T> where Self.Output == T?`
 위에서 봤던 오퍼레이터와 달리 매개변수로 nil 요소를 대체할 element가 들어간다.
 */
let doubleNumbers: [Double?] = [1.0, 2.0, nil, 3.0]
doubleNumbers.publisher
    .replaceNil(with: 0.0)
    .sink { print("\($0)", terminator: " ") } // nil을 제거하는게 아니라 대체하는 것이기 때문에 upstream으로부터 받은 데이터는 옵셔널이다. // Optional(1.0) Optional(2.0) Optional(0.0) Optional(3.0)
    .store(in: &cancellables)


print("=======")
/*
 scan(_:_:) : 현재 요소와 클로저에 의해 반환된 마지막 값을 다시 클로저에 제공함으로써 업스트림 publisher로부터 받은 element를 변형한다.
 `func scan<T>(
    _ initialResult: T,
    _ nextPartialResult: @escaping (T, Self.Output) -> T
  ) -> Publishers.Scan<Self, T>`
 
 initialResult - nextPartialResult 클로저에 의해 리턴받은 이전 결과 값
 nextPartialResult - 업스트림 게시자로부터 방출된 다음 element와 클로저에 의해 리턴된 이전 값을 매개변수로 받는 클로저
 
 이전에 게시된 모든 값을 하나의 값으로 축적(accumulate)할 때 사용할 수 있다.
 */
let range = (0...5)
range.publisher
    .scan(0) { return $0 + $1 }
    .sink { print("\($0)", terminator: " ") }
    .store(in: &cancellables)


print("=======")
/*
 tryScan(_:_:): scan이랑 똑같은데 클로저가 error를 throw함.
 */
func myThrowingFunction(_ lastValue: Int, _ currentValue: Int) throws -> Int {
    guard currentValue != 0 else { throw DivisionByZeroError() }
    return (lastValue + currentValue) / currentValue
}

let scanNumbers = [1, 2, 3, 4, 5, 0, 6, 7, 8, 9]
scanNumbers.publisher
    .tryScan(10, { try myThrowingFunction($0, $1) })
    .sink(
        receiveCompletion: { print("completion: \($0)") },
        receiveValue: { print("\($0)", terminator: " ") })
    .store(in: &cancellables)


print("=======")
/*
 setFailureType(to:) : 업스트림 게시자에 의해 정의된 failure를 변경함.
 `func setFailureType<E>(to failureType: E.Type) -> Publishers.SetFailureType<Self, E> where E : Error`
 Failure가 Never일 경우에만 사용 가능
 실패할 수 없는 publisher의 error type을 설정할 필요가 있을 때 이 오퍼레이터를 사용할 수 있음.
 반대로, 업스트림이 fail할 수 있다면 mapError(_:)를 사용하여 에러를 컨버팅하는게 가능하다.
 Never 타입의 failure를 가진 Publisher의 에러타입을 지정해주는 느낌이다.
 
 */
let pub1 = [0, 1, 2, 3, 4, 5].publisher
let pub2 = CurrentValueSubject<Int, Error>(0)
pub1
    .setFailureType(to: Error.self)
    .combineLatest(pub2)
    .sink(
        receiveCompletion: { print ("completed: \($0)") },
        receiveValue: { print ("value: \($0)")}
    )
    .store(in: &cancellables)


cancellables.forEach { $0.cancel() }

```