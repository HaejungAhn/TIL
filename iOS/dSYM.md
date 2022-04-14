2022-03-02

---
## dSYM

> *—[👀 Apple document: building your app to include debugging infromation](https://developer.apple.com/documentation/xcode/building-your-app-to-include-debugging-information)*
Xcode가 **소스코드를 머신코드로 컴파일할 때 앱 안에 있는 symbol 리스트를 생성**한다. **symbol에는 클래스이름, 전역변수, 메소드와 함수 이름 등이 포함**된다. “디버그 빌드”는 컴파일된 바이너리 파일 안에 디버그 심볼을 포함시킨다. 이와 달리 “릴리즈 빌드"는 배포할 앱의 사이즈를 줄이기 위해 `dSYM`(debug symbol file)파일에 디버그 심볼을 위치시킨다.
이 symbol은 포함하고 있는 정보들이 정의된 파일 및 줄번호에 해당한다. 이런 연관관계가 debug symbol을 만들고, Xcode에서 디버거를 사용하거나 crash report에서 줄번호를 포함하게끔 만든다.
> 

*— 👀 [[medium] How crashlytics works in iOS? An Overview of dSYM](https://medium.com/naukri-engineering/overview-of-dsym-crashlytics-in-ios-dfd72eae8b58)*

앱이 크래시되면 OS는 앱이 크래시될 때 어떤 작업을 했는지에 대한 진단 정보를 모은다. 진단 정보 중 가장 중요한 파트가 thread backtraces인데 16진수 주소로 표시된다. (unsymbolicated crash report)

**SYMBOLICATION이란?**

앱 크래시의 근본 원인을 파악하기 위해 thread backtrace를 해석해야함! 사람이 읽을 수 있는 언어로. 이 과정을 symbolication이라고 한다.

**Role of dSYM(Debug SYMbol)**

dSYM이 없으면 크래시 리포트는 단지 객체와 메소드의 메모리 주소만을 보여줄 뿐이다. 바이너리 파일을 symbolication 하지 않으면 크래시 리포트를 읽을 수 없다.

dSYM파일은 디버그 심볼을 저장하는데 stack-trace를 읽을 수 있는 형식으로 디코딩하기 위한 매핑정보가 포함되어 있다.

dSYM 파일의 목적은 충돌 로그의 심볼을 특정 메소드의 이름으로 교체하여 읽기 쉽고 디버깅에 도움이 되도록 하는 것이다.

**Key points regarding dSYM**

dSYM파일은 Debugging Information Format이 “`DWARF with dSYM file`”로 설정된 상태에서 앱의 소스코드가 컴파일될 때 만들어진다.

dSYM 파일은 코드가 변경된 앱을 컴파일할 때마다 변경된다.

dSYM 파일은 특정 Xcode build와 연결되어 있는 UUID를 가지고 있다. 따라서 크래시가 발생한 바이너리의 UUID가 Symbolication에 사용되는 dSYM의 UUID와 일치하는 경우에만 Symbolication이 작동한다.

**How to enable dSYM?**

1. Xcode → Project Navigator 

2. 타겟 리스트에서 앱이 빌드되는 타겟을 선택한다.

3. build setting 탭을 선택한다.

4. Build Option 설정에서 “Debugging Information Format”을 `DWARF with dSYM file` 로 설정한다.

근데 이렇게 항상 설정해두면 컴파일 시간이 길어지므로 필요하지 않을때는 DWARF로 설정하는게 좋다.