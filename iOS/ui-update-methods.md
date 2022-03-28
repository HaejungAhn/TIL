UI가 업데이트 되는 원리와 이에 사용되는 메소드를 공부함.
UI 업데이트와 관련된 가장 중요한 포인트는 아래와 같다.  

## UI 업데이트 대상
- 제약조건(constraints) 
- 레이아웃(layout) : 포지션 및 사이즈
- display: 텍스트, 컨텐츠, 백그라운드 컬러 등 포지션과 사이즈를 제외한 view의 나머지 프로퍼티    

## 동작과정  
1. 사용자의 이벤트가 들어오면 이를 처리할 이벤트 핸들러가 실행된다.  

2. 이벤트 핸들러에서 비즈니스 로직 처리와 더불어 아래와 같이 UI를 업데이트하는 코드도 실행된다.
```Swift
label.text = "change content!"
containerView.background = .black
containerViewHeight.constant = 30
```
이는 실제 UI를 업데이트하는 것이 아니다. **UI의 업데이트가 필요함을 표시**(이하 플래그 추가)하는 것이다.  

3. OS는 표시된 UI만 업데이트하며 이는 **update cycle**에서 이루어진다. 
    update cycle이란 이벤트 핸들러의 처리가 끝나고 컨트롤이 다시 main run loop로 돌아오는 시점을 말하며, 이때 UI 업데이트가 발생함.   
  
## 기능에 따라 나뉘어지는 메소드
- 실제 업데이트를 처리하는 메소드  
    실제 업데이트를 처리하는 메소드로, 오버라이딩만 가능하며 코드에서 직접적으로 호출하면 안된다.
    - 레이아웃의 업데이트 처리 : `layoutSubViews()`  
    - 제약조건의 업데이트 처리 : `updateConstraints()`  
    - display 업데이트 처리 : `draw(_:)`  
- 플래그(업데이트가 필요함을 표시)를 추가하는 메소드  
    아래 메소드들은 호출하는 즉시 반환되며 플래그를 추가하기 위함이 목적이다. 다음 update cycle까지 기다렸다가 '실제 업데이트를 처리하는 메소드'를 트리거한다. 

    - 레이아웃 업데이트 표시 : `setNeedsLayout()`  
    - 제약조건 업데이트 표시 : `setNeedsUpdateConstraints()`, `invalidateIntrinsicContentSize()`  
    - display 업데이트 표시 : `setNeedsDisplay()`  
- 즉시 업데이트 하는 메소드  
    아래 메소드들을 호출하면 그 즉시 '실제 업데이트를 처리하는 메소드'를 트리거하고 처리가 완료된 후 리턴한다.  
    단, `IfNeeded`라는 suffix에서도 알 수 있듯이 플래그가 추가된 View만 업데이트한다. 따라서 동일한 run loop에서 여러 번 아래 메소드들을 호출하더라도 '실제 업데이트를 처리하는 메소드'는 한번만 트리거된다. 왜냐하면 플래그가 추가된 View들의 업데이트가 모두 끝난 상태기 때문이다.

    - 레이아웃 즉시 업데이트 : `layoutIfNeeded()`
        - 이 메소드는 변경사항을 애니메이션할 때 특히 유용함. 애니메이션 블록을 시작하기 전에 반드시 layoutIfNeeded를 호출해야 한다. 애니메이션 시작 전 모든 변경사항이 전파되도록 하기 위해.
        - 새로운 제약조건을 구성한 다음 애니메이션 블럭 안에서 layoutIfNeeded를 호출하여 새 상태로의 변경에 애니메이션을 적용할 수도 있음.
        - 예시 : [SnapKit - 제약조건 만들 때 애니메이션 적용하기](https://github.com/SnapKit/SnapKit/issues/530)
    - 제약조건 즉시 업데이트 : `updateConstraintsIfNeeded()`
    - display 즉시 업데이트는 없음.  

## 자동으로 플래그가 추가되는 경우  
수동으로 플래그 추가하는 메소드(`setNeeds` prefix가 붙은 메소드들)를 호출할 수도 있지만 대부분의 경우 자동으로 플래그가 추가된다. 자동으로 플래그가 추가되는 경우는 다음과 같다.  
- 레이아웃
    - 뷰 리사이징
    - 서브뷰 추가 `addSubview`
    - 사용자가 `UIScrollView`를 스트롤함layoutSubView가 호출됨)
    - 디바이스를 회전시킴
    - 뷰의 제약을 업데이트함
- display
    - 대부분의 경우 display와 관련된 View의 프로퍼티(백그라운드, 이미지, 텍스트 등 사이즈나 포지션을 제외한 프로퍼티들)가 변경되면 암시적으로 플래그가 추가된다.
- 제약조건
    - 제약조건 activate/deactivate
    - 제약조건의 constant, priority 변경
    - view 계층구조에서 view를 제거할 때

## Auto layout에서 view를 업데이트하는 단계
1. 제약조건 업데이트 : 시스템이 view에 필요한 모든 제약 조건을 계산하고 설정함.
2. 레이아웃 업데이트 : 레이아웃 엔진이 view와 subview의 프레임을 계산하고 배치함.
3. display 업데이트 : update cycle을 완료하고 필요한 경우(만약 draw메소드가 구현되어 있거나 플래그 추가된 상태라면) draw 메소드를 호출하여 뷰의 내용을 다시 그림






***
#### 참고 링크
- [Demystifying iOS Layout](https://tech.gc.com/demystifying-ios-layout/)
