
## 00. 리플렉션(Reflection)

**리플렉션(Reflection)** 은 Java에서 **런타임에 클래스, 메서드, 필드 등의 정보를 동적으로 확인하고 조작할 수 있는 기능**이다. 

리플렉션을 활용하여 클래스의 정보를 몰라도 **런타임에서 동적으로 객체를 생성**하거나 **메서드를 호출**할 수 있다.


## 01. 어노테이션과 리플렉션

### 1.1 어노테이션이란?

* 어노테이션(`@`)은 코드에 메타데이터를 부여하는 기능이다.
* **컴파일러에 정보를 제공하거나**, **런타임에 리플렉션을 통해 동작을 제어**할 수 있다.
* 예: `@Override`, `@Deprecated`, 사용자 정의 어노테이션 등

### 1.2 리플렉션을 통한 어노테이션 처리

* `java.lang.reflect` 패키지의 `Class`, `Method`, `Field` 등을 사용해 런타임에 어노테이션을 읽고 처리할 수 있다.
* 어노테이션을 런타임까지 유지하려면 `@Retention(RetentionPolicy.RUNTIME)`을 명시해야 한다.

#### 예제 코드

```java
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.METHOD)
public @interface MyCustomAnnotation {
    String value();
}

public class MyClass {
    @MyCustomAnnotation("Hello")
    public void myMethod() {
        System.out.println("Method executed");
    }
}

public class AnnotationExample {
    public static void main(String[] args) throws Exception {
        Method method = MyClass.class.getMethod("myMethod");
        MyCustomAnnotation annotation = method.getAnnotation(MyCustomAnnotation.class);
        if (annotation != null) {
            System.out.println("Annotation value: " + annotation.value()); // Hello
        }
    }
}
```

### 1.3 어노테이션을 통한 동적 처리

* 어노테이션은 단순한 메타데이터를 넘어서 **프로그램 흐름을 제어하는 역할**도 할 수 있다.
* 대표적인 예로, Spring에서 `@Component`, `@Autowired`, `@RequestMapping` 등은 어노테이션 정보를 읽고 런타임에 동작을 동적으로 구성한다.

## 02. 스프링과 리플렉션

### 2.1 스프링에서의 리플렉션 활용

* Spring은 객체 생성, 의존성 주입, AOP, 트랜잭션 처리 등 **프레임워크 전반에 걸쳐 리플렉션을 사용**한다.
* 개발자가 직접 객체를 생성하지 않고 어노테이션 기반 설정만으로도 모든 동작이 가능한 이유는 리플렉션 덕분이다.

### 2.2 빈 생성 및 의존성 주입

```java
@Component
public class MyService {
    private final MyRepository repository;

    @Autowired
    public MyService(MyRepository repository) {
        this.repository = repository;
    }
}
```

* 위 코드에서 `@Autowired`는 스프링이 **리플렉션을 통해 생성자 정보를 파악하고**, `MyRepository` 객체를 자동으로 주입한다.

### 2.3 AOP와 리플렉션


* AOP는 프록시 객체를 만들어 메서드 실행 전후에 **리플렉션으로 메서드를 탐지**하고 로직을 삽입한다.

```java
@Aspect
@Component
public class LoggingAspect {

    @Before("@annotation(Loggable)")
    public void logBefore(JoinPoint joinPoint) {
        System.out.println("Before method: " + joinPoint.getSignature().getName());
    }
}
```

* `@Loggable` 어노테이션이 붙은 메서드는 AOP가 리플렉션으로 감지하여 사전 로직을 수행한다.

### 2.4 트랜잭션 처리

* `@Transactional` 어노테이션이 붙은 메서드는 스프링이 리플렉션으로 탐지하고, 트랜잭션 시작/커밋/롤백 로직을 적용한다.


## 03. Spring 내부 구현에서의 리플렉션

### 3.1 `@RestController` 예시

```java
@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Controller
@ResponseBody
public @interface RestController {
    @AliasFor(annotation = Controller.class)
    String value() default "";
}
```

* `@RestController`는 내부적으로 `@Controller`와 `@ResponseBody`를 포함하고 있으며, **스프링이 이를 리플렉션으로 분석하여 적절한 동작을 수행**한다.

### 3.2 `AnnotationFilter`

```java
package org.springframework.web.bind.annotation;

import java.lang.annotation.Documented;
import java.lang.annotation.ElementType;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;
import org.springframework.core.annotation.AliasFor;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.ResponseBody;

@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Controller
@ResponseBody
public @interface RestController {
    @AliasFor(annotation = Controller.class)
    String value() default "";
}

```

* 스프링은 이 인터페이스를 통해 특정 패키지의 어노테이션을 필터링할 수 있다.

### 3.3 `AnnotationsScanner`

```java
private static <C, R> R scan(...) {
    R result = process(...);
    return processor.finish(result);
}
```

* 어노테이션을 탐색할 때 **전략 패턴**(`MergedAnnotations.SearchStrategy`)을 사용하여 상속, 메타 어노테이션 등을 어떻게 추적할지 결정한다.

### 3.4 `ReflectionUtils`

```java
methods = ReflectionUtils.getDeclaredMethods(baseType);
```

* 스프링은 이 유틸 클래스를 통해 **선언된 모든 메서드를 리플렉션으로 가져와 분석 및 처리**한다.

### 3.5 상속/오버라이딩 어노테이션 병합 처리

```java
private static boolean isOverride(Method rootMethod, Method candidateMethod) {
    return candidateMethod.getName().equals(rootMethod.getName()) &&
           hasSameParameterTypes(rootMethod, candidateMethod);
}
```

* 메서드가 오버라이드된 경우, 스프링은 **상위 클래스의 어노테이션을 병합 처리**하여 일관된 어노테이션 적용을 보장한다.


## 요약

| 구분     | 설명                                                            |
| ------ | ------------------------------------------------------------- |
| 리플렉션   | 런타임에 클래스/메서드/필드를 동적으로 조회 및 조작                                 |
| 어노테이션  | 코드에 메타데이터를 부여, 리플렉션과 결합해 동작을 제어                               |
| 스프링 활용 | 객체 생성, 의존성 주입, AOP, 트랜잭션 등 전반적으로 사용                           |
| 내부구현   | `ReflectionUtils`, `AnnotationScanner`, 전략 패턴 등 다양한 내부 API 활용 |


## 면접 예상 질문 및 답변

### Q1. 리플렉션이란 무엇인가요?

리플렉션은 Java에서 **런타임에 클래스, 메서드, 필드 등의 정보를 조회하고 조작할 수 있는 기능**입니다. 이를 통해 코드의 유연성과 확장성을 확보할 수 있지만, 성능 저하와 보안 이슈가 있을 수 있어 신중하게 사용해야 합니다.


### Q2. 리플렉션과 어노테이션은 어떻게 연관되어 있나요?

어노테이션은 클래스나 메서드에 메타데이터를 부여하고, 리플렉션은 이 어노테이션을 **런타임에 읽어와 동작을 제어**하는 데 사용됩니다. 예를 들어, 스프링에서는 `@Autowired` 어노테이션을 리플렉션을 통해 읽고 의존성을 주입합니다.


### Q3. 스프링에서 리플렉션이 사용되는 대표적인 예는?

대표적으로 다음과 같습니다:

* **의존성 주입**(`@Autowired`)
* **AOP 처리**(`@Aspect`)
* **트랜잭션 처리**(`@Transactional`)
* **Request Mapping 처리**(`@RequestMapping`)
  모두 어노테이션을 리플렉션으로 읽고 그에 맞는 동작을 수행합니다.

