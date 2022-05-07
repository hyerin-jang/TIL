# @GeneratedValue

- 기본키(PK) 값에 대한 생성 전략을 제공
- @Id 와 함께 엔티티 또는 매핑된 슈퍼클래스의 기본 키 속성 또는 필드에 적용

## AUTO
- default
- 데이터베이스 방언에 따라 자동 지정

## IDENTITY
- 기본키 생성을 데이터베이스에 위임 
- JPA는 보통 영속성 컨텍스트에서 객체를 관리하다가 commit이 호출되는 시점에 쿼리문을 실행
- IDENTITY 전략에서는 EntityManager.persist()를 하는 시점에 Insert SQL을 실행하여 데이터베이스에서 식별자를 조회
- 영속성 컨텍스트는 1차 캐시에 PK와 객체를 가지고 관리를 하는데 기본키를 데이터베이스에게 위임햇기 때문에 EntityManager.persist()호출 하더라도 데이터베이스에 값을 넣기 전까지 기본키를 모르고 있어 관리가 되지 않음
- IDENTITY 전략에서는 EntityManager.persist()를 하는 시점에 Insert SQL을 실행하여 데이터베이스에서 식별자를 조회하고 영속성 컨텍스트 1차 캐시에 값을 넣어주기 때문에 관리가 가능
- 주로 MySQL, PostgreSQL, SQL Server, DB2에서 사용

## SEQUENCE
- DB가 자동으로 숫자를 generate 해줌
- @SequenceGenerator 어노테이션이 필요
- 오라클, PostgreSQL, DB2, H2 데이터베이스에서 사용 

### @SequenceGenerator
- name: 식별자 생성기 이름 (필수)
- sequenceName: 데이터베이스에 등록되어 있는 시퀀스 이름 
- initialValue: DDL 생성 시에만 사용됨, 시퀀스 DDL을 생성할 때 처음 시작하는 수를 지정 
- allocationSize: 시퀀스 한 번 호출에 증가하는 수. 성능 최적화에 사용됨. 데이터베이스 시퀀스 값이 하나씩 증가하도록 설정되어 있으면 이 값을 반드시 1로 설정해야 함
- catalog, schema: 데이터베이스 catalog, schema 이름

#### allocationSize의 작동방법
1) 최초 persist() 호출시 데이터베이스의 시퀀스 nextValue를 두번 호출하여 시작값과 끝값을 가져옴
2) 어플리케이션에서 시작값이 끝값이 될때까지 시퀀스를 메모리에서 할당
3) 시퀀스를 끝값까지 전부 사용하게 되면 다시 시퀀스를 호출하는데, 여기서 jpa는 allocationSize를 보고 다음 시작값을 계산
- 끝값 = 현재값 + allocationSize, 시작값 = 끝값 - (allocationSize - 1)
- 2~3번 사항을 반복

#### allocaitionSize 설정시 주의할 점
- allocaitionSize를 설정할때는 실제 데이터베이스의 증가값을 고려하여 시작값이 음수가 되지 않게 고려하며 성능 최적화를 해야함

## TABLE
- 키 생성 전용 테이블을 하나 만들어서 데이터베이스 시퀀스를 흉내내는 전략
- @TableGenerator 어노테이션이 필요
- 장점: 모든 데이터베이스에 적용 가능
- 단점: 성능 (성능때문에 권장하지 않음)

### @TableGenerator
- name: 식별자 생성기 이름 (필수)
- table: 키생성 테이블명
- pkColumnName:	시퀀스 컬럼명
- valueColumnName: 시퀀스 값 컬럼명
- pkColumnValue: 키로 사용할 값 이름
- initialValue:	초기 값, 마지막으로 생성된 값이 기준
- allocationSize: 시퀀스 한 번 호출에 증가하는 수(성능 최적화에 사용됨)
- catalog, schema: 데이터베이스 catalog, schema 이름 
- uniqueConstraints(DDL): 유니크 제약 조건을 지정할 수 있음