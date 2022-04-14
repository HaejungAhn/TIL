2022-03-02

---
- Swift에서 에러는 `Error` 프로토콜을 준수하는 타입의 값으로 표현된다.

- 에러 발생은 `throw` 키워드를 이용해 발생시킬 수 있다.

- Swift의 에러 처리(Handling Errors)

- 에러 전파
    
    에러가 발생할 수 있는 함수, 메소드, 또는 초기화 구문을 나타내기 위해 파라미터와 리턴 화살표 사이에 `throws` 를 추가한다.
    
    throws를 이용해 정의된 함수는 do-catch, try, try?, try!를 이용해 호출할 수 있다. 
    
- do - catch
    
    에러가 `do` body 안에서 발생하면 `catch` 절의 패턴과 비교하여 에러를 처리할 수 있다. 
    
    `catch` 절이 패턴을 가지고 있지 않다면 해당 catch는 모든 에러와 일치하고 error라는 이름을 가진 지역 상수로 에러를 바인딩한다.
    
    `try?` `try!`를 사용하면 do-catch문을 사용하지 않아도 간편하게 에러처리를 할 수 있다.
    
- 옵셔널 값으로 에러 처리
    
    `try?` 를 사용함. 코드 실행했을 때 에러가 발생하는 경우 nil을 리턴. 에러 발생하지 않을 경우 optional이다.
    
- 에러 발생하지 않는것을 보장
    
    `try!` 는 에러가 발생하지 않는 것이 확실할 경우 사용한다. 에러 전파 비활성화함.
    

- `defer`

defer - 미루다, 연기하다

`defer` 바디안에 있는 코드들은 현재 코드블록이 종료(에러, return, break 등으로 인해)되기 직전에 실행됨.

상단에 `defer` 가 정의되어 있어도 바로 실행되지 않는다. 스코프가 종료되기 직전에 실행된다. 내부에는 현재 스코프 외부로 제어를 이동시키는 return, break, 에러발생 코드를 포함할 수 없다.

defer는 코드에 작성된 순서와 반대로 실행된다. (즉, defer 구문이 두개 있을 때 두번째 작성된 defer먼저 실행되고 그 다음에 첫번째 defer 구문이 실행됨)

`**defer` 를 읽기 전 함수 종료 시 `defer` 실행안됨.**

`defer` 를 중첩해서 사용할 수도 있는데 이때 실행순서는 바깥쪽부터임.

- `Result<Value, Error>`

Swift 5.0부터 도입됨.

[https://onelife2live.tistory.com/1](https://onelife2live.tistory.com/1)

 

**무엇인가**

각 케이스별 연관값을 포함해서 성공 또는 실패를 표현하는 값이다.

- 초기화 구문 

```swift
init(catching: () -> Success)

let res = Result {
    try canThrowError(value: "r")
}

if case .success(_) = res {
    print("success")
} else {
    print("fail")
}
```

- get() : Result의 success 케이스에 대한 연관값을 가져오려고 시도한다. `try` 와 do-catch 혹은 `try?` 나 `try!` 를 활용해서 success 케이스의 연관값을 가져올 수 있다.

실패할 경우 catch 혹은 nil, 런타임에러 발생할 수 있다.

- 결과를 map(), flatMap() 등의 고차함수로 만들수도 있다.

`**Result` 가 해결하고자 하는 문제**

기존에는 func 정의 시 throws 키워드를 이용해 에러가 발생할 수 있음을 미리 알려줌. 이를 do-catch나 try로 받아서 처리했음. 

- throws 키워드는 단순히 에러가 발생할 수 있음을 알려줄 뿐, 어떤 유형의 에러가 발생하는지는 알 수 없음.

따라서 메소드를 호출할 때 어떤 타입의 에러가 발생하는지 알 수 없고, catch에서 error를 받아 처리하고 싶어도 Error 프로토콜 타입으로 들어오기 때문에 형변환이 필수적이었음.

- Result type을 function의 리턴타입으로 사용하게 되면 정확히 어떤 유형의 에러가 들어오는지 알 수 있고 형변환도 필요하지 않음. 또한 success, failure 두가지 케이스로 리턴하기 때문에 코드 가독성도 좋음.

- 함수(또는 메소드) 시그니처에 있는 throws만 보면 함수의 내부를 확인하지 않고서는 **정확히 어떤 타입의 에러인지는 알 수 없음**.

- catch에서 `error`를 받으면 실제 에러가 아닌 **Error 프로토콜 형식으로 전달됨** → error가 가지고 있는 case를 이용해 **처리를 하고 싶다면 error를 타입캐스팅**해야함. 

분명 catch에서 type(of: error) 를 찍어보면 내가 커스텀한 그 에러 타입이거든? 근데 switch문에서 쓰려고 하면 ‘cannot convert value of type ‘Error’  to ~~’ 라고 나옴. 즉, error의 타입이 “Error”이기때문에 값 convert 못한다는 내용인데.

error를 사용하려면 타입캐스팅이 필요함.

~~- 새로운 에러 형식이 enum에 추가되어도 컴파일러는 인지할 수 없음(따라서 런타임 에러의 위험성이 생김. 왜냐하면 enum을 swtich에서 사용할 때 모든 케이스에 대한 처리가 되야하기 때문에)?~~