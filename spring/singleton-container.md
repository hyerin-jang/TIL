# 싱글톤 컨테이너
## 싱글톤 패턴
- 클래스의 인스턴스가 딱 1개만 생성되는 것을 보장하는 디자인 패턴
- 객체 인스턴스를 2개 이상 생성하지 못하도록 막아야함
  - private 생성자를 사용해서 외부에서 임의로 new 키워드를 사용햐지 못하도록 막음
````
public class SingletonService {

    // 1. static 영역에 객체를 딱 1개만 생성
    private static final SingletonService instance = new SingletonService();
    
    // 2. public으로 열어서 객체 인스터스가 필요하면 이 static 메서드를 통해서만 조회하도록 허용
    public static SingletonService getInstance() {
        return instance;
    }
    
    // 3. 생성자를 private으로 선언해서 외부에서 new 키워드를 사용한 객체 생성을 못하게 막음
    private SingletonService() {
    }
    
    public void logic() {
        System.out.println("싱글톤 객체 로직 호출");
    }
    
}
````
1. static 영역에 객체 instance를 미리 하나 생성해서 올려둠
2. 이 객체 인스턴스가 필요하면 오직 getInstance() 메서드를 통해서만 조회 가능. 이 메서드를 호출하면 항상 같은 인스턴스를 반환
3. 딱 1개의 객체 인스턴스만 존재해야 하므로 생성자를 private으로 막아서 혹시라도 외부에서 new 키워드로 객체 인스턴스가 생성되는 것을 막음

### 싱글톤 패턴 문제점
- 싱글톤 패턴을 구현하는 코드 자체가 많이 들어감
- 의존관계상 클라이언트가 구체 클래스에 의존 -> DIP를 위반
- 클라이언트가 구체 클래스에 의존해서 OCP 원칙을 위반할 가능성이 높음
- 테스트 어려움
- 내부 속성을 변경하거나 초기화 하기 어려움
- private 생성자로 자식 클래스를 만들기 어려움
- 유연성이 떨어짐
- 안티패턴으로 불리기도 함

## 싱글톤 컨테이너
- 스프링 컨테이너는 싱글톤 패턴의 문제점을 해결하면서 객체 인스턴스를 싱글톤으로 관리
- 스프링 컨테이너는 싱글턴 패턴을 적용하지 않아도 객체 인스턴스를 싱글톤으로 관리
- 스프링 컨테이너는 싱글톤 컨테이너 역할을 함. 이렇게 싱글톤 객체를 생성하고 관리하는 기능을 싱글톤 레지스트리라 함
- 스프링 컨테이너 덕분에 요청이 올 때 마다 객체를 생성하는 것이 아니라 이미 만들어진 객체를 공유해서 효율적으로 재사용 할 수 있음

### 싱글톤 방식의 주의점
- 객체 인스턴스를 하나만 생성해서 공유하는 싱글톤 방식은 여러 클라이언트가 하나의 같은 객체 인스턴스를 공유하기 때문에 싱글톤 객체는 상태를 유지하게(stateful) 설계하면 안됨
- 무상태(stateless)로 설계해야 함
  - 특정 클라이언트에 의존적인 필드가 있으면 안됨
  - 특정 클라이언트가 값을 변경할 수 있는 필드가 있으면 안됨
  - 가급적 읽기만 가능해야 함
  - 필드 대신에 자바에서 공유되지 않는 지역변수, 파라미터, ThreadLocal 등을 사용해야 함
- 스프링 빈의 필드에 공유 값을 설정하면 큰 장애 발생 가능

## @Configuration과 싱글톤
- @Configuration을 붙이면 바이트코드를 조작하는 CGLIB 기술을 사용해 AppConfig 클래스를 상속받음 임의의 다른 클래스를 만들고, 그 다른 클래스를 스프링 빈으로 등록
- 그 임의의 다른 클래스가 싱글톤이 보장되도록 해줌
- AppConfig@CGLIB 예상 코드
````
@Bean
public MemberRepository memberRepository() {
    if (memoryMemberRepository가 이미 스프링 컨테이너에 등록되어 있으면?) {
        return 스프링 컨테이너에서 찾아서 반환;
    } else { // 스프링 컨테이너에 없으면
        // 기존 로직을 호출해서 MemoryMemberRepository를 생성하고 스프링 컨테이너에 등록
        return 반환;
    }
}
````
- @Bean이 붙은 메서드마다 이미 스프링 빈이 존재하면 존재하는 빈을 반환하고 스프링 빈이 없으면 생성해서 스프링 빈으로 등록하고 반환하는 코드가 동적으로 만들어짐
- 덕분에 싱글톤이 보장
- @Bean만 사용해도 스프링 빈으로 등록되지만 싱글톤을 보장하지 않음