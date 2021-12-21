CORS
========
CORS (Cross Origin Resoures Sharing)
-------------------------------------
- 교차 출처 리소스 공유
- 추가 HTTP 헤더를 사용하여 한 출처에서 실행 중인 웹 어플리케이션이 다른 출처의 선택한 자원에 접근할 수 있는 권한을 부여하도록 브러우저에 알려주는 체제
- 웹 어플리케이션은 리소스가 자신의 출처와 다를 때 교차 출처 HTTP 요청을 실행
- 서로 같은 출처: URL 구성 요소 중 scheme(protocol), host(domain), port 세가지가 동일해야 같은 출처
- 서버는 CORS를 위반하더라도 정상적으로 응답을 해주고, 응답의 파기 여부를 브라우저가 결정

CORS의 동작
------------
- 클라이언트가 다른 출처의 리소스를 요청할 때는 HTTP 프로토콜을 사용
- 요청 헤더의 Origin 이라는 필드에 요청을 보내는 출처를 함께 담아서 보냄
- 이후 서버가 이 요청에 대한 응답을 할 때 응답 헤더의 Access-Control=Allow-Origin 이라는 값에 이 리소스를 접근하는 것이 허용된 출처를 내려주고, 
  이후 응답을 받은 브라우저는 자신이 보냈던 요청의 origin과 서버가 보내준 응담의 Access-Comtrol-Allow-Origin을 비교해본 후 이 응답이 유효한 응답인지 아닌지를 결정

접근 제어 시나리오
---------------------
1. Preflight Request
- 가장 일반적
- Preflight: 브라우저가 본 요청을 보내기 전에 보내는 예비 요청
- 브라우저는 요청을 한번에 보내지 않고 예비 요청과 본 요청으로 나누어서 서버로 전송
- HTTP 메소드 중 OPTIONS 메소드가 사용
- 본 요청을 보내기 전에 브라우저 스스로 이 요청을 보내는 것이 안전한지 확인하는 것
- 응답 헤더에 유효한 Access-Control-Allow-Origin 값이 존재하는 가가 중요

2. Simple Request (단순요청)
- 예비 요청을 보내지 않고 바로 서버에게 본 여청을 보낸 후, 서버가 이에 대한 응답 헤더에 Access-Comtrol-Allow-Origin과 같은 값을 보내주면
  그 때 브라우저가 CORS 정책 위반 여부를 검사
- Simple Request를 사용할 수 있는 조건
    1) same-origin (기본값): 같은 출처 간 요청에만 인증 정보를 담을 수 있다.
    2) include: 모든 요청에 인증 정보를 담을 수 있다.
    3) omit: 모든 요청에 인증 정보를 담지 않음
- same-origin이나 include와 같은 옵션을 사용하여 리소스 요청에 인증 정보가 포함된다면, 
  브라우저는 다른 출처의 리소스를 요청할 때 단순히 Access-Comtrol-Allow-Origin만 확인하는 것이 아니라 좀 더 빡빡한 검사 조건을 추가
  
CORS 해결법
-------------
1. 서버에서 Access-Control-Allow-Origin 헤어에 알맞은 값을 세팅
2. webpack-dev-server 라이브러리가 제공하는 프록시 기능을 사용하여 CORS 정책 우회 (로컬에서만 사용가능)
```
module.exports = {
  devServer: {
    proxy: {
      "/api": {
        target: "domain.com",
        changeOrigin: true,
      },
    },
  },
};
```
- 프록시 서버로 인해 domain.com 서버에서는 같은 도메인에서 온 요청으로 인식하여 CORS 에러가 발생하지 않음
3. package.json에 proxy 값을 설정
```
{
  "proxy": "http://localhost:4000"
}
```
