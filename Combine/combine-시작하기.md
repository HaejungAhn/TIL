아래 내용의 출처
- [https://github.com/AvdLee/CombineSwiftPlayground](https://github.com/AvdLee/CombineSwiftPlayground)
- [Getting started with the Combine framework in Swift](https://www.avanderlee.com/swift/combine/)


> It’s a powerful framework to dynamically respond to value changes over time.
컴바인은 시간의 흐름에 따른 value 변화에 동적으로 응답할 수 있는 프레임워크임.
> 

### “processing values over time”의 의미

- What does it actually mean?
- What can I do with it?
- when should I use it?

### Functional Reactive Programming(이하 FRP)

- this allow you to process values over time.
    
    example of values : 네트워크 response, UI 이벤트, 다른 유형의 비동기 데이터 등
    
- FRP 시퀀스는 아래와 같이 묘사될 수 있다.
    - 네트워크 응답을 받는다.
    - 이 데이터를 JSON model로 mapping 한다.
    - 그리고 그걸 View에 할당하고 싶다.

### Basic principles of Combine

**Publishers and subscribers**

- publisher는 값과 에러를 게시할 수 있음
- subscriber는 publisher를 구독함으로써 publisher가 게시하는 값/에러를 받을 수 있음.
- subscriber가 publisher를 구독해야지만 publisher의 게시가 시작됨.
- publisher의 output 데이터 타입은 subscriber의 Input 데이터 타입과 일치해야함.
    
    publisher의 에러 타입 역시 마찬가지로 subscriber의 에러 타입과 일치해야함.
    

**The Foundation framework and Combine**

foundation 프레임워크를 Combine과 함께 사용하기 위해 많은 extension들이 포함되어 있음.

아래는 그 예시들임.

- URLSessionTask는 data response 또는 request error를 게시함.
- 특정 Notification.Name을 위한 publisher

Notification.Name 을 이용해 예제코드를 만듬.

```swift
import UIKit
import Combine

extension Notification.Name {
    static let newBlogPost = Notification.Name("new_blog_post")
}

struct BlogPost {
    let title: String
    let url: URL
}

class CombineTestViewController: UIViewController {
    override func viewDidLoad() {
        super.viewDidLoad()
        self.setupNotification()
    }
    
    private func setupNotification() {
        //MARK: Publisher 만들기
        let blogPostPublisher = NotificationCenter.Publisher(center: .default, name: .newBlogPost) // 여기까지만 했을 때는 타입이 NotificationCenter.Publisher
            .map { notification -> String? in
                return (notification.object as? BlogPost)?.title ?? ""
            } // map을 이용하면 타입이 Publishers.Map<NotificationCenter.Publisher, String?>
        
        //MARK: Subscriber 만들기
        let lastPostLabelSubscriber = Subscribers.Assign(object: lastPostLabel, keyPath: \.text)
        
        //MARK: Subscriber가 Pbulisher 구독하도록 만들기
        blogPostPublisher
            .receive(subscriber: lastPostLabelSubscriber) // map으로 처리 안해주면 Instance method 'receive(subscriber:)' requires the types 'String?' and 'Notification' be equivalent, 사이트 코드에는 subscribe 메소드를 사용해 등록하던데 지금은 그게 안됨.
        
        //MARK: Subscriber가 Pbulisher 구독하도록 만들기 - 더 쉬운 방법
				//label이 메모리에서 해제되면 subscription도 같이 해제된다.
        blogPostPublisher.assign(to: \.text, on: lastPostLabel2)
        
        let blogPost = BlogPost(title: "Getting started with the Combine framework in Swift",
                                url: URL(string: "https://www.avanderlee.com/swift/combine/")!)
        NotificationCenter.default.post(name: .newBlogPost, object: blogPost) // object로 그냥 값 전달 가능하구나..!
        print(lastPostLabel.text)
        print(lastPostLabel2.text)
    }
    
    
    private let lastPostLabel = UILabel()
    private let lastPostLabel2 = UILabel()
}
```

### The rules of a subscription

- A subscriber can only have one subscription
- Zero or more values can be published
- At most(최대) one completion will be called // ? Notification 예제를 보면 Publisher는 절대 complete 되지 않음.

 completing publisher의 예제: URLSessionTask Publisher

data response 혹은 request error로 끝나게 됨.

error가 스트림을 통해 던져지면, 구독은 취소되어진다. 비록 스트림이 여러 값을 통과하도록 허용하더라도?

**@Published usage to bind values to changes**

- 값을 변경사항에 바인딩하는(to bind values to changes) @Published의 사용
- 프로퍼티 wrapper, 어떠한 프로퍼티에도 Publisher를 붙일 수 있음.
- 클래스에서만 사용 가능

### Memory management in Combine

- Combine에서 메모리 관리는 매우 중요하다.
- 값을 받고 처리하기 위해서 subscribers는 subscription을 필요한 기간동안 retain 하고 있어야 한다. 그러나 더 이상 필요가 없으면 모든 참조가 release되어야 함.
- Combine에는 `AnyCancellable`이라는게 있음.
- `AnyCancellable` 클래스는 deinit 시 cancel()을 호출하고 구독이 조기 종료 되도록 한다.
- 여러개의 subscription을 유지해야할 경우 `store(in:)` operator를 사용해라.
    - 🤔 operator와 method의 차이는 뭘까?

**Error Types and streams**

에러 타입과 관련된 여러 operator를 언급하고 있음.

[Error handling in Combine explained with code examples](https://www.avanderlee.com/swift/combine-error-handling/)

**Debuggin Combine streams**

Functional Reactive Languages를 디버깅하는 것은 매우 어렵다.

- [long error descriptions](https://twitter.com/twannl/status/1141756611616284672?s=20) and [unreadable stack traces in Xcode](https://twitter.com/twannl/status/1141762979496648706?s=20)

다행히도 Combine에서는 디버깅을 할 수 있는 operator들을 제공한다.

- print(): 모든 게시된 이벤트에 대한 로그 메세지를 프린트함.
- breakpoint(): debugger signal을 발생시킴
- breakpointOnError(): 실패했을 경우에만 deubgger를 발생시킴.

[Combine debugging using operators in Swift](https://www.avanderlee.com/debugging/combine-swift/)

여기까지 읽으니 operator의 중요성이 더욱더 실감나게 되는구만.

## When should I use Combine?

If we take a look at a quote from the [Apple docs](https://developer.apple.com/documentation/combine), Combine gives you the following:

> By adopting Combine, you’ll make your code easier to read and maintain, by centralizing your event-processing code and eliminating troublesome techniques like nested closures and convention-based callbacks.
> 

but, 디버깅이 꽤 어렵고 러닝커브가 있음.