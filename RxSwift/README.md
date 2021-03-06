### ๐ย  Article

- RxSwift ๊ฐ๋ ํ์ํ๊ธฐ(with ํ๊ตญ์ด)
    - [https://ios-development.tistory.com/95](https://ios-development.tistory.com/95)
    - [https://magi82.github.io/ios-rxswift-01/](https://magi82.github.io/ios-rxswift-01/)
- RxSwift ๊ฐ๋ ํ์ํ๊ธฐ(with ์์ด)
    - [RxSwift github - Why Use Rx?](https://github.com/ReactiveX/RxSwift/blob/main/Documentation/Why.md)
    - [RxSwift github - GettingStarted.md](https://github.com/ReactiveX/RxSwift/blob/main/Documentation/GettingStarted.md)

### ๐ย  Book

[RxSwift: Reactive Programming with Swift](https://www.raywenderlich.com/books/rxswift-reactive-programming-with-swift/v4.0)

---

### ๐ย  ์คํฐ๋ ๊ณํ
1. Rx์ Observable, Subject์ ๊ธฐ๋ณธ ๊ฐ๋์ ์ก๋๋ค.
2. ์์  ํ๋ก์ ํธ๋ฅผ ๋ณด๋ฉด์ ๊ฑฐ๊ธฐ์ ์ฌ์ฉ๋๋ operator๋ ๊ธฐํ ๊ฐ๋๋ค์ด ๋์ฌ ๋๋ง๋ค ๊ฒ์ํ๋ค.   
    - [์์ ํ๋ก์ ํธ1](./example-project-01.md)   
    - https://github.com/DroidsOnRoids/RxSwiftExamples
    - https://github.com/marinbenc/ReactiveWeatherExample
    
3. ์ฑ์ ํํ์ด ์ฝ๋๋ค.

---

[Rx๋ ๋ฌด์์ธ๊ฐ?](./what-is-rx.md)

*โ Rx์ ์๋ฏธ์ 4๊ฐ์ง ๊ตฌ์ฑ์์*

- Rx๋ ๋ฐ์ํ ํ๋ก๊ทธ๋๋ฐ ๋ผ์ด๋ธ๋ฌ๋ฆฌ๋ค.
- Rx์ 4๊ฐ์ง ์์๋ก Observable, Subject, Operator, Scheduler๊ฐ ์๋ค.
- ๋ฐ์ํ์ ์ด๋ค ๊ฒ์ ๊ด์ฐฐํ๊ณ  ์๋ค๊ฐ ๋ณํ๊ฐ ๋ฐ์ํ๋ฉด ๊ทธ ๋ณํ์ ๋ฐ์ํด ํ๋ํ๋ค๋ ์๋ฏธ์ด๋ค.
- ๋ฌด์์ ํตํด ๊ด์ฐฐ ํ  ์ ์์๊น? Swift์ KVO๋ property observer๊ฐ ์๋ ๊ฒ์ฒ๋ผ Rx๋ Observable์ ํตํด ๊ด์ฐฐํ  ์ ์๋ค.
- Observable์ โ๊ด์ฐฐ ๊ฐ๋ฅํโ์ด๋ผ๋ ์๋ฏธ๋ฅผ ๊ฐ์ง๊ณ  ์์ผ๋ฉฐ (์ดํดํ๊ธฐ๋ก๋) ๋ฐ์ดํฐ๋ฅผ observer์๊ฒ ๋ฐฉ์ถํ๋ ์ญํ ์ ํ๋ค.
์ด๋ ๋ฐ์ดํฐ๋ฅผ 3๊ฐ์ง ์ด๋ฒคํธ(next, error, complete) ์ค ํ๋๋ก wrappingํด ๋ฐฉ์ถํ๋ฉฐ observer์๊ฒ ์ด๋ค ์ฒ๋ฆฌ๋ฅผ ํด์ผํ ์ง๋ฅผ ์๋ ค์ค. unicast์.
- observer๋ observable์ ๊ตฌ๋(subscribe)ํจ์ผ๋ก์จ ๊ด์ฐฐ์ด ๊ฐ๋ฅํ๋ค.
- Subject๋ Observable์ด์ Observer๋ก์ ๋ค๋ฅธ Observer์ ์ํด ๊ตฌ๋๋์ด ์ง ์๋ ์๊ณ , ์๊ธฐ์์ ์ด ๋ค๋ฅธ Observable์ ๊ตฌ๋ํ  ์๋ ์๋ค. multicast ๊ฐ๋ฅ.
