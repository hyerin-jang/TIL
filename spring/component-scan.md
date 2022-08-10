# 컴포넌트 스캔
- 스프링은 설정 정보가 없어도 자동으로 스프링 빈을 등록하는 컴포넌트 스캔 기능 제공
- 의존관계를 자동으로 주입하는 @Autowired 기능 제공
````
@Configuration
@ComponentScan(
    excludeFilters = @Filter(type = FilterType.ANNOTATION, 
    classes = Configuration.class))
public class AutoAppconfig {

}
````
- @ComponentScan을 설정 정보에 붙여줌
- 기존의 AppConfig와는 다르게 @Bean으로 등록한 클래스가 하나도 없어도 됨
````
@Component
public class MemoryMemberRepository implements MemberRepository {}
````
- 컴포넌트 스캔은 @Component 애노테이션이 붙은 클래스를 스캔해서 스프링 빈으로 등록
````
@Component
public class OrderServiceImpl implements OrderService {
    
    private final MemberRepository memberRepository;
    private final DiscountPolicy discountPolicy;
    
    @Autowired
    public OrderServiceImpl(MemberRepository memberRepository, DiscountPolicy discountPolicy) {
        this.memberRepository = memberRepository;
        this.discountPolicy = discountPolicy;
    }
}
````
- @Autowired를 사용하면 생성자에서 여러 의존관계도 한번에 주입받을 수 있음

## @ComponentScan
- @Component가 붙은 모든 클래스를 스프링 빈으로 등록
- 이때 스프링 빈의 기본 이름은 클래스명을 사용하되 맨 앞글자만 소문자 사용 (기본 전략)
- 스프링 빈 이름 직접 지정하고 싶으면 @Component("memberservice") 이런식으로 이름 부여

## @Autowired
- 생성자에 @Autowired를 지정하면 스프링 컨테이너가 자동으로 해당 스프링 빈을 찾아서 주입
- 이때 기본 조회 전략은 타입이 같은 빈을 찾아서 주입

## 탐색 위치와 기본 스캔 대상
- 탐색 시작 위치 지정 가능
````
@ComponentSacan (
    basePackages = "hello.core"
)
````
- basePackages: 탐색할 패키지의 시작 위치를 지정. 이 패키지를 포함해서 하위 패키지를 모두 탐색
  - basePackages = {"hello.core", "hello.service"} 이렇게 여러 시작 위치를 지정할 수도 있음
- basePackageClasses: 지정한 클래스의 패키지를 탐색 시작 위치로 지정
- 지정하지 않으면 @ComponentScan이 붙은 설정 정보 클래스의 패키지가 시작 위치
#### 권장방법
- 패키지 위치를 지정하지 않고 설정 정보 클래스의 위치를 프로젝트 최상단에 두는 것
- 최근 스프링 부트도 이 방법을 기본으로 제공

### 컴포넌트 스캔 기본 대상
- @Component: 컴포넌트 스캔에서 사용
- @Controller: 스프링 MVC 컨트롤러에서 사용
- @Service: 스프링 비즈니스 로직에서 사용
- @Repository: 스프링 데이터 접근 계층에서 사용
- @Configuration: 스프링 설정 정보에서 사용

### 필터
- includeFilters: 컴포넌트 스캔 대상을 추가로 지정
- excludeFilters: 컴포넌트 스캔에서 제외할 대상을 지정

#### FilterType 옵션
- **ANNOTATION**: 기본값, 애노테이션을 인식해서 동작
- ASSIGNABLE_TYPE: 지정한 타입과 자식 타입을 인식해서 동작
- ASPECTJ: AspectJ 패턴 사용
- REGEX: 정규표현식
- CUSTOM: TypeFilter 라는 인터페이스를 구현해서 처리

## 중복 등록과 충돌
### 자동 빈 등록 vs 자동 빈 등록
- 컴포넌트 스캔에 의해 자동으로 스프링 빈이 등록되는데, 그 이름이 같은 경우 스프링은 오류 발생
  - ConflictingBeanDefinitionException 예외 발생
### 수동 빈 등록 vs 자동 빈 등록
- 수동 빈 등록이 우선권을 가짐
- 수동 빈이 자동 빈을 오버라이