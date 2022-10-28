```Swift
//: [Previous](@previous)

import Foundation
import Combine

var cancellables = Set<AnyCancellable>()

// reduce는 "줄이다"라는 의미를 가지고 있음. reducing 함수들은 collection의 값을 하나로 만들어주는 역할을 한다.
print("\n=======collect ")
/*
 collect() : 전달받은 모든 elements를 모은 후 업스트림 publisher가 종료됐을 때 collection의 단일 array로 이 값들을 방출한다. 즉 element를 하나로 모아서 게시하는거네. 중요한 것은 '모은다'는 것과 '업스트림 publisher가 정상적으로 종료됐을 때에만 값을 방출한다'는 것!
 `func collect() -> Publishers.Collect<Self>`
 element들을 모아서 하나의 single array로 값을 방출하고 싶을 때 이를 사용할 수 있음.
 만약 업스트림 publisher가 에러로 실패했을 경우 이 publisher는 downstream으로 output을 보내는 대신 에러를 전달한다.
 이 publisher는 upstream pupblisher로부터 element를 무수히 많이(제약없이) 요청할 수 있고 전달받은 값들을 저장하기 위해 제한없는 양의 메모리를 사용한다. 따라서 해당 publisher는 시스템의 memory pressure를 일으킬 수 있다.
 */
let numbers = (0...10)
numbers.publisher
    .collect()
    .sink { print("\($0)") }
    .store(in: &cancellables)

print("\n=======collect(_:)")
/*
 collect(_:) : 지정된 개수만큼 요소들을 모은 후 single array로 방출한다.
 `func collect(_ count: Int) -> Publishers.CollectByCount<Self>`
 count - 게시하기 전 collecting할 요소의 최대 개수
 만약 지정된 count만큼 모으기 전에 publisher가 종료된다면 그동안 모은 element만 방출한다. 에러 발생 시 collect()와 마찬가지로 single array가 아닌 error가 downstream에 방출된다.
 */
let numbers2 = (0...10)
numbers2.publisher
    .collect(5)
    .sink { print("\($0)", terminator: " ") }
    .store(in: &cancellables)

print("\n=======collect(_:options:)")
/*
 collect(_:options:) : 주어진 time-grouping 전략에 따라 요소들을 collect하고 single array를 방출한다.
 `func collect<S>(
    _ strategy: Publishers.TimeGroupingStrategy<S>,
    options: S.SchedulerOptions? = nil
  ) -> Publishers.CollectByTime<Self, S> where S : Scheduler`
 strategy - operator가 element를 collect하고 게시하는데 사용되는 타이밍 그룹 strategy이다.
    두가지 케이스가 있음.
    case byTime : 아이템을 collect하고 주기적으로 게시하는 그룹
    case byTimeOrCount : 아이템을 collect하고 주기적으로 게시하거나 buffer가 최대 사이즈에 도달했을 때 게시하는 그룹
 options - strategy에 사용할 스케쥴러 옵션
 
 Scheduler와 Stride에 의해 지정된 스케쥴에 따라 element의 array를 방출하려면 collect(_:options:)를 사용해라.
 지정된 간격이 종료될 때마다 publisher는 collect한 items이 포함된 array를 보낸다. 만약 업스트림 publisher가 buffer를 채우기 전에 종료되면, publisher는 전달받은 아이템들이 포함된 array를 보낸다. 요청된 Stride에 지정된 element의 수보다 적을 수 있다.
 */

Timer.publish(every: 1, on: .main, in: .default)
    .autoconnect()
    .collect(.byTime(RunLoop.main, .seconds(5)))
    .sink { print("\($0)", terminator: "\n\n") }
    .store(in: &cancellables)

/*
 잠시 스케줄러에 대해 보도록 하자.
 https://developer.apple.com/documentation/combine/scheduler
 https://zeddios.tistory.com/972
 protocol Scheduler<SchedulerTimeType> : 언제(when), 어떻게(how) 클로저를 실행할지를 정의하는 프로토콜.
 스케줄러를 사용해 가능한 한 빨리 혹은 특정 시점 이후에 클로저를 실행시킬 수 있다. 해당 프로토콜은 SchedulerTimeIntervalConvertible을 채택하고 있기 때문에 .milliseconds(500)과 같은 편리한 함수를 사용해서 시간을 표현할 수 잇다.
 스케줄러는 element가 생성된 스레드와 동일한 스레드에서 실행된다. 즉, element가 백그라운드 스레드에서 생성됐다면 클로저 내에서 Thread.isMainThread를 찍어봤을 때 false가 나온다는 것. 같은 publisher라도 element가 어디서 생성됐느냐에 따라 다르다. (Combine에서는 명시적으로 스케줄러를 지정하지 않아도 기본 스케줄러가 지정된다.)
 스케줄러를 명시적으로 지정해주고 싶다면 receive(on:), subscribe(on:)을 이용하면 된다.
 - receive(on:) 다운스트림의 실행 컨텍스트를 변경하는 역할
 - subscribe(on:) 업스트림의 실행 컨텍스트를 변경하는 역할. subscribe, cancel, request operation을 수행할 schduler를 지정하는 역할을 한다. 업스트림의 실행 컨텍스트를 변경하게 되면 변경된 컨텍스트에서 element가 publish 되고, 이는 곧 다운스트림의 스케줄러에도 영향을 미치기 때문에 어떤 위치에서 호출해도 상관 없다.
 
 위 두 메소드는 매개변수로 Scheduler 타입을 받는데, DispatchQueue, OperationQueue, RunLoop, ImmediateScheduler 모두 Scheduler를 채택하고 있기 때문에 위에 언급된 것들을 다 사용할 수 있음.
 
 */
//["hello world"].publisher
//    .map{ _ in print("context1: \(Thread.isMainThread)") } // true
//    .receive(on: DispatchQueue.global())
//    .map{ _ in print("context2: \(Thread.isMainThread)") } // false
//    .sink { print("context3: \(Thread.isMainThread)") } // false
//
//["helloWolrd"].publisher
//    .map{ _ in print("context4: \(Thread.isMainThread)") } // true
//    .subscribe(on: DispatchQueue.global())
//    .map{ _ in print("context5: \(Thread.isMainThread)") } // false
//    .sink{ print("context6: \(Thread.isMainThread)") } // false

print("\n=======ignoreOutput()")
/*
 ignoreOutput() : 업스트림에서 게시한 모든 element들을 무시한다. 단, 업스트림 publisher의 completion state(completion, faile) 상태는 다운스트림에 전달한다.
 `func ignoreOutput() -> Publishers.IgnoreOutput<Self>`
 publisher가 성공적으로 완료할 수 있는지 혹은 실패할 것인지를 확인한다(?)

 업스트림 publisher가 에러 없이 성공할 경우 element를 consume하지만 이를 다운스트림으로 다시 게시하지는 않는다. 그러다 업스트림 publisher에서 에러가 발생하거나 정상적으로 종료되면 completion 결과를 다운스트림으로 게시한다.
 
 🤔 이게 어떤 경우에 필요한걸까?
 네트워크를 통해 API를 호출하는데 response는 필요하지 않지만, 실패/성공에 따른 처리가 필요한 경우..?
 */
struct NoZeroValuesAllowedError: Error {}
let nubmers3 = [1, 2, 3, 4, 5, 6, 0, 1, 2, 4]
nubmers3.publisher
    .tryFilter { anInt in
        guard anInt != 0 else { throw NoZeroValuesAllowedError() }
        return anInt < 20
    }
    .ignoreOutput()
    .sink(
        receiveCompletion: { print("completion : \($0)", terminator: " ") },
        receiveValue: { print("value:\($0)", terminator: " ") }) // Will never be executed
    .store(in: &cancellables)

print("\n=======reduce(_:_:)")
/*
 reduce(_:_:) : 스트림의 각 element를 수집하고 완료됐을 때 최종 결과를 publish하는 클로저를 적용한다.
 `func reduce<T>(
    _ initialResult: T,
    _ nextPartialResult: @escaping (T, Self.Output) -> T
  ) -> Publishers.Reduce<Self, T>`
 
 initialResult - 클로저가 처음 호출됐을 때 받게될 값
 nextPartialResult - 이전에 축적된 값과 업스크림 게시자로부터 받은 다음 값을 받아 새로운 값을 생성하는 클로저다.
 
 mapping operator의 scan과 매우매우 비슷하다. 차이가 있다면 scan은 클로저를 통해 연산될 때마다 해당 값을 다운스트림으로 게시하는 reduce는 하나의 값으로 만들어낸다는 것.
 
 reduce를 진행하면 모든 전달받은 element에 클로저를 적용하고 업스트림 publisher가 종료됐을 때 축적된 값을 생성하는 publisher가 리턴된다. 만약 업스트림 publisher로부터 error를 전달받게 되면, operator는 이 에러를 다운스트림에 전달하고 publisher는 종료되며 어떠한 값도 게시하지 않는다.
 */
let numbers4 = (0...10)
numbers4.publisher
    .reduce(0) { accum, next in
        print("\(accum), \(next)")
        return accum + next
    }
    .sink(
        receiveCompletion: { print("completion: \($0)") },
        receiveValue: { print("value: \($0)") })
    .store(in: &cancellables)

print("\n=======tryReduce(_:_:)")
/*
 tryReduce(_:_:) : reduce랑 똑같은데 클로저에서 error를 throw함.
 `func tryReduce<T>(
    _ initialResult: T,
    _ nextPartialResult: @escaping (T, Self.Output) throws -> T
  ) -> Publishers.TryReduce<Self, T>`
 
 */
struct DivisionByZeroError: Error {}
func myDivide(_ dividend: Double, _ divisor: Double) throws -> Double {
    guard divisor != 0 else { throw DivisionByZeroError() }
    return dividend / divisor
}

var numbers5: [Double] = [5, 4, 3, 2, 0, 1]
numbers5.publisher
    .tryReduce(numbers5.first!, { accume, next in try myDivide(accume, next) })
    .catch({ _ in Just(Double.nan) }) // subscriber에게 한번만 값을 emit하고 finish되는 publisher.
    .sink(
        receiveCompletion: { print("completion:\($0)") },
        receiveValue: { print("value: \($0)") })
    .store(in: &cancellables)




```