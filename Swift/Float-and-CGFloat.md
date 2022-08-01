## Float과 CGFloat
- Shadow와 관련된 속성들을 살펴보던 중 문득 들었던 의문이 있다.
- 🤔  Float과 CGFloat의 차이는 뭘까? Float이 있는데 CGFloat을 만든 이유는 뭘까?

#### <b>Float</b>
- `@frozen struct Float`
- A single-precision, floating-point value type.
- *single-precision(단정밀도), floating-point(부동소수점)*란?
    - IEEE가 제정한 표준에 따라 실수를 표현한다. 이 표준에 따른 표현법을 부동 소수점(floating-point)라고 부름.
    - 여기서 "부"는 둥둥 떠다닌다는 의미다.(ex. 부표)


#### <b>CGFloat</b>
- `@frozen struct CGFloat`
- 이 타입의 **사이즈와 정밀도는 CPU 아키텍처에 따라 다르다.** 64-bit CPU 위에서 빌드했다면 CGFloat의 타입은 64bit가 되고 이는 `Double` 타입에 해당한다.(IEEE double-presicion floating point type) 32-bit CPU 위에서 빌드했다면 CGFloat의 타입은 32bit가 되고 이는 `Float` 타입에 해당한다.(IEEE single-precision floating point type)


- 단정도와 배정도의 의미
- CPU 아키텍처에 따라 변한다면, 굳이 Float이나 Double을 만들 필요가 있었을까? 왜 만들었을까?
    - 문서를 보니 Float은 iOS 8.0 이상부터, CGFloat은 iOS 2.0부터 사용할 수 있음. 이것으로 보아 Float이 더 나중에 만들어진 것 같은데.. 왜 만들어졌을까? 라는 거지!
- 
- 32bit와 64bit의 의미
    - 컴퓨터는 데이터 단위로 비트를 사용한다. 데이터를 한번에 몇개 보낼 수 있는지에 대한 지표가 32bit와 64bit임. 32bit는 32차선 도로, 64bit는 64차선 도로라고 생각하면 됨. bit가 클 수록 한번에 보낼 수 있는 데이터의 양이 많아진다.
    - 한번에 보낼 수 있는 데이터의 양이 32bit면 32비트 컴퓨터, 64bit면 64비트 컴퓨터가 된다. 또한 메모리 주소의 표현도 32bit, 64bit로 표현됨.

