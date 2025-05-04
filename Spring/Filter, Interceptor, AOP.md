# 00. 공통 프로세스
- 자바 웹 개발을 하다보면, 로그, 권한 체크 등 공통된 코드가 존재함
- 중복 코드가 많아지면, 프로젝트 단위가 커질 수록 서버에 부하를 주고, 소스 관리가 어려워짐
- 즉, 공통 부분은 따로 빼서 관리하는 것이 좋음
=> Filter, Interceptor, AOP

# 01. Filter, Interceptor, AOP의 흐름
<img width="703" alt="image" src="https://github.com/user-attachments/assets/37589d42-b32d-408b-aa5b-a59aa3045edc" />

- Interceptor와 Filter는 Servlet 단위에서 실행됨 <-> AOP는 메소드 앞에 Proxy 패턴의 형태로 실행됨
- 요청이 들어오면, Filter -> Interceptor -> AOP -> Intercpetor -> Filter
- 1. 서버를 실행시켜 서블릿이 올라오는 동안에 init, 그 후 doFilter이 실행
- 2. 컨트롤러에 들어가기 전 preHandler가 실행
- 3. 컨트롤러에서 나와 postHandler, afterCompletion, doFilter 순으로 진행
- 4. 서블릿 종료시 destory 실행

# 02. Filter
- 요청과 응답을 거른 뒤 정제하는 역할
- DispatcherServlet 이전에 실행이 됨
- 응답 내용에 대해서도 변경하는 처리 가능
- 보통 web.xml에 등록하고, 일반적으로 인코딩 변환 처리, XSS 방어 등의 요청에 대한 처리로 사용
- `init()` : 필터 인스턴스 초기화
- `doFilter()` : 전/후 처리
- `destroy()` : 필터 인스턴스 종료

# 03. Interceptor
- 요청에 대한 작업 전/후로 가로채는 역할
- 필터는 스프링 컨텍스트 외부에 존재하여 스프링과 무관한 자원에 대한 동작
- 인터셉터는 스프링의 DispatcherServlet이 컨트롤러를 호출하기 전, 후로 끼어들기 때문에 스프링 컨텍스트 내부에서 Controller(Handler)에 관한 요청과 응답에 대해 처리
- 로그인 체크, 권한 체크, 프로그램 실행시간 계산 작업등의 업무 처리
- `preHandler()` : 컨트롤러 메서드가 실행되기 전
- `postHandler()` : 컨트롤러 메서드가 실행된 직 후, view 페이지 랜더링 되기 전
- `afterCompletion` : view 페이지가 랜더링 되고 난 후

# 04. AOP
- OOP를 보완하기 위해 나온 개념
- 객체 지향의 프로그래밍을 했을 때 중복을 줄일 수 없는 부분을 줄이기 위해 종단면(관점)에서 바라보고 처리
- 주로 로깅, 트랜잭션, 에러 처리 등 비즈니스 단의 메서드에서 조금 더 세밀하게 조정하고 싶을 때 사용
- 인터셉터와 필터와는 달리 메소드 전후의 지점에 자유롭게 설정이 가능
- 인터셉터와 필터는 주소로 대상을 구분해서 걸러내지만, AOP는 주소, 파라미터, 어노테이션 등으로 대상을 지정할 수 있음
- `Before` : 대상 메서드의 수행 전
- `After` : 대상 메서드의 수행 후
- `After-returning` : 대상 메서드의 정상적인 수행 후
- `After-throwing` : 예외 발생 후
- `Around` : 대상 메서드의 수행 전/후

# 05. 자주 나오는 질문
### 1. Filter, Interceptor, AOP의 차이점
<img width="486" alt="image" src="https://github.com/user-attachments/assets/deeea4bc-e47f-4c88-a2e9-e31cc13bceae" />

### 2. Filter와 Intercpetor 중 로그인 인증을 처리한다면 어떤 것을 선택 ? 그 이유는 ?
<img width="496" alt="image" src="https://github.com/user-attachments/assets/7ee4ce18-cb9e-40a6-a2fd-202f433d32c6" />

### 3. AOP는 어떤 문제를 해결하기 위해 사용하는가 ?
<img width="487" alt="image" src="https://github.com/user-attachments/assets/d21d0374-b984-4852-b2d9-68c79dd48312" />
