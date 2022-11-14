## async-let binding
지난 코드리뷰 때 아래 코드에 대한 피드백을 받은 적이 있다.
```Swift
func fetchSomeDatas() async {
    async let user = self.fetchUserData() // API 호출
    async let comments = self.fetchCommentData() // API 호출

    let userModel = await user
    let commentsModel = await comments
    //..이하 생략..
}
```
<br>

💬 위 코드는 아래와 동일한 효과일 것 같은데 async let을 사용한 이유가 별도로 있는지?<br>
```Swift
let userModel = await self.fetchUserData()
let commentsModel = await self.fetchCommentData()
```

<br>

당시에는 아래와 같이 코멘트 드리고 제안주신 방법으로(await를 사용) 수정했었다.
>await만 사용할 경우 첫번째 API 응답이 완료될때까지 두번째 API가 호출되지 않기 때문에 호출 자체를 비동기적으로 진행하고자 async let으로 빼두었습니다 !<br>
그런데 지금 코드를 보니 말씀하신대로 큰 차이가 없을 것 같아요😅 <br>
비동기적으로 API를 호출한다고 한들 값을 받아 사용할 때까지 대기해야하기 때문에 async let을 사용하기에 적절하지 않을 것(큰 이득이 없을 것) 같습니다.<br>
해당 부분은 await만 사용할 수 있도록 수정해두겠습니다 !<br>
관련하여 생각해 볼 수 있는 코멘트 주셔서 감사합니다~ 🙇‍♀️

<br>

근데 지금 와서 비슷한 코드를 보니 그때 당시에도 내가 async let을 제대로 이해하지 못했다는 생각이 들어 async let에 대해 다시 한번 찾아보게 됐다. 

- [Async let explained: call async functions in parallel](https://www.avanderlee.com/swift/async-let-asynchronous-functions-in-parallel/)
    - 이 아티클은 좀 별로다. 그냥 병렬적으로 호출하는 방법만 알려주고, 왜 특정 케이스에서 async-await을 사용하는 것보다 async-let을 사용하는게 더 성능적으로 좋은지에 대해 설명하지 않고 있다.
- [How to call an async function using async let](https://www.hackingwithswift.com/quick-start/concurrency/how-to-call-an-async-function-using-async-let)
    - `async let` 은 서로 연관이 없는 두개 이상의 네트워크 요청을 보내야할 때 유용할 수 있다.
    - A와 B를 서버에서 가져와야 할 때, B를 가져오기 전에 A를 반드시 가져와야하는 경우가 아니라면 async let이 유용하다는 것이다.
    - 만약 A를 가져온 직후 바로 UI 업데이트 작업이 필요하다면 따로 Task를 만들어 작업하는게 더 적절하다.
    - async let은 자신이 사용하는 값들을 캡처링한다.
    - async 함수를 호출할 때 Task { } 에 감싸서 호출할 수도 있지만, async let을 이용할 수도 있음. 어떤 상황에서 사용할지는 조금 더 생각해봐야함.

아티클들을 읽고 난 후 다시 내가 짰던 코드를 보니 await 두개를 사용하는 것과 큰 차이가 없다는 의미가 어떤 말인지 이제 이해가 됐다😅 <br>
만약 두번째로 호출한 API가 첫번째로 호출한 API 보다 훨씬 빨리 끝난 경우에는 어떨까?<br> 
두번째 API의 응답을 먼저 받았음에도 불구하고 첫번째 API의 응답을 await 하고 있기 때문에 await만 사용한 코드와 비교했을 때 차이가 없다. 네트워크 요청을 병렬적으로 보낸다 하더라도 순차적으로 값을 받아와 사용하고 있는 것이다. <br>
위 코드를 아래와 같이 수정했더라면 더 좋은 방향으로 수정이 가능했을 것 같아 아쉬움이 남는다.
```Swift
func fetchSomeDatas() async {
    async let user = self.fetchUserData() // API 호출
    async let comments = self.fetchCommentData() // API 호출
    
    let (userModel, commentsModel) = await (user, comments)
    //..이하 생략..
}
```
