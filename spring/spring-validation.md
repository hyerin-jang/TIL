# Spring Validation

### 1. spring-boot-starter-validation dependency 추가
````
implementation 'org.springframework.boot:spring-boot-starter-validation'
````

### 2. Bean Validation

- @NotEmpty: NULL 체크 및 문자역의 경우 길이가 0인지 검사
- @NotBlank: NULL 체크 및 문자열의 경우 길이가 0인지, 빈 문자열인지(" ") 검사
- @Length(min=, max=): 최소, 최대 길이 검사
- @Email: 이메일 형식인지 검사
- @Max(숫자): 지정한 값보다 작은지 검사
- @Min(숫자): 지정한 값보다 큰지 검사
- @Null: 값이 NULL 인지 검사
- @NotNull: 값이 NULL이 아닌지 검사
- @Pattern(regexp = ): 정규식 검사
- @Size(min=, max=): 길이를 제한할 때 사용
- @Max(value = ): value 이하의 값을 받을 때 사용
- @Min(value = ): value 이상의 값을 받을 때 사용
- @Positive: 값을 양수로 제한
- @PositiveOrZero: 값을 양수와 0만 가능하도록 제한
- @Negative: 값을 음수로 제한
- @NegativeOrZero: 값을 음수와 0만 가능하도록 제한
- @Future: 현재보다 미래
- @Past: 현재보다 과거
- @AssertFalse: false 여부, NULL은 체크하지 않음
- @AssertTrue: true 여부, NULL은 체크하지 않음

### 3. @Valid
- JSR-303 자바 표준 스펙
- 제약 조건이 부여된 객체에 대해 Bean Validator을 이용해서 검증하도록 지시하는 어노테이션
- 특정 ArgumentResolver를 통해 진행되어 컨트롤러 메소드의 유효성 검증만 가능
- 유효성 검증에 실패할 경우 MethodArgumentNotValidException이 발생
- 검증하려는 객체 앞에 @Valid annotaion을 선언하고, 파라미터로 BindingResult 객체를 추가

### 4. @Validated
- 자바 표준 스펙이 아닌 스프링 프레임워크가 제공하는 기능
- @Valid의 기능을 포함하고, 유효성을 검토할 그룹을 지정할 수 있는 기능을 추가로 가지고 있음
- 유효성 검증에 실패할 경우 ConstraintViolationException이 발생

### 5. @Valid vs @Validated
- AOP를 기반으로 스프링 빈의 유효성 검증을 위해 사용되며 클래스에는 @Validated를, 메소드에는 @Valid를 사용

### 6. BingResult
- 검증 오류가 발생할 경우 오류 내용을 보관하는 스프링 프레임워크에서 제공하는 객체
- BindingResult가 있으면 @ModelAttribute에 데이터 바인딩 시 오류가 발생해도 오류 정보를 FieldError 개체를 BindingResult가 담은 뒤 컨트롤러가 호출
- BindingResult 객체의 파라미터 위치는 반드시 @ModelAttribute 어노테이션이 붙은 객체 다음에 위치해야 함
- hasError(): 유효성 검사 실패 시 true 반환