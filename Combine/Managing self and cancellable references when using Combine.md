아래 링크를 보고 정리한 내용임.<br>
📚  [Managing self and cancellable references when using Combine](https://www.swiftbysundell.com/articles/combine-self-cancellable-memory-management/#a-cancellable-manages-the-lifetime-of-a-subscription)

<br>

### A cancellable manages the lifetime of a subscription

- Cancellable은 subscription이 얼마나 오래 메모리 상에 남아 있어야 하는지(stay alive and active)를 컨트롤함.
    - subscription이 cancellabel에 묶여있지 않으면, 해당 subscription은 더이상 active (또는 alive)상태가 아닌건가?
- cancellable이 deallocated 되거나 수동으로 취소가 되면 여기에 묶여있던 subscription들은 자동으로 무효화(invalidated)된다.
- 아래 코드는 retain cycle(즉, memory leak)이 발생할 가능성이 있을까? 만약 그렇다면 어떤 부분에서 어떻게 retain cycle이 발생하게 될까?
    - 👀 정답은 이곳에
        - Timer를 구독하는 Subscription(`sink`) 내부에서 self를 캡쳐링하고 있음. 해당 Subscription은 cancellable에 묶여 있는 것이기 때문에 cancellable이 sink 안에 있는 self를 소유하고 있음.
        - 그리고 cancellable 프로퍼티는 멤버변수이기 때문에 self가 소유하고 있음.
        - cancellable과 self가 서로를 참조하는 retain cycle이 발생함.
    
    ```swift
    class Clock: ObservableObject {
        @Published private(set) var time = Date().timeIntervalSince1970
        private var cancellable: AnyCancellable?
    
        func start() {
            cancellable = Timer.publish(
                every: 1,
                on: .main,
                in: .default
            )
            .autoconnect()
            .sink { date in
                self.time = date.timeIntervalSince1970
            }
        }
    
        func stop() {
            cancellable = nil
        }
    }
    ```
    
    🤔  AnyCancellables의 store() 메소드를 이용해도 위 코드와 동일한 memory leak이 발생할 수 있을까?
    
<br>

### Avoiding self-related memory leaks

- 첫번째 대안: sink 대신 assign operator를 쓰자.
    - 코드적으로 봤을 때 self를 직접 참조하진 않는다.(keyPath를 사용하기 때문)
    - 하지만 이 방법은 문제가 있다. assign operator는 넘겨받은 object에 대한 강한참조를 유지하기 때문임.
    
- 두번째 대안: 캡쳐 리스트를 통해 self를 약하게 참조한다.(`[weak self]`)

### Assigning output values directly to a Published property

— iOS 14, macOS Big Sur 기준

— @Published는 iOS 13이상부터 사용이 가능한데 왜 기준을 저렇게 두었을까?

직접 combine pipeline을 published proeprty에 연결할 수 있다.  하지만 이 경우 AnyCancellable을 리턴하지 않는다는 특징이 있음. 

🤔 이렇게 했을 때 Timer 인스턴스의 값 게시가 여러번 될까? 그리고 이 경우 메모리 누수가 발생할 가능성은 없는걸까? 이건 Published 프로퍼티에만 적용이 가능한 것일까?

```swift
class Clock: ObservableObject {
    @Published private(set) var time = Date().timeIntervalSince1970
    
    init() {
        Timer.publish(
            every: 1,
            on: .main,
            in: .default,
        )
        .autoconnect()
        .map(\.timeIntervalSince1970) // 이 부분 이해가 잘 안감.
        .assign(to: &$time)
    }
}
```

이런 접근방법의 장점은 `time` 프로퍼티의 lifetime을 기반으로 Combine이 자동으로 subscription의 lifetime을 관리한다는 것이다. — memory leak을 피하면서 cancellable과 관련한 부기코드(bookkeeping code)를 줄일 수 있는 것이다.

<br>

### Weak proeprty assignments

memory leak을 피하기 위해 매번 weak self를 캡처리스트에 추가하고 이를 이용해 값 할당하는게 조금 번거로울 수도 있다. 이럴 경우에 사용할 수 있는 syntatic sugar를 소개하고 있다.

```swift
class ModelLoader<Model: Decodable>: ObservableObject {
    enum State {
        case idle
        case loading
        case loaded(Model)
        case failed(Error)
    }

    @Published private(set) var state = State.idle

    private let url: URL
    private let session: URLSession
    private let decoder: JSONDecoder
    private var cancellable: AnyCancellable?

    ...

    func load() {
        state = .loading

		// 여기 아래부분 코드 잘 이해 안감.
        cancellable = session
            .dataTaskPublisher(for: url)
            .map(\.data)
            .decode(type: Model.self, decoder: decoder)
            .map(State.loaded)
            .catch { error in
                Just(.failed(error))
            }
            .receive(on: DispatchQueue.main)
            .sink { [weak self] state in
						    self?.state = state
						}
    }
}
```

- 어떻게 sink 부분에서 weak self를 사용하지 않고도 memory leak을 방지할 수 있도록 만들 수 있을까?
    
```swift
extension Publisher where Failure == Never {
    func weakAssign<T: AnyObject>(
        to keyPath: ReferenceWritableKeyPath<T, Output>,
        on object: T
    ) -> AnyCancellable {
        sink { [weak object] value in
            object?[keyPath: keyPath] = value
        }
    }
}
```
    
<br>

### Capturing stored objects, rather than self

```swift
class ModelLoader<Model: Decodable>: ObservableObject {
    enum State {
        case idle
        case loading
        case loaded(Stored<Model>)
        case failed(Error)
    }

    ...
    private let database: Database
    ...

    func load() {
        state = .loading

        cancellable = session
            .dataTaskPublisher(for: url)
            .map(\.data)
            .decode(type: Model.self, decoder: decoder)
            .flatMap {
    [weak self] model -> AnyPublisher<Stored<Model>, Error> in
    
    guard let database = self?.database else {
        return Empty(completeImmediately: true)
            .eraseToAnyPublisher()
    }
    
    return database.store(model)
}
            .map(State.loaded)
            .catch { error in
                Just(.failed(error))
            }
            .receive(on: DispatchQueue.main)
            .weakAssign(to: \.state, on: self)
    }
}
```

<br>

새로운 단어

- ObservableObject
- Timer.publish().autoconnect()
- bookkeeping code