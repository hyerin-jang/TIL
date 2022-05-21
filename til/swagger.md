# Swagger
- Open Api Specification(OAS)를 위한 프레임워크
- API 스펙을 명세, 관리할 수 있는 프로젝트/문서
- Java나 Spring에 기본 종속된 라이브러리가 아니기 때문에 Swagger는 버전을 직접 명시해줘야 함

## 의존성 주입
````
implementation 'io.springfox:springfox-boot-starter:3.0.0'
````

## Swagger Annotation
1. @Api(value="~", tags="~")
- 해당 클래스가 Swagger 리소스임을 명시
  1. value: 사용자 지정 이름 기재, tags사용 시 무시
  2. tags: 태그에 여러 이름을 콤마(,) 단위로 기재 시, 여러 태그 정의 가능

2. @ApiOperation(value="~", notes="~")
   - 해당 api에 대한 명세
   1. value: 현재 api에 대한 정의
   2. notes: 현재 api에 대한 Comment


3. @ApiParam(value="~", required="~", example="~")
   - 파라미터에 대한 명세
   1. value: 현재 파라미터에 대한 설명
   2. required: 필수 여부
   3. example: 파라미터 예시

