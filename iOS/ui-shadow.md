UI 작업을 하다보면 view에 그림자 효과가 들어간 것을 볼 수 있다.   
오늘은 view에 shadow를 추가하는 방법에 대해 알아보려 한다. 맨날 구글링하는 것 같아서 !  

- 우선 shadow 효과를 주기 위해서는 특정 속성들을 가지고 있는 CALayer를 생성하고, 효과가 필요한 UIView에 붙여주면 된다.
- 공식문서에는 Shadow와 관련된 프로퍼티가 5개 정도 나와있다. 하나씩 살펴보자.

#### <b>shadowOpacity</b>
- `Float`
- layer 그림자의 불투명도를 결정하는 속성이며 Animatable하다.
- 0.0(transperant) ~ 1.0(opaque) 범위의 값을 가지며 기본값은 0.0이다.

#### <b>shadowRadius</b>
- `CGFloat`
- layer의 그림자를 렌더링하는데 사용되는 blur 반경으로 Animatable하다.

#### <b>shadowOffset</b>
- `CGSize`
- layer 그림자의 offset으로 Animatable하다.
- 기본값은 (0.0, -3.0)이다.


#### <b>shadowOffset</b>
- `CGColor?`
- layer 그림자의 색상으로 Animatable하다.
- 기본값은 불투명한 검정색이다.
- 해당 프로퍼티의 값은 Core Foundation의 retain/release semantics를 사용하여 retained(유지)된다. 이러한 행동은 프로퍼티 정의가 object retention을 위한 기본적인 assign semantics처럼 보임에도 불구하고 발생한다.  (네..?🤔)


#### <b>shadowPath</b>
- `CGPath?`
- 기본 값은 nil이며, 이 경우 layer는 표준적인 그림자 shape를 사용하게 된다.
- 만약 이 값을 지정한다면, layer는 layer의 합성 알파 채널(composited alpha channel) 대신 지정된 path를 이용해서 shadow를 생성할 것이다. 지정된 path는 shadow의 윤곽을 정의하게 된다.
- non-zero winding rule과 현재의 shadow color, opacity, blur radius를 이용해 shadow가 채워진다.
- 대부분의 animatable 속성들과 다르게 이 속성은(모든 CGPathRef animatable 속성과 마찬가지로) 암시적인 애니메이션을 지원하지 않는다. 하지만 path 객체는 CAPropertyAnimation의 concrete 서브클래스를 이용해 animated될 수 있다.
- Paths will interpolate as a linear blend of the "on-line" points; "off-line" points may be interpolated non-linearly (to preserve continuity of the curve's derivative). If the two paths have a different number of control points or segments, the results are undefined. If the path extends outside the layer bounds it will not automatically be clipped to the layer, only if the normal layer masking rules cause that.
- ????

- 해당 값을 명시적으로 지정하는 것은 렌더링 퍼포먼스의 성능을 향상시킨다.

공식 문서만 읽어보았는데 궁금한 것들이 산더미다.
- Float과 CGFloat의 차이는 무엇일까?
- Animatable하다는 것은 무슨 의미일까?
- 개발문서에서 offset은 어떤 의미로 해석하면 좋을까?
- >Core Foundation의 retain/release semantics를 사용하여 retained(유지)된다. 이러한 행동은 프로퍼티 정의가 object retention을 위한 기본적인 assign semantics처럼 보임에도 불구하고 발생한다. 
    - retain/release semantics란?
    - Core Foundation의 retain/release semantics? default semantics와의 차이?
- CGPath란?
- 합성 알파 채널?
- non-zero winding rule
- 암시적 애니메이션?
- CGPathRef, CAPropertyAnimation?

앞으로 하나씩 알아가보도록 하자!