2022-03-08

---
### 앱이 실행되는 과정

- 앱이 시작할 때 main.c 에 있는 UIApplicationMain 함수에 의해서 생성되는 객체는 무엇인가?
- UIApplication 객체의 컨트롤러 역할은 어디에 구현해야 하는가? 이건 질문을 이해 못함ㅠ
- @Main에 대해서 설명하시오.
- UIApplication 객체의 역할은 무엇인지 설명하시오.

### 앱의 상태

- App의 Not running, Inactive, Active, Background, Suspended에 대해 설명하시오.
- 앱이 In-Active 상태가 되는 시나리오를 설명하시오.
- 앱이 foreground에 있을 때와 background에 있을 때 어떤 제약사항이 있나요?
- 상태 변화에 따라 다른 동작을 처리하기 위한 앱델리게이트 메서드들을 설명하시오.

---

공부해야할 것

~~앱이 실행되는 과정은 어떻게 되는가?~~ 

~~UIApplication은 무엇이며 어떤 역할을 하는가?~~

앱의 각 상태는 언제 발생하고 어떤 의미를 담고 있고 어떤 동작까지 할 수 있는가?

~~AppDelegate와 SceneDelegate는 무엇인가?~~

**MultiTasking과 Multi Window : 단어에 익숙해져야함.**

# iOS 앱의 실행 과정

![https://docs-assets.developer.apple.com/published/dd1b7996c3/rendered2x-1635759039.png](https://docs-assets.developer.apple.com/published/dd1b7996c3/rendered2x-1635759039.png)

1. 사용자나 시스템이 앱을 런치하거나 또는 시스템이 앱을 prewarms 한다.
    
    🤔 [prewarms](https://developer.apple.com/documentation/uikit/app_and_environment/responding_to_the_launch_of_your_app/about_the_app_launch_sequence#3894431) 
    
    iOS 15 이상부터 시스템은 디바이스 컨디션에 따라 앱을 prewarm 할 수 있는데 이는 앱을 사용하기 전까지 사용자가 기다려야하는 시간을 제거하기 위해 nonrunning 앱 프로세스를 런치하는 것이다.
    
2. 시스템은 Xcode가 제공한 main() 함수를 실행한다.
3. main() 함수는 UIApplicationMain() 함수를 호출하는데 해당 함수는 UIApplication과 AppDelegate 인스턴스를 만들고 앱의 Run Loop를 포함한 기본 event cycle을 설정한다.
4. UIApplicationMain() 함수에서 앱의 Info.plist 파일에 설정된 기본 스토리보드를 로드한다.(없으면 건너뜀)
    
    아래 이미지 가장 아래쪽에 “Main”이 지정된 것을 볼 수 있음.
    
    ![스크린샷 2022-03-08 오후 12.37.47.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/a24dcdc7-d734-49d3-9b75-d98fe86341d3/스크린샷_2022-03-08_오후_12.37.47.png)
    
5. UIKit은 AppDelegate의 application(_:`will`FinishLaunchingWithOptions:)를 호출
    
    (이때 앱이 어떤 이유로 실행됐는지를 옵션을 통해 알 수 있다. 이에 대한 적절한 처리 가능)
    
6. UIKit은 state restoration(상태복원)을 수행한다.
    
    🤔  state restoration
    
    *— 🔗 [[tistory, littleshark] 앱 상태 복원(UIViewControllerRestoration)](https://littleshark.tistory.com/63)*
    
    앱이 항상 실행되고 있는 것처럼 보이도록 UI를 유지하는 것이 좋다. iOS 디바이스에서는 빈번하게 시스템이 앱을 중지시킬 수 있다. 하지만 유저는 앱을 사용하면서 상태가 달라지는 것을 기대하지 않는다. 그래서 앱이 중지(terminated)된 후에 재실행(lauches again)될 때 상태를 유지할 수 있도록 하는데 이게 **state restoration**이다. 
    
    — 읽어보기 [About the UI Restoration Process](https://developer.apple.com/documentation/uikit/view_controllers/preserving_your_app_s_ui_across_launches/about_the_ui_restoration_process)
    
7. UIKit은 AppDelegate의 application(_:`did`FinishLaunchingWithOptions:)를 호출
8. 위 과정이 끝나고 나면 시스템은 앱/scene delegate를 사용해 UI를 디스플레이하고 라이프사이클을 관리한다.

# Entry Point 지정

— 🔗 [[tistory, barosalki] [iOS & Swift 5.3+] @main : type기반의 프로그램 진입점](https://barosalki.tistory.com/entry/iOS-Swift-53-main-type%EA%B8%B0%EB%B0%98%EC%9D%98-%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%A8-%EC%A7%84%EC%9E%85%EC%A0%90)

앱 실행 시 아래 두 속성 중 하나를 선택하면 됨. 둘다 앱의 진입점을 설정할 수 있는 속성이다. 차이가 있다면 첫번째는 class에서만 설정 가능하다는 것, 두번째는 값 타입에도 설정이 가능하고 앱 런치 시 커스텀할 작업이 있을 경우 처리하고, AppDelegate.main()을 반드시 호출해야한다는 점이 있다. Swift 5.3부터 사용 가능함.

🤔    만약 두개 다 지정하게 되면 어떻게 될까? → 아래 에러와 함께 빌드가 안된다.

```swift
1 duplicate symbol for architecture arm64
duplicate symbol '_main' in: ..(생략)..
ld: 1 duplicate symbol for architecture arm64
clang: error: linker command failed with exit code 1 (use -v to see invocation)
```

### `@UIApplicationMain`

iOS 앱의 진입점을 표시해줌. Xcode 12 이전에는 UIApplicationMain이었고 그 이후부터는 @main이라고 함.

프로젝트에는 기본적으로 AppDelegate 클래스에 `@UIApplicationMain` 속성이 지정되며, 해당 클래스는 UIResponder(NSObject를 상속받은 클래스 타입)와 UIApplicationDelegate 프로토콜을 채택함. 그리고 해당 프로토콜의 extension에 타입메소드인 main()이 정의되어 있음.

해당 속성은 class에서만 사용할 수 있다. 값타입에 붙이면 아래 에러 발생함.

*— ⚠️  @UIApplicationMain may only be used on 'class' declarations*

### `@main`

**structure, class, 열거형 선언에 해당 속성을 적용함**으로써 프로그램 실행의 최상위 entry point를 포함하고 있다는 것을 나타낼 수 있다. 해당 타입은 반드시 **`static func main() → Void`**  타입메소드를 추가해야한다.

**커스텀 엔트리 포인트**를 만들기 위해 새롭게 추가된 속성임. (Swift 5.3 이후)

```swift
import Foundation

protocol EntryPointProtocol {
    
}
extension EntryPointProtocol {
    static func main() {
        print("EntryPointProtocol, main()")
    }
}

@main
struct EntryPoint {
    var num: Int = 0
}
extension EntryPoint: EntryPointProtocol {
    static func main() {
        let entryPoint = EntryPoint(num: 10)
        print("EntryPoint struct main is called! number is \(entryPoint.num)")
        AppDelegate.main()
    }
}

// EntryPoint의 main()에 지정된 작업이 모두 끝나고 나서 AppDelegate.main()을
// 반드시 호출해야 앱이 실행된다. 그러지않으면 앱 실행 안됨.
```

# UIApplication이란

— 🔗  [[Apple Document] UIApplication](https://developer.apple.com/documentation/uikit/uiapplication)

**UIApplication이 생성되는 과정**

- UIApplicationMain() 함수에 의해 싱글톤으로 생성되며 “UIApplication.shared”를 통해 접근할 수 있다.

**UIApplication의 역할**

앱으로부터 들어오는 이벤트를 제일 처음 받는 객체로 사용자 이벤트가 들어왔을 때 적절한 target으로 이벤트를 전달해주고, 시스템 이벤트(앱런치, 메모리 부족, 앱 상태변화, 푸시 알림 등)를 받아 app delegate에게 넘겨주는 역할을 한다. 또한 이벤트들이 처리되는 main run loop를 시작시킨다.

- 앱으로 들어오는 **사용자 이벤트의 초기 라우팅을 핸들링**한다. 즉, UIControl 인스턴스와 같은 control 객체로부터 들어온 action message를 적절한 타겟 object로 전달하는 것이다. 

- **UIWindow 객체의 목록을 유지하고 관리**한다.

- 적절하게 반응할 기회를 주기 위해 **AppDelegate에게 중요한 런타임 이벤트를 전달**한다.(앱 런치, 메모리 부족 warning, 앱 종료, 푸시 알림 등)

- 제공하는 API는 다음과 같다.

- 들어오는 터치 이벤트를 일시적으로 묶어놓음(suspend) — `beginIgnoringInteractionEvents()`
- remote notification 등록 — `registerForRemoteNotifications()`
- undo-redo UI 트리거 — `applicationSupportsShakeToEdit`
- 특정 URL 스킴을 처리하기 위해 등록된 installed app이 있는지 확인 — `canOpenURL()`
- 백그라운드에서 작업을 완료할 수 있도록 앱 실행 확장 — `beginBackgroundTask()`
- local notification 스케줄 및 cancel — `scheduleLocalNotification()`, `cancelLocalNotification()`
- remote-control 이벤트 수신 조정 — `beginReceivingRemoteControlEvents()`, `endReceivingRemoteControlEvents()`
- 앱 수준의 상태복원 task 실행 (methods in the [Managing the State Restoration Behavior](https://developer.apple.com/documentation/uikit/uiapplication#1657552) task group)

🤔   UIApplication 클래스 정의 앞에 @MainActor가 붙던데 이건 뭘까?

*— 🔗 [[Hacking With Swift] How to use @MainActor to run code on the main queue](https://www.hackingwithswift.com/quick-start/concurrency/how-to-use-mainactor-to-run-code-on-the-main-queue)*

작업을 수행하기 위해 main queue를 사용하는 global actor. `@MainActor`로 마크된 메소드나 타입은 안전하게 UI를 수정할 수 있다는 말이 된다.

# 앱의 State

*— 🔗 [[Apple Developer] Managing Your App’s Life Cycle](https://developer.apple.com/documentation/uikit/app_and_environment/managing_your_app_s_life_cycle)*

앱의 현재 상태는 앱이 무엇을 할 수 있고 무엇을 할 수 없는지를 결정한다.

- `**Foreground**` 사용자의 attention을 가지고 있고 그렇기 때문에 CPU를 포함한 시스템 리소스에 대한 우선순위를 가진다.
- `**Background**` 가능하면 적은 task를 처리해야하며 가급적이면 아무것도 하지 않는게 좋다. 왜냐하면 offscreen 상태이기 때문이다.(그래서 사용자가 작업이 진행되는지를 인식하기 어렵기 때문이다.)

앱의 상태가 변경되면, UIKit은 적절한 delegate 객체의 메소드를 호출하여 이를 notify 하게된다.

- iOS 13이상 - scene-based app에서 life-cycle event에 반응하기 위해 UISceneDelegate 객체를 사용함.
- iOS 12 및 이전 - UIApplicationDelegate객체를 사용함.

### Respond to Scene-Based Life-Cycle Events

앱이 scene들을 지원한다면, UIKit은 각각의 분리된 라이프 사이클 이벤트를 전달한다. scene을 지원하는 것은 opt-in 특징이기 때문에 iOS 13 이상이라고 해서 반드시 이걸 활성화해야할 필요는 없다. opt-in 설정과 관련해서는 “[Specifyin the scenes Your App Supports](https://developer.apple.com/documentation/uikit/app_and_environment/scenes/specifying_the_scenes_your_app_supports)”를 확인!

scene : 디바이스에서 실행되는 앱의 UI 인스턴스 한개

각 scene들은 자신만의 life cycle을 가지고 있기 때문에, 각각은 서로 다른 상태를 가지고 있을 수 있다. “브라우저의 새 창 열기”를 생각해보면 될듯. scene 하나하나가 새 창임. 

scene-based app은 만들어본적이 없고 현재로서도 당장 필요하지 않기 때문에 전체 article을 읽지는 않음!

### Respond to App-Based Life-Cycle Events

`Not running` `Inactive` `Active` `Background` `Suspend`

iOS 12 이전이거나 13 이후라도 scene을 지원하지 않는다면 UIKit은 모든 라이프사이클 이벤트를 UIApplicationDelegate에 보낸다. app delegate는 분리된 스크린에 디스플레이된 것을 포함해서 모든 window를 관리한다.

앱이 런치되면 화면에 UI가 나타나는지에 따라 inactive 상태 또는 background 상태에 들어가게 된다. foreground로 런칭되면 시스템은 자동으로 active 상태로 앱을 전환한다.

![https://docs-assets.developer.apple.com/published/64a2e0dab8/app-state@2x.png](https://docs-assets.developer.apple.com/published/64a2e0dab8/app-state@2x.png)

UIApplicationDelegate는 아래의 이벤트도 핸들링할 수 있다.

- Memory warnings
- 디바이스를 잠금 또는 잠금해제할 때 데이터를 보호?
- Handoff tasks — application(_:didUpdate:)
- Time changes — applicationSignificantTimeChange(_:)
- Open URLs — application(_:open:options:)

*— launch, activation, deactivation, backgroun 및 ternimation 등 각 상태에 따라 어떤 메소드가 호출되고 어떤 작업을 하면 좋은지를 써놔서 읽어보면 좋을 것 같음. 하지만 지금 필요한 것은 아니라 우선 다른 글 보기로 함.*

*— 🔗 [[medium, @chinthaka01] The execution states of an iOS application](https://medium.com/@chinthaka01/the-execution-states-of-an-ios-application-84e117132e27)*

**`Not running`**

앱이 실행되지 않은 상태 또는 사용자 또는 OS에 의해 종료된 상태임.

`**Inactive**`

포그라운드 상태이지만 어떤 이벤트도 전달받지 못함. 실행중인 작업은 계속 할 수 있음. 

*— 음악 플레이어 앱을 생각해보면 앱이 종료되지만 안으면 inactive에 있던 백그라운드에 있던 계속 음악이 재생됨*

앱의 상태가 아래와 같이 전환될 때 나타난다. **Active에서 어떤 상태가 되거나, 어떤 상태에서 Active가 될 때** 반드시 Inactive를 거치게 됨. 또는 **멀티태스킹 상태**일 경우에도 Inactive가 될 수 있음. 혹은 앱이 실행되는 동안 전화가 오거나, siri가 활성화된 경우에도 Inactive 상태가 될 수 있다.

- 🤔  Multitasking
    
    멀티태스킹은 사람들이 app switcher를 사용하여 한 앱에서 다른 앱으로 빠르게 전환할 수 있도록 한다. picture-in-picture 모드도 multitasking의 한 예이다.
    
- Not Running → Inactive → Active
- Active → Inactive → Background
- Background → Inactive → Active

멀티태스킹 UI에서 앱을 계속 홀딩하고 있으면 OS는 Inactive 상태로 앱을 유지하고 사용자의 다음 행동을 고려하여 다음 상태로 전환한다.

멀티태스킹 UI(iOS앱에서는 multitasking switcher)에서 할 수 있는 작업은 아래와 같다.

- multitasking UI에서 다른 앱을 열면서 앱이 백그라운드로 이동함

- 앱을 바로 백그라운드로 이동시킴

- 파워 버튼을 이용해 스크린이 off되고 앱이 백그라운드로 이동함

- 앱을 스와이프해서 종료

- 앱을 다시 시작함

`**Active**`

iOS application의 실행상태. 앱은 foreground에서 실행되고 UI에도 접근이 가능함. 어떤 이벤트도 수신받을 수 있고 코드 실행도 가능함. 해당 상태에 진입하려면 반드시 Inactive를 거쳐야 한다.

`**Background**`

앱이 백그라운드로 이동하면 Background 상태로 전환된다. 화면에 앱이 보여지는 상태는 아니지만 그럼에도 불구하고 앱은 코드를 실행할 수 있고 UI 이벤트를 제외한 나머지 이벤트를 전달받을 수 있다.

그러나 제한된 시간이 지나면 OS는 자동으로 앱을 Suspend 상태로 전환하고 코드 실행을 중지시킨다. (iOS 12이라면 180초 동안만 백그라운드 상태가 유지되는데 iOS 버전마다 상이하다)

~~원래 백그라운드 상태로 전환되는 목적은 이미 실행중인 task를 쉽게 완료할 수 있도록 제공된 것~~이다. 하지만 [`beginBackgroundTask(expirationHandler:)`](https://developer.apple.com/documentation/uikit/uiapplication/1623051-beginbackgroundtask) 또는 `beginBackgroundTask(withName:expirationHandler:)` 메소드를 이용해 백그라운드에서의 execution time을 늘릴 수 있다.

특별한 작업을 하지 않는다면 바로 suspended로 상태가 전환된다.

`**Suspended**`

앱이 백그라운드이며 메모리에 남아있는 상태이다. 하지만 어떠한 코드도 실행하지 않는 상태임.

메모리에 남아있긴 해도 메모리가 부족하거나 새로운 앱이 실행되야 할 경우 시스템에 의해 종료될 수 있다. suspend 상태로 전환될 때 OS는 어떤 noti도 주지 않음.

— 🔗  아래 이미지 출처는 [여기](https://lxxyeon.tistory.com/90)

![스크린샷 2022-03-08 오후 5.08.10.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/32993a59-1a46-4d2e-b712-f9fbfd211bdc/스크린샷_2022-03-08_오후_5.08.10.png)

### AppDelegate와 SceneDelegate

원래 iOS 12까지만 하더라도 AppDelegate가 앱 프로세스(=UIApplication object, 앱 런치 및 terminate)와 UI(didBecomeActive, willResignActive, willEnterForeground, didEnterBackground)의 라이프 사이클 변화에 대한 이벤트를 처리하고 있었음.(하나의 앱에 하나의 window)

그러나 iOS 13부터는 App Delegate가 앱 프로세스와 “Session”의 라이프사이클을 관리하고 SceneDelegate라는 새로운 object가 UI의 라이프사이클을 관리하게 됨. (하나의 앱에 여러개의 scene을 동시에 띄울 수 있게 됨) session은 앱에서 생성한 scene의 정보를 관리함.

scene에는 UI의 하나의 인스턴스를 나타내는 window와 view controller들이 포함되어 있음. 또한 각 scene에 해당하는 UIWindowSceneDelegate객체를 가지고 있고, UIKit과 앱 간 상호작용을 조정함. scene들은 같은 메모리와 앱 프로세스 공간을 공유하면서 서로 동시에 실행됨. 따라서 하나의 앱은 여러 scenen과 scene delegate 객체를 동시에 활성화할 수 있음.

앱이 처음 lauch될 때

→ AppDelegate의 willFinishLaunchingWithOptions 호출 → AppDelegate의 didFinishLaunchingWithOptions 호출 → AppDelegate의 세션 생성 관련 메소드 호출(근데 이게 두번째 런치-완전종료 후 다시 실행-에서는 호출이 안되네 willConnectTo와 Forground 사이에 AppDelegate의 didDiscardSceneSessions이 호출됨)→ SceneDelegate의 willConnectTo 메소드 호출 → sceneWillEnterForground → sceneDidBecomActive

멀티태스킹 앱 스위처가 올라오면 : sceneWillResignActive

멀티태스킹 앱 스위처에서 다른 앱 선택하면 : sceneDidEnterBackground

멀티태스킹 앱 스위처에서 다시 앱 선택하면 : sceneWillEnterForeground → ~~`sceneWillResignActive`~~ → sceneDidBecomActive

버튼으로 화면 닫을 때

sceneWillResignActive → sceneDidEnterBackground 

제어센터는 내려 → sceneWillResignActive → 다시 올리면 didBecomeActive

알림센터는 내리면 willResignActive가 두번이나 호출됨 → sceneWillResignActive → sceneDidBecomeActive → sceneWillResignActive 

**🤔  UIWindow**

The backdrop for your app’s user interface and the object that dispatches events to your views.

- 앱 UI의 배경이며 view에 event를 보내는 object다.

- [iOS앱에서 window는 한개만 가진다.](https://stackoverflow.com/questions/18282311/what-is-the-purpose-of-uiwindow) 

- UIWindow는 시각적 컨텐츠를 가지고 있지 않다. container일 뿐이다.