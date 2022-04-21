Moya로 네트워크 기능 테스트 중인데 API 호출 후 제네릭으로 Response를 받아서 전달하고 싶었다. 

**왜냐하면!**   
API 호출 후 서버로부터 받은 데이터가 아래와 같이 한번 wrapping되어 오는 경우도 있는데 이런 상황을 가정해서 토이 프로젝트를 만들고 있었기 때문이다.
```JSON
{
    "data": {
        // API 호출 후 데이터가 있다면 이곳에 전달.
    },
    "error": null
}
```
wrapping 된 데이터를 담을 `ResponseWrapper` 모델을 만들고, 진짜 데이터가 들어가 있는 `data`를 적절한 타입으로 디코딩하고 싶었다.    
그래서 제네릭을 사용했는데 오늘따라 `타입명.self`와 `타입명.Type`이 눈에 밟힌다. 👀 그래서 공부하게 된 오늘의 주제는 **`metatype`**!
```Swift
struct ResponseWrapper<T: Decodable>: Decodable {
    enum CodingKeys: String, CodingKey {
        case data
        case errors
    }
    
    var data: T?
    var errors: APIError?
    
    
    init(from decoder: Decoder) throws {
        let container = try decoder.container(keyedBy: CodingKeys.self)
        data = try? container.decode(T.self, forKey: .data)
        errors = try? container.decode(APIError.self, forKey: .errors)
    }
}
```


---
## MetaType   
— [Swift) Metytaype(.self, .Type, .Protocol) 정복하기 (1/2)](https://babbab2.tistory.com/151)   
— [Swift) Metytaype(.self, .Type, .Protocol) 정복하기 (2/2)](https://babbab2.tistory.com/152?category=828998)   
− [[Swift] Metatype 이란? (.Type, .self, .Protocol) (번역)](https://onelife2live.tistory.com/49)

- meta는 "~에 대한" 이란 의미를 가지고 있기 때문에 직역하면 <u>타입에 대한 타입</u>이라고 할 수 있다.
- "타입에 대한 타입"이 무슨 의미일까?   
아래와 같은 함수가 있다고 가정해보자. 이 함수의 매개변수로는 숫자와 문자, 두 가지를 전달할 수 있다.
    ```Swift
    func someMethod(num: Int, str: String) {
        // do Something
    }
    ```
    만약 이 함수를 사용하고 싶다면 다음과 같이 사용해야한다.
    ```Swift
    someMethod(num: 100, str: "Hello World!")
    ```
    그런데 함수를 사용할 때 100이나 "Hello World!"와 같은 <u>구체적인 값이 아니라 Int나 String 같은 타입 자체를 전달하고 싶다면</u> 어떻게 해야할까?   
    이럴때 사용하는게 바로 MetaType이다. `타입.Type`을 이용해 MetaType을 사용할 수 있다.
    ```Swift
    func someMethod(num: Int.Type, str: String.Type) {
        // do Something
    }
    ```
    위와 같이 정의된 함수를 호출할 때는 `타입.self`를 이용함!   
    ```Swift
    someMethod(num: Int.self, str: String.self)
    ```
    만약 호출할 때 `타입.Type`을 사용하게 되면 오류가 발생한다. 왜냐하면 `타입.Type`은 타입 그 자체이기 때문임. 
    ```Swift
    /// 아래와 같이 호출하는게 정상인데
    print("실제 값")

    /// 호출할 때 `타입.Type`을 사용하면 이렇게 사용하는거나 다름없는거임.
    print(String)
    ```

- MetaType에도 Static과 Dynamic이 있다.
    - `타입.self` : Static(컴파일 시점에 타입 정해짐)
    - `type(of:)` : Dynamic(런타임 시점에 타입 정해짐)


### Protocol의 MetaType
- 위에서의 내용을 토대로 보면 아래와 같은 것도 가능하다.
    ```Swift
    class Human {
        var name: String = "Jane"
    }
    let type: Human.Type = Human.self
    ```
- 근데 Protocol은 조금 다르게 동작함.
    ```Swift
    protocol Human { }
    /// error: cannot convert value of type 'Human.Protocol' to specified type 'Human.Type'
    let type: Human.Type = Human.self

    // 하지만 아래와 같이 Protocol을 채택한 타입을 이용하면 에러 없이 잘 동작한다.
    struct Jane: Human { }
    let type: Human.Type = Jane.self
    ```
    Protocol의 MetaType은 Protocl 그 자체보다는 Protocol을 채택한 모든 타입의 MetaType을 가리키는 것이다.   
    Apple에서는 이를 `existential metatype`이라고 부른다함.
- 따라서 진짜 Human이란 프로토콜의 meta type을 얻고 싶을 때 사용하는게 `타입.Protocol`이다.   
    ```Swift
    /// error: cannot convert value of type 'Jane.Type' to specified type 'Human.Protocol'
    let type: Human.Protocol = Jane.self
    /// 에러 없이 잘 동작함.
    let type: Human.Protocol = Human.self
    ```

### Self
- 이것도 하나의 타입이다.
- Self가 속해 있는 곳의 타입이 곧 Self의 타입임.    
  아래 코드에서 Self의 타입은 Self가 속해있는 SomeClass가 된다.
    ```Swift
    class SomeClass {
        required init() {  }
        func someMethod() -> Self {
            /// required 이니셜라이저로 호출하지 않으면 error발생함.
            /// Constructing an object of class type 'Self' with a metatype value must use a 'required' initializer
            return Self()
        }
    }

    let a = SomeClass()
    let b = a.someMethod()
    type(of: b) /// SomeClass.Type
    ```
- Self를 썼을 때 장점 : 타입에 의존하지 않는 코드, 프로토콜 내에서 실제 타입 대신 사용하기도 한다고 함.