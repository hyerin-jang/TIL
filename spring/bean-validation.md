# Bean Validation

### 1. spring-boot-starter-validation dependency 추가
````
implementation 'org.springframework.boot:spring-boot-starter-validation'
````

### 2. Annotation

- @NotEmpty: NULL 체크 및 문자역의 경우 길이가 0인지 검사
- @NotBlank: NULL 체크 및 문자열의 경우 길이가 0인지, 빈 문자열인지(" ") 검사
- @Length(min=, max=): 최소, 최대 길이 검사
- @Email: 이메일 형식인지 검사
- @Max(숫자): 지정한 값보다 작은지 검사
- @Min(숫자): 지정한 값보다 큰지 검사
- @Null: 값이 NULL 인지 검사
- @NotNull: 값이 NULL이 아닌지 검사