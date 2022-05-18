2022-03-07   
2022-05-16: Autolayout과 Autoresize에 대한 비교를 해보자!


---
# AutoLayout

- ~~오토레이아웃을 코드로 작성하는 방법은 무엇인가? (3가지)~~
- ~~hugging, resistance에 대해서 설명하시오.~~
- ~~Intrinsic Size에 대해서 설명하시오.~~
- ~~스토리보드를 이용했을때의 장단점을 설명하시오.~~
- ~~Safearea에 대해서 설명하시오.~~
- ~~Left Constraint 와 Leading Constraint 의 차이점을 설명하시오.~~
- ~~AutoLayout 동작 방식 및 성능향상 방법을 설명하시오.~~

---

## Auto Layout이란?

> Auto Layout dynamically calculates the size and position of all the views in your view hierarchy, based on constraints placed on those views.
> 

Auto Layout은 뷰들의 constraints를 기반으로 동적으로 view hierarchy에 있는 모든 뷰의 사이즈와 포지션을 계산한다.

즉, constraints based layout이 Auto Layout이라는 의미다.

[하지만 모든 Auto Layout이 constraints 기반인건 아니다.](https://reasonable-repo.tistory.com/entry/Auto-layout-Constraint-based-layout-%EC%9D%80-%EC%84%9C%EB%A1%9C-%EB%8B%A4%EB%A5%B8-%EA%B2%83%EC%9D%BC%EA%B9%8C) 

## constraints의 예시

ImageView가 화면의 정 중앙에 위치해 있고, Button의 윗부분과 ImageView의 아랫부분이 10 포인트 간격을 둔 채 제약조건에 묶임 → Imageview의 사이즈가 변경되면 Button의 위치도 함께 변경됨. 

→ 특정 뷰의 변경에 맞춰 자동으로 다른 뷰의 위치나 사이즈가 변하기 때문에 Auto layout이라 부른다. 

## External / Internal Change

constraints 기반의 접근방식은 내,외부 변경에 따라 동적으로 UI가 반응하도록 만들 수 있다. 

- External Change : superview의 사이즈나 모양이 변경될 때 발생한다.
    
    → 예를 들어 OS X에서 윈도우의 사이즈가 변경되는 것, iOS에서 화면이 회전하는 것, 서로 다른 사이즈의 스크린을 지원해야할 때 등.
    
    여기서 얘기하는 superview에는 하위 뷰를 가진 UIView의 서브클래스들도 포함되는 걸까? 그럴 것 같은데. 예시에는 그런 내용이 나오지 않았지만,,
    

- Internal Change : 뷰나 컨트롤의 컨텐츠 사이즈(아래 내용을 보니 컨텐츠의 사이즈인 것으로 보임 = intrinsic size?)가 변경될 때 발생한다.
    - 텍스트나 이미지 등 컨텐츠가 변경될 때
    - Internationalization(국제화)에 대응할 때(국가에 따라 단어에 대한 표기도 다르고, 읽는 방향도 다를 수 있으므로)
        
        국제화는 레이아웃에 3가지 영향을 준다. ① 언어별로 라벨이 요구하는 사이즈가 다르다. ② 날짜에 대한 표기가 국가별로 달라질 수 있기 때문에 필요한 사이즈가 달라진다. ③ 언어의 변경은 텍스트 사이즈뿐만 아니라 레이아웃의 조합(organization)에도 영향을 미친다. → 읽는 방향 다름.
        
    - 앱에서 다양한 폰트와 폰트 사이즈를 지원할 경우 : UI에 있는 텍스트 요소의 width와 height에 변화가 필요하다.

## Auto Layout vs. Frame based layout

UI 레이아웃 하는 방법은 3가지가 있다.

① 프로그래밍적으로 레이아웃 함.

특징 : 뷰 계층구조에 있는 모든 view의 origin, size를 직접 지정, 변화가 발생하면 모든 view를 하나하나 다시 계산해야함

장점 : 뷰의 프레임을 프로그래밍적으로 정의하면 유연성과 파워?가 극대화 됨. (유연성 극대화??)

단점 : 레이아웃이 변할 때마다 모든 View들을 하나하나 다시 계산해줘야하기 때문에 복잡한 UI는 물론 단순한 UI일지라도 완전 adaptive한 UI를 만들기 위해서는 많은 작업이 필요하다.

② autoresizing mask를 사용

특징 : superview의 크기 변화에 따라 하위 뷰들의 크기도 같이 변한다.

장점 : auto resizing mask는 superview의 프레임이 변할 때 하위 뷰들의 프레임이 어떻게 변화되야 하는지를 정의한 것이다. external change에 대한 대응이 가능하다.(프로그래밍적인 방식에 비해 external change에 대응하기 쉬워짐)

단점 : autoResizing이 가지고 있는 속성의 개수가 다양하지 않다. (flexibleWidth, flexibleHeight, flexibleTop, flexibleBottom, flexibleLeft, flexibleRight), internal change 대응에는 여전히 프로그래밍적인 방식에 의존한다.

③ Auto Layout

일련의 Constraints를 기반으로 UI의 위치와 사이즈를 정한다. 여기서 말하는 Constraints는 두 뷰 사이의 관계를 나타냄. 

장점 : external, internal change에 대응할 수 있다.

## [Auto Layout Without Constraints](https://developer.apple.com/library/archive/documentation/UserExperience/Conceptual/AutolayoutPG/AutoLayoutWithoutConstraints.html#//apple_ref/doc/uid/TP40010853-CH8-SW1)

UIStackView는 복잡한 constraints를 사용하지 않고도 Auto Layout의 장점을 쉽게 활용할 수 있도록 만들어준다.

UIStackView 내에서 Auto Layout을 활용할 수 있도록 만들어주는 속성들은 아래와 같다.

- axis(element를 가로로 놓을 것이냐, 세로로 놓을 것이냐)
- distribution(axis가 horizontal-가로로 배치-일 때의 뷰 배치 정의)
- alignment(axis가 vertical-세로로 배치-일 때의 뷰 배치를 정의)
- spacing(스택뷰 안에 있는 element간 간격)

## Intrinsic Size에 대해서 설명하시오.

> — 🔗  [[O’Relly] Intrinsic Content Size](https://www.oreilly.com/library/view/ios-programming-the/9780133491876/ch16s04.html)
Intrinsic content size is information that a view has about how big it should be based on what it displays.

view가 **표시해야할 컨텐츠를 기반**으로 얼마만큼의 사이즈를 가져야 하는지에 대한 정보다.
> 

UILabel의 경우 지정된 텍스트의 폰트와 사이즈를 기준으로 intrinsic size가 된다. UIImageView의 경우 이미지의 사이즈가 intrinsic content size가 된다.

## hugging, resistance에 대해서 설명하시오.

둘다 intrinsic size와 관련된 제약이다.

`content hugging`은 고유 사이즈를 밖에서 안으로 조여오는 것과 관련있다. 상대적으로 priority가 낮을수록 고유 사이즈보다 커질 수 있다.

`compression resistance`는 고유 사이즈가 현재보다 더 줄어들길 원치 않을 때 사용한다. priority가 상대적으로 클수록 고유 사이즈가 줄어들지 않는다.

## Left Constraint 와 Leading Constraint 의 차이점을 설명하시오.

오른쪽에서 왼쪽으로 읽는 언어를 사용할 때 차이가 나타난다. Left Constraint는 무조건 왼쪽에 제약을 걸지만 Leading Constraint는 언어의 시작점을 기준으로 제약을 걸기 때문에 아랍어와 같이 오른쪽에서 왼쪽으로 읽는 언어에 유연하게 대응할 수 있다.

## Safearea에 대해서 설명하시오.

노치나 상태바, 네비게이션(위쪽)바, 탭바, 툴바 등에 의해 가려지거나 겹치지 않고 완전히 보여지는 영역.

🤔 탭바와 툴바의 차이

*— 🔗 [[brunch, @flatdesign] # 07. 내비게이션UI(2) — iOS vs Android](https://brunch.co.kr/@flatdesign/12)*

탭바는 메인화면의 컨텐츠 이동 역할을 한다. 툴바는 특정화면에서 가능한 액션을 보여준다. 사진첩의 사진 하나를 확인해보면 하단에 공유, 좋아요, 정보, 삭제 툴바 버튼이 나오는 걸 볼 수 있음. 이게 툴바

*— 🔗 [[Apple Doc] Positioning Content Relative to the Safe Area](https://developer.apple.com/documentation/uikit/uiview/positioning_content_relative_to_the_safe_area)*

각각의 view는 `safeAreaLayoutGuide` 속성을 가지고 있다. 이 속성은 view 내부 항목에 제약조건을 거는데 사용할 수 있다.

위 속성은 [UILayoutGuide](https://developer.apple.com/documentation/uikit/uilayoutguide) 타입인데 이는 Auto Layout과 상호작용 할 수 있는 사각형 영역이라고 한다.

만약 autolayout을 사용하지 않는다면 view의 `safeAreaInsets` 속성을 통해 raw inset value를 얻을 수 있다.

위 속성은 view가 화면에 아직 보여지지 않거나 또는 view hierarchy에 installed 되지 않은 경우 0으로 설정된다.

---

`additionalSafeAreaInsets` 속성을 이용하면 safeArea의 rawValue를 수정할 수 있다?

예를 들어 Container ViewController가 있음. 해당 VC는 하단과 우측에 커스텀 view를 보여주고 있어. 그럼 그 안에 임베드된 child viewController의 safeArea는 ContainerVC와 동일한게 아니라 하단과 우측의 커스텀 view만큼 제외시켜줘야 하는거임. 아래 그림과 같이.

![https://docs-assets.developer.apple.com/published/29ea4c9db6/419597f1-8b60-47bb-a8af-2d2f6809f2dc.png](https://docs-assets.developer.apple.com/published/29ea4c9db6/419597f1-8b60-47bb-a8af-2d2f6809f2dc.png)

childViewcontroller에서는 viewDidAppear시 아래와 같은 처리를 해주면 됨.

```swift
override func viewDidAppear(_ animated: Bool) {
   var newSafeArea = UIEdgeInsets()
   // Adjust the safe area to accommodate the width of the side view.
	 // 위 이미지 우측에 있는 custom view가 sideview
   if let sideViewWidth = sideView?.bounds.size.width {
      newSafeArea.right += sideViewWidth
   }
   // Adjust the safe area to accommodate the height of the bottom view.
   if let bottomViewHeight = bottomView?.bounds.size.height {
      newSafeArea.bottom += bottomViewHeight
   }
   // Adjust the safe area insets of the 
   //  embedded child view controller.
   let child = self.childViewControllers[0]
   child.additionalSafeAreaInsets = newSafeArea
}
```

## 스토리보드를 이용했을때의 장단점을 설명하시오.

**[단점]**

*— 🔗[[medium, @gurdeep060289] Clean Code for Multiple Storyboards](https://medium.com/@gurdeep060289/clean-code-for-multiple-storyboards-c64eb679dbf6)*

- 하나의 스토리보드에 많은 화면이 있을 경우 : storyboard를 로딩하는 시간자체가 오래 걸리고, 한눈에 파악하기도 힘들다. 

- 협업 시 문제 : merge 할 때 충돌이 발생한다. 

충돌이 발생하는 원인은 아래와 같다. (변경된 상태에서 커밋 후 서로 머지할 때 문제가 생기는거임)

①  개발자들 간 **Xcode 버전이 맞지 않을때**(Xcode tool version이 xml에 포함되어 있기 때문에 그냥 스토리보드를 열고 아무 작업을 하지 않아도 충돌이 발생할 가능성이 있다)

②  **모니터 사이즈가 다르거나 모니터 타입이 다를 경우**(retina vs. non-retina) : IB는 개발자가 스토리보드를 열었을 때마다 자동으로 업데이트하는데 이때 모니터 사이즈가 맞지 않으면 문제가 발생할 가능성이 있다.

③ **리소스를 추가한 경우**(이미지, 색상 등) 

어떤 VC의 요소에 추가하던 상관없이 두 개발자가 리소스를 추가한 후 머지하면 충돌이 발생할 수 있다. 왜냐하면 xml에서 <resources> 태그에 스토리보드의 모든 리소스가 들어가므로.

④  **새로운 화면을 추가**할 때

두 개발자가 같은 스토리보드에 새로운 화면을 추가 → 스토리보드가 동일한 위치에 xml을 추가한 경우 충돌 발생할 수 있음.

⑤  **동일한 화면을 수정**할 때

**[장점]**

- 화면 구성과 더불어 앱의 기능을 파악하는데 도움을 줌
(어떤 UI가 있는지, 어떤 역할을 하는 ViewController인지, 제약조건이 어떻게 걸려있는지 등)

- UI가 있기 때문에 직관적으로 화면을 만들 수 있음.

- 제약조건에 오류가 있거나 제약이 유효하지 않을 때 오류를 쉽게 파악할 수 있음.

## 오토레이아웃을 코드로 작성하는 방법은 무엇인가? (3가지)

— 🔗 [[tistory, nsios] [Swift] AutoLayout 코드작성방법](https://nsios.tistory.com/99)

- 코드로 작성하기 위해서는 먼저 해당 View의 `translateAutoresizingMaskIntoConstraints` 옵션을 false로 만들어야 함.

🤔  위 속성은 무엇이길래 false로 만들어야 할까?

우선 autoresizingMask를 보자면 자동으로 리사이징되는 mask라는 의미. **superview의 바운드(사이즈)가 변경될때 이에 맞춰 subview의 사이즈도 자동으로 재조정** 하는 것.

설정 시 배열 안에 활성화할 옵션들을 지정해줌. 

```swift
// 옵션종류는 left, right, top, bottom, width, height
view.autoresizingMask = [.flexibleLeftMargin]
```

`translateAutoresizingMaskIntoConstraints`는 auto resizing mask를 autolayout의 제약조건으로 번역할지 말지를 결정하는 속성

`true` -  autoResizing mask에 의해 지정된 동작을 시스템이 복제해 자동으로 제약조건을 만든다. autorisizing mask는 뷰의 사이즈와 포지션을 완전히 지정하기 때문에 제약조건을 추가할 수 없다. 따라서 autolayout을 이용해 제약조건을 만들고 싶다면 이 속성을 `false`로 지정해라.

① NSLayoutConstraint 

아래와 같은 수식을 이용해 제약을 추가함. 제약조건을 적용받을 view, 그 view의 속성과 제약조건에 활용할 view 및 속성, 둘의 관계, multiplier, constant 를 하나하나 지정해야함.

```swift
view1.attr1 = view2.attr2 * multiplier + constant
```

② Visual Fromat Language : 시각적으로 두 view 사이의 관계를 표현한 것.

SnapKit을 사용해 제약을 만들었을 때 조건 충돌하면 콘솔에 찍히는 것들이 visual formant language. 실제 개발할 때 사용하진 않지만 알고 있으면 제약조건 충돌이 발생했을 때 유용할 것 같다.

③ NSLayoutAnchor : NSLayoutConstraint에 비해 코드 가독성이 좋아짐.

## AutoLayout 최적화

*— 🔗 [[github.io, hcn1519] AutoLayout 성능 최적화하기](https://hcn1519.github.io/articles/2020-08/autolayout_performance_optimization)* 

**Autolayout이 나온 이유**

frame 기반 레이아웃은 사이즈와 위치를 직접 지정하는 방식이다. 이는 가장 직관적인 방법이지만 디바이스의 해상도에 따라 각각의 대응을 해야한다. (예를 들어 어떤 view의 width가 400pt로 고정되어 있을 경우 디스플레이의 사이즈에 따라 어떨 때는 다 보이고 어떨때는 잘려서 보이는 문제가 발생함) 이를 위해 AutoLayout이 고안되었다. 제약조건만 걸어주면 디바이스 사이즈에 따라 알아서 사이즈를 조정해준다.

**AutoLayout과 방정식**

두 view 사이에는 1개의 1차 방정식이 있다.

attr은 leading, trailing 등을 의미함

```swift
view1.attr1 = view2.attr2 * multiplier + constant
```

**AutoLayout Engine**

설정된 제약조건을 계산하는 코어 모듈. 순서는 아래와 같다.

1. view가 제약을 추가하고 이를 방정식으로 변환, engin으로 전달
2. engin은 전달받은 방정식을 계산하고 해(minX, minY, width, height등)를 구해 view에게 결과 리턴
    
    

engin은 각 해(variables)에 값을 설정할 때마다 view에게 값아 변경되었다는 것을 알린다. view는 이 이벤트를 받을 때마다 setNeedsLayout()을 호출한다.

setNeedsLayout()이 호출되면 view의 layoutSubviews()가 호출됨.

→ 레이아웃과 관련된 메소드의 연결관계? 호출관계?를 잘 모르겠음.

**AutoLayout Performance Tips**

**핵심은 불필요한 업데이트 줄이기**

1. 관계가 없는 View 사이에 constraints 설정하지 않기

2. 억지로 제약조건을 설정하지 않기?

3. Constraint churn(레이아웃 변경은 없지만 engin이 더 많이 일하게 되는 것)

- 모든 constraint 제거 후 다시 설정

→ add/remove보다 hidden을 이용하기

- 변경이 필요하지 않은 constraint 변경

- 반복적 constriants 추가/삭제

→ 동적으로 추가될 필요가 없을 경우 init 과정에서 정적으로 한번만 추가하기

1. updateConstraints
    
    ① `updateConstraints()` 호출 - View Hierarchy의 leaf부터 Window 방향으로 호출
    
    ② `layoutSubViews()` 호출 - Window부터 SubViews 방향으로 호출
    
    ③ `draw(rect:)` 호출 - Window부터 SubViews 방향으로 호출
    
    Render Loop는 view의 바운드가 변경되거나 회전이벤트 발생, setNeedsLayout(), layoutIfneeded()가 호출될 때마다 수행된다.