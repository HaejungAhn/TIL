## MainActor.run에 대하여

참고한 아티클: [MainActor usage in Swift explained to dispatch to the main thread](https://www.avanderlee.com/swift/mainactor-dispatch-main-thread/)

<br>

### MainActor

- main actor는 Swift 5.5부터 도입되었으며, <u>task를 메인스레드에서 수행할 수 있도록</u> 한다. 
프로퍼티, 메소드, 인스턴스, 클로저에 사용되어야 한다.
- `GlobalActor` 프로토콜을 채택하고 있으며 싱글톤인것으로 보인다.
    - 내부 구현에 shared가 있고, 생성자가 노출되지 않고 있음
    ```Swift
    @globalActor final public actor MainActor : GlobalActor { }
    ```
    
- `@MainActor` 를 클래스에 추가하면, 클래스에 선언된 모든 task들이 메인 스레드에서 실행된다. 
- `nonisolated` 키워드를 사용해 메인스레드에서 실행될 필요가 없는 것들이 최대한 빨리 수행될 수 있도록 할 수 있다. 
- 프로퍼티 앞에 `@MainActor`를 추가하면 해당 프로퍼티의 수정은 반드시 메인 스레드에서만 이루어져야 한다.
- 클로저에 `@MainActor`가 붙는다면 이는 메인스레드에서 클로저가 수행됨을 마킹할 수 있다.(클로저가 메인스레드에서 호출되지 않더라도 실행은 메인스레드에서 이루어짐)
    
    ```swift
    func updateData(completion: @MainActor @escaping () -> ()) {
        /// Example dispatch to mimic behaviour
        DispatchQueue.global().async {
            print("completion 호출하는 곳은 메인스레드인가? \(Thread.isMainThread)") // false
            Task { await completion() } // 반드시 별도 Task 안에서 await 키워드와 함께 호출되어야 함.
        }
    }
    
    updateData {
        print("completion은 메인스레드에서 실행되는가? \(Thread.isMainThread)") // true
    }
    ```
<br>

### MainActor.run  
- MainActor의 extension에 보면 run 메소드가 정의되어 있음. 클로저에 있는 코드를 메인스레드에서 실행할 수 있도록 한다.
    
    ```swift
    @available(macOS 10.15, iOS 13.0, watchOS 6.0, tvOS 13.0, *)
    extension MainActor {
    
        /// Execute the given body closure on the main actor.
        public static func run<T>(resultType: T.Type = T.self, body: @MainActor @Sendable () throws -> T) async rethrows -> T where T : Sendable
    }
    ```
    
- 언제 `@MainActor`를 사용하고 언제 `MainActor.run`을 사용해야 하는가?
    
    반드시 메인스레드에서 처리될 필요가 없는 경우에는 @MainActor를 사용하는 것 보다는 MainActor.run을 사용하는게 더 적절할 수도 있다.
    
    ```swift
    viewModel.updateData { result in
        Task {
            await MainActor.run {
                // Handle result
            }
        }
    }
    ```