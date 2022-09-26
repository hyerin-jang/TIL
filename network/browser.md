## Q. 브라우저에서 주소창까지 url을 입력한 뒤 화면이 나오기까지의 과정을 최대한 구체적으로 설명하시오.

브라우저에 url을 입력한 후 과정을 개념부터 세세하게 설명하자면 책 한권으로도 부족할 지도 모른다. 그렇기 때문에 이번 주제는 신입 개발자라도 이정도는 알아야 한다고 생각하는 부분을 중점으로 설명하겠다.

## **전체 Flow**

우선 전체적인 flow를 간단하게 살펴보면 다음과 같다.

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/c3aab63a-d6b8-4e50-b91f-5ce8ebb937ba/Untitled.png)

1. 브라우저에 URL을 입력하면 DNS에 URL에 해당하는 IP주소를 요청한다.
2. DNS는 URL에 해당하는 IP주소를 브라우저에 응답한다.
3. 브라우저는 DNS에게 응답받은 IP 주소와 일치하는 서버와 연결하기 위해 TCP 연결을 진행한다.
4. TCP 연결이 완료되면 브라우저는 웹서버에 요청 메세지를 보낸다.
5. 서버는 브라우저의 요청을 받아 처리한 후, 응답 메세지를 브라우저에 보낸다.

## **세부 Flow**

위에 설명한 전체 Flow에서 파트별로 나눠서 세부적인 Flow를 설명하겠다.

### **Part 0. IP 주소 탐색**

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/bf8ed4ce-9e5d-4bdb-a32f-a989428f96e1/Untitled.png)

- 웹사이트에 접근하기 위해서는 웹서버의 IP 주소가 필요하다. IP 주소가 아닌 문자로된 도메인 주소를 입력해도 웹사이트에 접근할 수 있는 것은 웹사이트가 DNS가 도메인에 해당하는 IP 주소를 응답받아 서버에 접속하기 때문이다.
1. 브라우저는 캐싱된 IP 주소 데이터가 있는지 먼저 확인을 하고 있을 경우 해당 IP 주소로 이동한다.
2. 캐싱된 데이터가 없다면 DNS에 IP 주소를 요청한다.

   2-1. 인터넷 서비스 제공업체가 관리하는 Local DNS로 이동한다.

   2-2. Local DNS에 해당하는 IP 주소가 있으면 이를 응답한다.

3. Local DNS에 IP 주소가 없을 경우

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/6f917cbf-bdef-42de-be6e-c9b9d75f9261/Untitled.png)

3-1. Local DNS는 Root DNS에 IP 주소를 요청하고, 있을 경우 Local DNS에 응답한다.

3-2. Local DNS는 Top-Level DNS에 IP 주소를 요청하고, 있을 경우 Local DNS에 응답한다.

3-3. Local DNS는 Second-Level DNS에 IP 주소를 요청하고, 있을 경우 Local DNS에 응답한다.

3-4. Local DNS는 Third-Level DNS에 IP 주소를 요청하고, 있을 경우 Local DNS에 응답한다.

### **Part 1. 서버와 연결**

- 인터넷 통신의 대부분은 데이터를 작게 쪼개서 보내는 패킷 통신을 기본으로 한다. 패킷 통신을 위해 TCP/IP 프로토콜이 있다.
    - **TCP (Transmission Control Protocol)**: 데이터 전송은 패킷이라는 작은 단위로 쪼개서 보내는데, 이는 순서가 바뀌거나 빠지는 패킷이 발생할 수 있다. TCP의 역할은 이러한 에러 없이 패킷이 순서대로, 빠짐없이 신뢰할 수 있게 전달 되었는지 보증해 준다.
    - **IP (Internet Protocol)**: 패킷을 목적지로 보내는 역할로 TCP와는 다르게 순서나 누락을 신경쓰지 않는다.

- **HTTP 일 경우**

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/fa76e234-12d1-44cc-a6bd-a60145167242/Untitled.png)

- 브라우저는 클라이언트와 서버와의 데이터 통신을 위해 TCP 소켓 연결을 진행한다. 이는 3-way-handshake 라는 과정을 통해 이루어 진다.
1. 클라이언트가 서버에 접속을 요청하는 SYN(Synchronize Sequence Number) 패킷을 보낸다.
2. 서버는 SYN 요청을 받고, 클라이언트에세 요청을 수락한다는 ACK(Acknowledgment)와 SYN flag가 설정된 패킷을 발송한다.
3. 서버에게 패킷을 전달받은 클라이언트는 서버에게 응답확인했다는 뜻으로 ACK 패킷을 보낸다.
4. 서버와의 연결이 완료된다.

- **HTTPS 일 경우**

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/ff8ec648-3494-40da-b645-9aacd1b43909/Untitled.png)

- HTTPS로 통신할 경우 브라우저와 서버는 암호화된 데이터를 교환하기 위해 TSL-Handshake 과정을 추가로 거친다.
1. HTTP로 통신할 때와 동일
2. HTTP로 통신할 때와 동일
3. HTTP로 통신할 때와 동일
4. 클라이언트가 서버로 "헬로" 메시지를 전송하면서 핸드셰이크를 개시한다. 이 메시지에는 클라이언트가 지원하는 TLS 버전, 지원되는 암호 제품군, 그리고 "클라이언트 무작위"라고 하는 무작위 바이트 문자열이 포함된다.
5. 클라이언트 헬로 메시지에 대한 응답으로 서버가 서버의 SSL 인증서, 서버에서 선택한 암호 제품군, 그리고 서버에서 생성한 또 다른 무작위 바이트 문자열인 "서버 무작위"를 포함하는 메시지를 전송한다.
    1. 클라이언트가 서버의 SSL 인증서를 인증서 발행 기관을 통해 검증한다. 이를 통해 서버가 인증서에 명시된 서버인지, 그리고 클라이언트가 상호작용 중인 서버가 실제 해당 도메인의 소유자인지를 확인한다.
6. 클라이언트가 "예비 마스터 암호"라고 하는 무작위 바이트 문자열을 하나 더 전송한다. 예비 마스터 암호는 공개 키로 암호화되어 있으며, 서버가 개인 키로만 해독할 수 있다. (클라이언트는 서버의 SSL 인증서를 통해 공개 키를 받는다.)
    1. 서버가 예비 마스터 암호를 해독한다.
    2. 클라이언트와 서버가 모두 클라이언트 무작위, 서버 무작위, 예비 마스터 암호를 이용해 세션 키를 생성한다. 모두 같은 결과가 나와야 한다.
7. 클라이언트가 세션 키로 암호화된 "완료" 메시지를 전송한다.
8. 서버가 세션 키로 암호화된 "완료" 메시지를 전송한다.
9. 핸드셰이크가 완료되고, 세션 키를 이용해 통신이 계속 진행된다.

### **Part 2. HTTP Request & Response**

- **Request & Response 구조**

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/5a9973f1-0b19-4edf-8783-4162f2e28609/Untitled.png)

- **Request**

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/06e1b485-5ffe-4d83-acc9-7588ca549f12/Untitled.png)

- Start line
    - Method: URL을 이용하면 클라이언트에서 서버에 특정 데이터를 요청할 수 있는데, 요청하는 데이터에 특정 동작을 수행하고 싶을 때 HTTP 요청 메서드(Http Request Methods)를 이용한다.
      일반적으로 HTTP 요청 메서드는 GET, POST, PUT, PATCH, DELETE 가 있다.
    - Path: 가져오려는 리소스의 경로로, 포트 번호를 제거한 리소스의 URL을 주로 사용한다.
    - Version of the protocol: 프로토콜 버전. HTTP/1.1, HTTP2 등이 있다.
- Request Headers: 두 번째 줄 부터는 헤더. 요청에 대한 정보를 담고 있다.
- Request Body: 헤더에서 한 줄 띄고 본문이 시작된다. 본문은 요청을 할 때 함께 보낼 데이터를 담는 부분이다.

- **Response**

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/1cd32d07-0446-462e-b3eb-088b2d3d447f/Untitled.png)

- Start line
    - Version of the protocol: 프로토콜 버전
    - Status Code: 요청의 성공 여부와 그 이유를 나타내는 상태 코드
        - 2xx - 성공
        - 3xx - 리다이렉션
        - 4xx - 클라이언트 에러
        - 5xx - 서버 에러
- Status Message: 상태코드에 대한 짧은 설명을 나타내는 상태 메시지
- Response Header: 요청 헤더와 비슷한 HTTP 헤더
- Response Body: 응답 메시지에 HTML이 담겨 있다. 이 HTML을 받아 브라우저가 화면에 렌더링한다.

### **Part 3. 서버에서 요청 처리**

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/a8069532-cbe0-4cf4-9fb9-7c7ebf5bd3e8/Untitled.png)

- 웹 서버는 클라이언트가 요청한 웹 페이지가 정적 페이지라면 웹 서버는 정적 페이지를 응답한다. 클라이언트가 요청한 웹 페이지가 동적 페이지라면 WAS라는 미들웨어에게 요청 처리를 맡긴다. 그리고 WAS에서 처리한 결과를 클라이언트에게 응답한다.
- **웹 서버 (Web Server)**
  웹 서버는 웹 페이지, 사이트 또는 앱을 저장하는 프로그램을 말한다. 서버는 클라이언트에서 요청한 HTTP 메시지를 확인한 후, 이에 맞는 데이터를 처리한 뒤에 다시 클라이언트에게 응답한다. 대표적인 웹 서버의 종류는 아파치 웹 서버(Apache Web Server), GWS, IIS 등이 있다.
- **WAS (Web Application Server)**
  WAS는 사용자 컴퓨터나 장치에 웹 어플리케이션을 수행해주는 미들웨어이다. 웹 서버와 웹 컨테이너가 합쳐진 형태이며, 웹 컨테이너는 JSP, Servlet을 실행시킬 수 있는 소프트웨어이다. 즉 WAS는 JSP, Servlet 구동 환경을 제공해준다. 이런 이유로 WAS는 웹 컨테이너 혹은 서블릿 컨테이너라고 불린다.
  클라이언트에게 메시지를 받으면 서버는 요청에 필요한 페이지의 로직이나 데이터베이스의 연동을 위해서 WAS에 이들의 처리를 요청한다. 그러면 WAS는 이 요청을 받아와 동적인 페이지 처리를 담당하고 DB에서 데이터 정보를 받아온다. 이렇게 WAS는 DB와 연동하여 데이터를 처리한 뒤, 생성한 파일을 다시 서버에게 반환한다.
  WAS의 등장으로 웹 서버의 할 일을 분배하여 서버의 부담을 줄일 수 있고, 빠르게 동적 컨텐츠를 처리할 수 있게 되었다. 종류로는 아파치 톰캣(Apache Tomcat), 레진(Resin), 제이런(JRun) 등이 있다.
- **DB (Data Base)**
  데이터베이스는 데이터의 정보를 저장하는 곳이며 WAS에서 데이터를 요청하면 필요한 데이터를 응답한다. WAS에서 로직을 수행하다가 DB접근이 필요하면 SQL질의를 통해 데이터를 요청하고, DB는 요청사항에 맞는 응답을 보낸다.

### **Part 4. 서버와 연결 종료**

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/386ec7b9-e298-4d32-8259-217549656d95/Untitled.png)

- 서버와의 연결 종료는 4-way-handshake 과정을 통해 이루어진다.
1. 클라이언트가 연결을 종료하겠다는 FIN 플래그를 서버에게 전송한다.
2. 서버는 확인 메시지(ACK)를 보내고 자신의 통신이 끝날때까지 기다린다.
3. 서버가 통신이 끝났으면 연결이 종료되었다고 클라이언에게 FIN 플래그를 전송한다.
4. 클라이언트는 확인했다는 메시지(ACK)를 서버에게 보낸다.
5. 서버와의 연결이 종료된다.

## 참고 사항

### CDN (Contents Delivery Network)

- 지리적 제약 없이 전 세계 사용자에게 빠르고 안전하게 콘텐츠를 전송할 수 있는 콘텐츠 전송 기술이다. CDN은 서버와 사용자 사이의 물리적인 거리를 줄여 콘텐츠 로딩에 소요되는 시간을 최소화한다. CDN은 각 지역에 캐시 서버를 분산 배치해, 근접한 사용자의 요청에 원본 서버가 아닌 캐시 서버가 콘텐츠를 전달한다.
- **CDN 장점**
    1. 웹사이트 로딩 속도 개선
    2. 인터넷 회선 비용 절감
    3. 컨텐츠 제공의 안정성
    4. 웹사이트 보안 개선
- **CDN의 작동원리**

    1. 최초 요청은 서버로 부터 컨텐츠를 가져와 클라이언트에게 전송하며 동시에 CDN 캐싱장비에 저장한다.

    2. 두번째 이후 모든 요청은 CDN 업체에서 지정하는 해당 컨텐츠 만료 시점까지 CDN 캐싱장비에 저장된 컨텐츠를 전송한다.

    3. 자주 사용하는 페이지에 한해서 CDN 장비에서 캐싱이 되며, 해당 컨텐츠 호출이 없을 경우 주기적으로 삭제된다.

    4. 서버가 파일을 찾는 데 실패하는 경우 CDN 플랫폼의 다른 서버에서 콘텐츠를 찾아 엔드유저에게 응답을 전송한다.

    5. 콘텐츠를 사용할 수 없거나 콘텐츠가 오래된 경우, CDN은 서버에 대한 요청을 향후 요청에 대해 응답할 수 있도록 새로운 콘텐츠를 저장한다.


## Reference

https://aws.amazon.com/ko/blogs/korea/what-happens-when-you-type-a-url-into-your-browser/

https://aws.amazon.com/ko/route53/what-is-dns/

https://www.cloudflare.com/ko-kr/learning/ssl/what-happens-in-a-tls-handshake/

https://www.akamai.com/ko/our-thinking/cdn/what-is-a-cdn