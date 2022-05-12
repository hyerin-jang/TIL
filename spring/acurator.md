# Acurator
- 스프링 부트 애플리케이션의 모니터링이나 매트릭(metric)과 같은 기능을 HTTP와 JMX 엔드포인트를 통해 제공
- 실행 중인 애플리케이션의 내부를 볼 수 있게 하고, 어느 정도까지는 애플리케이션의 작동 방법을 제어할 수 있게 함

## 의존성 추가
````
implementation 'org.springframework.boot:spring-boot-starter-actuator'
````

## 기본 경로 구성
````
management:
  endpoints:
    web:
      base-path: /hear
````
- acturator의 기본 경로는 /acurator
- base-path 에 경로를 설정해줄 수 있음

## endpoint 활성화 / 비활성화
- acturator 기본 설정 시, /health와 /info 만 활성화
- management.endpoints.web.exposure.include 와 management.endpoints.web.exposure.exclude로 노출 여부 제어
````
management:
  endpoints:
    web:
      exposure:
        include: health,info,beans,conditions
        exclude: threaddump, heapdump
````