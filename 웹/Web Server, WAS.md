# 01. Web server
- 웹 브라우저 클라이언트로부터 HTTP 요청을 받아들이고 HTML 문서와 같은 웹 페이지를 반환하는 컴퓨터 프로그램
![image](https://github.com/user-attachments/assets/300f3e00-1769-4f6b-8a2b-ea3f9389d922)

- 클라이언트가 웹 브라우저에서 어떠한 페이지 요청을 하면 웹 서버가 그 요청을 받아 정적 컨텐츠를 제공하는 서버
- 정적 컨텐츠 : HTML 문서, CSS, javascript, 이미지, 파일 등 즉시 응답 가능한 컨텐츠
- <b>동적 컨텐츠 요청을 받으면 WAS에게 해당 요청을 넘겨주고, WAS에서 처리한 결과를 클라이언트에게 전달해주는 역할</b>

### 정적 페이지 vs 동적 페이지
![image](https://github.com/user-attachments/assets/b7e9d119-dedd-4c99-a227-c5a8e40af159)

# 02. Web Application Server (WAS)
- 인터넷 상에서 HTTP 프로토콜을 통해 사용자 컴퓨터나 장치에 애플리케이션을 수행하는 미들웨어로서, 주로 동적 서버 컨텐츠 수행하는 것으로 웹 서버와 구별되며, 주로 DB 서버와 같이 수행되는 것을 의미
![image](https://github.com/user-attachments/assets/ee28677d-ef8a-429f-a6af-900063f923ac)

- WAS는 웹 서버와 웹 컨테이너가 합쳐진 형태
- 웹 서버 단독으로 처리할 수 없는 데이터베이스의 조회나 다양한 로직 처리가 필요한 동적 컨텐츠를 제공
- JSP, Servlet 구동 환경을 제공하므로 웹 컨테이너 혹은 서블릿 컨테이너라고 불린다

### Web Container
- 웹 서버가 보낸 JSP, PHP 등의 파일을 수행한 결과를 다시 웹 서버로 보내는 역할

### Middleware
- Client - MiddleWare Server - DB Server
- 비즈니스 로직을 Client와 DBMS 사이의 미들웨어 서버에서 동작하게 함으로써 Client는 입력과 출력만 담당

# 03. Web Service Architecture
1. Client - web server - DB
2. Client - WAS - DB
3. Client - Web Server - WAS - DB

- 이때 우리가 주로 사용하는 아키텍처는 3번 아키텍처
![image](https://github.com/user-attachments/assets/724d4cde-52d8-454a-aa29-011a5f24258a)

1. Web Server는 웹 브라우저 클라이언트로부터 HTTP 요청을 받는다
2. Web Server는 클라이언트 요청을 WAS로 보낸다
3. WAS는 관련된 Servlet을 메모리로 올린다
4. WAS는 web.xml을 참조하여 해당 Servlet에 대한 Thread를 생성한다 (이때 Thread Pool 사용)
5. HttpServletRequest와 HttpServletResponse 객체를 생성하여 Servlet에 전달
   Thread는 Servlet의 service() 메서드 호출
   service()메서드는 요청에 맞게 doGet() 혹은 doPost()를 호출
6. protected doGet(HttpServletReqeust req, HttpServletResponse res)
7. doGet() 혹은 doPost() 메서드는 인자에 맞게 생성된 적절한 동적 페이지를 Response에 담아 WAS에 전달
8. WAS는 Response 객체를 HttpResponse 형태로 바꾸어 Web Server에 전달한다
9. 생성된 Thread를 종료하고, HttpServletRequest와 HttpServletResponse 객체를 제거한다

### Web server 가 필요한 이유?
- 동적인 컨텐츠를 제공하는 WAS가 정적인 컨텐츠까지 하면 되는 것 아닌가?
- 클라이언트에게 정적 컨텐츠를 보내는 과정은 생각보다 복잡하다.
- 이미지 파일과 같은 정적인 파일들은 웹 문서가 클라이언트로 보내질 때 함께 가는 것이 아니라, 클라이언트는 HTML 문서를 먼저 받고 그에 맞게 필요한 이미지 파일들을 서버로 요청하면 그제서야 받는다.
- 따라서 Web Server를 통해 정적인 파일들을 Application Server까지 가지 않고 앞단에 빨리 보낼 수 있다.
- Web Server에서 정적인 컨텐츠만 다루도록 하여 서버의 부담을 줄일 수 있다!

# 04. 자주 나오는 질문
### web server와 web application server의 차이점 ?
- 웹 서버, 즉 WS는 클라이언트가 요청한 정적인 콘텐츠를 제공하는 역할을 합니다.
- 사용자가 웹사이트에 접속하면 HTML, CSS, JavaScript 같은 정적인 파일을 그대로 브라우저에 전달하는 게 웹 서버의 역할입니다. 대표적으로 Apache나 Nginx 같은 서버가 있습니다.
- 반면에 WAS, 즉 웹 애플리케이션 서버는 단순히 정적인 파일을 제공하는 게 아니라, 동적인 요청을 처리합니다.
- 사용자가 로그인 요청을 보내거나 데이터를 조회하는 경우, WAS가 이 요청을 받아서 내부에서 비즈니스 로직을 수행하고, 데이터베이스와 연동하여 필요한 결과를 생성한 뒤 응답을 반환합니다. WAS의 대표적인 예로는 Tomcat, JBoss, WebSphere 같은 서버가 있습니다.

### ws와 was를 분리해서 사용하는 이유 ?
- 분리해서 사용하는 이유는 성능 최적화, 확장성, 보안 강화가 있습니다. 
- 웹 서버(WS)와 웹 애플리케이션 서버(WAS)를 분리하면 성능과 효율성이 크게 향상됩니다.
- 웹 서버는 정적인 리소스(HTML, CSS, JavaScript, 이미지)를 빠르게 제공하는 역할을 하고, WAS는 데이터베이스와 연동하거나 비즈니스 로직을 처리하는 역할을 합니다.
- 만약 모든 요청을 WAS가 직접 처리한다면, 단순히 정적인 HTML 파일 하나를 전송하는 요청까지도 WAS가 담당하게 되어 불필요한 리소스를 낭비됩니다. 
- 또 하나의 중요한 이유는 확장성과 안정성입니다. 웹 서버와 WAS를 분리하면 각각의 서버를 독립적으로 확장할 수 있습니다.
- 보안적인 측면에서도 도움이 됩니다. 웹 서버가 앞단에서 클라이언트 요청을 필터링하고 WAS로 넘기면, 직접적으로 WAS가 외부에 노출되지 않으므로 보안이 강화됩니다.
