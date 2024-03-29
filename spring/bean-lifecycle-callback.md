# 빈 생명주기 콜백
- 데이터베이스 커넥션 풀이나 네트워크 소켓처럼 애플리케이션 시작 시점에 필요한 연결을 미리 해두고 애플리케이션 종료 시점에 연결을 모두 종료하는 작업을 진행하려면, 객체의 초기화와 종료 작업이 필요
#### 스프링 빈의 이벤트 라이브 사이클
> 스프링 컨테이너 생성 -> 스프링 빈 생성 -> 의존관계 주입 -> 초기화 콜백 -> 사용 -> 소멸전 콜백 -> 스프링 종료
- 초기화 콜백: 빈이 생성되고 빈의 의존관계 주입이 완료된 후 추출
- 소멸전 콜백: 빈이 소멸되기 직전에 호출


- 객체의 생성과 초기화를 분리할 것
  - 생성자는 필수정보(파라미터)를 받고 메모리를 할당해서 객체를 생성하는 책임을 가짐
  - 초기화는 이렇게 생성된 값들을 활용해서 외부 커넥션을 연결하는 등 무거운 동작을 수행
  - 따라서 생성자 안에서 무거운 초기화 작업을 함께 하는 것보다는 객체를 생성하는 부분과 초기화 하는 부분을 명확하게 나누는 것이 유지보수 관점에서 좋음


- 싱글톤 빈들은 스프링 컨테이너가 종료될 때 싱글톤 빈들도 함께 종료되기 때문에 스프링 컨테이너가 종료되기 직전에 소멸전 콜백 발생
- 생명주기가 짧은 빈들은 컨테이너와 무관하게 해당 빈이 종료되기 직전에 소멸전 콜백이 일어남


- 스프링은 3가지 빈 생명주기 콜백 지원
  1. 인터페이스(InitializingBean, DisposableBean)
  2. 설정 정보에 초기화 메서드, 종료 메서드 지정
  3. @PostConstruct, @PreDestroy 애노테이션 지원

## 인터페이스 InitializingBean, DisposableBean
- InitializingBean은 afterPropertiesSet() 메서드로 초기화 지원
- DisposableBean은 destroy() 메서드로 소멸을 지원
- 단점
  - 스프링 전용 인터페이스라 해당 코드가 스프링 전용 인터페이스에 의존
  - 초기화, 소멸 메소드의 이름을 변경할 수 없음
  - 내가 코드를 고칠 수 없는 외부 라이브러리에 적용할 수 없음
  - 지금은 거의 사용하지 않음

## 빈 등록 초기화, 소멸 메서드 지정
- 설정 정보에 @Bean(initMethod = "init", destroyMethod = "close") 처럼 초기화, 소멸 메서드를 지정할 수 있음
- 메서드 이름을 자유롭게 줄 수 있음
- 스프링 빈이 스프링 코드에 의존하지 않음
- 코드가 아니라 설정 정보를 사용하기 때문에 코드를 고칠 수 없는 외부 라이브러리에도 초기화, 종료 메서드 적용 가능

## @PostConstruct, @PreDestroy 애노테이션
- 최신 스프링에서 가장 권장하는 방법
- 스프링에 종속적인 기술이 아니라 JSR-250라는 자바 표준
- 컴포넌트 스캔과 잘 어울림
- 외부 라이브러리에는 적용하지 못함. 외부 라이브러리를 초기화, 종료해야 하면 @Bean의 기능을 사용