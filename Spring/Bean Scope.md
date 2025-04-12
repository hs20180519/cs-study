# 00. Bean ?
- 스프링에서 사용하는 POJO(Plain Old Java Object) 기반 객체
- 애플리케이션의 핵심을 이루는 객체이며, Spring IoC 컨테이너에 의해 인스턴스화, 관리, 생성된다. (XML 파일에 <bean id="..", class="...">)
- 상황과 필요에 따라 Bean을 사용할 때 하나만 만들어야 할 수도 있고, 여러 개가 필요할 때도 있고, 어느 한 시점에서만 사용해야 할 때가 있을 수 있다.
- 이를 위해 Scope를 설정해서 Bean의 사용 범위를 개발자가 설정할 수 있다.

# 01. Bean Scope ?
- 기본적으로 Singleton
- 보통 특정 타입의 Bean을 딱 하나만 만들고 모두 공유해서 사용

# 02. Bean Scope 종류
### Singleton
- 해당 Bean에 대해 IoC 컨테이너에서 단 하나의 객체로만 존재한다

### Prototype
- 해당 Bean에 대해 다수의 객체가 존재할 수 있다
  
### Request
- 해당 Bean에 대해 하나의 HTTP Request의 라이프사이클에서 단 하나의 객체로만 존재한다

### Session
- 해당 Bean에 대해 하나의 HTTP Session의 라이프사이클에서 단 하나의 객체로만 존재한다

### Global Session
- 해당 Bean에 대해 하나의 Global HTTP Session의 라이프사이클에서 단 하나의 객체로만 존재한다
- request, session, global session은 MVC 웹 어플리케이션에서만 사용한다

# 03. 사용방법
- `@Scope("prototype")`

# 04. Spring boot?
- Spring Container가 Bean을 관리한다 (ApplicationContext = Bean의 생성, 초기화, 생명주기)
- 이 컨테이너는 @SpringBootApplication에서 자동 생성되며, 여기서 모든 Bean을 관리
- @Component, @Service, @Repository, @Controller등의 어노테이션이 붙은 클래스는 자동으로 Bean으로 등록
- SpringBootApplication에는 @ComponentScan이 포함되어 있어, 해당 패키지 하위 클래스들을 자동 스캔
- 혹은 @Configuration 클래스 내부에서 @Bean 메서드로 직접 정의 가능
