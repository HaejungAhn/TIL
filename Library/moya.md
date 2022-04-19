## Moya란 무엇인가?
![Moya란 무엇인가요?](https://github.com/Moya/Moya/blob/master/web/diagram.png?raw=true)

> — [[iOS] Moya를 사용한 네트워킹 (Swift Http 통신)](https://velog.io/@dlskawns96/iOS-Moya%EB%A5%BC-%EC%82%AC%EC%9A%A9%ED%95%9C-%EB%84%A4%ED%8A%B8%EC%9B%8C%ED%82%B9-Swift-Http-%ED%86%B5%EC%8B%A0)
> 
> 
> iOS에서 네트워킹을 구현하는 가장 기본적인 방법은 `URLSession`을 사용하는 것입니다.
> 
> `URLSession`은 로우레벨의 코드를 작성할 수 있고, 다른 프레임워크를 사용할 필요가 없다는 장점이 있지만, 사용이 복잡하고 코드의 가독성이 좋지 않아서 Foundation Networking을 기반으로한 인터페이스를 제공해 네트워킹 작업을 단순화 해주는 라이브러리인`Alamofire`를 많이 사용합니다.
> 
> `Alamofire`는 iOS 앱을 개발할 때 가장 보편적으로 많이 사용되는 방법이지만, **유지보수와 유닛 테스트가 힘들다는 단점**이 있습니다.
> 
> 그래서 등장한 `Moya`는 `URLSession`을 추상화한 `Alamofire`를 다시 추상화한 프레임워크로 **Network Layer를 템플릿화 해서 재사용성을 높히고, 개발자가 request, response에만 신경을 쓰도록** 해줍니다.
> 

Moya는 네트워크 추상 레이어를 이용해 Alamofire의 직접적인 호출을 캡슐화한다.

## Moya는 어떻게 사용할 수 있을까?
— [[github] Moya Basic](https://github.com/Moya/Moya/blob/master/docs/Examples/Basic.md)
- enum(=네트워크 템플릿)을 정의한다. 연관값을 이용해 API 호출에 필요한 파라미터 값도 정의가능
    
    해당 enum은 컴파일 시 각 대상에 대해 필요한 구현들을 모두 제공하는지 확인하는데 사용한다.
    
- enum이 TargetType 프로토콜을 채택하도록 확장한다.
    
    지정해야하는 항목은 아래와 같다. Endpoint를 생성하는데 필요한 모든 정보를 가지고 있다.
    
    ```swift
    /// The target's base `URL`.
    var baseURL: URL { get }
    
    /// The path to be appended to `baseURL` to form the full `URL`.
    var path: String { get }
    
    /// The HTTP method used in the request. Alamofire.HTTPMethod
    var method: Moya.Method { get }
    
    /// Provides stub data for use in testing. Default is `Data()`.
    var sampleData: Data { get }
    
    /// The type of HTTP task to be performed.
    var task: Task { get }
    
    /// The type of validation to perform on the request. Default is `.none
    var validationType: ValidationType { get }
    
    /// The headers to be used in the request.
    var headers: [String: String]? { get }
    ```
    
- MoyaProvider를 생성하면서 제네릭타입에 위에서 생성한 네트워크 템플릿(enum)을 지정한다.
    
    ```swift
    provider.request(.zen) { result in
        switch result {
        case let .success(moyaResponse):
            let data = moyaResponse.data // Data, your JSON response is probably in here!
            let statusCode = moyaResponse.statusCode // Int - 200, 401, 500, etc
    
            // do something in your app
        case let .failure(error):
    				// 서버가 요청을 받지못했거나 request 타임아웃으로 서버가 response를 보내지 못한 경우임.
            // TODO: handle the error == best. comment. ever.
        }
    }
    ```
    
    이때 provider를 어딘가에서 retain하고 있어야 한다. 이렇게 하지 않으면 자동으로 해제되며 잠재적으로 응답을 받기 전 해제된다. 
    
- Endpoint를 커스텀하여 기본적인 동작을 커스텀할 수도 있다. <u>이런 특성을 이용해 다양한 상황에 맞게 테스팅을 쉽게 할 수 있음.</u>*→이건 아직 이해못함.*
    
    ```swift
    let endpointClosure = { (target: MyService) -> Endpoint in
        return Endpoint(url: URL(target: target).absoluteString, 
                        sampleResponseClosure: {.networkResponse(200, target.sampleData)}, 
                        method: target.method, 
                        task: target.task, 
                        httpHeaderFields: target.headers)
    }
    
    // Tuck this away somewhere where it'll be visible to anyone who wants to use it
    var provider: MoyaProvider<MyService>!
    
    // Create this instance at app launch
    let provider = MoyaProvider(endpointClosure: endpointClosure)
    ```
    
- Endpoint 인스턴스는 URLRequest를 만드는데 사용된다. Moya는 Alamofire를 wrapping한 것이기 때문에 실제 네트워킹 처리는 Alamofire로 이루어진다. Alamofire를 통해 통신 후 response가 오면 Result 타입으로 success와 failure를 wrapping한다.
    
- Moya.Response를 통해 데이터와 Status code를 unpack할 수 있음.
    
## Moya 사용하기
- 토큰 필요없는 테스트 API 찾기
    
    API출처는 [여기](https://github.com/sameerkumar18/geek-joke-api)
    
    GET: [https://geek-jokes.sameerkumar.website/api?format=json](https://geek-jokes.sameerkumar.website/api?format=json)
    
    → “joke”라는 key에 랜덤으로 농담(String) 하나를 리턴해줌.
    ```JSON
    // 리턴 예시
    {
        "joke": "If brute force doesn’t solve your problems... then you aren’t using enough."
    }
    ```
    
- 테스트 프로젝트 생성하기
- Alamofire와 비교를 위해 Moya, Alamofire 각각의 라이브러리를 활용해 request 만들고, 코드 만들때 어떤 장,단점이 있는지 생각해보기
    
    내가 생각했을 때 alamofire에 비해 moya의 장점은 다음과 같다.
    
    - API를 기능별로 그룹핑할 수 있고 enum으로 관리함으로써 코드의 중복을 제거함.
    - Alamofire는 request 요청을 만들 때 get/post 지정했지만 moya는 enum의 method 계산프로퍼티를 통해 이를 지정할 수 있음.
    - Alamofire는 reuqest 요청을 만들 때 매개변수를 각각 생성해서 넣어줬던 반면 moya는 enum의 task 계산프로퍼티를 통해 이를 지정할 수 있음.
    - headers도 enum의 타입별로 지정할 수 있음.
    - sampleData 계산프로퍼티를 이용해 unit test를 쉽게 해줌.
    
    **→ 전반적으로 코드의 재사용성을 높임. 한곳에서 API 설정(get/post, 매개변수 생성, header설정 등)을 관리함. 또한 unit test를 쉽게 만들어줌.**
    
- 아래는 테스트했던 예제코드임.
    
    ```swift
    struct Joke: Decodable {
        let joke: String
    }
    
    protocol NetworkingProtocol {
        func execute()
    }
    ```
    
    Alamofire 예제코드
    
    ```swift
    class AlamofireNetworking: NetworkingProtocol {
        static let shared = AlamofireNetworking()
        private init() { }
        
        func execute() {
            AF.request("https://geek-jokes.sameerkumar.website/api?format=json").responseDecodable(of: Joke.self) {
                response in
                switch response.result {
                case .success(let result):
                    print(result.joke)
                case .failure(let err):
                    debugPrint(err)
                }
            }
        }
    }
    ```
    
    Moya 예제코드
    
    ```swift
    enum Service {
        case joke
    }
    extension Service: TargetType {
        var baseURL: URL { URL(string: "https://geek-jokes.sameerkumar.website/api?format=json")! }
        
        var path: String {
            switch self {
            case .joke:
                return ""
            }
        }
        
        var method: Moya.Method {
            switch self {
            case .joke:
                return .get
            }
        }
        
        var task: Task {
            switch self {
            case .joke:
                return .requestPlain
            }
        }
        
        var headers: [String : String]? {
            return ["Content-type": "application/json"]
        }
        
        
    }
    
    class MoyaNetworking: NetworkingProtocol {
    	  private let provider = MoyaProvider<Service>()
        static let shared = MoyaNetworking()
        private init() { }
        
        func execute() {
            provider.request(.joke) { result in
                switch result {
                case .success(let moyaResponse):
                    let statusCode = moyaResponse.statusCode
                    switch statusCode {
                    case 200:
                        let data = try? moyaResponse.map(Joke.self)
                        print(data!.joke)
                    default:
                        break
                    }
                case .failure(let err):
                    print(err)
                }
            }
        }
    }
    ```
    

## MoyaSugar 무엇이며 왜 사용할까?
— [[github] Moya Sugar](https://github.com/devxoul/MoyaSugar)   
**→ Moya를 사용하면서 새로운 endpoint 추가에 따른 코드 수정을 최소화하도록 도와주는 라이브러리**   

>Moya는 enum을 활용해 API endpoint들을 추상화한 네트워크 추상 Layer다. 그러나 endpoint를 추가할 때마다, 적어도 5군데에 코드를 작성해야한다 : enum case 추가, method, path, parameters, parameterEncoding 프로퍼티   
만약 http header field도 다르게 설정한다면 MoyaProvider의 endpointClosure도 커스텀해야함.   
이런 문제를 해결하기 위해 Moya sugar가 나옴.

> Forget about `method`, `path`, `parameters`, `parameterEncoding` and `endpointClosure`.   
Use `route`, `params`, `httpHeaderFields` instead.

route : method와 path를 합침   
params : parameters, parameterEncoding합침