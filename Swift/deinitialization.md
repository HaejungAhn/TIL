2022-02-24

---
- deinitializer는 클래스 인스턴스가 메모리에서 해제되기 직전에 자동으로 호출된다.

- 클래스 타입에서만 사용 가능

- **초기화 해제 동작**

- superclass의 deinit은 subclass로 상속된다. subclass의 deinit이 종료되면 **자동**으로(subclass에서 명시하지 않더라도) superclass의 deinit이 호출됨
- deinit에서는 메모리에서 해제되기 직전에 호출되기 때문에 아직 클래스의 모든 프로퍼티에 접근할 수 있다.