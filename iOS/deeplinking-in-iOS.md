## 유니버셜 링크
이번 주는 AppsFlyer를 활용한 딥링크 기능을 추가하는 작업을 진행하고 있다. AppsFlyer는 무엇인지, 딥링크란 또 무엇인지 알아보고 iOS에서 어떻게 딥링크 기능을 구현할 수 있는지에 대해 공부할 수 있는 좋은 기회였다.

### AppsFlyer?
- 간략히 말하자면 AppsFlyer에서 제공하는 SDK를 활용해 마케팅 데이터를 측정하고 분석할 수 있는 서드파티 툴이다.
- 여기서 제공하는 OneLink를 활용, 디바이스에 앱을 설치하지 않은 사용자의 경우 앱스토어로 이동시키고 앱을 설치한 사용자는 의도된 앱 화면으로 이동하도록 만드는 것이 이번 작업의 목표다.
- OneLink란 어트리뷰션(앱 설치까지 어떤 매체를 거쳤고 그 매체는 얼마만큼의 기여를 했는지), `redirection`, `deep-linking` 기능을 가진 링크로, 단 하나의 링크로 고객을 모바일 앱으로 전환시키는 기술이라고 한다.

### 딥링킹?
- 딥링킹은 단순히 앱을 실행시키는 것이 아니라 URI(Uniform Resource Identifier)를 사용하여 유저를 <u>모바일 앱 내의 특정 위치로 연결시키는 것</u>을 말한다.
- Deferred 딥링킹이라는 것도 있다. Deferred란 "지연된" 이라는 의미를 가지고 있는데 Deferred 딥링킹을 사용하면 디바이스에 앱이 설치되어 있지 않다 하더라도 컨텐츠에 딥링킹할 수 있다.(앱 스토어에서 앱 설치 후 컨텐츠로 딥링킹 가능)
- 카카오톡 앱 상단의 광고를 터치해보면 앱 설치가 이미 된 경우 해당 앱의 특정 페이지로, 앱 미설치 시 `*.onelink.me`를 통해 앱스토어로 이동하게 되는걸 볼 수 있는데 이게 딥링킹이다.

### iOS에서 딥링킹 구현하기
여기서는 웹에서 링크를 눌렀을 때 앱의 특정 화면으로 이동하는 시나리오만 고려하겠음.   
iOS에서 딥링킹을 구현하는 방법은 두가지가 있다.   
#### URI Scheme
- ✋ 들어가기에 앞서 URI와 URL의 차이를 좀 찾아봤다. 어떤 문서에서는 URI라 하고 어떤 문서에서는 URL이라 해서 궁금했음.
    - URI(Uniform Resource Identifier) : 자원의 위치 + 자원에 대한 고유 식별자
    - URL(Uniform Resource Locator) : 자원이 실제로 존재하는 "위치"를 가리킴
    - URI가 URL보다 조금 더 큰 범위라고 할 수 있다. 
        - 예를 들어 https://example.com 또는 https://example.com/skin 등은 특정 서버의 위치 또는 특정서버에 있는 특정 자원의 위치를 가리키는 locator이자 고유 식별자이기 때문에 URI이면서 URL이라고 할 수 있음.
        - https://example.com/user/1 (Path Variable 방식이라고 함)의 경우 /user까지가 특정 자원에 도달하기 위한 URL이지만, 필요로 하는 정보에 도달하기 위해서는 `1`이라는 식별자가 필요하다.(실질적으로 해당 위치에 저런 이름을 가진 파일이 존재하진 않음) 따라서 전체를 봤을 때 URI는 맞지만 URL은 아니다.
        - https://example.com?id=1 (Query Parameter 방식이라고 함)의 경우 example.com까지는 URL이 맞지만, 바로 위 예시와 마찬가지로 특정 정보에 도달하기 위해서는 id가 필요하기 때문에 전체적으로 봤을 때 URI는 맞지만 URL은 아니다.
        - 그렇다면 https://example.com/logo.gif는 URL일까 URI일까? 자원의 위치를 가리키기도 하면서 식별자이기도 하기 때문에 URL이면서 URI이다.
- 앱마다 등록할 수 있는 값. "특정 스킴값을 호출하면 특정 앱이 오픈된다"라는 약속을 실행한다.
- 앱 내에서의 특정 페이지는 'path'로 구분한다. 예를 들어 트위터 앱의 회원가입 화면을 오픈하고자 한다면 `twitter://signup`이라는 값을 사용하면 됨.
- Scheme://Path의 형태이며 Scheme는 앱을 특정하기 위해, Path는 앱 내 화면을 특정하기 위해 사용한다.
- iOS의 경우 Target > Info > URL Types에서 스킴값을 추가할 수 있다. (더 자세한 방법은 (여기)[https://developer.apple.com/documentation/xcode/defining-a-custom-url-scheme-for-your-app]를 참고)
- 커스텀 스킴에 포함된 URL을 다른 앱이 열면, 시스템은 앱을 실행시키고 필요한 경우 이를 포그라운드로 가져온다. 이후 AppDelegate의 `application(_:open:options)`가 호출된다.
- SceneDelegate를 사용하는 경우, 앱이 NotRunning 상태라면 시스템은 앱 런치 후 URL을 `scene(_:willConnectTo:options:)` 메소드에 전달한다. 앱이 실행중이거나 메모리에서 suspend된 상태라면 `scene(_openURLContexts:)` 메소드를 호출한다.
- URI Scheme 방식의 한계
    - 앱이 많아지게 되면서 **스킴값이 중복되는 문제**가 발생한다. 앱 개발자는 자신들의 스킴값이 고유한 값인지 알 수가 없었고, 사용을 막도록 할 방법도 없었음.
    - 그래서!! Universal Link가 만들어지게 됨.

#### Universal Link
- 사용자가 유니버셜 링크를 탭하거나 클릭하면, 시스템은 Safari나 웹사이트를 거치지 않고 링크를 앱으로 직접 리디렉션 시킨다. 
- 게다가 유니버셜 링크는 일반적인 HTTP 또는 HTTPS 링크이기 때문에 하나의 URL이 웹사이트와 앱에서 동작할 수 있다.
    - 만약 유저가 앱을 설치하지 않은 상태라면, 시스템은 Safari에서 URL을 열고 웹사이트가 링크를 처리할 수 있도록 한다.
    - 만약 유저가 앱을 설치했다면, 시스템은 웹서버에 저장된 파일을 체크하여 앱이 대신 URL을 열 수 있도록 허용하는지 확인한다.


---
#### 📚 참고자료
- [Mobile Deep Linking, Wiki](https://en.wikipedia.org/wiki/Mobile_deep_linking)
- [URL과 URI의 차이, medium.com](https://medium.com/@js230023/url-%EA%B3%BC-uri%EC%9D%98-%EC%B0%A8%EC%9D%B4-154d70814d2a)
- [딥링크(Deeplink) : URI스킴, 유니버셜 링크, 앱링크 구분과 이해](https://help.dfinery.io/hc/ko/articles/360039757433-%EB%94%A5%EB%A7%81%ED%81%AC-Deeplink-URI%EC%8A%A4%ED%82%B4-%EC%9C%A0%EB%8B%88%EB%B2%84%EC%85%9C-%EB%A7%81%ED%81%AC-%EC%95%B1%EB%A7%81%ED%81%AC-%EA%B5%AC%EB%B6%84%EA%B3%BC-%EC%9D%B4%ED%95%B4)
- [Defining a custom URL scheme for your app, Apple Developer Document](https://developer.apple.com/documentation/xcode/defining-a-custom-url-scheme-for-your-app)
- [Allowing Apps and Websites to Link to Your Content, Apple Developer Document](https://developer.apple.com/documentation/xcode/allowing-apps-and-websites-to-link-to-your-content)