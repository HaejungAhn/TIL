2022-03-16

---
[https://developer.apple.com/library/archive/featuredarticles/ViewControllerPGforiPhoneOS/ImplementingaContainerViewController.html#//apple_ref/doc/uid/TP40007457-CH11-SW1](https://developer.apple.com/library/archive/featuredarticles/ViewControllerPGforiPhoneOS/ImplementingaContainerViewController.html#//apple_ref/doc/uid/TP40007457-CH11-SW1)

## ContainerViewController란?

- content view controller가 여러 view들을 하나의 UI로 묶은거라면 container view controller는 **여러 view controller들을 하나의 UI로 묶은(combine)것**이다.
- 탐색을 가능하게 하고 **이미 존재하는 컨텐츠를 기반으로 새로운 UI를 만들 수 있도록** 해준다.
- UIKit에서는 NavigationViewController, TabBarViewController가 여기에 해당한다.
- 포함되는 VeiewController의 view(root view)는 ContainerViewController의 뷰 계층구조에 임베드되지만, 여전히 원래의 View Controller가 하위 뷰를 관리한다.

## ContainerViewController를 만들기에 앞서 생각해봐야 할 것

- **containerVC와 children의 역할**을 무엇인가?
- 동시에 얼마나 많은 children이 display되야 하는가?
- (sibling VC가 있다면) sibling VC 사이의 관계는 무엇인가?
- ContainerVC로부터 child VC를 어떻게 추가하거나 제거하는가?
- children의 사이즈나 position이 변화하는가? 어떤 조건에서 변화하는가?
- container가 자체적으로 decorative 또는 navigation-related view를 제공하는가?
- children과 container 간 어떤 종류의 커뮤니케이션이 필요한가? container가 UIViewController 클래스에 정의된 표준 이벤트 이외에 children에게 특정 이벤트를 알려줘야 하는가?
- container의 appearance를 다른 방식으로 구성할 수 있는가?

## ContainerViewController 커스텀하기

- ContainerVC와 ChildVC 간 parent-child 관계를 만들어줘야 한다. 이 관계는 childVC를 manage 하려고 하기 전에 만들어져있어야 한다.
- ChildVC 추가하기
1. `addChild(_:)` 메소드를 호출한다 : UIKit에게 ContainerVC가 이제 ChildVC를 managing 한다는걸 얘기해줌.
2. childVC의 root view를 ContainerVC의 뷰 계층구조에 추가해줌. 이때 childVC의 root view 사이즈와 포지션을 정해야함.
3. `didMove(toParent:)` 메소드를 호출한다 : VC가 ContainerVC에 추가되거나 삭제된 이후에 호출한다. removeFromParent()를 호출하면 이게 자동으로 호출됨.

- ChildVC 제거하기 : parent-child 관계를 제거해주면 된다.
1. childVC의 `willMove(toParent:)` 메소드를 호출한다. 이때 매개변수로 nil을 전달한다.
2. childVC의 root view에 걸린 제약조건을 제거한다.
3. childVC의 root view를 ContainerVC의 뷰 계층구조에서 제거한다. ( childVC의 view.removeFromSuperview )
4. childVC의 `removeFromParent()` 메소드를 호출한다.

## willMove와 didMove에 대하여

Container subclasses will typically define a method that transitions to a new child by first calling addChildViewController:, then executing a transition which will add the new child's view into the view hierarchy of its parent, and finally will call didMoveToParentViewController:. Similarly, subclasses will typically define a method that removes a child in the reverse manner by first calling [child willMoveToParentViewController:nil].

- **위 두 메소드는 child 뷰컨트롤러 간 트랜지션 발생 시 container 뷰컨트롤러에서 호출**한다.

> — *[[Apple Document] willMove(toParent:)](https://developer.apple.com/documentation/uikit/uiviewcontroller/1621381-willmove)*
Your view controller can override this method when it needs to know that it has been added to a container.
child뷰컨트롤러 입장에서는 자기가 parent의 뷰 계층구조에 추가되거나 삭제될 때를 알지 못한다. 이를 알려주기 위해 오버라이딩해서 사용할 수 있다.
> 

- child 뷰컨트롤러가 parent로부터 삭제되어질 때에는 매개변수가 nil로 들어간다.

- `addChild` 메소드는 child를 붙이기 전 `willMove`를 호출한다. 하지만 `didMove`는 호출하지 않는다. 따라서 conatinaer 뷰컨트롤러는 새로운 child의 전환이 완료된 이후 또는 트랜지션이 없는 경우에는 addChild를 호출한 즉시 didMove를 호출해야한다.

- `removeFromParent` 메소드는 child를 제거하기 전에 `willMove` 를 호출하지 않는다. 따라서 이를 명시적으로 호출하는 것은 container뷰컨트롤러의 역할이다. 

- 컨테이너의 서브클래스는 먼저 `addChild` 메소드를 처음 호출함으로써 새로운 child로의 트랜지션을 위한 메소드를 정의한다. 그리고 나서, 새로운 child의 view를 parent의 뷰 계층 구조에 붙이는 트랜지션을 실행한다. 그리고 마침내 `didMove`를 호출하게 된다. `willMove`를 제일 처음 호출하기 시작해서 반대반향으로 만들면 child를 제거하는 메소드도 만들 수 있다. 

테스트 해봤을 때

- `addChild` 메소드 호출 시 container 뷰컨트롤러에서 `didMove`를 호출하지 않더라도 `willMove`는 호출된다.
- 단, `didMove`를 호출하지 않으면 `viewDidLoad`도 호출되지 않음.
- container 뷰컨트롤러의 생명주기에 따라 child뷰컨트롤러의 생명주기 메소드도 호출된다.
- `removeFromParent` 를 호출하면 container뷰컨트롤러가 가지고 있는 `children:[UIViewController]` 에서 해당 child가 제거된다.

## 왜 사용할까?

“나는 container ViewController를 왜 사용하게 된걸까? 그게 최선의 방법이었을까?” 하는 생각이 들었음. 왜냐면 ViewController의 재사용을 위해 사용했는데 생각해보면 UIView를 이용해도 충분했거든. 

그래서 처음에는 UIView와 UIViewController의 차이를 생각해봄.

- UIView는 parent-child 관계가 만들어지지 않는다.
    
    → 이 관계가 없더라도 delegate로 통신할 수 있음.
    
- UIView는 UIViewController의 생명주기 메소드를 사용할 수 없음.
    
    → 이것 역시 UIView를 포함하고 있는 곳에서 해당 주기에 UIView의 메소드를 호출함으로써 처리할 수 있음.
    

둘다 대체제가 있었다. 

“why we use container view controller in swift” 라고 검색해봤다.

다시한번 공식문서가 짱임을 깨달음.

*— [[Apple Document] Creating A Custom Container View Controller](https://developer.apple.com/documentation/uikit/view_controllers/creating_a_custom_container_view_controller)*

> *Container view controllers promote **better encapsulation** by separating out your content from how you display that content onscreen.

a container view controller **displays other view controllers**, **arranging them onscreen** and **handling navigation between them.***
> 

즉, 목적은 다른 뷰컨트롤러들을 디스플레이하는데 이들을 화면에 정리해서 보여주고 다른 뷰컨트롤러들 간 navigation이 원활하도록 하는게 목적이다. 즉, 뷰컨트롤러 간 이동을 편리하게 만들어주기 위한 목적으로 Container View Controller를 사용하는 것이다. 

이런 의미에서 UIViewController를 재사용하기 위해 Container ViewController를 사용하는 건 옳은 목적으로 사용한 것은 아니라는걸 깨달았다.

애플이 만든 모든 것들에는 고유의 목적과 이유가 있다. 이를 잘 인지하고 개발에 사용하는 것이 매우 중요하단 걸 깨달을 수 있었던 아주 뜻깊은 질문이었다!