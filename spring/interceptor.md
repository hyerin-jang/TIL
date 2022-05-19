# Spring Interceptor
- Spring interceptor는 dispatcher servlet과 controller 사이에서 HttpRequest, HttpResponse를 가로채는 역할
- 컨트롤러에 요청이 도착하기 전에 해당 유저가 컨트롤러에 접근할 권한이 있는지 세션을 확인한다거나, 처리 결과에 따라서 응답을 돌리고자 하는 경우에 자주 사용