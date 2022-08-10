# 객체 지향 원리 적용
## AppConfig
- 애플리케이션의 전체 동작 방식을 구성(config)하기 위해 구현 객체를 생성하고 연결하는 책임을 가지는 별도의 설정 클래스
- 애플리케이션의 실제 동작에 필요한 구현 객체를 생성
- 생성한 객체 인스턴스의 참조(레퍼런스)를 생성자를 통해서 주입(연결)해줌

````
public class MemberRepository implements MemberService {
    private final MemberRepository memberRepository;
    
    public MemberServiceImpl(MemberRepository memberRepository) {
        this.memberRepository = memberRepository;
    }
}
````
- MemberServiceImpl은 MemoryMemberRepository를 의존하지 않음
- MemberRepository 인터페이스만 의존
- MemberServiceImpl 입장에서 생성자를 통해 어떤 구현 객체가 들어올지(주입될지)는 알 수 없음
- MemberServiceImpl의 생성자를 통해 어떤 구현 객체를 주입할지는 오직 외부(AppConfig)에서 결정
- MemberServiceImpl은 의존관계에 대한 고민은 외부에 맡기고 실행에만 집중하면 됨


- 객체의 생성과 연결은 AppConfig가 담당
- DIP 완성: MemberServiceImpl은 MemberRepository인 추상에만 의존하면 됨
- 관심사의 분리: 객체를 생성하고 연결하는 역할과 실행하는 역할이 명확히 분리

## SOLID 원칙 적용
- SRP, DIP, OCP 적용

### SRP 단일 책임 원칙
- 한 클래스는 하나의 책임만 가져야 함
- 클라이언트 객체는 직접 구현 객체를 생성, 연결, 실행하는 다양한 책임을 가지고 있음
- SRP 단일 책임 원칙을 따르면서 관심사를 분리
- 구현 객체를 생성하고 연결하는 책임은 AppConfig가 담당
- 클라이언트 객체는 실행하는 책임만 담당

### DIP 의존관계 역전 원칙
- 프로그래머는 추상화에 의존해야지 구체화에 의존하면 안됨
- AppConfig가 객체 인스턴스를 클라이언트 코드 대신 생성해서 클라이언트 코드에 의존관계를 주입

### OCP
- 소프트웨어 요소는 확장에는 열려있으나 변경에는 닫혀있어야 함
- 애플리케이션을 사용 영역과 구성 영역으로 나눔
- AppConfig가 의존관계를 변경해서 클라이언트 코드에 주입하므로 클라이언트 코드는 변경하지 않아도 됨
- 소프트웨어 요소를 새롭게 확장해도 사용 영역의 변경은 닫혀 있음

## IoC, DI, 컨테이너
### 제어의 역전 IoC(Inversion of Control)
- 구현 객체는 자신의 로직을 실행하는 역할만 담당
- 프로그램의 제어 흐름은 AppConfig가 담당
- 예를들어 MemberServiceImpl은 필요한 인터페이스들을 호출하지만 어떤 구현 객체들이 실행될지 모름
- 프로그램에 대한 제어 흐름에 대한 권한은 모두 AppConfig가 가지고 있음. 심지어 MemberServiceImpl도 AppConfig가 생성
- AppConfig는 MemberServiceImpl이 아닌 MemberService 인터페이스의 다른 구현 객체를 생성하고 실행할 수 있음. 그런 사실도 모른체 MemberServiceImpl은 묵묵히 자신의 로직을 실행
- 프로그램의 제어 흐름을 직접 제어하는 것이 아니라 외부에서 관리하는 것을 제어의 역전(IoC)라고 함

### 프레임워크 VS 라이브러리
- 프레임워크가 내가 작성한 코드를 제어하고 대신 실행하면 그것은 프레임워크
- 내가 작성한 코드가 직접 제어의 흐름을 담당한다면 그것은 라이브러리

### 의존관계 주입 DI(Dependency Injection)
- 의존관계는 정적인 클래스 의존관계와 실행 시점에 결정되는 동적인 객체 의존관계 둘을 분리해서 생각해야 함
#### 정적인 클래스 의존관게
- 클래스가 사용하는 import 코드만 보고 의존관계를 쉽게 판단 가능
- 그러나 이러한 클래스 의존관계 만드로는 실제 어떤 객체가 주입될질 알 수 없음
#### 동적인 객체 인스턴스 의존관계
- 애플리케이션 실행 시점에 실제 생성된 객체 인스턴스의 참조가 연결된 의존관계

- 의존관계 주입: 애플리케이션 실행시점(런타임)에 외부에서 실제 구현 객체를 생성하고 클라이언트에 전달해서 클라이언트와 서버의 실제 의존관계가 연결되는 것
- 객체 인스턴스를 생성하고 그 참조값을 전달해서 연결
- 의존관계 주입을 사용하면 클라이언트 코드를 변경하지 않고 클라이언트가 호출하는 대상의 타입 인스턴스를 변경할 수 있음
- **의존관계 주입을 사용하면 정적인 클래스 의존관계를 변경하지 않고, 동적인 객체 인스턴스 의존관계를 쉽게 변경할 수 있음**

### IoC 컨테이너, DI 컨테이너
- AppConfig 처럼 객체를 생성하고 관리하면서 의존관계를 연결해주는 것
- 의존관계 주입에 초점을 맞추어 최근에는 주로 DI 컨테이너라고 함
- 또는 어셈블러, 오브젝트 팩토리 등으로 불리기도 함

## 스프링으로 전환
- AppConfig에 설정을 구성한다는 뜻의 '@Configuration'을 붙여줌
- 각 메서드에 '@Bean'을 붙여줌
- 이렇게 하면 스프링 컨테이너에 스프링 빈으로 등록

### 스프링 컨테이너
- ApplicationContext를 스프링 컨테이너라고 함
- 스프링 컨테이너는 @Configuration이 붙은 AppConfig를 설정(구성)정보로 사용
- 여기서 @Bean이라 적힌 메서드를 모두 호출해서 반환된 객체를 스프링 컨테이너에 등록
- 이렇게 스프링 컨테이너에 등록된 객체를 스프링 빈이라 함
- 스프링 빈은 @Bean이 붙은 메서드릐 명을 스프링 빈의 이름으로 사용
- 스프링 컨테이너를 통해 필요한 스프링 빈(객체)를 찾아야 함
- applicationContext.getBean() 메서드를 통해 찾을 수 있음