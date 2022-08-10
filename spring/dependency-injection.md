# 의존관계 자동 주입
- 생성자 주입
- 수정자 주입 (setter 주입)
- 필드 주입
- 일반 메서드 주입

## 생성자 주입
- 생성자를 통해 의존 관계를 주입
- 생성자 호출 시점에 딱 1번만 호출되는 것이 보장
- 불변, 필수 의존관계에 사용
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
- 생성자가 딱 1개만 있으면 @Autowired를 생략해도 자동 주입

## 수정자 주입(setter 주입)
- setter라 불리는 필드 값을 변경하는 수정자 메서드를 통해서 의존관계를 주입
- 선택, 변경 가능성이 있는 의존관계에 사용
- 자바빈 프로퍼티 규약의 수정자 메서드 방식을 사용하는 방법
````
@Component
public class OrderServiceImpl implements OrderService {

    private MemberRepository memberRepository;
    private DiscountPolicy discountPolicy;
    
    @Autowired
    public void setMemberRepository(MemberRepository memberRepository) {
        this.memberRepository = memberRepository;
    }   
    
    @Autowired
    public void setDiscountPolicy(DiscountPolicy discountPolicy) {
        this.discountPolicy = discountPolicy;
    }
}
````
- @Autowired의 기본 동작은 주입할 대상이 없으면 오류가 발생
- 주입할 대상이 없어도 동작하게 하려면 @Autowired(required = false)로 지정

## 필드 주입
- 필드에 바로 주입
- 코드가 간결하지만 외부에서 변경이 불가능해서 테스트 하기 힘들다는 단점
- DI 프레임워크가 없으면 아무것도 할 수 없음
- 사용하지 말자
- 테스트코드나 스프링 설정을 목적으로 하는 @Configuration 같은 곳에서만 특별한 용도로 사용
````
@Component
public class OrderServiceImpl implements OrderService {
    
    @Autowired
    private MemberRepository memberRepository;
    
    @Autowired
    private DiscountPolicy discountPolicy;
    
}
````

## 일반 메서드 주입
- 일반 메서드를 통해서 주입
- 한번에 여러 필드를 주입 받을 수 있음
- 잘 사용 안함
````
@Component
public class OrderServiceImpl implements OrderService {

    private MemberRepository memberRepository;
    private DiscountPolicy discountPolicy;
    
    @Autowired
    public void init(MemberRepository memberRepository, DiscountPolicy discountPolicy) {
        this.memberRepository = memberRepository;
        this.discountPolicy = discountPolicy;
    }   
    
}
````

### 옵션처리
- @Autowired만 사용하면 required 옵션의 기본값이 true로 되어 있어서 자동 주입 대상이 없으면 오류 발생
- @Autowired(required=false): 자동 주입할 대상이 없으면 수정자 메서드 자체가 호출 안됨
- org.springframework.lang.@Nullable: 자동 주입할 대상이 없으면 null이 됨
- Optional<>: 자동 주입할 대상이 없으면 Optional.empty가 입력

### 생성자 주입 권장 이유
#### 불변
- 대부분의 의존관계 주입은 한번 일어나면 애플리케이션 종료 시점까지 의존관계를 변경할 일이 없음. 오히려 대부분의 의존관계는 애플리케이션 종료 전까지 변하면 안됨(불변해야 함)
- 수정자 주입을 사용하면 setXxx, getXxx 메서드를 public으로 열어두어야 함
- 누군가 실수로 변경할 수 있고, 변경하면 안되는 메서드를 열어두는 것은 좋은 설계 방법이 아님
- 생성자 주입은 객체를 생성할 때 딱 1번만 호출되므로 이후에 호출되는 일이 없음 -> 불변하게 설계할 수 있음

#### 누락
- 생성자 주입을 사용하면 주입 데이터를 누락했을 때 컴파일 오류가 발생
- IDE에서 바로 어떤 값을 필수로 주입해야 하는 지 알 수 있음

#### final 키워드
- 생성자 주입을 사용하면 필드에 final 키워드를 사용할 수 있어 생성자에서 값이 설정되지 않는 오류를 컴파일 시점에서 막아줌
- 수정자 주입을 포함한 나머지 주입 방식은 모두 생성자 이후에 호출되므로 필드에 final 키워드를 사용할 수 없음
- 오직 생성자 주입 방식만 final 키워드를 사용할 수 있음

## 롬복
### @RequiredArgsConstructor
- final이 붙은 필드를 모아서 생성자를 자동으로 만들어 줌
````
@Component
@RequiredArgsConstructor
public class OrderServiceImpl implements OrderService {

    private final MemberRepository memberRepository;
    private final DiscountPolicy discountPolicy;

}
````

## @Autowired 필드 명, @Qualifier, @Primary
- 조회 대상 빈이 2개 이상일 때 해결 방법
  - @Autowired 필드 명 매칭
  - @Qualifier -> @Qualifier끼리 매칭 -> 빈 이름 배칭
  - @Primary 사용

### @Autowired 필드 명 매칭
- 먼저 타입 매칭을 시도하고 그 결과에 여러 빈이 있을때 필드 이름, 파라미터 이름으로 빈 이름을 추가 매칭

### @Qualifier 사용
- 추가 구분자를 붙여줌
- 빈 등록시 @Qualifier를 붙여줌
````
@Component
@Qualifier("mainDiscountPolicy")
public class RateDiscountPolicy implements DiscountPolicy {}
````
- 주입시에 @Qualifier를 붙여주고 등록한 이름을 적어줌
````
@Autowired
public OrderServiceImpl(MemberRepository memberRepository,
    @Qualifier("mainDiscountPolicy") DiscountPolicy discountpolicy) {
    
    this.memberReposiotry = memberRepository;
    this.discountPolicy = discountPolicy;
}
````
- @Qualifier끼리 매칭 -> 빈 이름 매칭 -> 못 찾을 경우 NoSuchBeanDefinitionException 예외 발생

### @Primary 사용
- @Autowired 시 여러 빈이 매칭되면 @Primary가 우선권을 가짐
````
@Component
@Primary
public class RateDiscountPolicy implements DiscountPolicy {}

@Component
public class FixDiscountPolicy implements DiscountPolicy {}
````

#### @Qualifier, @Primary
- 메인 데이터베이스의 커넥션을 획득하는 스프링 빈은 @Primary를 적용해서 조회하는 곳에서 @Qualifier를 지정 없이 편리하게 조회하고,
서브 데이터베이스 커넥션 빈을 획득할 때는 @Qualifier를 지정해서 명시적으로 획득하는 방식으로 사용하면 코드를 깔끔하게 유지할 수 있음
- @Qualifier가 @Primary보다 우선권이 높