#Feign
- netflex에서 개발된 Http client binder
- 웹 서비스 클라이언트를 보다 쉽게 작성 가능
- interface를 작성하고 annotation을 선안하기만 하면 사용 가능

## 의존성 추가
````
implementation 'org.springframework.cloud:spring-cloud-starter-openfeign:3.1.0'
````

## Feign 사용
````
@SpringBootApplication
@EnableFeignClients
public class Application {

    public static void main(String... args) {
        SpringApplication.run(Application.class, args);
    }
}
````
````
@FeignClient(value = "example", url = "$")
public interface ExampleClient {

    @GetMapping("/status/")
    void status(@PathVariable("status") int status);
}
````
- @EnableFeignClients 어노테이션 사용
- root package 에 있어야 하며, 그렇지 않은 경우 basePackages 또는 basePackageClasses 를 지정해 줘야 함
- @EnableFeignClients 은 지정된 package 를 돌아다니면서 @FeignClient 를 찾아 구현체를 만들어 줌
