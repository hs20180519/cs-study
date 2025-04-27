# 00. Spring Application
스프링 부트로 프로젝트를 실행할 때 Application 클래스를 만든다.

클래스명은 개발자가 프로젝트에 맞게 설정할 수 있지만, 큰 틀은 아래와 같다.

```java
@SpringBootApplication
public class Application {

	public static void main(String[] args) {
		SpringApplication.run(Application.class, args);
	}

}
```

# 01. 내부 구성 어노테이션

```
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@SpringBootConfiguration
@EnableAutoConfiguration
@ComponentScan(excludeFilters = { @Filter(type = FilterType.CUSTOM, classes = TypeExcludeFilter.class),
		@Filter(type = FilterType.CUSTOM, classes = AutoConfigurationExcludeFilter.class) })
public @interface SpringBootApplication {
...
}
```

위처럼 `@SpringBootApplication` 어노테이션에는 많은 어노테이션이 달려있다.

= 하나 이상의 @Bean 메서드를 선언하고, auto-configuration, component scanning을 일으키는 구성 클래스

@SpringBootApplication 를 쓰지 않아도 @Configuration, @EnableAutoConfiguration 및 @ComponentScan만 선언하면 동일한 역할을 기대할 수 있다.

즉 @SpringBootApplication 은 @Configuration, @EnableAutoConfiguration 및 @ComponentScan 3가지의 역할을 수행하는 어노테이션이다.

### @SpringBootConfiguration

스프링 부트 애플리케이션에서 애플리케이션의 구성을 정의하는 데 사용되는 중요한 어노테이션.

이 어노테이션은 일반적인 스프링의 @Configuration 을 확장하며, 스프링 부트의 자동 구성 및 패키지 스캔과 관련된 여러 기능을 제공한다.

스프링에서는 @Configuration으로 환경설정 빈을 표현함.

@Configuration으로 선언해도 동일한 역할을 수행한다고 했는데, 왜 @SpringBootConfiguration이라는 특별한 이름이 붙는가?

`@Configuration vs @SpringBootConfiguration`

- 스프링 부트만의 환경 설정 구성이란 것을 명확히 하기 위한 네이밍
- 구성을 자동으로 찾을 수 있음 (@SpringBootConfiguration는 한 서비스 내에 2개 이상 사용할 수 없음)


### @ComponentScan

@ComponentScan은 지정된 패키지에서 @Component, @Service, @Repository, @Controller 등의 어노테이션이 붙은 클래스를 자동으로 스캔하여 스프링 컨테이너에 빈으로 등록.

- @ComponentScan이 명시되지 않으면, @SpringBootApplication이 선언된 클래스의 패키지를 기준으로 하위 패키지를 스캔한다.
- 따라서 메인 애플리케이션 클래스는 루트 패키지에 위치시키는 것이 좋다.
- excludeFilters는 스캔 대상을 제외하고 싶을 때 사용하며, Filter 어노테이션을 선언해 필터링할 정보를 작성한다.

@Filters 어노테이션은 아래와 같이 제외대상과 추가 대상을 설정할 수 있게 옵션을 제공한다.

- inculedFillters : ComponentScan 대상을 추가로 지정
- excludeFilters : ComponentScan에서 제외할 대상을 지정



### @EnableAutoConfiguration

스프링 부트의 자동 구성 기능을 활성화하는 어노테이션으로 
@ComponentScan으로 빈이 등록된 이후, 추가적인 Bean들을 읽어 등록한다.

- spring-boot-autoconfigure 모듈의 META-INF/spring.factories 파일에 정의된 자동 구성 클래스들을 로드
- 특정 자동 구성을 제외하려면 exclude 또는 excludeName 속성을 사용

# 02. 실행

SpringApplication.run()으로 해당 클래스를 run하면, 내장 WAS를 실행한다. 

내장 WAS의 장점으로는 개발자가 따로 톰캣과 같은 외부 WAS를 설치 후 설정해두지 않아도 애플리케이션을 실행할 수 있다.

또한, 외장 WAS를 사용할 시 이 프로젝트를 실행시키기 위한 서버에서 모두 외장 WAS의 종류와 버전, 설정을 일치시켜야만 한다. 

따라서 내장 WAS를 사용하면 이런 신경은 쓰지 않아도 되기 때문에 매우 편리하다.
