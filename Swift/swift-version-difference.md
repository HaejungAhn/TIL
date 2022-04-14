2022-03-02

---

[https://jusung.github.io/ABI-Stability/](https://jusung.github.io/ABI-Stability/)

[https://zeddios.tistory.com/680](https://zeddios.tistory.com/680)

[ABI Stability and More](https://www.swift.org/blog/abi-stability-and-more/)

**궁금한 점**

바이너리는 무엇이고, 컴파일러는 무엇인지? / ABI는 무엇이고 어떤 역할을 하는지? / Swift의 버전과 ABI는 무슨 상관이 있는지? Swift compiler란 무엇인지?

> *— [[Swift official blog, 02.2019] ABI Stability and More](https://www.swift.org/blog/abi-stability-and-more/)*
Swift 5 provides binary compatibility for apps: a guarantee that going forward, an app built with one version of the Swift compiler will be able to talk to a library built with another version. This applies even when using the compatibility mode with older language versions (`-swift-version 4.2`).
> 

### 바이너리는 무엇인가

*— 👀 [Xcode help document](https://help.apple.com/xcode/mac/current/#/dev7af8b1d18)*

app binary : 컴퓨터가 앱을 실행하기 위한 기계어 코드(machine code)가 포함된 파일이다.

### 컴파일러는 무엇인가

*— 👀 [Xcode Build System](https://www.vadimbulavin.com/xcode-build-system/)*

모든 Swift 프로그램은 실제 디바이스에서 실행되기 전에 몇번의 변형과정을 거치게 된다. 그리고 이 과정은 보통 Xcode Build System이 처리한다. 

컴퓨터 시스템은 하드웨어와 소프트웨어로 구성되어 있다. 하드웨어는 소프트웨어에 의해 제어된다. 하드웨어는 우리가 작성한, 사람에게 친화적인 코드를 이해하지 못한다. 하드웨어가 이해하는 것은 0과 1의 숫자뿐이다. 그래서 우리가 작성한 코드를 0과 1로 바꾸는 과정이 필요한데 이걸 language processing system이 해준다. Xcode build system도 language processing system이다. 

5개의 파트로 이루어져있는데 이 안에 컴파일러가 있다.

Preprocessor | Compiler | Assembler | Linker | Loader

![https://www.vadimbulavin.com/assets/images/posts/xcode-build-system/language-processing-system.svg](https://www.vadimbulavin.com/assets/images/posts/xcode-build-system/language-processing-system.svg)

preprocessing은 compiler에서 처리될 수 있도록 코드를 적절히 변형하는 단계다. (Swift는 preprocessor가 없긴 하지만)

*컴파일 - 개발자가 작성한 소스코드를 바이너리 코드로 변환하는 과정*

**Compiler(명령어 번역 프로그램)는** 한 언어의 소스 프로그램을 다른 언어로 만들어진 의미론적으로 동등한 타겟 프로그램으로 매핑하는 프로그램이다. 즉, **Swift나 Objective-C, C/C++ 코드를 machine code로 변환하는데 기존 코드의 의미를 잃지 않으면서 변환한다**는 것이다. Xcode는 두개의 컴파일러를 사용한다. Swift에는 `swfitc`, c계열 언어에는 `clang`을 이용한다. 컴파일러는 2부분으로 나뉘는데 `front end` 파트는 의미론이나 타입 정보 없이 소스프로그램을 조각으로 나누고 문법적인 구조를 강제한다.(컴파일  타임에 문법구조가 맞는지를 체킹한다는 뜻인 것 같다.) 그리고 나서 **컴파일러는 이 구조를 사용해 소스 프로그램의 중간 표현(intermediate representation)을 생산**해낸다. 또한 소스프로그램에 대한 정보를 모으는 symbol table을 생성하고 관리한다. symbol table에는 변수나 함수, 클래스 등의 이름이 저장되어 특정 데이터에 매핑된다. Swift 컴파일러에서 intermediate representation 이름은 SIL(Swift Intermediate Language)이다. SIL을 이용해 다이렉트로 머신코드를 만들수 없기 때문에 LLVM intermediate Representation(bitcode?)으로 한번더 변환이 이뤄진다.
`back end` 단계에서 **intermediate representation은 어셈블리 코드로 변형**된다.

어셈블리어는 기계어와 일대일 대응이 되는 컴퓨터 프로그래밍 언어임! ( 어떤 언어이던, 어떤 버전이던 상관없이 동일 )

Assembler는 사람이 읽을 수 있는 어셈블리 코드를 재배치가능한 머신코드로 번역한다. 어셈블러는 Mach-O 파일을 생성하는데 이 파일은 코드 및 데이터의 모음이다.  머신코드는 CPU에 의해 바로 실행될 수 있는 instruction 모음이다. Mach-O파일은 iOS, macOS 운영체제를 위한 특별한 파일 포맷인데, object file, 실행파일, 라이브러리에 사용된다. 해당 파일은 ARM 프로세서나 Intel 프로세서에서 실행되어질 의미있는 chunk로 묶인 바이트스트림이다. 

Linker는 iOS, macOS 시스템에서 실행될 수 있는 하나의 Mach-O 실행파일을 만들기 위해 다양한 object 파일과 라이브러리를 병합하는 프로그램이다. Linker의 input 타입은 두종류로 assembler단계에서 나온 object file과  라이브러리다(.dylib, .tbd, .a)

Linker의 결과도 object file이라 헷갈릴수 있겠는데, Assembler의 결과로 나온 object file은 아직 완성단계가 아니다. 몇몇 object file은 다른 object file이나 라이브러리 참조가 누락되어 있는 상태다. linker는 딱풀과 같은 역할을 한다.

Loader는 OS가 처리하는 부분으로 프로그램을 메모리에 가져가 실행시키는 역할이다.

### ABI란 무엇인가

*— 👀*  [라이브러리, API, ABI 뜻 비교 정리](https://eine.tistory.com/entry/%EB%9D%BC%EC%9D%B4%EB%B8%8C%EB%9F%AC%EB%A6%AC-API-ABI-%EB%9C%BB-%EB%B9%84%EA%B5%90-%EC%A0%95%EB%A6%AC)

library : 도서관, (개인이 가진 책, 시디 등의) 장서[수집품]들

라이브러리는 어떤 기능 수행과 관련된 코드, 문서 등을 하나로 묶어 공개한 것이다. 

Framework: 뼈대, 구조, 틀

어떤 프로그램을 쉽게 만들기 위해 미리 구조를 만들어둔 것. 프레임워크에서 제공한 대로만 하면 어떤 프로그램을 쉽게 만들 수 있다. 프레임워크 안에는 여러 라이브러리가 들어간다.

**호환된다 = 같은 입력값을 넣었을 때 같은 결과값이 나온다.**

API; Application Programming Interface의 약자. 응용프로그램을 만들때(Programming) 사용하는 인터페이스.

인터페이스는 서로 다른 두 물체를 연결해주는 매개체이다. 

프로그램을 만들 때 처음부터 혼자 다 만들 수도 있지만 다른 팀과 협업을 하거나 외부 라이브러리를 결합해 만드는 경우도 있다. 이때 결합이 되도록 도와주는게 API다. ( 즉, 활용할 때 필요한 것이 API )

예를 들어 어떤 라이브러리 안에 있는 메소드를 사용하는 경우 메소드에 필요한 매개변수나 리턴타입, 메소드 명 등이 API가 되는 것이다. 통신의 경우 통신할 URL, 매개변수, 리턴되는 변수명이나 타입 등이 API가 된다. 

ABI; Application Binary Interface의 약자. 응용프로그램의 바이너리(0과 1로 이루어진 수준) 간 통신할 때 사용하는 인터페이스

API는 소스코드 상 호환이 되는 것이고, ABI는 바이너리 상 호환이 되는 것.

CPU가 실행할 수 있는 기계어 명령어 세트를 ISA(Instruction Set Architecture)라고 부르는데 ABI와 ISA는 밀접한 연관이 있다.

같은 바이너리 값을 입력했을 때 같은 결과가 나온다면 바이너리 수준에서 호환이 된다.

ABI는 응용프로그램과 OS사이 통신을 할 때 필요한 것, ISA는 OS와 하드웨어 사이 통신할 때 필요한 것

*— 👀 [Swift ABI Stability Manifesto](https://github.com/apple/swift/blob/main/docs/ABIStabilityManifesto.md)*

Swift의 가장 주요한 목표는 향후 출시될 Swift 버전과의 “compatibility(호환성)” 이다. 이를 위한 목표는 아래 두가지다.

1. Source Compatibility 

새로운 컴파일러가 이전 버전 swift로 작성된 코드도 컴파일 할 수 있다는 의미. 이는 개발자들이 새로운 Swift 버전으로 마이그레이션 하는 작업을 줄이기 위한 것이다. ( 마이그레이션이 필요한 이유는 새로운 것이 추가되어 오류가 발생하거나 기존 것이 없어져 오류가 발생할 수 있기 때문 ) 이게 없다면 프로젝트는 모든 코드가 동일한 Swift로 작성되야 하는 version lock 문제에 직면하게 될 거임.

2. Binary framework & runtime compatibility

**바이너리 프레임워크와 런타임에 대한 호환성은 여러 Swift 버전에서 사용가능한 바이너리 형태의 프레임워크가 배포될 수 있도록 한다.**

바이너리 프레임워크는 Swift module file(프레임워크 API의 소스레벨 정보를 전달하는), shared library(런타임시 로드되는 컴파일된 implementation을 제공하는)를 포함한다. 따라서 두개의 필수적인 목표가 있다.

- Module format stability : 모듈 파일(프레임워크의 public 인터페이스에 대한 컴파일러의 representation)을 안정화시킴. 모듈파일은 프레임워크를 사용하여 클라이언트 코드를 컴파일 할 때 타입체킹이나 코드 생성과 같은 작업을 위해 컴파일러에서 사용한다.
- ABI stability : 어플리케이션과 다른 Swift 버전으로 컴파일된 라이브러리 간 바이너리 호환이 가능하도록 만든다.

**What Is ABI?**

**런타임 시 Swift 프로그램 바이너리는 다른 라이브러리나 컴포넌트와 ABI를 통해 상호작용한다.** ABI는 어플리케이션 바이너리 인터페이스 또는 독립적으로 컴파일된 바이너리 개체가 함께 연결되고 실행되기 위해 반드시 따라야 하는 specification이다.

이 바이너리 개체는 low-level 세부 정보에 반드시 동의해야한다 : 함수를 어떻게 호출할지, 메모리에서 데이터는 어떻게 표현할지, 메타데이터는 어디에 있는지 그리고 어떻게 접근하는지에 대한 정보

ABI는 아키텍처와 OS 모두에 영향을 받는 low-level concern이므로 플랫폼별로 제각기 존재한다. 대부분의 플랫폼 벤더들은 “standard ABI”를 정의해두고 있는데 이는 C code에 사용되며 C기반 언어 위에서 만들어졌다. 그러나 Swift는 C와는 매우 다른 언어이며 고유의 per-platform(플랫폼별) ABI를 가지고 있다.

각 플랫폼별 ABI는 [여기](https://github.com/apple/swift/blob/main/docs/ABIStabilityManifesto.md#platform-abis)를 참고

**What is ABI Stability?**

ABI stability는 향후 만들어질 컴파일러 버전이 안정적인 ABI를 준수하는 바이너리를 생성할 수 있도록 ABI를 잠그는 것을 의미한다.(즉, 향후 수정되지 않음을 보장하는 것)

**What Does ABI Stability Enable?**

ABI stability는 OS 벤더가 이전 혹은 최신 Swift로 만들어진 어플리케이션과 호환 가능한 Swift standard library와 런타임을 포함할 수 있게 한다. 이렇게 하면 앱이 해당 플랫폼에서 Swift standard library와 런타임에 대한 자체 복사본을 배포할 필요가 없게 만든다.

**Components of the Swift ABI**

- Types

구조체나 클래스, 열거형과 같은 타입들은 해당 타입의 인스턴스를 위해 in-memory layout이 있어야 한다. 바이너리 개체가 상호운용되려면 동일한 layout convention을 공유하고 있어야 한다.

- Type metadata

Type metadat는 Swift 프로그램, Swift 런타임, 디버거나 시각화 도구 등에서 광범위하게 사용된다. 이 메타데이터 또한 메모리 레이아웃이 정의도어 있어야 한다. 또는 타입의 메타데이터 정보를 가져오기 위해 정의된 API 세트를 가지고 있어야 한다.

- Name mangling(이름을 꼬아서 만드는것)

라이브러리 안에 있는 exported, external 심볼들은 고유한 이름이 필요하다. 하지만 전역적으로 봤을 때 이름이 반드시 unique 하지는 않을 수 있다. 따라서 unique한 이름을 만들때 name mangling이란 기술을 사용한다.

- Calling Convention

함수들은 서로를 호출할 때 어떻게 호출하는지(호출스택 레이아웃, 보존되는 레지스터 및 소유권 규칙 등이 포함된)를 알아야 한다.

- Runtime

Swift는 동적 캐스팅, RC(Reference Counting), reflection 등을 처리하는 런타임 라이브러리와 함께 제공된다. 컴파일된 스위프트 프로그램은 이 런타임에 대한 외부 호출을 수행한다. 그러므로 **Swift runtime API는 Swift ABI**라고 할 수 있다. (Compiled Swift programs make external calls out to this runtime. Thus, Swift runtime API is Swift ABI.)

[Swift Runtime?](https://github.com/apple/swift/blob/main/docs/Runtime.md)

무슨 역할을 하는가 

- 할당, reference countin을 포함한 메모리 관리
- 동적 캐스팅, 제네릭 타입 인스턴스화 등 런타임 시 동적으로 타입을 관리하는 시스템?

- Standard Library

Swift는 공통 타입, 구조 및 작업을 정의하는 standard library와 함께 제공된다. 제공되는 standard library가 다른 버전의 Swift로 만들어진 앱과 함께 작동하려면 안정적인 API를 노출해야한다. 그러므로 **Swift Standard Library API는 Swift ABI다**.

> — *출처: [https://zeddios.tistory.com/654](https://zeddios.tistory.com/654) [ZeddiOS]*
> 
> 
> 잠깐 API이야기로 가봅시다.
> 
> API에 v1과 v2가 있다고 생각해볼게요. v2에는 인터페이스에 대한 몇가지 중요한 변경사항이 있어요.
> 
> 예를들어, 뭐 v1때와 다르게 A라는 걸 만들 때 인수를 하나 더 넘겨야 한다는 식으로요.
> 
> 여기서 중요한 점은, API v2는 v1과 호환되지 않으므로, v2를 호출 할 때 v1에 대해 작성된 클라이언트가 고장(break down)나게 됩니다.
> 
> 여기까진 이해가 가시죠? 지금 든 예랑 똑같이!!
> 
> 우리가 Swift2.0으로 binary를 만들고, Swift3.0에 연결하려고 하면 실패하게 됩니다. 왜냐? 그 사이에 많은 변경사항이 있었으니까요.
> 
> 우리가 v1클라이언트에서 v2를 호출하려는 격입니다.
> 
> 이게 바로 ABI가 호환이 안되어서 나는 문제입니다.
> 
> 서로 다른 컴파일러 버전으로 컴파일 된 바이너리 코드간의 통신을 가능하게 하려면, ABI가 잘 정의되고 안정적(stable)이어야 합니다.
> 
> 그것을 **ABI stability**이라고 합니다.
> 

🤔  ABI란?

API와 비슷함. API는 Application Programming Interface의 약자임. 

ABI는 Application Binary Interface의 약자로 바이너리에서 사용하는 인터페이스다~ 라는 의미.

- 바이너리?
    
    Binary는 0과 1을 사용해 수를 나타내는 이진법을 의미함. 일반적으로 **컴파일 후 최종 결과물로 나온 실행파일**을 바이너리 코드라고 하며 컴퓨터의 CPU에 의해 처리됨.
    
- 컴파일 시 언어(Swift)의 역할?
    
    [https://velog.io/@minjunkim-dev/swift의-빌드-과정은-과연-어떻게-되는걸까](https://velog.io/@minjunkim-dev/swift%EC%9D%98-%EB%B9%8C%EB%93%9C-%EA%B3%BC%EC%A0%95%EC%9D%80-%EA%B3%BC%EC%97%B0-%EC%96%B4%EB%96%BB%EA%B2%8C-%EB%90%98%EB%8A%94%EA%B1%B8%EA%B9%8C)
    

런타임 시 Swift 프로그램 바이너리는 ABI를 통해 운영체제와 앱, 앱과 라이브러리간 상호작용을 하게 됨.

![https://jusung.github.io/images/2018/abi%20stability1.png](https://jusung.github.io/images/2018/abi%20stability1.png)

ABI가 안정화되었다 → 앞으로 ABI가 변하지 않을 것이라는 걸 보장한다. ( 인터페이스가 유지된다는 의미 )

**Swift의 버전과 ABI는 무슨 상관이 있는지?**

*“Swift ABI 안에 Swift standard Library와 swift runtime이 포함된다.”*

*— 👀 [https://jusung.github.io/ABI-Stability/](https://jusung.github.io/ABI-Stability/)*

# **이 점**

ABI가 안정화 되면서 얻게 되는 이점은 무엇일까요?

- **앱 번들 크기 축소** : 이제 Swift 표준 라이브러리를 앱마다 포함시킬 필요가 없기 때문에 이 라이브러리 크기 만큼 앱 용량을 줄일 수 있습니다.
- **소스 호환성** : Swift가 새로 나올때마다 마이그레이션 하느라 고생 하셨죠? 이제 구버전으로 작성된 Swift 코드를 새 컴파일러에서 컴파일할 수 있습니다. 즉, 새버전으로의 마이그레이션 고통을 줄여줍니다. 😆
- **Swift 언어의 변경 축소** : ABI를 유지하기 위해서는 Swift 언어에서 함수의 인터페이스를 바꾸면 안됩니다. 리턴 타입, 매개변수, 숫자, 타입 등 자료형 정의나 구조체 정의, 상수 정의 등도 바뀌면 안됩니다. 그렇기 때문에 앞으로 Swift 언어가 보다 적게 변경될 것을 기대할 수 있습니다.
- **바이너리 프레임워크 & 런타임 호환성** : 바이너리로 된 프레임워크를 다양한 Swift 버전으로 배포하는 것이 가능해 집니다.

# **제 한**

앞으로 Swift는 ABI stability를 보장하기 위해 Swift 런타임이나 표준 라이브러리에 추가가 필요한 피처는 제안이 제한됩니다. 이 조건에 해당하는 것은 다음과 같습니다.

- 표준 라이브러리에 새로운 타입, 프로토콜, 프로토콜 순응(Conformance), 함수, 매소드, 프로퍼티 등을 추가하는 것
- Swift 타입시스템을 변경하는 것. 예를 들면, 새로운 타입을 추가하거나 기존 타입에 함수 에트리뷰트 같은 새로운 변경자를 추가하는 것 그리고 새로운 브릿징, 서브 타이핑, 동적 케스팅 관계를 추가하는 것