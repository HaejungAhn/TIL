```Swift
//: [Previous](@previous)

import Foundation
import Combine

var cancellables = Set<AnyCancellable>()

print("\n=======contains")
/*
 contains(_:) : argument와 동일한 element를 업스트림으로부터 받으면 Bool 값(true)을 게시한다.
 `func contains(_ output: Self.Output) -> Publishers.Contains<Self>`
 Output이 Equatable 프로토콜을 채택하고 있는 경우에만 사용할 수 있음.
 
 이 오퍼레이터를 사용하면 업스트림으로부터 게시된 element 중 argument와 일치하는 첫번째 element를 찾을 수 있다.
 contains 오퍼레이터는 argument와 일치하는 첫번째 값이 나오기 전까지는 업스트림으로부터 받은 element들을 consume한다.
 첫번째로 매치된 값을 찾으면 true를 게시하고 정상적으로 finish된다.
 만약 매칭된 값 없이 업스트림 publisher가 끝나게 되면 false를 리턴하고 정상적으로 finish된다.
 */
[-1, 4, 5, 1, 10, 5].publisher
    .print()
    .contains(5)
    .sink { print("contains:\($0)") }
    .store(in: &cancellables)

print("\n=======contains(where:)")
/*
 contains(where:) : 제공된 클로저를 만족하는 element를 받으면 Bool 값을 게시한다.
 `func contains(where predicate: @escaping (Self.Output) -> Bool) -> Publishers.ContainsWhere<Self>`
 
 predicate - element를 파라미터로 받음. element가 클로저의 compare logic을 충족하는지 여부를 나타내는 bool 값을 리턴함.
 
 Output이 Equatable을 채택하지 않은 경우 사용할 수 있다.
 여기도 조건을 충족하는 first element를 리턴함. first가 아닌 second, third element를 리턴할 수 있는 방법은 없나? -> 만약 struct라면 이 질문이 무의미할 수 있지만 class라면 좀 다를듯.
 */

[-1, 0, 15, 10].publisher
    .print()
    .contains { $0 > 4 }
    .sink { print("contains(where:):\($0)") }
    .store(in: &cancellables)

print("\n=======tryContains(where:)")
/*
 tryContains(where:) : contians(where:)와 비슷한데 클로저에서 에러를 throw할 수 있음.
 `func tryContains(where predicate: @escaping (Self.Output) throws -> Bool) -> Publishers.TryContainsWhere<Self>`
 매칭되는 값을 찾으면 true를 emit하고 publisher는 정상적으로 종료됨. 매칭되는 값이 없으면 false를 emit함.
 error가 throw됐을 경우 failure로 끝나나? ㅇㅇ
 */
struct IllegalValueError: Error {}

[3, 2, 10, 5, 0, 9].publisher
    .print()
    .tryContains {
        guard $0 % 2 == 0 else { throw IllegalValueError() } // 3에서 바로 에러가 throw되며 종료됨.
        return $0 < 10 // early finish를 위한 조건
    }
    .sink(
        receiveCompletion: { print("\($0)") },
        receiveValue: { print("tryContains(where:): \($0)") })
    .store(in: &cancellables)


print("\n=======allSatisfy(_:)")

/*
 allSatisfy(_:) : 업스트림으로부터 전달받은 element 모두가 주어진 클로저를 pass 했는지 여부를 emit함.
 `func allSatisfy(_ predicate: @escaping (Self.Output) -> Bool) -> Publishers.AllSatisfy<Self>`
 predicate - 주어진 element를 evaluate한다. true가 리턴되면 continue되고, false가 리턴되면 업스트림을 cancle하고 complete시킨다.
 
 Upon receiving any request greater than zero, this publisher requests unlimited elements from the upstream publisher.
 */
let targetRange = (-1...100)
let numbers = [-1, 0, 10, 5, 201]
numbers.publisher
    .allSatisfy { targetRange.contains($0) }
    .sink(
        receiveCompletion: { print("\($0)") },
        receiveValue: { print("allSatisfy:\($0)") })
    .store(in: &cancellables)
// reduce와 비슷하다는데 차이점으로는 해당 오퍼레이터는 단 하나의 값을 리턴한다는 것.
// reduce처럼 스트림에 대한 결과값을 하나로 모은다는 점에서 비슷하다고 설명한 것 같다.

print("\n=======tryAllSatisfy(_:)")
/*
 tryAllSatisfy(_:) : allSatisfy(_:)랑 비슷한데 클로저에서 에러를 throw할 수 있음.
 `func tryAllSatisfy(_ predicate: @escaping (Self.Output) throws -> Bool) -> Publishers.TryAllSatisfy<Self>
 `
 Upon receiving any request greater than zero, this publisher requests unlimited elements from the upstream publisher.
 */

numbers.publisher
    .tryAllSatisfy {
        guard $0 != 0 else { throw IllegalValueError() }
        return targetRange.contains($0)
    }
    .sink(
        receiveCompletion: { print("\($0)") },
        receiveValue: { print("tryAllSatisfy:\($0)") })
    .store(in: &cancellables)


/*
 Upon receiving any request greater than zero, this publisher requests unlimited elements from the upstream publisher.
 -> 이게 무슨의미일까? 오늘 본 mathmetical에 해당하는 operator들에도 위와 같은 문구가 있었는데.
 해석 : 0보다 큰 요청을 받으면 이 publisher는 업스트림 게시자에게 무제한 요소를 요청합니다.
 
 "요청을 받으면"에서 요청은 publish에 대한 요청인건가? 즉 다운스트림 publisher를 한개 이상 가지고 있을때를 의미하는 건가?
 "무제한 요소를 요청"한다는 것은 publisher를 종료시키지 않는다는 건가? 근데 downstream을 가지는 것과 unlimited elements를 요청하는 것은 무슨 상관관계가 있는거지?
 */

```