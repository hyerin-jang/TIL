### **Flyway란?**

- open-source database migration tool
- desktop, CLI, API 지원
- JVM 이라면 Java API 추천
- 아니면 maven이나 gradle 플러그인 사용
- [Migrate](https://flywaydb.org/documentation/command/migrate) , [Clean](https://flywaydb.org/documentation/command/clean) , [Info](https://flywaydb.org/documentation/command/info) , [Validate](https://flywaydb.org/documentation/command/validate) , [Undo](https://flywaydb.org/documentation/command/undo) , [Baseline,](https://flywaydb.org/documentation/command/baseline) [Repair](https://flywaydb.org/documentation/command/repair) 존재
- 지원되는 DB: Oracle, MySQL, PostgreSQL, H2 등

- Flyway에서는 데이터베이스에 대한 모든 변경 사항을 마이그레이션 이라고 함
- 마이그레이션은 버전이 지정 되거나 반복 가능할 수 있음
- 버전이 지정된 마이그레이션은 일반 및 실행 취소 의 두 가지 형식으로 제공
- 버전 마이그레이션에는 버전, 설명 및 체크섬이 있음
- 버전은 고유해야 함
- 체크섬은 우발적인 변경을 감지하기 위해 존재. 버전 마이그레이션은 가장 일반적인 마이그레이션 유형으로 정확히 한 번 순서대로 적용됨

### **Gradle 로 시작하기**

```yaml
implementation("org.flywaydb:flyway-core")
```

```yaml
flyway:
    enabled: true
    baseline-on-migrate: true # Spring Boot version 2 이상이면 설정 해줄 것!
```

- `src/main/resources/db/migration` 경로에 마이그레이션 파일 생성 (default)
- `spring.flyway.locations` 로 마이그레이션 파일 경로 설정 가능

### ****How Flyway works****

**빈 데이터베이스 일 때**

- db가 비어있어서 flyway가 flyway_schema_history 라는 테이블 새로 생성함
- 이 테이블은 데이터베이스의 상태를 추적하는 데 사용

  ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/12364f5a-944f-4e8f-bcde-87c6c08189e3/Untitled.png)

- 그 후 Flyway는 마이그레이션 을 위해 애플리케이션의 파일 시스템 또는 클래스 경로를 스캔
- 마이그레이션은 버전 번호를 기준으로 정렬 되고 순서대로 적용

  ![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/d2127bfe-6de9-42fa-8fb4-97d0116c570c/Untitled.png)

- 각 마이그레이션이 적용될 때 스키마 기록 테이블이 그에 따라 업데이트됨
- Flyway는 마이그레이션을 위해 애플리케이션의 파일 시스템 또는 클래스 경로를 다시 한 번 스캔
- 마이그레이션은 스키마 기록 테이블에 대해 확인. 버전 번호가 현재로 표시된 버전 중 하나보다 낮거나 같으면 무시
- 현재 보다 높은 버전의 마이그레이션은 pending상태 (사용 가능하지만 적용은 되지 않은 상태)

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/0538c82f-7d7b-4eff-89a9-2bad9f352d05/Untitled.png)

- 이 후 버전 번호별로 정렬되고 순서 대로 실행

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/189dda4e-cf13-4d5d-8cf1-4deb1c728aa4/Untitled.png)

- flyway_schema_history 테이블이 그에 따라 업데이트
- 데이터베이스를 발전시켜야 할 필요성이 발생할 때마다 현재 버전보다 높은 버전 번호로 새 마이그레이션을 생성하기만 하면 됨

**기존 테이블, 데이터가 있을 때**

- V1 script를 생성한다. (빈 script 가능)

****flyway_schema_history 테이블까지 있는 경우****

1. baselineOnMigration을 false로 설정

    ```yaml
    flyway:
        baseline-on-migrate: false
    ```

2. migration script가 남아있지 않은 경우, 히스토리 내역 최신보다 더 높은 버전으로 빈 스크립트를 추가하고 실행
3. migration script가 남아있는 경우 spring.flyway.locations 위치에 기존 히스토리 내역과 일치하는 스크립트를 추가하고 실행

### 버전 관리

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/27bf7735-5606-4d38-8a00-66d822a0112f/Untitled.png)

- **Prefix**: `V` - 버전 마이그레이션, `U` - 실행 취소 마이그레이션, `R` - 반복 가능한 마이그레이션
- **Version**: 밑줄은 런타임에 자동으로 점으로 대체됨. Repeatable migration 에서는 사용되지 않음
- **Separator**: `__` (밑줄 두개)
- **Description**: 밑줄은 단어를 구분. 런타임에 자동으로 공백으로 대체됨

### 주의사항

- Flyway 사용 시에는 직접 스키마 변경을 하면 안됨
    - JPA ddl-auto 옵션 create, create-drop, update 사용 불가
- 버전은 유일해야함

### **Reference**

https://documentation.red-gate.com/fd?_ga=2.46672807.1608954031.1674968950-1654077121.1674968950

[https://ecsimsw.tistory.com/entry/Flyway로-DB-Migration](https://ecsimsw.tistory.com/entry/Flyway%EB%A1%9C-DB-Migration)

[https://www.blog.ecsimsw.com/entry/Flyway-DB-마이그레이션-기존-데이터가-있는-경우](https://www.blog.ecsimsw.com/entry/Flyway-DB-%EB%A7%88%EC%9D%B4%EA%B7%B8%EB%A0%88%EC%9D%B4%EC%85%98-%EA%B8%B0%EC%A1%B4-%EB%8D%B0%EC%9D%B4%ED%84%B0%EA%B0%80-%EC%9E%88%EB%8A%94-%EA%B2%BD%EC%9A%B0)

https://backtony.github.io/spring/2021-10-22-spring-db-1/