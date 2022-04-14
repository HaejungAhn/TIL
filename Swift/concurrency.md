2022-03-01

---
스레드와 프로세스의 차이

*— 👀 참고자료*

[[소들이님] iOS) 프로세스(Process) vs 쓰레드(Thread)](https://babbab2.tistory.com/63)

[[StackOverflow] What is the difference between a process and a thread?](https://stackoverflow.com/questions/200469/what-is-the-difference-between-a-process-and-a-thread)

[blocking과 non-blocking](https://musma.github.io/2019/04/17/blocking-and-synchronous.html)

### **프로세스와 스레드는 무엇인가**

**프로세스**

- **활성화된 프로그램(실행중인 프로그램)으로 독립된 메모리 영역을 가지고 있다.**

- 운영체제로부터 시스템자원을 할당받는 작업 단위

**멀티 프로세스**

- 하나의 프로그램을 여러개의 프로세스로 구성하여 각 프로세스마다 하나의 작업씩 처리하도록 하는 것

- 각기 독립된 메모리(code, data, heap, stack) 영역을 할당받는다. 따라서 프로세스 간에는 서로의 변수나 자료구조에 접근할 수 없음. 만약 서로 다른 프로세스 간 자원 접근이 필요한 경우 프로세스간 통신(IPC)를 사용해야 함(파일, 소켓 등)

멀티 프로세스의 장점

- 프로세스 간 서로의 자원에 침투할 수 없음 → 독립된 구조기 때문에 안정성 높음.

멀티 프로세스의 단점

- 여러개의 프로세스를 왔다갔다해야하기 때문에 잦은 context switching이 발생 → CPU의 부담이 커지고 오버헤드 발생하게 된다.

- 프로세스 간 자원 공유가 어렵다.

**스레드**

- 한 프로세스 내에서 동작되는 실행 흐름

- 스레드는 하나의 프로세스 내에서 동작하기 때문에 스레드 별 메모리 영역을 독립적으로 할당받지 못한다. 따라서 스레드 간 자원공유가 가능함. 

- 공유되는 영역 - code, data, heap / 독립적인 영역 - stack(LIFO 특성 상 여러 스레드가 함께 사용하게 되면 실행흐름에 방해가 되기 때문에 stack은 분리)

**멀티스레드**

- 하나의 프로그램을 여러 스레드로 구성하여 각 쓰레드마다 하나의 작업씩 처리하도록 하는 것

멀티 스레드의 장점

- Context Switching이 빠름(스레드 별 code, data, heap을 공유하기 때문에)

- 스레드 간 통신이 프로세스간 통신보다 간단함

멀티 스레드의 단점

- 설계가 까다로움(자원 공유의 문제가 발생할 수 있기 때문 - 동기화 문제)

- 하나의 스레드에서 발생한 문제가 전체 스레드에 영향을 미침

🤔  Context switching이란?

---

~~동기와 비동기의 차이는 **어떤 하나의 작업에 대한 처리가 시작되서 끝날때까지 기다리냐 기다리지 않냐의 차이**다.~~ 

~~task의 입장에서 보면 어떤 task가 sync라는 것은 task의 작업이 시작돼서 완전히 끝났을 때를 task의 끝이라고 본다.~~ 

~~async는 작업에 대한 처리를 요청하면 task의 끝이다.~~

~~동기는 하나의 작업에 대한 처리가 끝날때까지 기다리는 것이고 비동기는 작업에 대한 처리 요청을 보내고 바로 다음 task로 넘어간다.(자신의 task를 끝낸다.)~~

동기와 비동기에서 “동시에 처리되는” 대상이 있음. 요청과 응답 또는 호출한 함수와 호출된 함수

serial과 concurrent의 차이는 한개 이상의 task를 어떻게 처리할 것이냐와 관련있다. serial이라면 하나의 스레드에서 여러 task를 순차적으로, 한번에 하나씩 처리하는 것이고, concurrent는 여러 스레드에서 동시에 여러개를 처리하는 방식을 말한다.

동기와 비동기, serial과 concurrent, bloking과 nonbloking

위 개념들이 헷갈리는 이유

동기는 이전 작업이 끝나야 다음 작업이 시작되는 거니까 순차적으로 처리되네? ㅇㅋ 이해함. 다음!

serial은 순차적으로 task를 처리하는거네? 동기랑 뭐가 다른지 모르겠지만 어쨌든 봤음. 다음!

blocking은 흐름을 block하는거네? 동기랑 뭔 차이,,? 

비동기는 이전 작업의 종료와 상관없이 동시에 여러작업이 이뤄지는거네? ㅇㅋ 다음!

concurrent는 동시에 여러task를 처리하는거네? 비동기아님 이거,,? 어쨌든 다음!

nonblocking은 흐름을 막지않는거네!.. ?

이런 흐름으로 가는 순간 서로 개념이 혼동되고 그렇게 이해하지 못할 수렁으로 빠지게 됨. 너무 어려워지는거임 개념자체가.(내가 그랬음)

어느 관점에서 위 개념들을 바라보느냐가 중요하다.

- ~~콜센터 예시?~~
    
    동기 → 고객이 고객센터에 전화를 걸었어. 고객과 상담원의 전화통화로 문제가 처리됐어. 고객과 상담원이 **함께 같은 시간동안 같은 문제**에 대해 얘기를 나눴고 처리됐잖아. 이게 동기임. 
    
    비동기 → 고객이 고객센터에 전화를 걸었어. 상담원이 이런 자료가 필요하다고 하니 고객이 알아보고 전화를 주겠대. **고객만 그 문제에 대해 알아보고** 처리가 되면 상담원한테 전화를 줌. 상담원은 그 동안 다른일을 할 수 있음.
    
    serial과 concurrent
    
    serial → 고객센터에 전화기가 딱 한대야. 고객들이 전화를 해도 순서대로 한번에 하나씩밖에 응대할 수 없어. serial
    
    concurrent → 고객센터에 전화기가 여러대야. 고객들이 전화를 했을 때 여러 전화기에서 동시에 이를 응대할 수 있어. concurrent
    
    blocking과 nonblocking (return)
    
    blocking → 고객이 고객센터에 전화를 걸었어. 문제 처리를 위해 상담원이 뭘 물어봤고 고객이 전화를 끊지 않은채로 이것저것 찾아보고 있어. 상담원 입장에서 이게 blocking
    
    nonblocking → 고객이 고객센터에 전화를 걸었어. 문제 처리를 위해 상담원이 뭘 물어봤고 고객이 좀 알아보고 다시전화준대. 상담원 입장에서 이게 nonblocking
    

**동기와 비동기 - 함수를 호출하는 쪽에서 호출된 함수의 종료를 기다릴지 말지를 결정**

**serial과 concurrent - 여러개의 함수호출을 어떻게 처리할지를 결정**

**blocking과 nonblocking - 호출된 함수쪽에서 종료를 처리 다 하고 할지 처리 다 안됐지만 할지를 결정**

하나씩 파보도록 하겠음.

**동기와 비동기 - 함수 하나하나의 관점에서**

먼저 “동기"를 찾아보면 “같은 시기"라는 의미를 가지고 있음. “같은 시기”를 곰곰히 생각해보면 다음과 같은 사항들을 발견할 수 있다.

- “같은” → 두개 이상의 대상이 있다.
- **누가** 같은 시기에 **무엇을** 하는가

Who? 

두개 이상의 대상이라고 했음. 모든 프로그램은 함수로 움직인다. 호출한 함수와 호출된 함수

What?

호출한 함수와 호출된 함수는 동시에 무엇을 하냐? 호출된 함수의 처리가 종료되는 것을 기다린다.

즉, 동기는 호출한 함수와 호출된 함수가 같은 시기에 같은 작업(호출된 함수의 처리가 종료되는 것을 기다리는)을 한다.

비동기는 그럼 무엇이냐(같은시기가 아니다)

호출한 함수와 호출된 함수가 

싱크로나이즈 경기를 생각해보자. 싱크로나이즈는 물속에서 여러명의 사람들이 음악에 맞춰 군무를 이루는 것임.

**싱크로나이즈, 동기와 비동기**

동기는 “같은 시기”라는 의미를 가지고 있다. “같은 시기”를 유심히 보면 다음과 같은 사항을 발견할 수 있다.

🤔  Who: 대상은 무엇인가

프로그램은 함수로 돌아간다.

프로그램을 구성하는 함수 하나 하나가 대상이 된다. 여기서는 호출한 함수와 호출된 함수가 그 대상임

🤔  What: 무엇을 하는가

호출된 함수와 호출한 함수 모두 같은 동작을 한다. 바로 호출된 함수의 처리가 종료되길 기다리는 것이다.

[[소들이님] iOS) Sync vs Async / Serial vs Concurrent](https://babbab2.tistory.com/64)

sync, async는 작업 하나하나에 대한 처리

**Sync : 동기** 

- “동시에 발생하는”이라는 의미를 가지고 있음.
- 무엇이 동시에 발생하냐 : 응답과 요청이 동시에 발생한다. 기존 작업에 대한 응답을 받기 전까지 새로운 요청을 보낼 수 없다.

**Async : 비동기**

- “동시에 발생하지 않는" → 요청과 응답이 동시에 발생하지 않는다.
- 기존 작업은 요청을 보내두고 (응답이 오진 않았지만) 다른 요청이 보내지도록 허용한다.

Queue의 입장에서 봤을 때 Sync는 응답까지 받아야 처리 완료가 되어 Queue에서 없어지는거고, Async는 응답만 보내놓으면 처리 완료로 보고 Queue에서 없어지는 것?

serial, concurrent는 queue에 들어온 작업들을 어떻게 처리할 것이냐

**Serial : 순차**

- Queue에 들어온 작업들을 순차적으로 하나씩 실행시키겠다. **한번에 하나의 task만 처리 가능**
- First-In-First-Out

**Concurrent : 동시**

- Queue에 들어온 작업들을 동시다발적으로 실행시키겠다. **한번에 여러개의 task 처리 가능**

```swift
//MARK: Serial
print("1-\(Thread.current)")
print("Start!")
DispatchQueue.main.async {
    print("2-\(Thread.current)")
    for _ in 0...3 {
        print("async")
    }
}

print("3-\(Thread.current)")
for _ in 0...3 {
    print("sync")
}

print("4-\(Thread.current)")
print("End!")
/*
1-<_NSMainThread: 0x600001b50500>{number = 1, name = main}
Start!
3-<_NSMainThread: 0x600001b50500>{number = 1, name = main}
sync
sync
sync
sync
4-<_NSMainThread: 0x600001b50500>{number = 1, name = main}
End!
2-<_NSMainThread: 0x600001b50500>{number = 1, name = main}
async
async
async
async
 */

/*
🤔 왜 async보다 sync가 먼저 나올까?
일단 여기는 serialQueue임. serial queue는 "한번에 하나의 작업만" 수행함.
 1. print("Start!") 작업이 시작되고 콘솔에 찍힘으로써 작업이 완료된다.
 2. async 블럭에 있는 작업이 시작된다. async 작업이기 때문에 작업 요청과 함께 (콘솔에 찍히는걸 기다리지 않고) 바로 다음 작업으로 이동한다.
 3. 4번 for문을 돌면서 print("sync") 작업을 진행한다. sync이기 때문에 콘솔에 "sync"가 4번 찍히는걸(완료되길) 기다린다.
    serial queue는 한번에 하나의 작업만 수행하기 때문에 2번 작업의 처리가 지연된다.
 4. print("End!") 작업이 시작되고 콘솔에 찍힘으로써 작업이 완료된다.
 5. serial queue에 남아있던 작업이 모두 종료됨. 2번 작업의 동작이 실행된다.

 조금 이해는 됐지만 여전히 의문이 남는다. 왜 async는 가장 마지막에 실행되는 걸까? 다른 작업이 실행되는동안 대기타고 있는건가?
 */
```

Dispatch : 급파하다, 재빨리 해치우다, 없애버리다, 택배, 보내다

DispatchQueue : **Queue에 보내다**

thread와 Queue의 차이? [https://medium.com/omarelgabrys-blog/threads-vs-queues-a71e8dc30156](https://medium.com/omarelgabrys-blog/threads-vs-queues-a71e8dc30156)

[[iOS개발자 앨런] 프로그래밍에서 동기/비동기 개념에 대한 가장 직관적인 이해](https://www.youtube.com/watch?v=zRJOte7TaPw)

비동기 : 다른 스레드로 task를 보내고 바로 리턴함. 일을 시작시키고, 작업이 끝날때까지 안기다리고 바로 다른 작업을 진행시킨다.

동기 : 다른 스레드로 task를 보내고 완료될때까지 기다림. 일을 시작시키고 작업이 끝날때까지 기다린다.

→ 다른 스레드로 보낸 task를 기다릴지 말지

직렬: 다른 하나의 스레드로만 보내는 개념, 순서가 중요한 작업을 처리할 때

동시: 다른 여러개의 스레드로 보내는 개념(몇개로 보낼지는 시스템이 결정하겠지만 어쨌든 여러 스레드로 보낸다), 독립적이지만 유사한 여러개의 작업을 처리할 때?(ex, 당근마켓 물품의 이미지를 가져올 때)

→ 다른 스레드로 작업을 보낼 때 한개의 스레드에 보낼지 여러개의 스레드에 보낼지

Concurrent 프로그래밍이 왜 필요할까?

성능, interaction(반응성), 최적화

GCD, Operation으로 처리하고 있음.

**Swift: Concurrency, GCD**

둘다 동시성 프로그래밍을 할 때 사용하는 것

GCD : **동시성 프로그래밍 API**. Objective-C 시절부터 존재해왔음. serial dispatch queue와 concurrent dispatch queue가 있으며 둘다 Queue이기 때문에 FIFO의 구조다. escaping closure를 활용해서 async 이후에 처리할 코드를 구현함.

Swift Concurrency : WWDC21에서 새로 소개된 **동시성 프로그래밍 API**
`async`, `await` 키워드를 이용함. await 이후에 사용해야하는 데이터를 heap에 저장해두고, 이후에 다시 데이터를 가져와서 사용함.

**GCD와 Swift Concurrency의 비교**

가독성 / 에러핸들링 / 동기화 처리

기존에는 너무 많은 콜백으로 인해 들여쓰기가 많아지면서 가독성이 저하됨.

```swift
func processImageData(completionBlock: (_ result: Image) -> Void) {
	loadWebResource("dataprofile.txt") { dataResource in
		loadWebResource("imagedata.dat") { imageResource in
			decodeImage(dataResource, imageResource) { imageTmp in
				dewarpAndCleanupImage(imageTmp) { imageResult in 
					completionBlock(imageResult)
				}
			}
		}
	}
}
```

위 코드를 Swift Concurrency를 사용하게되면

```swift
func processImageData() **async** throws -> Image {
	let dataResource  = **try** **await** loadWebResource("dataprofile.txt")
	let imageResource = **try** **await** loadWebResource("imagedata.dat")
	let imageTmp      = **try** **await** decodeImage(dataResource, imageResource)
	let imageResult   = **try** **await** dewarpAndCleanupImage(imageTmp)
	return imageResult
}
```

에러 핸들링 관련

기존에는 에러가 발생할 수 있는 경우마다 에러 핸들링 코드를 추가해줘야했고, 에러핸들링을 해주지 않아도 컴파일에러가 발생하지 않았음.

```swift
func downloadImageWithURL(url: URL,
													session: URLSession,
													completionHandler: @escaping (UIImage?, Error?) -> Void) {
	session.dataTask(with: url) { data, response, error in
		guard let imageData = data else {
			**completionHandler(nil, DownloadManagerError.invalidData)**
			return
		}

		guard let urlResponse = response as? HTTPURLResponse, 
				  urlResponse.statusCode == 200 else {
			**completionHandler(nil, DownloadManagerError.networkFail)**
			return
		}
		
		let image = UIImage(data: imageData)
		completionHandler(image, nil)
	}.resume()

}
```

이걸 Swift Concurrency로 처리하면 에러 핸들링과 결과전달을 분리시킬 수 있다.

```swift
@available(iOS 15.0, *) 
func downloadImageWithURLSwiftConcurrency(url: URL, session: URLSession) async throws -> UIImage {
	let (data, response) = try await session.data(from: url)
	guard let urlResponse = response as? HTTPURLResponse,
			  urlResponse.statusCode == 200 else {
		**throw DownloadManagerError.networkFail**
	}
	
	guard let image = UIImage(data: data) else {
		**throw DownloadManagerError.invalidData**
	}

	return image
}
```

**동기화 처리**

동기화 - 동시에 실행되는 여러 코드에서 하나의 변수에 접근할 수 있는데 이 경우 문제가 발생할 수 있음. 그래서 동시성 프로그래밍에서는 동기화 처리를 잘 해야함.

GCD에서 동기화 처리하기 - DispatchQueue.sync를 통해 순서대로 접근하는 것을 보장하거나 mutex, semaphore를 이용

Swift Concurrency는 컴파일 단계에서 동기화 처리가 제대로 됐는지를 확인함.

데이터 레이스 문제란?

🤔  동시성이란 무엇이며 왜 필요한가 

동시성 프로그래밍 - 동시에 진행되는 것처럼 보이게끔 하는 기법. 

병렬성 프로그래밍 - 실제로 동시에 진행됨.

Swift 5.5에서 도입됨

비동기 함수 : 실행도중 일시적으로 중단될 수 있는 특수한 함수

`async` : 비동기임을 나타낸다. 함수의 파라미터와 반환 화살표 사이에 `async` 를 표기함.

만약 `throws` 키워드가 있다면 이 전에 `async`을 작성한다.

`async` 를 추가하면 반환될때까지 실행이 일시중단된다????

`await` 비동기함수가 리턴을 할 때까지 실행을 일시적으로 중단할 수 있음을 나타낸다.

`await` 를 사용하면 swift가 현재 스레드에서 코드의 실행을 일시중단하고 대신 해당 스레드에서 다른 코드를 실행하기 때문에 이를 yielding the thread(스레드 양보)라고 부른다. `await` 가 있는 코드는 일시중단할 수 있어야 하므로 프로그램의 특정위치에서만 비동기 함수를 호출할 수 있다.

- 비동기 함수/메소드 또는 프로퍼티 바디에 있는 코드
- `@main` 으로 표시된 구조체, 클래스 또는 열거형의 정적 main()메소드에 있는 코드
- 구조화되지 않은 하위 작업의 코드?