Spring Security
================

1. dependency 추가
    ````
    implementation 'org.springframework.boot:spring-boot-starter-security'
    ````
   
   1. 설정
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
   
               http.formLogin()
                   .loginPage("/login")
                   .defaultSuccessUrl("/")
                   .usernameParameter("email")
                   .failureUrl("/login/error")
                   .and()
                   .logout()
                   .logoutRequestMatcher(new AntPathRequestMatcher("/logout"))
                   .logoutSuccessUrl("/");
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

      <br/>

      1) Web Security 설정
         ````
         web.ignoring().antMatchers("/css/**", "/js/**", "/img/**", "/lib/**"); 
         ````
         - Spring Security에서 해당 요청은 인증 대상에서 제외시킴

         <br/>

      2) HttpSecurity 설정
         - HTTP 요청에 대한 보안을 설정할 수 있음

         ````
         http.authorizeRequests()
          .antMatchers("/member/**").authenticated()
          .antMatchers("/admin/**").authenticated()
          .antMatchers("/**").permitAll();
         ````
         - authorizeRequests(): HttpServletRequest에 따라 접근 제한
         - antMatchers("pathPattern"): 요청 url 경로 패턴 지정
         - authenticated(): 인증된 유저만 접근 허용
         - permitAll(): 모든 유저 접근 허용
         - anonymous(): 인증되지 않은 유저만 접근 허용
         - denyAll(): 모든 유저에 대해 접근을 허용하지 않음

      <br/>
      
      3) 로그인 설정
         - formLogin(): 로그인 설정 진행
         - loginPage("path"): 커스텀 로그인 페이지 경로와 로그인 인증 경로를 등록
         - defaultSuccessUrl("path"): 로그인 인증을 성공하면 이동하는 페이지 등록
         
      <br/>

      4) 로그아웃 설정
         - logout(): 로그아웃 설정 진행
         - logoutRequestMatcher(new AntPathRequestMatcher("path")): 로그아웃 겅로 지정
         - logoutSuccessUrl("/path"): 로그아웃 성공 시 이동할 페이지 등록
         - invalidateHttpSession(true): 로그아웃 성공 시 세션 제거

      <br/>
      
      5) 권한 없는 사용자 점근 시 설정
         - .exceptionHandling()
           .accessDeniedPage("/path"): 권한 없는 사용자가 접근 시 이동할 페이지 등록
   
      
         

