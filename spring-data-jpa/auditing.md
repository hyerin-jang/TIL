Auditing
=========
- 해당 데이터를 보고 있다가 생성 또는 수정사항이 발생했을 때 자동으로 값을 넣어주는 기능

###1. SpringBootApplication에 @EnableJpaAuditing 어노테이션 추가

###2. Auditing이 필요한 Entity에서 상속받을 BaseEntity를 생성
   ````
    @Getter
    @MappedSuperclass
    @EntityListeners(AuditingEntityListener.class)
    public abstract class BaseEntity {
    
        @CreatedDate
        @Column(updatable = false)
        private LocalDateTime createdDate;
    
        @LastModifiedDate
        private LocalDateTime modifiedDate;
    
        @CreatedBy
        @Column(updatable = false)
        private String createdBy;
    
        @LastModifiedBy
        private String modifiedBy;
    
    }
   ````
   - @MappedSuperclass: Entity에서 Table에 대한 공통 매핑 정보가 필요할 때 부모 클래스에 정의하고 상속받아 해당 필드를 사용하여 중복을 제거
   - @EntityListeners: Entity를 DB에 적용하기 이전, 이후에 커스텀 콜백을 요청할 수 있음
   - AuditingEntityListener.class: Entity 영속성 및 업데이트에 대한 Auditing 정보를 캡처
   - @CreatedDate: 데이터 생성 날짜 자동 저장
   - @LastModifiedDate: 데이터 수정 날짜 자동 저장
   - @CreatedBy: 데이터 생성자 자동 저장
   - @LastModifiedBy: 데이터 수정자 자동 저장

###3. Entitiy에 BaseEntity를 상속
   ````
    @Getter
    @Entity
    @NoArgsConstructor(access = PROTECTED)
    public class User extends BaseEntity {
    
    }
   ````

###4. "@CreateBy", @LastModifiedBy
   - AuditingAware<T>를 등록 하여 직접 정의
   ````
    @Bean
    public AuditorAware<String> auditorProvider() {
    return () -> Optional.of(UUID.randomUUID().toString());
    }
   ````
   - @Configuration이 포함된 클래스에 AuditorAware를 Bean으로 등록하면 빈의 구현 내용에 따라 Auditor를 추출해 해당 필드에 매핑시켜 값을 업데이트
   