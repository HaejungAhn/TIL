## 5. Singleton 패턴
드디어 디자인 패턴 중 익숙한 패턴이 나왔다.   
#### 👋 <b>들어가며</b>
>아주 단순하지만 구현하기는 까다롭습니다.

처음에는 이 문구를 보고 Swift의 싱글톤 패턴 적용은 비교적 쉽지 않나? 어떤 부분이 까다로운 걸까? 구현이 아니고 사용 시 까다로운 거 아닐까? 라는 생각이 들었다.

참고로 Swift에서의 싱글톤 패턴은 아래와 같이 구현할 수 있음.
```Swift
public class SingletonClass {
    static let shared = SingletonClass()
    private init() {

    }

    func getApple() {
        print("🍎 apple")
    }    
}

// 사용하는 곳에서는 아래와 같이 사용할 수 있다.
SingletonClass.shared.getApple()

// 가장 익숙한 싱글톤은 UserDefaults일 것이다.
UserDefaults.standard.set("🍎 apple", forKey: "appleName")
```

#### <b>🤔 싱글톤은 왜 사용하는걸까요?</b>
- Singleton 🄽 하나씩 일어나는 것
- 단어의 의미에서 추측해볼 수 있다시피 Singleton은 앱에서 딱 한개의 인스턴스만 생성할 수 있으며, 전역에서 이 싱글톤 인스턴스에 접근할 수 있는 패턴이다.
- 한개만 생성했을 때 뭐가 좋을까? 인스턴스가 2개 이상일때 아래와 같은 문제가 발생할 수 있는 경우라면 싱글톤을 쓰는게 좋다.
    -  프로그램의 로직이 제대로 동작하지 않는 경우
        - 예를 들면 음악 앱에서 음악을 재생하는 MusicPlayer 인스턴스가 여러 곳에서 생성되어질 경우 음악이 겹쳐서 재생되어질 가능성도 있을 것이다.
    - 자원을 불필요하게 잡아먹는 경우
        - 이에 해당하는게 (아마도) 로그 기록용 객체일 것 같다.
    - 결과에 일관성이 없어지는 경우
- 전역 접근이면 그냥 전역변수를 만들어도 되지 않음? 이라고 생각할 수도 있다.   
이 책에서는 전역변수와 싱글톤 패턴 적용의 차이가 "필요할 때만 객체를 생성할 수 있는 것"이라고 언급하고 있다.
    - 근데 전역변수라 하더라도 `lazy` 키워드를 붙이면 상관 없지 않나? 아래처럼!
        ```Swift
        import UIKit

        lazy var test: UIView = UIView()

        class ViewController: UIViewController {
            // 이하 생략
        }
        ```
        - 이렇게 만들었더니 `'lazy' cannot be used on an already-lazy global`라는 오류 메세지와 함께 이를 해결하려면 `lazy` 키워드를 제거하라는 메세지가 나왔다.
            >[Properties - Global and Local Variables, Swift Doc](https://docs.swift.org/swift-book/LanguageGuide/Properties.html#ID263)   
            전역 상수와 변수는 항상 lazy하게 계산되어진다. lazy 저장프로퍼티와 달리, 전역 상수/변수는 `lazy` modifier가 필요하지 않다.

        - 😮 오늘도 + 1 획득 !
        - 아무튼 그럼 실제 사용되어질 때만 생성이 되는 것이니까 전역으로 만들어도 되는거네?    
        그럼 이 책에서 언급한, "필요할 때만 객체를 생성할 수 있다"만으로는 전역변수 대신 싱글톤 객체를 사용해야 것에 대한 이유를 제공하지 못하는 거임.
        - "인스턴스를 교체할 수 없다"가 차이인 것 같았으나.. 전역변수를 `let`으로 선언하면 상수이기 때문에 인스턴스 교체 불가함.
        - 그거네!! 전역변수는 "변수"에 제한이 걸려있는 것이고, 싱글톤은 "객체"에 제한이 걸려있는 것임. 말을 좀 정리하긴 해야겠는데, 예를 들어 Apple 이라는 클래스가 있음. 이걸 전역 변수로만 만든다? 그럼 Apple이라는 클래스를 어쨌든 이곳저곳에서 생성할 수 있음. 앱에서 유일한 객체가 아닌거지. 근데 싱글톤으로 만든다 ! 그럼 이곳저곳에서 생성할 수 없음. 앱에서 유일한 객체가 되는거지!
        - 오케이
- 싱글톤의 핵심은 "한 클래스의 인스턴스를 2개 이상 만들 수 없도록 만드는 것"에 있다.
- 다른 언어에서는 싱글톤을 위한 코드를 구현해야한다는데 어떤 코드를 말하는 것일까? 그리고 Swift에서는 왜 싱글톤 코드 구현이 간편하다고 말하는 것일까? 그건 뒷장에 조금 더 예시가 나온다.
- 여기까지 해서 싱글톤의 특징을 정리해보자면~
    - 생성자를 private으로 지정하기 때문에 딱 한개의 클래스 인스턴스만 생성된다.
    - 전역에서 접근할 수 있고, 따라서 전역에서 동일한 데이터를 공유할 수도 있다.

#### <b>싱글톤의 단점은요?</b>
- 싱글톤 패턴은 멀티스레드 환경에서 문제가 발생할 수 있다.
    - 이 책에서는 Java를 예시로 들어줬는데 Java에서는 싱글톤 패턴 구현 시 아래와 같은 코드가 필요할 수도 있다.
        ```Java
        private static SomeInstance uniqueInstance;

        public static SomeInstance getInstance() {
            if (uniqueInstance == null) {
                uniqueInstance = new SomeInstance()
            }
            return uniqueInstance
        }
        ```
    - 만약 두 스레드 `Thread A`와 `Thread B`가 아주 미세한 시간차를 두고 `getInstance()`를 호출하게 되면 어떻게 될까?   
    `Thread A`가 uniqueInstance에서 값을 읽은 후 값이 없다는 것을 알고 SomeInstance를 생성하기 직전에,   
    `Thread B`가 uniqueInstance의 값을 읽게 된다면 어떻게 될까?   
    `Thread A`와 `Thread B` 모두 인스턴스를 생성하게 될 것이다.
    - 이런 경우가 발생할 가능성이 있기 때문에 별도의 처리를 해줘야 한다. Java까지 갈 필요도 없음!   
    Objective-C의 경우 `dispatch_once_t` 라는 것을 사용하여 인스턴스가 1회만 생성되는 것을 보장했다고 한다.
    - Swift는 `static let`으로 선언하는 것만으로도 1회 생성을 보장받는다고 한다. 왜 그러냐! 나중에 기회가 되면 알아보자. 오늘은 싱글톤에 집중할 것!
- 멀티스레드 + 싱글톤 객체가 가지고 있는 공유자원(`var`로 선언된 변수들)을 읽거나 쓸 때 문제가 발생할 수 있다.
    - 이를 Reader-Writer problem이라고 한다.
    - 여러 스레드에서 동시에 특정 자원을 읽는 것은 큰 문제를 야기하지 않지만, 어떤 스레드에서 공유 자원을 수정하고 있는데 다른 스레드에서 이를 읽는다면 이는 문제가 발생할 가능성이 큼.
    - 읽는 것도 문제가 발생할수도..? race condition?



#### <b>🙋🏻‍♀️ 이런 것도 궁금했어요 !</b>
- 218p, private으로 지정된 생성자를 가진 클래스는 확장할 수 없기에..(생략)..
    - 여기서 말하는 "확장"이란 서브클래싱을 말하는 것 같다.
    - 아래 코드로 Swift에서 테스트를 해봄!
        ```Swift
        class SomeClass {
            private init() { }
        }

        class OtherClass: SomeClass {
            init() {
                // 이렇게 하면 접근을 하지 못하기 때문에 에러가 발생한다.
                // 또한 super.init()을 호출하지 못하면 Swift의 클래스 이니셜라이저 규칙에 맞지 않기 때문에 아래와 같은 에러 메세지가 발생한다.
                // 'super.init' isn't called on all paths before returning from initializer
                super.init() 
            }
        }    
        ```
    - 결론 : Swift도 private으로 지정된 생성자(정확히는 designated 생성자)를 가질 경우 서브클래싱할 수 없다.

- 219p, enum으로 싱글턴을 생성해서 해결할 수 있습니다.
    - enum으로 클래스를 어떻게 만드는거지..?

- 218p, 클래스 로더, 리플렉션, 직렬화, 역직렬화

---    
#### <b>📚  참고 사이트</b>
- [[Swift] 싱글톤 패턴(Singleton Pattern)](https://dvlpr-chan.tistory.com/36)