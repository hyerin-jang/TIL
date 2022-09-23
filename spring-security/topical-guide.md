# Spring Security Topical guide

## Spring Security
- Spring 기반의 애플리케이선의 보안(인증, 인가)을 담당하는 스프링 하위 프레임워크
- 인증과 인가에 대한 부분을 filter 흐름에 따라 처리

## 인증 vs 인가
- 인증 (Authentication): 해당 사용자가 본인이 맞는지 확인. Who are you?
- 인가 (Authorization): 인증된 사용자가 요청한 자원에 접근 가능한지 확인. What are you allowed to do?

- Spring Security는 기본적으로 인증 절차를 거친 후에 인가 절차를 진행하게 되며, 인가 과정에서 해당 리소스에 대한 접근 권한이 있는지 확인
- Spring Security에서는 이러한 인증과 인가를 위해 Principal을 아이디로, Credential을 비밀번호로 사용하는 Credential 기반의 인증 방식을 사용
    - Principal(접근 주체): 보호받는 Resource에 접근하는 대상
    - Credential(비밀번호): Resource에 접근하는 대상의 비밀번호


## 인증(Authentication)
### AuthenticationManager
- 인증에 대한 주요 전략 인터페이스로 authenticate() 메소드 단 한개만 가지고 있음
```
public interface AuthenticationManager {

  Authentication authenticate(Authentication authentication)
    throws AuthenticationException;

}
```

#### authenticate()
1) 유효한 principal이면 Authentication을 리턴
2) 유효하지 않으면 AuthenticationException을 throw
    - AuthenticationException은 런타임 예외
    - 이는 대개 어플리케이션의 스타일과 목적에 따라 일반적인 방법으로 핸들링될 수도 있고, 직접 예외를 catch해서 핸들링할 수 있음
3) 결정하지 못하면 null을 리턴

### ProviderManager
- AuthenticationManager의 가장 일반적인 구현체
- AuthenticationProvider는 호출자자 해당 인증유형을 지원하는지 확인할 수 있는 메소드를 제공
- 일련의 AuthenticationProverder 인스턴스들에게 위임
```
public interface AuthenticationProvider {

	Authentication authenticate(Authentication authentication)
			throws AuthenticationException;

	boolean supports(Class<?> authentication);

}
```
- Class<?> 는 실제로는 Class<? extends Authentication>
- authenticate() 메소드에 전달될 어떤 것을 지원하는지 여부를 물음
- ProviderManager는 일련의 AuthenticationProvider들에게 위힘함으로써 동일 어플리케이션 내에서 여러 다른 인증 메커니즘을 제공
- ProviderManager가 특정 인증 유형을 인식하지 못한다면 그 인증은 그냥 스킵됨

<img src="https://t1.daumcdn.net/cfile/tistory/2206624D5935160D2C">
<img src="https://gregor77.github.io/images/spring-security/03/AuthenticationManager.png">


## Customizing Authentication Managers
- 스프링 시큐리티는 일반적인 인증관리 특징들을 빠르게 설정할 수 있는 헬퍼들을 제공
- 가장 많이 사용하는 헬퍼는 AuthenticationManagerBuilder 이고, 메모리, JDBC, LDAP 사용자 정보를 셋팅하고 커스텀 UserDetailService를 추가할 수 있음
```
@Configuration
public class ApplicationSecurity extends WebSecurityConfigurerAdapter {

   ... // web stuff here

  @Autowired
  public initialize(AuthenticationManagerBuilder builder, DataSource dataSource) {
    builder.jdbcAuthentication().dataSource(dataSource).withUser("dave")
      .password("secret").roles("USER");
  }

}
```
```
@Configuration
public class ApplicationSecurity extends WebSecurityConfigurerAdapter {

  @Autowired
  DataSource dataSource;

   ... // web stuff here

  @Override
  public configure(AuthenticationManagerBuilder builder) {
    builder.jdbcAuthentication().dataSource(dataSource).withUser("dave")
      .password("secret").roles("USER");
  }

}
```
- 스프링부트는 별도의 AuthenticationManager를 선언하지 않으면 기본 전역 AuthentiionManager(with just one user)를 제공
- 커스텀 global AuthenticationManager가 필요하지 않다면 이 디폴트만으로 충분
- 필요하다면 지역적으로 보호가 필요한 자원에 대해서만 AuthenticationManager를 추가할 수 있음

## Authentication or AccessControl
- 인증이 되었다면 다음 인가를 처리할 수 있고, 이것의 핵심은 AccessDecisionManager
- 프레임워크에서 제공되는 3가지 구현체가 있고. 이 3가지는 AccessDecisionVoter 체인에 위임
- ProviderManager가 AuthenticationProviders 들에게 위임하는 것과 유사

<img src="https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FYMRYo%2FbtqNaE4hAfi%2F6FcJ85nXrRyDHxOneoDUK1%2Fimg.png">
- FilterSecurityInterceptor(권한을 처리하는 필터)가 AccessDecisionManager 에게 decide(authentication(인증정보), object(요청정보), configAttributes(권한정보)) 3가지 정보를 AccessDecisionManager 에게도 전달
- 이후에 AccessDecisionManager는 자신이 가지고 있는 Voter클래스 에게도 3가지 정보를 전달해서 권한심사 처리를 위임
- Voter들은 3가지의 정보를 기반으로 허용 또는 거부를 판단
- 최종적으로 승인되면 FilterSecurityInterceptor필터에게 전달
- 거부가 되면  AccessDeniedException가 호출되어서 Exception TranslationFilter로 전달하게 되고 예외처리

### AccessDecisionManager
- Access Control or Authorization 결정을 내리는 인터페이스
- 인증이 완료된 사용자가 리소스에 접근하려고 할때 해당 요청을 허용할것인지 판단하는 인터페이스
- 인터페이스 구현체는 3가지를 기본으로 제공
- AccessDecisionManager는 decide 1개와 supports 메서드 2개를 제공
- AccessDecisionManager 를 구현하는데 필수적으로 구현해야하는 메서드는 decide() 메서드
- decide()는 Authentication, ConfigAttribute 등을 전달받으며, 전달받은 parameter에 대한 access 제어를 결졍하는 역할. 이때 만약 거부된다면 AccessDeniedException을 던짐
- supports()는 각각 전달받은 parameter를 처리 가능한지 판단. 전달받고 있는 ConfigAttribute는 권한을 나타내는 문자열을 말함(ex. ROLE_ADMIN)
- Voter라는 개념을 가지고 있음
- 투표를 기반으로 하는 AccessDecisionManager는 AccessDecisionVoter를 투표에 이용
```
// AffirmativeBased 클래스에서 decide 메서드

@Override
@SuppressWarnings({ "rawtypes", "unchecked" })
public void decide(Authentication authentication, Object object, Collection<ConfigAttribute> configAttributes)
		throws AccessDeniedException {
	int deny = 0;
	for (AccessDecisionVoter voter : getDecisionVoters()) {
		int result = voter.vote(authentication, object, configAttributes);
		switch (result) {
		case AccessDecisionVoter.ACCESS_GRANTED:
			return;
		case AccessDecisionVoter.ACCESS_DENIED:
			deny++;
			break;
		default:
			break;
		}
	}
	if (deny > 0) {
		throw new AccessDeniedException(
				this.messages.getMessage("AbstractAccessDecisionManager.accessDenied", "Access is denied"));
	}
	// To get this far, every AccessDecisionVoter abstained
	checkAllowIfAllAbstainDecisions();
}
```

### AccessDecisionVoter
- Spring Security는 투표를 기반으로 request에 대한 access에 대한 승인 여부를 결정
- Voter는 의사결정을 내리는데 사용 하며 여러개의 Voter를 가질 수 있음
- 스프링 시큐리티가 기본적으로 3개의 인터페이스 구현체를 제공
    - AffirmativeBased: 여러 Voter중에 하나라도 허용되면 허용 (기본 전략)
    - ConsensusBased: 다수결
    - UnanimousBased: 만장일치
- 모든 Voter가 허용하지 않는다면 예외를 발생
- vote()는 소스에 정의된 1, 0, -1 값을 반환하며, 각각은 승인, 보류, 거부를 나타냄
- 승인 결정에 대한 의견이 없는 경우 ACCESS_ABSTAIN, 승인인 경우는 ACCESS_DENIED를 거부일 경우는 ACCESS_GRANTED를 반환

```
public interface AccessDecisionVoter<S> {

   int ACCESS_GRANTED = 1;
   int ACCESS_ABSTAIN = 0;
   int ACCESS_DENIED = -1;

   int vote(Authentication authentication, S object, Collection<ConfigAttribute> attributes);
   boolean supports(ConfigAttribute attribute);
   boolean supports(Class<?> clazz);
}
```

- AccessDecisionVoter를 보면 AccessDecisionManager가 권한심사를 맡기는데 vote라는 메소드에도 authentication(인증정보), object(요청정보), configAttributes(권한정보) 들을 파라미터로 받고 있음
- 실질적인 권함심사를 한다고 보면됨

## Web Security
- 웹 티어에서의 스프링 시큐리티는 서블릿 필터에 기반
- 클라이언트가 요청을 보내면 컨테이너는 요청 URI의 경로에 기초해서 어떤 필터와 서블릿이 적용될지 결정
- 하나의 서블릿이 하나의 요청을 처리하지만, 필터들은 체인을 형성하고 순서가 정해짐
- 필터는 요청 자체를 저리하기 원한다면 체인의 나머지를 거부할 수 있음
- 필터는 요청 또는 응답을 수정할 수 있음
- 필터의 순서는 매우 중요
- 스프링 부트는 두가지 메커니즘으로 이를 관리한
    1) 필터 빈에 @Order 를 사용하거나 Ordered를 구현
    2) API일부로써 순서를 가지는 FiterRegistraionBean의 일부가 되는 것



- 스프링 시큐리티는 하나의 Filter로 설치되고, 이는 FilterChainProxy로 완성
- 스프링 부트에서 security filter는 ApplicationContext에 잇는 @Bean 이고, 모든 요청에 대해 적용
- SecurityProperties.DEFAULT_FILTER_ORDER로 정의된 위치에 설치되고, 이는 FilterRegistrationBean.REQUEST_WRAPPER_FILTER_MAX_ORDER에 의해 고정
- 컨테이너에서 보면 Spring Security는 단일 필터이지만, 그 안에는 특별한 역할을 하는 추가 필터들이 있음
  <img src="https://github.com/spring-guides/top-spring-security-architecture/raw/main/images/security-filters.png">


- 실제로 시큐리티 필터에는 간접적인 계층이 하나 더 있음
- 일반적으로 컨테이너에는 DelegatingFilterProxy로 설치되며 Spring @Bean일 필요는 없음
- 프록시는 항상 @Bean인 FilterChainProxy에 위임
- 보통 springSecurityFilterChain 이라는 고정된 이름
- FilterChainProxy는 내부적으로 필터 체인으로 정렬된 모든 보안 논리를 포함
- 모든 필터에는 동일한 API가 있고(서블릿 스펙의 필터 인터페이스 구현), 나머지 필터는 모두 거부할 수 있음
- 동일한 최상위 FilterChainProxy와 컨테이너에 알려지지 않은 모은 것? 에 스프링 시큐리티에 의해 관리되는 여러 개의 필터 체인이 있을 수 있음
- 스프링 시큐리티 필터는 필터체인 목록을 포함하고, 클라이언트 요청을 그와 매치되는 첫번째 필터에 dispatch
- 아래 그림은 요청 path 매칭에 기반한 dispatch 상황을 나타냄(/foo/** 는 /** 보다 이전에 매칭)
- 이는 일반적인 방법이지만 요청을 매칭하는 유일한 방법은 아님
- 이 dispatch 프로세서의 중요한 점은 요청을 처리하는 체인은 하나의 체인이라는 것

<img src="https://github.com/spring-guides/top-spring-security-architecture/raw/main/images/security-filters-dispatch.png">

- 스프링 시큐리티 내부의 모든 필터가 컨테이너에 알려지지 않는다는 것이 중요
- 특히, Spring Boot 어플리케이션에서 필터 유형의 모든 @Bean은 기본적으로 컨테이너에 자동으로 등록
- 따라서 사용자 정의 필터를 보안 체인에 추가하려면 컨테이너를 @Bean으로 만들지 않거나 FilterRegistrationBean으로 포장하여 컨테이너 등록을 명시적으로 비활성화해야 함

## Creating and Customizing Filter Chains
- 스프링 부트 애플리케이션의 기본 폴백 필터 체인(/** 요청 일치 항목이 있는 체인)에는 보안 속성의 순서가 미리 정의되어 있음
- BASIC_AUTH_ORDER.security.basic.enabled=false를 설정하여 완전히 끌 수도 있고, 예비로 사용할 수도 있고 낮은 순서로 다른 규칙을 정의할 수도 있음
- 후자를 수행하려면 WebSecurityConfigurerAdapter(또는 WebSecurityConfigurer) 유형의 @Bean을 추가하고 아래와 같이 클래스에 @Order를 사용
```
@Configuration
@Order(SecurityProperties.BASIC_AUTH_ORDER - 10)
public class ApplicationConfigurerAdapter extends WebSecurityConfigurerAdapter {
  @Override
  protected void configure(HttpSecurity http) throws Exception {
    http.antMatcher("/match1/**")
     ...;
  }
}
```
- 이 빈은 Spring Security가 새로운 필터 체인을 추가하고 폴백 전에 정렬
- 많은 애플리케이션은 다른 리소스와 비교하여 한 리소스 집합에 대해 완전히 다른 액세스 규칙을 가지고 있음
- 각 리소스 집합에는 고유한 순서와 고유한 요청 일치자가 있는 고유한 WebSecurityConfigurerAdapter가 있음
- 일치 규칙이 겹치면 가장 먼저 정렬된 필터 체인이 우선

## Request Matching for Dispatch and Authorization
- 보안 필터 체인(동일하게 WebSecurityConfigurerAdapter)은 http요청을 처리할 지를 결정하는 request matcher를 가지고 있음
- 특정 필터 체인이 결정되면 다른 체인은 적용되지 않음
- 하지만, 필터 체인 내에서 HttpSecurity configurer 에 추가적인 matcher를 설정하여 세분화된 권한 제어를 할 수 있음
```
@Configuration
@Order(SecurityProperties.BASIC_AUTH_ORDER - 10)
public class ApplicationConfigurerAdapter extends WebSecurityConfigurerAdapter {
  @Override
  protected void configure(HttpSecurity http) throws Exception {
    http.antMatcher("/match1/**")
      .authorizeRequests()
        .antMatchers("/match1/user").hasRole("USER")
        .antMatchers("/match1/spam").hasRole("SPAM")
        .anyRequest().isAuthenticated();
  }
}
```

## Combining Application Security Rules with Actuator Rules
- 스프링 시큐리티 어플리케이션에 스프링 액츄에이터를 추가하면 자동으로 액츄에이터 endpoints에 추가 필터 체인이 생기며, 기본 SecurityProeprties폴백 필터보다 5 작은 순서(ManagementServerProperties.BASIC_AUTH_ORDER)로 적용되므로 폴백 전에 참조
- 엑츄에이터 기본보안설정을 선호하는 경우는 액츄에이터보다 +1 로 순서 설정
```
@Configuration
@Order(ManagementServerProperties.BASIC_AUTH_ORDER + 1)
public class ApplicationConfigurerAdapter extends WebSecurityConfigurerAdapter {
  @Override
  protected void configure(HttpSecurity http) throws Exception {
    http.antMatcher("/foo/**")
     ...;
  }
}
```
- 웹 계층의 Spring Security는 현재 Servlet API에 연결되어 있으므로 임베디드든 아니든 서블릿 컨테이너에서 애플리케이션을 실행할 때만 실제로 적용할 수 있음
- 그러나 Spring MVC나 나머지 Spring 웹 스택에 연결되어 있지 않으므로 JAX-RS를 사용하는 것과 같은 모든 서블릿 애플리케이션에서 사용할 수 있음 (????)

## Method Security
- 메소드 보안을 위해서는 먼저 아래와 같이 설정
```
@SpringBootApplication
@EnableGlobalMethodSecurity(securedEnabled = true)
public class SampleSecureApplication {
}
```
- 해당 보안 메소드에 @Secured 어노테이션을 사용한다.
```
@Service
public class MyService {

  @Secured("ROLE_USER")
  public String secure() {
    return "Hello Security";
  }

}
```
- caller가 권한이 없다면 AccessDeniedException 이 발생
- @PreAuthorize 및 @PostAuthorize 어노테이션으로 메서드 매개변수 및 반환값에 대한 참조가 포함된 식을 작성 가능
- 웹 보안과 메소드 보안을 같이 사용하는 것이 드문 일은 아님
- 필터 체인은 인증 및 로그인 페이지로의 리다이렉션 등과 같은 사용자 경험 기능을 제공하며, 메소드 보안은 보다 세부적인 수준의 보호기능을 제공

## Working with Threads
- 스프링 시큐리티는 근본적으로 thread bound
- 현재의 인증된 principal을 다양한 downstream consumer들이 이용할 수 있게 해야하기 때문
- 기본 빌딩 블록은 Authentication을 포함 할 수 있는 SecurityContext
- 사용자가 로그인하면 명시적으로 인증된 Authentication이 됨
- SecurityContextHolder의 정적 메소드를 통해 항상 SecurityContext에 액세스하고 조작할 수 있음
- SecurityContextHolder는 ThreadLocal
```
SecurityContext context = SecurityContextHolder.getContext();
Authentication authentication = context.getAuthentication();
assert(authentication.isAuthenticated);
```
- 코드에서 이 작업을 하는 것은 일반적이지 않지만, 사용자 정의 인증필터를 작성해야 하는 경우는 유용할 수 있음
- 웹 엔드포인트에서 현재 인증된 사용자엑 액세스해야하는 경우 @RequestMapping에서 메소드 매개변수로 사용할 수 있음

```
@RequestMapping("/foo")
public String foo(@AuthenticationPrincipal User user) {
  ... // do stuff with user
}
```
- 이 어노테이션은 현재 Authentication을 SEcurityContext에서 꺼내서 getPrincipal() 메소드를 호출하여 메소드 매개변수를 생성
- Principal의 유형은 인증을 확인하는데 사용되는 AuthenticationManager에 따라 다르므로 사용자 데이타에 대한 type safe 한 참조를 얻는 것이 유용
- 스프링 시큐리티가 사용중이면, HttpServletReqeust의 Principal은 Authentication 유형이 될 것이므로 직접 사용할 수도 있음

```
@RequestMapping("/foo")
public String foo(Principal principal) {
  Authentication authentication = (Authentication) principal;
  User = (User) authentication.getPrincipal();
  ... // do stuff with user
}
```
- 이것은 스프링 시큐리티가 사용되지 않을 때 작동하는 코드를 작성해야 할 때 유용
- Authentiction 클래스를 로드하는 것에 대해 좀 더 방어해야함

## Processing Secure Methods Asynchronously
- SecurityContext는 쓰레드 바운드이므로 보안 메서드를 호출하는 백그라운드를 수행하려는 경우 @Async를 사용하면 컨텍스트가 전달되는 시 확인해야 함
- 이것은 백그라운드에서 실행되는 작업(Runnable, Callable)으로 SecurityContext를 래핑하는 것으로 귀결
- 스프링 시큐리티는 Runnable과 Callable에 대한 래퍼와 같은 좀 더 쉬운 헬퍼를 제공
- SecurityContext를 @Async메소드에 전파하려면 AsyncConfigureer를 제공하고 Executor가 올바른 유형인지 확인해야 함
```
@Configuration
public class ApplicationConfiguration extends AsyncConfigurerSupport {

  @Override
  public Executor getAsyncExecutor() {
    return new DelegatingSecurityContextExecutorService(Executors.newFixedThreadPool(5));
  }

}
```
