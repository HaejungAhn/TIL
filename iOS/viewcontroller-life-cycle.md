2022-03-15

---
### 화면의 생명주기와 구성요소

- ViewController의 생명주기를 설명하시오.
- 모든 View Controller 객체의 상위 클래스는 무엇이고 그 역할은 무엇인가?
    
    → UIResponder
    
- UIWindow 객체의 역할은 무엇인가?
    
    → 앱의 UI를 화면에 보여주는 역할. UIWindow 자체는 아무런 시각적 컨텐츠가 없다. UIKit으로부터 이벤트를 받아서 root view controller와 연관된 뷰에게 전달한다. 
    

### Layout

- setNeedsLayout와 setNeedsDisplay의 차이에 대해 설명하시오.
- Bounds 와 Frame 의 차이점을 설명하시오.

### UIView

- 자신만의 Custom View를 만들려면 어떻게 해야하는지 설명하시오.
- View 객체에 대해 설명하시오.
- UIView 에서 Layer 객체는 무엇이고 어떤 역할을 담당하는지 설명하시오.
- prepareForReuse에 대해서 설명하시오.

---

공부할 목록

- ViewController의 역할

- ViewController의 생명주기 

willAppear, didAppear 는 어떤 차이가 있을까? 

언제 willDisappear가 되고 언제 didDisappear가 될까?

- View에도 생명주기가 있을까?

- ViewController 안에 있는 View들의 생명주기는 어떤 영향을 받을까?

# UIViewController

*— 👀  [[Apple document] UIViewController](https://developer.apple.com/documentation/uikit/uiviewcontroller)*

### UIViewController의 역할

- 뷰의 컨텐츠를 업데이트한다. 

- 뷰와 사용자의 상호작용에 응답한다.

- 뷰를 리사이징하고 전체 인터페이스의 레이아웃을 관리한다.

- 다른 view controller를 포함한 다른 객체들과 협력한다?(coordinating)

- 뷰 계층구조를 관리한다.

viewcontroller는 UIResponder 객체이고 그렇기 때문에 다른 view controller의 root view와 해당 view의 superview 사이 responder chain에 삽입되어 질 수 있다.

view controller는 두 종류가 있다.

`content view controller` : 단 하나의 root view를 가지고 있으며 이 root view안에 여러개의 하위뷰들이 들어가있음.

`container view controller` : 다른 뷰 컨트롤러의 컨텐츠를 자기 자신의 root view에 embed 하는 것. 대표적으로 UINavigationController가 있다. 해당 객체는 네비게이션바와 child view controller들의 스택을 관리한다. 또한 stack에 child view controller를 추가하거나 삭제하는 API도 제공한다.

### Handling View-Related Notifications

viewdml visiblity가 변하면 view controller는 자동으로 관련된 메소드를 호출하여 변화에 적절하게 대응 할 수 있도록 한다.

viewDidLoad

`viewWillAppear` 

view controller에게 view가 뷰계층구조에 추가될 예정임을 알리거나 view를 표시하기 위해 애니메이션이 구성되어지기 전에 호출된다.

`viewDidAppear`

view가 계층구조에 추가됨

`viewWillDisappear`

view가 계층구조에서 삭제될 예정임을 알린다.

`viewDidDisappear`

view가 계층구조에서 삭제됨.

**viewController의 생명주기는 사실상 view의 생명주기나 마찬가지다. 
왜냐하면 화면에 viewcontroller가 보여짐과 동시에 view가 계층구조에 attach되거나 detach되기 때문이다.**

![출처 : [https://mini-min-dev.tistory.com/33](https://mini-min-dev.tistory.com/33)](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/52307be0-7376-4012-a27f-b35d925e327e/스크린샷_2022-03-14_오후_6.09.32.png)

출처 : [https://mini-min-dev.tistory.com/33](https://mini-min-dev.tistory.com/33)

loadView(뷰 컨트롤러의 메인 뷰를 만드는 메소드) → viewDidLoad(컨트롤러의 뷰 계층구조가 메모리에 로드되고 난 이후 호출됨) → viewWillAppear → viewDidAppear → viewWillDisappear → viewDidDisappear → viewDidUnload

초록색으로 마킹된 것들은 경우에 따라 여러번 호출된다. viewDidAppear와 viewDidDisappear의 경우 해당 뷰컨위에 띄워지는 뷰컨의 presentation style에 따라 호출될 수도 있고 안될 수도 있다. 만약 overFullScreen과 같이 “over”라는 prefix가 붙는 style로 화면이 띄워진다면 호출되지 않는다.

그럼 attach되거나 detach되는것 말고 view가 메모리에 올라가는 과정은 어떻게 될까?

*— 🔗 [[tistory, mrgamza] [iOS] View가 Load될 경우](https://mrgamza.tistory.com/279)*

1. awakeFromNib - xib 파일이 생성되어 있을 경우 호출됨.
2. loadView - xib를 사용하지 않고 View를 로드하는 경우. xib가 존재하는데 이를 오버라이딩한다면 xib는 무시된다. 

ViewController의 view가 생성되는 과정

1. view 프로퍼티에 접근
2. 메모리 상에 view가 존재하지 않을 경우 loadView 호출. 오버라이딩 되어있다면 해당 내용 실행, 오버라이딩 안되어 있다면 nibName, nibBundle 프로퍼티의 nib파일 로드 시도
3. 2번에서 nib파일이 없다면 ViewController의 nib파일 로드 시도
4. 그래도 없다면 UIView를 생성
5. viewDidLoad 호출

viewWillLayoutSubviews, viewDidLayoutSubviews들은 그럼 언제 호출되는걸까?

`viewWillLayoutSubviews` 

- ViewController의 인스턴스 메소드

- ViewController의 view bound가 변경될 때 view는 하위뷰들의 포지션을 조정하게된다. 해당 메소드를 오버라이딩해서 view가 하위뷰들을 조정하려고 하기 전 변화를 줄 때 사용한다.

- root view의 사이즈가 변경될 때만 호출되는지 보겟음. → 확인해보니 root view가 가지고 있는 하위뷰에 변화가 있을 때 호출됨.
- 하위뷰의 frame 좌표가 변한다면? 코드로 view를 만들고 frame도 코드로 지정. 버튼 누를때마다 y좌표만 변경되도록 테스트해봤는데 bound가 변하진 않았지만 호출됨. 아마 내부적으로 bound가 변하는 과정이 있어 그런것 같다.
- 버튼의 target 메소드에 아무 로직도 넣지 않음. → 그래도 호출하고 있음.
- 백그라운드 색상만 변경 → 호출됨.

*— 🔗 [[tistory, woonhyeong] iOS - View Controller의 Life Cycle](https://woonhyeong.tistory.com/4)*

아래와 같은 시점에 호출된다고 함.

- 뷰들을 추가 or 제거
- 뷰들의 크기나 위치를 업데이트
- 레이아웃 constraints 업데이트
- 뷰와 관련된 프로퍼티 업데이트

`viewDidLayoutSubviews`

- view의 바운드가 변경되면 하위뷰의 포지션을 조정하고 시스템이 이를 호출한다. 그러나 이 메소드가 호출된다고해서 view의 하위뷰들 각각의 레이아웃(individual layout)이 조정됐다는 의미는 아니다. 각 서브뷰들은 자체적으로 레이아웃을 조정해야할 책임이 있다.

*— 🔗 [Displaying and Managing Views with a View Controller](https://developer.apple.com/documentation/uikit/view_controllers/displaying_and_managing_views_with_a_view_controller)*

UIKit은 아래의 과정을 거쳐 view를 만든다.

1. view의 init(coder:) 메소드를 통해 view를 만든다.
2. view controller에 있는, view의 action과 아울렛을 연결한다.
3. view와 viewController의 awakeFromNib()메소드를 호출한다.
4. view hierarchy에 view controller의 view(root view) 프로퍼티를 할당한다.
5. view controller의 viewDidLoad() 메소드를 호출한다.

## UIWindow

*— 🔗  [[Apple Document] UIWindow](https://developer.apple.com/documentation/uikit/uiwindow)*

- 앱의 시각적인 컨텐츠를 표현한다.
- 터치 이벤트를 전달한다.
- ViewController와 함께 이벤트를 핸들링하고 앱 operation에 필수적인 여러 task를 수행한다.
- 터치 이벤트는 터치가 발생한 윈도우로 전달되는 반면, 연관된 좌표 값이 없는 이벤트 혹은 키보드 이벤트는 키 윈도우로 전달된다.