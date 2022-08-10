# Spring 핵심
- 스프링은 자바 언어 기반의 프레임워크
- 자바 언어의 가장 큰 특징 -> 객체 지향 언어
- 스프링은 객체 지향 언어가 가진 강력한 특징을 살려내는 프레임 워크
- 스프링은 좋은 객체 지향 애플리케이션을 개발할 수 있게 도와주는 프레임워크

## 객체 지향 프로그래밍
- 객체 지향 프로그래밍은 컴퓨터 프로그램을 명령어의 목록으로 보는 시각에서 벗어나 여러개의 독립된 단위, 즉 **객체**들의 **모임**으로 파악하고자 하는 것
- 각각의 객체는 메시지를 주고받고, 데이터를 처리 (**협력**)

### 객체 지향의 특징
- 추상화
- 캡슐화
- 상속
- 다형성

#### 다형성
- 역할과 구현을 세상으로 구분
- 장점
  - 클라이언트는 대상의 역할(인터페이스)만 알면 됨
  - 클라이언트는 구현 대상의 내부 구조를 몰라도 됨
  - 클라이언트는 구현 대상의 내부 구조가 변경되어도 영향 받지 않음
  - 클라이언트는 구현 대상 자체를 변경해도 영향받지 않음
- 자바 언어의 다형성을 활용
  - 역할 = 인터페이스
  - 구현 = 인터페이스를 구현한 클래스, 구현 객체
- 객체를 설계할 때 역할과 구현을 명확히 분리
- 객체 설계시 역할(인터페이스)을 먼저 부여하고, 그 역할을 수행하는 구현 객체 만들기

### 객체의 협력
- 클라이언트: 요청, 서버: 응답
- 수 많은 객체 클라이언트와 객체 서버는 서로 협력 관계를 가짐

#### 자바 언어의 다형성
- 오버라이딩
- 다형성으로 인터페이스를 구현한 객체를 실행 시점에 유연하게 변경 가능
- 클래스 상속 관계도 다형성, 오버라이딩 적용 가능

#### 다형성의 본질
- 인터페이스를 구현한 객체 인스턴스를 실행 시점에 유연하게 변경할 수 있음
- 다형성의 본질을 이해하려면 협력이라는 객체 사이의 관계에서 시작해야 함
- 클라이언트를 변경하지 않고, 서버의 구현 기능을 유연하게 변경할 수 있음

#### 역할과 구현을 분리 - 정리
- 실세계의 역할과 구현이라는 편리한 컨셉을 다형성을 통해 객체 세상으로 가져올 수 있음
- 유연하고 변경 용이
- 확장 가능한 설계
- 클라이언트에 영향을 주지 않는 변경 가능
- 인터페이스를 안정적으로 잘 설계하는 것이 중요

#### 역할과 구현을 분리 - 한계
- 역학(인터페이스) 자체가 변하면 클라이언트, 서버 모두에 큰 변경 발생
- 인터페이스를 안정적으로 잘 설계하는 것이 중요

### 스프링과 객체 지향
- 다형성이 가장 중요
- 스프링은 다형성을 극대화해서 이용할 수 있게 도와줌
- 스프링에서 이야기하는 제어의 역전(IoC), 의존관계 주입(DI)은 다형성을 활용해서 역할과 구현을 편리하게 다룰 수 있도록 지원

## 객체 지향 설계 원칙 (SOLID)
### SRP 단일 책임 원칙 (Single Responsibility Principle)
- 한 클래스는 하나의 책임을 가여야 함
- 하나의 책임이라는 것은 모호
  - 클 수도 작을 수도 잇음
  - 문맥과 상황에 따라 다름
- 중요한 기준은 변경. 변경이 있을 때 파급 효과가 적으면 단일 책임 원칙을 잘 따른 것
  - ex) UI 변경, 객체의 생성과 사용을 분리

### OCP 개방-폐쇄 원칙 (Open/Closed Principle)
- 소프트웨어 요소는 확장에는 열려 있으나 변경에는 닫혀 있어야 함
- 다형성을 활용
- 인터페이스를 구현한 새로운 클래스 하나를 만들어서 새로운 기능을 구현
````
public class MemberService {
    private MemberRepository memberRepository = new MemoryMemberRepository(); // 기존코드
    private MemberRepository memberRepository = new JdbcMemberRepository(); // 변경코드
}
````
- 위 상황일 때 MemberService 클라이언트가 구현 클래스 직접 선택
- 구현 객체를 변경하려면 클라이언트 코드를 변경해야 함
- 다형성을 사용했지만 OCP원칙 지켜지지 않음
- 해결방안 -> 객체를 생성하고 연관관계를 맺어주는 별도의 조립, 설정자가 필요

### LSP 리스코프 치환 원칙 (Liskov Substitution Principle)
- 프로그램의 객체는 프로그램의 정확성을 깨뜨리지 않으면서 하위 타입의 인스턴스로 바꿀 수 있어야 함
- 다형성에서 하위 클래스는 인터페이스 규약을 다 지켜야 함

### ISP 인터페이스 분리 원칙 (Interface Segregation Principle)
- 특정 클라이언트를 위한 인터페이스 여러 개가 범용 인터페이스 하나 보다 나음
- ex) 자동차 인터페이스 -> 운전 인터페이스, 정비 인터페이스로 분리
- ex) 사용자 클라이언트 -> 운전자 클라이언트, 정비사 클라이언트로 분리
- 분리하면 정비 인터페이스 자체가 변해도 운전자 클라이언트에 영향을 주지 않음
- 인터페이스가 명확해지고 대체 가능성이 높아짐

### DIP 의존관계 역전 원칙 (Dependency Inversion Principle)
- 프로그래머는 추상화에 의존해야지 구체화에 의존하면 안됨
- 구현클래스에 의존하지 말고 인터페이스에 의존해야 함
- 역할(Role)에 의존하게 해야 한다는 것과 같음. 객체 세상도 클라이언트가 인터페시으세 의존해야 유연하게 구현체를 변경할 수 있음. 구현체게 의존하게 되면 변경이 아주 어려워짐

## 스프링과 객체지향
- 스프링은 아래 기술로 다형성 + OCP, DIP를 가능하게 지원
  - DI(Dependency Injection): 의존관계, 의존성 주입
  - DI 컨테이너 제공
- 클라이언트 코드의 변경 없이 기능 확장