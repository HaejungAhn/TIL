## Combine 개요

📒 combine : 결합

<br>

>“*The Combine framework provides a declarative approach for how your app processes events. Rather than potentially implementing multiple delegate callbacks or completion handler closures, you can create a single processing chain for a given event source. Each part of the chain is a Combine operator that performs a distinct action on the elements received from the previous step.*”
    
- 앱이 이벤트를 처리하는 방법과 관련하여 선언적인(Declarative) 접근법을 제공한다. 
- 여러개의 delegate 콜백 또는 completion handler 클로저를 잠재적으로 구현하기 보다는 주어진 이벤트 소스에 대해 **단일 프로세싱 체인**을 만들 수 있다. 
- 체인의 각 파트는 **이전 단계로부터 받은 요소에 대해 고유한 작업을 수행하는 Combine operator**다.

<br>

#### Combine의 구성요소
`publishers`<br>
*시간의 흐름에 따라 변경되는 값을 노출한다.*<br>
- <u>Publisher 프로토콜은 시간의 흐름에 따라 일련의 값을 전달할 수 있는 형식을 선언</u>한다.<br>
- Publisher는 `operator`를 가지고 있는데 이는 upstream publisher로부터 값을 받아 처리한 후 이를 republish하게 만들어준다. (🤔Publisher는 transformer의 역할도 하는 것으로 보인다)<br>
- **Publisher들은 Subscriber가 명시적으로 요청한 경우에만 값을 내보낸다.**
- Publisher들의 chain 가장 마지막에 Subscriber가 있다.

<br>

`subscribers`<br>
publishers로부터 시간의 흐름에 따라 변경되는 값들을 받는다.

> You can combine the output of multiple publishers and coordinate their interaction.
> 

여러 publishers의 결과를 조합할 수 있고 그들의 interaction을 조정할 수 있다.

예시 1. textfield의 publisher로부터 업데이트를 구독할 수 있고 URL request 수행에 이 텍스트를 사용할 수 있다. 그리고 나서 또 다른 publisher를 사용해서 response를 처리할 수 있고 앱을 업데이트하는데 사용할 수 있다.

`operators`