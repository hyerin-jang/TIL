### 공통점

- 오픈소스
- db 스키마 변경사항을 관리, 추적하는데  도움을 줌
- 자바기반으로 java 프레인워크에 대한 광범위한 지원 제공
- 데이터베이스 스키마 변경에 대한 버전 마이그레이션 접근 방식을 사용
- Maven 및 Gradle과 같은 빌드 도구와의 통합을 지원
- 다양한 데이터베이스를 지원

### 차이점

|  | Liquibase | Flyway | 비고 |  |
| --- | --- | --- | --- | --- |
| 지원 | - Java 11 ~ | - Java 8 ~ 12
- Gradle 3.0 ~ 6.0 |  |  |
| 변경사항 정의 | - XML, YAML, JSON, SQL 등
- 스크립트 파일명 규칙 없음
- master changelog 하나로 관리됨 | - SQL
- 버전이 증가할 때마다 변경되는 linear database versioning system
- 스크립트 파일명의 규칙이 있어 이를 따라야 함 | Liquibase를 사용하면 데이터베이스에 구애받지 않는 언어로 작업하고 스키마 변경 사항을 다양한 데이터베이스 유형에 쉽게 적용할 수 있음 |  |
  | 변경사항 저장 | - databasechangelog 라는 테이블에 저장 | - flyway_schema_history 라는 기본테이블에 저장 |  |  |
  | 변경 실행 순서 | - 변경 사항이 정의된 순서대로 배포되는 master_changelog 라는 별도의 파일을 사용 | - 실행 순서 변경 어려움
- 파일 이름의 버전 번호와 마이그레이션 유형에 따름 |  |  |
  | 변경사항 롤백 | - 모든 항목을 롤백하거나 특정 마이그레이션을 취소하는 방법을 제공 | - 실행을 취소하는 U(Undo) 마이그레이션 지원
- 유료 | Flyway 승 |  |
  | 변경사항 선택적 배포 | - https://www.liquibase.com/blog/contexts-vs-labels?_ga=2.161326136.488045199.1647282742-629483959.1647282742  를 추가하여 특정 위치에 배포할 수 있음 | - 각 환경 또는 데이터베이스에 대해 다른 구성 파일을 설정해야 함 | Liquibase 승 |  |
  | Java 기반 마이그레이션 |  |  | 둘다 Java 지향적이며 Java 기반 마이그레이션을 제공 |  |
  | 스냅샷 및 데이터베이스 비교 | - 사용자가 데이터베이스의 현재 상태에 대한 스냅샷을 찍을 수 있음 | - 스냅샷 기능을 지원하지 않음 | Liquibase 승 |  |
  | 조건부 배포 | - 사전 조건이라는 추가 기능을 제공
- 사전 조건을 통해 사용자는 데이터베이스의 현재 상태에 따라 변경 사항을 적용할 수 있음 | - 지원안하지만 프로시저를 통해 해결 가능 |  |  |
  | 공식 문서 | - 가독성이 별로다
- 한글 자료 적음
  https://docs.liquibase.com/home.html | - 비교적 가독성 좋음
- 한글 자료 다수 존재https://flywaydb.org/documentation/ |  |  |

### **사용 방법 예시**

- Liquibase
    - XML

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <databaseChangeLog
    	xmlns="http://www.liquibase.org/xml/ns/dbchangelog"
    	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    	xmlns:ext="http://www.liquibase.org/xml/ns/dbchangelog-ext"
    	xmlns:pro="http://www.liquibase.org/xml/ns/pro"
    	xsi:schemaLocation="http://www.liquibase.org/xml/ns/dbchangelog
    		http://www.liquibase.org/xml/ns/dbchangelog/dbchangelog-latest.xsd
    		http://www.liquibase.org/xml/ns/dbchangelog-ext http://www.liquibase.org/xml/ns/dbchangelog/dbchangelog-ext.xsd
    		http://www.liquibase.org/xml/ns/pro http://www.liquibase.org/xml/ns/pro/liquibase-pro-latest.xsd">
        <changeSet id="1" author="Liquibase">
        <createTable tableName="test_table">
               <column name="test_id" type="int">
                     <constraints primaryKey="true"/>
               </column>
               <column name="test_column" type="varchar"/>
        </createTable>
        </changeSet>
    </databaseChangeLog>
    ```

    - SQL

    ```sql
    -- liquibase formatted sql
    
    -- changeset liquibase:1
    CREATE TABLE test_table (test_id INT, test_column VARCHAR, PRIMARY KEY (test_id))
    ```

    - YAML

    ```yaml
    databaseChangeLog:
       - changeSet:
           id: 1
           author: Liquibase
           changes:
           - createTable:
               columns:
               - column:
                   name: test_column
                   type: INT
                   constraints:  
                       primaryKey:  true  
                       nullable:  false  
               tableName: test_table
    ```

    - JSON

    ```yaml
    { 
      "databaseChangeLog": [
      {
        "changeSet": {
          "id": "1",
          "author": "Liquibase",
          "changes": [
            {
              "createTable": {
                "columns": [
                {
                  "column": 
                  {
                    "name": "test_column",
                    "type": "INT",
                    "constraints": 
                  {
                    "primaryKey": true,
                    "nullable": false
                    }
                    }
                  }]
                ,
                "tableName": "test_table"
              }
            }]
          }
        }]
      }
    ```

- Flyway

```sql
drop table if exists sample_entity;

create table sample_entity(
    id   bigint auto_increment,
    name varchar(255),
    primary key (id)
);

INSERT into sample_entity (name) values ('룰루');
INSERT into sample_entity (name) values ('랄라');
INSERT into sample_entity (name) values ('야호');
```

**Reference**

[https://www.baeldung.com/liquibase-vs-flyway](https://www.baeldung.com/liquibase-vs-flyway)

[https://plugins.gradle.org/plugin/org.liquibase.gradle](https://plugins.gradle.org/plugin/org.liquibase.gradle)