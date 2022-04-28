# Constructor
- Lombok을 사용하면 생성자를 자동으로 생성할 수 있음

## @NoArgsConstructor
- 파라미터가 없는 기본 생성자 생성
  ### AccessLevel.PROTECTED
  - Entity 나 DTO 를 사용할때 @NoArgsConstructor(access = "AccessLevel.PROTECTED") 를 많이 사용 
  - 기본 생성자의 접근 제어를 PROCTECTED 로 설정해놓게 되면 무분별한 객체 생성에 대해 한번 더 체크할 수 있는 수단이 되기 때문

## @AllArgsConstructor
- 모든 필드 값을 받는 생성자 생성

## @RequiredArgsConstructor
- final이나 @NonNull 인 필드 값만 받는 생성자 생성