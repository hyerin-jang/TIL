DB 연동
=======
- application.yml 'spring:' 하위에 datasource 추가

###mariadb
````
  datasource:
      driver-class-name: org.mariadb.jdbc.Driver
      url: jdbc:mariadb://localhost:3306/database명
      username: 계정명
      password: 비밀번호
````

###h2
````
    datasource:
      url: jdbc:h2:mem:shop
      driverClassName: org.h2.Driver
      username: sa
      password:
````
