2022-03?

---
들어가며 

어제는 이것저것 링크를 찾아보고 사이트에 있는 글자 그대로 옮겨 담느라 집중도 안되고 이해도 잘 안됐던 것 같다. 오늘은 한글로 된 자료를 보고 종이에 중요한 키워드, 공통적으로 나오는 설명, 키워드 간 연결성을 그려가며 봤더니 적어도 함수형 프로그래밍이 무엇인지에 대해서는 스스로 설명할 수 있을 정도가 됐다. 앞으로의 개념들도 이런식으로 공부해나가면 좋을 것 같다.

---

### 함수형 프로그래밍은 왜 주목받게 됐나?

*— [https://hyunndyblog.tistory.com/163](https://hyunndyblog.tistory.com/163)*

> 기술*의 발전 → 사용자들의 인식 변화 → 프로그램 설계에 있어 우선순위가 달라짐*
> 

예전에는 숫자만 달랑 보이는 삐삐 하나만으로도 사람들이 만족하며 잘 썼음. 이때는 숫자만 잘 나오면 됐을거야. 근데 어느 순간 휴대용 전화기(아주 투박하고 무거웠지만)가 나왔어. 이제 더이상 숫자만으로는 만족을 못해, 통화도 되야함. 그러다가 문자와 전화가 가능한 가벼운 핸드폰이 나왔다. 이땐 디자인이 대박이었지(바나나?폰?, 엄청 독특한 핸드폰이 많았음) 단순히 문자, 통화 잘 터진다고 만족하지 않았지. 그러다가 스마트폰이 나옴. 이제 더 이상 문자와 전화, 독특한 디자인만으로는 사람들을 만족시킬 수 없었어. 더 많은 기능이 있어야 했지.

이렇듯 기술의 발전은 사용자들의 눈높이를 점점 올려놨고 그에 따라 중요하게 생각되어지는 것들이 달라져왔음.

현재의 기술은 하나의 컴퓨터에 여러 CPU가 있고 CPU 안에 여러 프로세스가 이고 프로세스 안에 여러 스레드가 있음. 병렬적으로 일을 처리하면서 속도가 빨라졌음. 이제 사람들은 오랫동안 돌아가는 로딩바를 참지 못해. 속도와 반응성이 중요해진거지. 그러면서 자연스레 “멀티"를 오류 없이 잘 활용할 수 있는 방법을 찾기 시작했어. 그 중 하나가 함수형 프로그래밍이지.

### 아주 많은 생소한 단어들과 상호 connection

```swift
(순수함수, immutable data) → (side effect 줄임, 참조투명성, thread 안정성, race condition, unit test 쉬움)
[고차함수, first-class citizen, 선언형과 명령형, [flat, map, ...], currying] 
```

- 순수함수
    
    ① 동일파라미터, 동일리턴 
    
    ② side effect 없음
    
- side effect
    
    > *— 🔗 [[Wiki] Side Effect(Computer science)](https://www.notion.so/Functional-Programming-67a0c2280b33465c8b86c64cffa96dd3)*
    
    In [computer science](https://en.wikipedia.org/wiki/Computer_science), an operation, [function](https://en.wikipedia.org/wiki/Subroutine) or [expression](https://en.wikipedia.org/wiki/Expression_(programming)) is said to have a **side effect** if it modifies some [state](https://en.wikipedia.org/wiki/State_(computer_science)) variable value(s) outside its local environment, that is to say has an observable effect besides returning a value (the primary effect) to the invoker of the operation.
    > 
    
    함수 내부의 변수가 아닌 외부의 변수값을 수정했을 때 side effect가 발생했다고 본다.
    
    side effect의 예로는 함수 외부의 변수를 수정하거나 전역 local 변수를 수정하거나 참조형식으로 들어온 파라미터를 수정하거나 I/O 작업을 수행하거나 side effect가 있는 다른 함수를 호출하는 것이 포함된다.
    
    side effect가 존재하는 상태에서 프로그램은 시간 흐름에 의존하게된다. 즉, 호출 순서가 중요하다는 것이다. side effect가 있는 코드를 이해하고 디버깅 하는 것은 코드가 작성된 앞뒤 문맥과 가능한 값?들에 대한 지식이 요구된다.
    

- immutable data : 변하지 않는 데이터
    
    순수함수는 side effect가 없는 함수이고 따라서 외부 값의 수정을 할 수 없고, 외부 값을 참조할 때도 데이터 값이 변해서는 안된다.
    
    명령형 프로그래밍은 side effect를 이용해 state를 변경하는 것이고, 선언형 프로그래밍은 side effect 없이 state를 변경하는 것이다. 
    

함수형 프로그래밍은 왜 함수를 first-class citizen으로 만들고 고차함수를 지원할 수밖에 없었을까?

 → 기존에는 함수 외부에 변수를 선언하고 연산 결과를 해당 변수에 넣었음. 근데 함수형 프로그래밍은 함수 외부에 있는 어떤 것도 수정할 수 없음. 만약 연산 결과가 필요하다면 새로운 데이터를 만들어 리턴하는 수밖에 없음. 

- 고차함수 : 함수를 매개변수로 받고 리턴 타입으로도 사용.

<aside>
✨ 각각이 무엇인지 먼저 알아보고, 이를 연결하는게 필요하다.

</aside>

### **함수형 프로그래밍에 대한 다양한 정의**

— 상태와 데이터의 변경을 피하면서 프로그래밍 하는 것

— 대입문 없이 프로그래밍 하는 것

— 거의 모든 것을 순수함수로 나누어 문제를 해결하는 기법

— 함수를 중심으로 사이드 이펙트가 없도록 프로그래밍 하는 것

<aside>
👩🏻‍💻 **순수함수**와 **변하지 않는 데이터**를 기반으로 프로그램을 설계하는 방법

</aside>

순수 함수란 함수 내부에서 외부에 위치한 값을 수정하거나 삭제하거나 할당하지 않는 함수(side effect 없음)이며, 프로그램의 어떠한 위치에서 호출되더라도 특정 input에 대해 동일한 output이 나오는 함수를 의미한다. (즉, input에 의해서만 output이 결정되는)

프로그램의 어떠한 위치에서 호출되더라도 동일 input, 동일 output 원칙이 보장되려면 함수 내부에서 참조하는 외부 데이터는 변하지 않아야 한다.

함수형 프로그래밍으로 설계를 하면 데이터의 변화로 인해 발생하는 에러를 방지할 수 있고 이로 인해 thread safe(여러 스레드에서 접근하더라도 항상 동일한 결과? 올바른 결과? 예측한 결과?를 얻는 것)한 프로그램을 만들 수 있다.

[https://mangkyu.tistory.com/111](https://mangkyu.tistory.com/111)

[https://jeong-pro.tistory.com/23](https://jeong-pro.tistory.com/23)

[https://hyunndyblog.tistory.com/163](https://hyunndyblog.tistory.com/163)

예제 : [https://www.raywenderlich.com/9222-an-introduction-to-functional-programming-in-swift#toc-anchor-005](https://www.raywenderlich.com/9222-an-introduction-to-functional-programming-in-swift#toc-anchor-005)

Race Condition : [https://iredays.tistory.com/125](https://iredays.tistory.com/125)