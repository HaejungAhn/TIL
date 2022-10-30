```Swift
//: [Previous](@previous)

import Foundation
import Combine

var cancellables = Set<AnyCancellable>()

// Mathmatical operator는 업스트림 publisher가 종료된 후에만 결과를 publish한다.

print("\n=======count")
/*
 count() : 업스트림 publisher로부터 받은 element의 개수를 게시한다.
 `func count() -> Publishers.Count<Self>`
 업스트림 publisher가 종료될때까지 모든 element를 consume함.(여기서 consume한다는 것은 다운스트림으로 게시하지 않고 해당 operator가 먹는다는 의미로 보면 됨) 그리고 전달받은 element의 전체 개수를 단일 값으로 emit함.
 즉, 업스트림 게시자가 종료될 때 딱 한번만 호출되는거네. 이런 것들은 종료되지 않는 업스트림 publisher에서는 사용해도 의미가 없겠다.
 그럼 count() 이후에 배치된 다운스트림 publisher들도 호출되지 않는건가? ㅇㅇ 다운스트림 publisher도 호출되지 않음.
 */
(0...10).publisher
    .count()
    .sink(receiveValue: { print("totla count:\($0)") })
    .store(in: &cancellables)

let testPassthroughSubject = PassthroughSubject<Int, Never>()
testPassthroughSubject
    .count()
    .sink {
        print("passthrough subject value: \($0)") // 아무것도 찍히지 않음.
    }
    .store(in: &cancellables)
testPassthroughSubject.send(1)
testPassthroughSubject.send(2)

print("\n=======max")
/*
 max() : 업스트림 publisher가 종료된 후에 전달받은 값 중 가장 큰 값을 게시한다.
 `func max() -> Publishers.Comparison<Self>`
 Output 타입이 Comparable을 채택해야지만 사용할 수 있다.
 */
[0, 15, 10].publisher
    .max()
    .sink { print("max value:\($0)") }
    .store(in: &cancellables)

print("\n=======max(by:)")
/*
 max(by:) : max() 메소드랑 동일한데 주어진 클로저(ordering closure)를 이용해 max 값을 찾음. 마찬가지로 업스트림 게시자가 종료된 후 값 받을 수 있음.
 `func max(by areInIncreasingOrder: @escaping (Self.Output, Self.Output) -> Bool) -> Publishers.Comparison<Self>
`
 areInIncreasingOrder(직역하면 증가하는 순서) - 두개의 element를 받는다. 만약 increasing order일 경우 true를 리턴한다. 현재 값과 다음 element 값을 비교한다.
 */
enum Rank: Int {
    case ace = 1, two, three, four, five, six, seven, eight, nine, ten, jack, queen, king
}

let cards: [Rank] = [.five, .queen, .ace, .eight, .jack]
cards.publisher
    .max {
        return  $0.rawValue < $1.rawValue // 오름차순 정렬일 때 true를 리턴해야하기 때문에 부등호 방향이 < 여야 하지만, 애플 공홈 예제에는 >로 나와있다. 뭐지..?🤔 근데 prints되는 결과는 "queen"이 나와야 함..?
    }
    .sink { print("max(by:):\($0)") }
    .store(in: &cancellables)

print("\n=======tryMax(by:)")
/*
 tryMax(by:) : max(by:)와 동일한데 ordering closure에서 에러를 throw할 수 있는 것.
 `func tryMax(by areInIncreasingOrder: @escaping (Self.Output, Self.Output) throws -> Bool) -> Publishers.TryComparison<Self>`
 에러가 throw되면 failure로 publisher가 종료되게 된다.
 */
struct IllegalValueError: Error {}

[0, 10, 6, 7, 22, 22].publisher
    .tryMax {
        guard $0 % 2 == 0 else { throw IllegalValueError() }
        return $0 < $1
    }
    .sink(
        receiveCompletion: { print("\($0)") },
        receiveValue: { print("tryMax(by:):\($0)") })
    .store(in: &cancellables)

print("\n=======min")
/*
 min() : 업스트림 publisher가 종료된 후, 전달받은 element 중 가장 작은 값을 게시한다.
 `func min() -> Publishers.Comparison<Self>`
 Output 타입이 Comparable을 채택해야지만 사용할 수 있다.
 */
[-10, 0, 10, 100].publisher
    .min()
    .sink { print("min: \($0)") }
    .store(in: &cancellables)


print("\n=======min(by:)")
/*
 min(by:) : min()이랑 동일한데 closure를 이용한다.
 `func min(by areInIncreasingOrder: @escaping (Self.Output, Self.Output) -> Bool) -> Publishers.Comparison<Self>
 `
 areInIncreasingOrder - max()와 동일하게 increasing order일 경우 true를 리턴한다.
 업스트림 publisher로부터 받은 Output이 Comparable을 채택하지 않은 경우 유용하다.
 */
[Rank.five, Rank.queen, Rank.ace, Rank.eight, Rank.jack].publisher
    .min {
        return  $0.rawValue < $1.rawValue
    }
    .sink { print("min(by:):\($0)") }
    .store(in: &cancellables)

print("\n=======tryMin(by:)")
/*
 tryMin(by:) : min(by:)랑 동일한데 closure에서 에러를 throw 할 수 있다.
 `func tryMin(by areInIncreasingOrder: @escaping (Self.Output, Self.Output) throws -> Bool) -> Publishers.TryComparison<Self>`
 에러 throw 시 failure로 종료된다.
 */
// tryMax와 예제코드가 동일하다(tryMin 오퍼레이터를 사용한다는 것만 빼면 내부 클로저 구현도 모두 동일함)

```