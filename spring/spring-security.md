Spring Security
================

1. dependency 추가
    ````
    implementation 'org.springframework.boot:spring-boot-starter-security'
    ````
   
2. 설정
   - Security Config class 작성
    ````
    package com.shop.projectlion.global.config;
    
    import org.springframework.context.annotation.Bean;
    import org.springframework.context.annotation.Configuration;
    import org.springframework.security.config.annotation.web.builders.HttpSecurity;
    import org.springframework.security.config.annotation.web.configuration.EnableWebSecurity;
    import org.springframework.security.config.annotation.web.configuration.WebSecurityConfigurerAdapter;
    import org.springframework.security.crypto.bcrypt.BCryptPasswordEncoder;
    import org.springframework.security.crypto.password.PasswordEncoder;
    
    @Configuration
    @EnableWebSecurity
    public class SecurityConfig extends WebSecurityConfigurerAdapter {
    
        @Override
        protected void configure(HttpSecurity http) throws Exception {
            // http 요청에 대한 보안 설정
            // 페이지 권한 설정, 로그인 페이지 설정, 로그아웃 메소드 등에 대한 설정 작성
        }
    
        @Bean
        public PasswordEncoder passwordEncoder() {
            return new BCryptPasswordEncoder();
        }
    }
    ````
    - WebSecurityConfigurerAdapter를 상속받는 클래스에 @EnableWebSecurity 어노테이션을 선언하면 SpringSecurityFilterChain이 자동으로 포함
    - WebSecurityConfigurerAdapter를 상속받아서 메소드 오더라이딩을 통해 보안 설정을 커스터마이징 할 수 있다
    - BCryptPasswordEncoder의 해시 함수를 이용하여 비밀번호를 암호화하여 저장
    

