Thymeleaf
==========

Thymeleaf란?
-------------
- 템플릿 엔진: 미리 정의된 템플릿을 만들고 동적으로 HTML 페이지를 만들어 클라이언트의 전달하는 방식
- 서버사이드 템플릿 엔진: 요청이 올 때마다 서버에서 새로운 HTML 페이지를 만들어 줌
- thymeleaf는 서버사이드 템플릿 엔진의 한 종류
- 스프링에서는 thymeleaf 권장
- thymeleaf의 확장자명은 .html이며 문법은 html 태그 안쪽에 속성으로 사용

Thymeleaf 페이지 레이아웃
-----------------------
1. Thymeleaf Layout Dialect dependency 추가
    - build.gradle
   ````
       implementation 'nz.net.ultraq.thymeleaf:thymeleaf-layout-dialect:3.0.0'
   ````

2. fragment로 만들고 싶은 요소의 태그에 th:fragment="이름"을 삽입
   1. footer
        ````
      <!DOCTYPE html>
        <html xmlns:th="http://www.thymeleaf.org">
          <div th:fragment="footer">
            footer 영역
          </div>
        </html>
      ````
   2. header
       ````
      <!DOCTYPE html>
       <html xmlns:th="http://www.thymeleaf.org">
         <div th:fragment="header">
           header 영역
         </div>
       </html>
      ````
   
3. 만들어진 fragment를 삽입하고자 하는 대상 HTML파일에서 th:replace="파일경로::조각이름"을 통해 삽입
   1. layout
       ````
      <!DOCTYPE html>
       <html xmlns:th="http://www.thymeleaf.org"
             xmlns:layout="http://www.ultraq.net.nz/thymeleaf/layout">
       <head>
         <meta charset="UTF-8">
         <title>Title</title>
    
         <!-- CSS only -->
         <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.1.3/dist/css/bootstrap.min.css" rel="stylesheet" integrity="sha384-1BmE4kWBq78iYhFldvKuhfTAU6auU8tT94WrHftjDbrCEXSU1oBoqyl2QvZ6jIW3" crossorigin="anonymous">
         <link href="/css/shoplayout.css" rel="stylesheet">
         <link href="/css/common.css" rel="stylesheet">
    
         <!-- jQuery, JS, Popper.js, and  -->
         <script src="https://code.jquery.com/jquery-3.5.1.min.js"></script>
         <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.1.3/dist/js/bootstrap.bundle.min.js" integrity="sha384-ka7Sk0Gln4gmtz2MlQnikT1wXgYsOg+OMhuP+IlRH9sENBO0LRn5q+8nbTov4+1p" crossorigin="anonymous"></script>
    
       </head>
       <body>
    
         <div th:replace="fragments/header::header"></div>
         <div layout:fragment="content" class="content">
    
         </div>
         <div th:replace="fragments/footer::footer"></div>
    
       </body>
       </html>
       ````
   
   2. thymeleaf 파일
       ````
      <!DOCTYPE html>
       <html xmlns:th="http://www.thymeleaf.org"
             xmlns:layout="http://www.ultraq.net.nz/thymeleaf/layout"
             layout:decorate="~{layouts/layout}">
    
           <div layout:fragment="content">
               본문 영역 입니다.
           </div>
    
       </html> 
      ````
