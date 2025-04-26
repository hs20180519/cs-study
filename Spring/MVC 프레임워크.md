# 01. Spring MVC
- Model-View-Controller 아키텍처를 바탕으로 웹 애플리케이션을 개발하는 Spring의 서브 프레임워크
- 웹 요청과 응답을 처리하는 DispatcherServlet을 중심으로 구성

# 02. Spring MVC 아키텍처 구성요소
1. DispatcherServlet : 모든 요청을 받아 각 컴포넌트로 분기하는 중앙 컨트롤러
2. HandlerMapping : 요청 URL에 따라 어떤 컨트롤러가 호출될지 결정
3. Controller : 사용자의 요청을 처리하고, 결과 데이터를 준비
4. Model : 데이터를 담는 객체(DTO나 VO)
5. ViewResolver : 어떤 View를 사용할지 결정
6. View : 사용자에게 보여줄 화면을 렌더링

# 03. Spring MVC 동작 흐름
<img width="943" alt="image" src="https://github.com/user-attachments/assets/89035b58-8e84-4cef-8aa6-a9e0398bebb0" />

- 요청이 들어온 순간부터 응답을 반환하기 까지의 전체 과정

1. 사용자 요청이 들어오면, DispatcherServlet이 요청을 받음
2. HandlerMapping을 통해 요청 URL에 맞는 Controller을 찾음
3. Controller가 요청을 처리하고, 결과 데이터를 Model에 담음
4. Controller는 논리적 View 이름을 반환함
5. ViewResolver가 View 이름을 물리적 View(JSP 파일 등)로 변환함
6. 최종적으로 View가 데이터를 렌더링하여 사용자에게 응답을 보냄

=> 사용자 요청 -> DispatcherServlet -> HandlerMapping -> Controller -> Model -> ViewResolver -> View  -> 사용자 응답

# 04. 사용하는 이유
- 높은 생산성
- 명확한 역할 분리 : Controller, Service, Repository 계층 구분
- 확장성과 유연성
- Spring과의 연계
- 테스트 용이성

# 05. 주요 특징
- Annotation 기반 개발(@Controller)
- POJO 기반 설계(Plain Old Java Object)
- RESTful API 개발 지원(@RestController)
- 유연한 View 선택(JSON, XML, JSP, Thymeleaf)
- 비즈니스 로직과 UI 분리

# 06. 자주 나오는 질문
### 1. DispatcherServlet의 역할?
DispatcherServlet은 모든 HTTP 요청을 받아 적절한 Controller로 전달하고, 처리 결과를 View로 연결해 최종 응답을 생성하는 중앙 관리 역할을 합니다.

### 2. Spring MVC 흐름 설명
사용자의 요청이 DispatcherServlet에 전달되면, HandlerMapping을 통해 Controller를 찾아 요청을 처리하고, ViewResolver를 통해 View를 렌더링해 응답을 반환합니다.

### 3. ViewResolver의 역할?
ViewResolver는 Controller가 반환한 논리적 View 이름을 실제 View 파일 경로로 변환해주는 컴포넌트입니다.
