## Coordinator 패턴이란 무엇인가?

- ViewController간 흐름 제어 및 탐색을 담당하는 패턴
- ViewController에서 flow 로직을 모두 꺼내고 ViewController보다 더 높은 계층으로 해당 로직들을 모두 이동시킴. → flow 로직을 한 곳에서 관리할 수 있다.
- Coordinator는 뷰컨트롤러를 생성 및 설정하거나 서브태스크 수행을 위해 새로운 child coordinator들을 생성할 수 있다.
- coordinator를 조합해서 사용할 수 있다 → 이건 아직 이해가 안됨.

### [The Coordinator](https://khanlou.com/2015/01/the-coordinator/)

2015년 Soroush Khanlou가 자신의 블로그에 [The Coordinator](https://khanlou.com/2015/01/the-coordinator/) 라는 글을 작성하면서 처음 알려지게 됨.

해당 글에서 ViewController의 문제는 flow logic과 view logic, business logic이 서로 얽혀있는 것이라고 소개함. 아래 예제 코드를 보자.

```swift
func tableView(_ tableView: UITableView, didSelectRowAt indexPath: IndexPath) {
	let object = self.dataSource[indexPath.row]
	let detailViewController = SKDetailViewController()
    self.navigationController?.pushViewController(detailViewController, animated: true)
}
```

- 아주 심플한 코드다. 앱의 규모가 작다면, 각 `ViewController`는 아마 딱 하나의 컨텍스트에서 딱 한번만 사용될 것이고, 이 경우 위와 같은 코드는 문제가 없다.
- 하지만 앱이 점점 커지고 복잡해지면, 동일한 `ViewController`는 새로운 방식이나 새로운 곳에서 사용될 수 있다.   
위 코드를 자세히 보면 다음과 같은 특징이 있다.   

    - dataSource를 처리하는 부분은 크게 문제가 되지 않음.
    - 하지만 `ViewController`에서 다음에 올 구체적인 ViewController를 알고 있는 것은 문제가 된다.   
    (왜냐하면 특정 `ViewController`에 의존하고 있기 때문에 변경이 발생할 경우 영향을 받을 수 있기 때문이라고 생각함!)
    - 자신의 parent ViewController에게 구체적으로 어떤 동작을 하라고 요청하는 것
    - 이런 flow code가 여러 개의 `ViewController` 이곳저곳에 퍼져있는 것

- 만약 flow logic이 `ViewController` 자체에 인코딩 된 경우(예를 들면 어떤 버튼을 눌렀을 때 다른 화면으로 이동하는 코드가 들어가 있다던가 하는) 모든 flow 코드를 해당 VC에 복사/붙여넣기 하지 않으면 재사용할 수 없을 것이다.   
*If the flow logic is encoded into the view controller itself, the view controller can’t be reused for its stunningly good looks without dragging all that flow code with it.*
- 잊지말자! ViewController도 기본적으로 `UI` prefix를 가지고 있다. 다시말해, user flow를 핸들링하는 것은 ViewController의 역할 밖이라는 것이다.


보통 view controller가 전체적인 흐름을 실행하는 방법을 아는 앱의 가장 최상위 계층이라고 생각할 수 있다. 그러나 모든 viewController를 관리하고 정렬시키는 역할을 하는 더 높은 계층이 관리하도록 함으로써 오는 많은 이점들이 있다.

이렇게 정렬해주는 객체를 Coordinator라고 함. 이 패턴을 잘 적용시키기 위해서는 전체 앱을 감독하는 단 하나의 높은 계층 coordinator가 있어야 한다. (때때로 [Application Controller pattern](https://martinfowler.com/eaaCatalog/applicationController.html)이라고도 함)

- AppDelegate(또는 SceneDelegate)가 AppCoordinator를 가지고 있어야 한다.
- 모든 coordinator는 그것의 child coordinator 배열을 가지고 있어야 한다. 특히 탭바와 같이 한개 이상일 경우, 각각의 navigation controller는 자신만의 coordinator를 가지고 있어야 한다.
- 추가적인 child coordinator들도 특별한 task(회원가입 또는 컨텐츠 생성 등)를 위해 만들어질 수 있다.
- 각 coordinator는 상위 coordinator에 의해 생성된다.
- child coordinator는 특정 delegate를 채택함으로써 언제 다음 스텝으로 넘어가야할지를 알려줄 수 있다.
- 특정 흐름에 속하는 그 어떤 ViewController도 다음에 어떤 ViewController를 만들어야하는지 알 필요가 없다.

## ViewController로부터 flow 로직을 추출했을 때 나오는 장점

- VC는 model을 view에 바인딩하는 것에만 집중하면 된다.
- 서로 다른 context를 관리하고자 복잡한 조건을 사용할 필요도 없다.
- viewController의 이니셜라이저도 추출된다. 이니셜라이저는 클래스나 설정값들에 대한 많은 사전지식을 필요로 하기 때문에 이니셜라이저가 추출되는 것도 아주 중요한 장점이다. → 근데 진짜 이니셜라이저가 없어지는게 맞는건가? 내가 이 부분을 잘못 이해한건가? 내가 본 예제 코드들은 모두 이니셜라이저를 가지고 있었는데..

## TabBar에 Coordinator 패턴 적용하기
방법 1. [블로그 글](https://somevitalyz123.medium.com/coordinator-pattern-with-tab-bar-controller-33e08d39d7d) ([github](https://github.com/Vitalyz123/CoordinatorTabBarFlow))

방법 2. [블로그 글](https://holyswift.app/coordinators-and-tab-bars-a-love-story) ([github](https://github.com/leopug/CoordinatorTabbbarStudy))

- 방법 1과 방법 2 모두 parent 코디네이터에 대한 참조를 가지고 있다.    
다만 방식의 차이가 있는데 방법 1은 delegate 패턴을 활용하고 방법 2는 weak로 parent를 직접 참조하고 있음.

- 방법 1은 명시적으로 child를 관리하고 있음. 따라서 다른 coordinator로 이동할 때 자기 자신이 가지고 있는 childCoordinator들을 삭제해야하고, parent에서도 방금 사라진 coordinator를 child에서 지워야 한다.

- 방법 2는 childCoordinator를 직접적으로 관리하지는 않음. 탭바만 있는 예제이기도 하고, 탭바 별로 단 하나의 coordinator를 관리해서 그런 것 같다. 다시 말하면 구조가 단순해서 childCoordinator를 관리하지 않는 것 같음. 그리고 이런 단순한 구조로 인해 특정 coordinator가 이동할 수 있는 화면에 대한 경우의 수(ex. HomeScreen이나 OrderScreen 열거형)를 사전에 정의해두고 있음. 서브태스크에 관한 coordinator가 없음.

- 방법 1과의 또 다른 차이점은 coordinator간 데이터 전달이 가능하도록 만들어두었다는 것.


👀  참고   
— [[브랜디 기술블로그] 화면 전환을 해결해 준 Coordinator 패턴](http://labs.brandi.co.kr/2020/06/16/kimjh.html)   
— [The Coordinator, Soroush Khanlou](https://khanlou.com/2015/01/the-coordinator/)   