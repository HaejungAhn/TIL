## 20장. 업무 규칙

이 장에서 알 수 있는 것

- 핵심업무규칙과 핵심업무데이터의 개념
- 엔티티와 유스케이스의 개념

- 애플리케이션을 업무규칙과 플러그인으로 구분하려면 업무규칙이 실제로 무엇인지를 잘 이해해야만 한다.
### 핵심 업무 규칙과 핵심 업무 데이터
- 업무규칙은 사업적으로 수익을 얻거나 비용을 줄일 수 있는 규칙 또는 절차다.
- 컴퓨터상으로 구현했는지와 상관없이, 업무규칙은 사업적으로 수익을 얻거나 비용을 줄일 수 있어야 한다. 심지어 사람이 수동으로 직접 수행하더라도 마찬가지다.
- 비즈니스에 있어 핵심적이며 규칙을 자동화하는 시스템이 없더라도 업무 규칙은 그대로 존재하기 때문이다.
- 이러한 업무 규칙을 `핵심업무규칙`이라고 부른다.
- 핵심업무규칙에 필요한 데이터를 `핵심업무데이터`라고 부른다. 마찬가지로 자동화되지 않은 경우에도 존재한다.

### 엔티티
- 핵심 업무규칙과 핵심 업무 데이터는 본질적으로 결합되어 있기 때문에 객체로 만들 좋은 후보가 된다.   
그리고 이런 유형의 객체를 이 책에서는 `엔티티`라고 명명한다.
- 핵심 업무 데이터와 핵심 업무 규칙을 하나로 묶어 별도의 소프트웨어 모듈로 만드는 것.
- 컴퓨터 시스템 내부의 객체로서, 핵심 업무 데이터를 기반으로 동작하는 핵심 업무 규칙을 구체화한다.
- 엔티티 객체는 핵심 업무 데이터를 직접 포함하거나 여기에 매우 쉽게 접근할 수 있다.
- 엔티티의 인터페이스는 핵심 업무 데이터를 기반으로 동작하는 핵심 업무 규칙을 구현한 함수들로 구성된다.
- DB, UI, 서드파티 프레임워크에 대한 고려사항들로 인해 오염되어서는 절대 안된다.

### 유스케이스
- 자동화된 시스템이 동작하는 방법을 정의하고 제약함으로써 수익을 얻거나 비용을 줄이는 업무 규칙
- 즉, 자동화된 시스템이 사용되는 방법을 설명한다.
- <u>애플리케이션에 특화된 업무 규칙을 설명한다.</u>
- 엔티티 내부의 핵심 업무 규칙을 어떻게, 언제 호출할지 명시하는 규칙을 담고 있다.
- 시스템이 사용자에게 어떻게 보여지는지를 설명하지 않는다. **사용자와 엔티티 사이의 상호작용을 규정한다.**
- 엔티티가 유스케이스보다 더 고수준이다. 왜냐하면 유스케이스는 단일 애플리케이션에 특화되어 있고, 따라서 해당 시스템의 입출력에 더 가깝기 때문이다. 이와 달리 엔티티는 수많은 애플리케이션에 사용될 수 있도록 일반화된 것이다.

### 요청 및 응답 모델
- 유스케이스는 데이터가 이동하는 방식에 대해 몰라야 한다.