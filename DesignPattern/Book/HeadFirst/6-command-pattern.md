## 6장. 커맨드패턴

### 들어가며
- 메소드 호출 캡슐화하기
- 원하는 것을 적어서 넣기만 하면 간단하게 처리할 수 있다.
- 어떤 객체가 어떻게 처리하는지에 대해서는 전혀 신경 쓸 필요가 없다.

#### <b>예제 시나리오</b>
- IoT 리모컨 만들기
- 리모컨에는 7개의 슬롯이 있으며 슬롯에 할당된 기능을 켜고 끄는 on/off 버튼을 가지고 있다.
- 슬롯은 서로 다른 가정용 기기에 연결할 수 있으며 추후 더 많은 기기들이 연결될 가능성이 있으므로   
확장 가능하게 만들어야 한다.

#### <b>처음에는 이렇게 생각했다.</b>
- 커맨드 패턴을 모르는 지금은 만들게 되면 아래와 같이 만들 것 같다.
- on, off 기능을 가진 인터페이스(protocol)를 정의하고 조명, 오디오 등의 가정용 기기 클래스들이 서로 동일한 상위타입으로  
묶여질 수 있도록 만든다.
- 그리고 클라이언트에서 상위타입이 가지고 있는 메소드를 호출한다.

- 🤔 <b>이 방식의 문제점은?</b>
    - 인터페이스의 메소드 시그니처가 변경되면, 가정용 기기 클래스의 수만큼 수정이 필요하며 이를 사용하고 있는 곳에도 수정이 필요하다.
    - 인터페이스의 메소드가 추가되면 가정용 기기 클래스의 인터페이스에도 변경이 필요하다.
    - 인터페이스 중 필요없는 기능들에 대해서도 (어쨌든) 코드를 가지고 있긴 해야한다.
    - 모든 가정용 기기 클래스가 on/off 기능을 가지고 있는 것은 아니다. 예를 들어 창고 문에 대한 IoT 기기는 up/down/stop의 기능을 가지고 있는데 이 경우 별개의 인터페이스를 만들어 사용해야할 수도 있다. 큰 역할만 놓고 보면 비슷한 것들이 여러개 생기는 것이다.


#### <b>커맨드 패턴</b>
- 📒 Command 🄽 명령
- 요청 내역을 객체로 캡슐화해서 객체를 서로 다른 요청 내역에 따라 매개변수화할 수 있음.
- 커맨드 객체는 일련의 행동을 특정 리시버와 연결함으로써 요청을 캡슐화한 것이다. 밖에서 볼 때는 어떤 객체가 리시버 역할을 하는지, 그 리시버가 어떤 일을 하는지 알 수 없다.

#### 이 책에서는 위 시나리오와 더불어 좀 더 쉬운 예시를 들어주었는데 바로 식당에서 주문하는 과정이다. 이를 커맨드 패턴과 연관지어 소개함.
- 식당에서 주문하는 과정에 필요한 객체들은 다음과 같다.
    - 손님(Client) : 주문서를 만들어 종업원에게 알려준다.
    - 주문서(Command) : 누가 이 주문을 처리해야하는지 알고 있다. 
        - 여러개의 concrete 주문서가 있다.(바리스타용 주문서, 제빵사용 주문서 등)
        - 각 concrete 주문서는 이 주문서를 받을 receiver가 누구인지 알고 있고, 어떤 순서대로 주문을 처리해야하는지 알고 있다.
    - 종업원(Invoker) : 주문서를 받으면 이를 처리해달라고 요청한다.
        - 📒 Invoker 🄽 호출자, Invoke 🅅 부르다.
    - 바리스타(BaristaReceiver) : 커피 만드는 구체적인 방법을 알고 있다.
    - 제빵사(BakerReceiver) : 빵 만드는 구체적인 방법을 알고 있다.

- 과정은 다음과 같다.
    - ✋ 이 식당은 김밥천국처럼 주문서에 나의 주문을 체크하는 시스템이다 !
    - 손님(Client)이 커피를 선택하고 주문서(Command)를 작성한다.
    - 손님이 종업원을 불러 주문서를 건낸다.
    - 종업원은 (누구에게 요청하는지는 모르겠으나) 주문서를 처리해달라고 요청한다.

- 이걸 코드로 작성해보면 다음과 같다.
    ```Swift
    class BaristaReceiver {
        // 커피 만드는 방법을 알고 있는 객체
    }
    
    protocol Command {
        func execute()
    }

    class CoffeCommand: Command {
        let receiver: BaristaReceiver
        
        init(receiver: BaristaReceiver) {
            self.receiver = receiver
        }
        
        func execute() {
            receiver.dropShot()
            ..생략..
        }
    }
    
    class Invoker {
        var command = Command()
        init() {}
        
        func setCommand(_ command: Command) {
            self.command = command
        }
        
        func requestOrder() {
            command.execute()
        }
    }

    class Client {
        let waiter = Invoker()
        
        func orderCoffee() {
            let barista = BaristaReceiver()
            let coffeeOrder = CoffeCommand(receiver: barista)
            waiter.setCommand(coffeeOrder)
            waiter.requestOrder()
        }
    }
    
    
    ```
- 이걸 클래스 다이어그램으로 그리면 아래와 같다.



