## 01. **리플렉션(Reflection) 개념**

<img src=”https://haon.blog/static/5696fd056f2da4f1b405dd5eb7332266/1d499/image.png”>

![](https://haon.blog/static/5696fd056f2da4f1b405dd5eb7332266/1d499/image.png)

리플렉션은 **JVM의 메서드 영역**에서 클래스의 메타정보를 가져와, 이를 기반으로 **동적으로 객체를 생성**, **메서드를 호출**, **필드를 수정**하는 기법이다. 

리플렉션을 사용하면 자바는 **정적 타입 시스템**의 제약을 벗어나, 컴파일 타임에 알 수 없는 **클래스와 객체의 정보를 런타임에 탐색**하고 조작할 수 있다. 

리플렉션은  **JVM의 메서드 영역**에서 클래스의 메타정보를 가져와, 이를 기반으로 **동적으로 객체를 생성**하거나 **메서드를 호출**하거나 **필드를 수정**할 수 있게 해준다.

## 02. **리플렉션의 핵심 클래스**

![](https://blog.kakaocdn.net/dn/FDHmk/btrMNiuam3v/IqkmnURRJv1rjoGTlXPz20/img.png)

리플렉션은 주로 **java.lang.reflect** 패키지의 **Class**, **Method**, **Field**, **Constructor** 클래스를 통해 구현된다.

- **Class 클래스**: 클래스의 메타정보를 담고 있으며, 클래스 정보를 동적으로 조회할 수 있게 해준다.
- **Method 클래스**: 클래스의 메서드에 대한 정보를 제공하며, 동적으로 메서드를 호출할 수 있다.
- **Field 클래스**: 클래스의 필드에 대한 정보를 제공하며, 필드 값을 동적으로 읽고 쓸 수 있다.
- **Constructor 클래스**: 클래스의 생성자 정보를 제공하며, 동적으로 객체를 생성할 수 있다.

## 03. **리플렉션 사용 예시**

리플렉션을 통해 **클래스 정보 조회**, **메서드 호출**, **필드 접근**, **객체 생성** 등을 할 수 있다.

### 1. **클래스 정보 조회**

```java
Class<?> clazz = Class.forName("java.util.ArrayList");
System.out.println("클래스 이름: " + clazz.getName());

```

### 2. **필드 접근**

```java
import java.lang.reflect.Field;

class Person {
    private String name;

    public Person(String name) {
        this.name = name;
    }
}

public class ReflectionExample {
    public static void main(String[] args) throws Exception {
        Person person = new Person("John");

        // private 필드에 접근
        Field nameField = person.getClass().getDeclaredField("name");
        nameField.setAccessible(true); // private 필드에 접근 가능하게 설정
        String name = (String) nameField.get(person);

        System.out.println("Person의 이름: " + name); // John
    }
}

```

### 3. **메서드 호출**

```java
import java.lang.reflect.Method;

class Person {
    public void greet(String name) {
        System.out.println("Hello, " + name);
    }
}

public class ReflectionExample {
    public static void main(String[] args) throws Exception {
        Person person = new Person();
        Method method = person.getClass().getMethod("greet", String.class);
        method.invoke(person, "Alice"); // Hello, Alice
    }
}

```

### 4. **객체 생성**

```java
import java.lang.reflect.Constructor;

class Person {
    private String name;

    public Person(String name) {
        this.name = name;
    }

    public void greet() {
        System.out.println("Hello, " + name);
    }
}

public class ReflectionExample {
    public static void main(String[] args) throws Exception {
        Constructor<?> constructor = Person.class.getConstructor(String.class);
        Person person = (Person) constructor.newInstance("Bob");
        person.greet(); // Hello, Bob
    }
}

```

## 04. **리플렉션 사용 사례**

리플렉션은 주로 **프레임워크**나 **라이브러리**에서 **동적 객체 생성**과 **메서드 호출**을 위해 사용된다.

### 1. **Spring Framework**

Spring은 리플렉션을 사용하여 객체를 **동적으로 생성**하고, **의존성 주입(Dependency Injection)**을 수행한다. 

Spring은 **런타임**에 클래스 정보를 확인하고, 해당 클래스의 메서드를 호출하여 객체를 생성하고 의존성을 주입한다.

### 2. **JUnit**

JUnit 테스트 프레임워크는 리플렉션을 사용하여 **테스트 메서드**를 동적으로 실행하고, **테스트 클래스**에서 메서드를 동적으로 탐색한다.

### 3. **JPA (Java Persistence API)**

JPA는 리플렉션을 통해 **엔티티 클래스**와 **데이터베이스 테이블 간의 매핑**을 관리한다. 필드에 동적으로 접근하여 데이터를 설정하거나 조회한다.

### 4. **전략 패턴(Strategy Pattern)**

전략 패턴은 **동적으로 알고리즘을 선택**할 수 있는 디자인 패턴으로, 리플렉션을 통해 다양한 알고리즘을 런타임에 동적으로 선택할 수 있다. 

**인터페이스 기반 프로그래밍**을 통해 전략을 선택하고, 리플렉션을 사용하여 해당 전략을 런타임에 선택하여 실행할 수 있다.

```java
interface PaymentStrategy {
    void pay();
}

class CreditCardPayment implements PaymentStrategy {
    public void pay() {
        System.out.println("Credit Card Payment");
    }
}

class PayPalPayment implements PaymentStrategy {
    public void pay() {
        System.out.println("PayPal Payment");
    }
}

public class PaymentProcessor {
    private PaymentStrategy strategy;

    public PaymentProcessor(String strategyClass) throws Exception {
        Class<?> clazz = Class.forName(strategyClass);
        this.strategy = (PaymentStrategy) clazz.getDeclaredConstructor().newInstance();
    }

    public void processPayment() {
        strategy.pay();
    }

    public static void main(String[] args) throws Exception {
        PaymentProcessor processor = new PaymentProcessor("CreditCardPayment");
        processor.processPayment(); // Credit Card Payment
    }
}

```

## **03. 리플렉션 장단점**

### **장점**

- **동적 객체 조작**: 컴파일 시점에 알 수 없는 클래스를 런타임에 동적으로 객체 생성, 메서드 호출, 필드 수정 가능.
- **유연한 코드 작성**: 타입을 사전에 알지 못해도 런타임에 객체 생성 및 메서드 호출, 필드 수정 가능.
- **프레임워크 활용**: Spring, Hibernate, JUnit 등에서 동적 객체 생성, 메서드 호출, 의존성 주입 등에서 사용.

### **단점**

- **성능 저하**: 리플렉션은 정적 방식보다 느리며, JVM 최적화가 어려워져 성능이 저하됨.
- **JIT 최적화 어려움**: 런타임 동적 코드 호출로 JIT 컴파일러가 최적화를 어렵게 만들어 성능 저하.
- **보안 문제**: private, protected 필드 및 메서드 접근 가능하여 보안 취약점 발생 가능.
- **디버깅 어려움**: 런타임 동적 코드로 인해 디버깅이 어려워지고, 오류를 사전에 감지하기 힘듦
    - 따라서 리플렉션을 사용하는 경우 예외처리에 매우 유의해야 한다.

<aside>
💡

**JIT 최적화**

리플렉션을 사용할 경우 **JIT(Just-In-Time) 컴파일러**의 최적화가 어려워진다. JIT 컴파일러는 컴파일 타임에 최적화할 수 있는 정보를 제공하지만, 리플렉션은 런타임에 동적으로 코드를 호출하고 조작하기 때문에 컴파일 타임에 최적화를 수행할 수 없다. 따라서 리플렉션을 사용할 경우 **성능 상의 손해**를 볼 수 있다.

</aside>

### **리플렉션 예외 처리**

| 예외 이름 | 설명 |
| --- | --- |
| **ClassNotFoundException** | 클래스를 로드할 때 해당 클래스가 존재하지 않으면 발생. |
| **NoSuchMethodException** | 메서드가 존재하지 않으면 발생. |
| **NoSuchFieldException** | 필드가 존재하지 않으면 발생. |
| **IllegalAccessException** | 접근 권한이 없는 필드나 메서드를 호출할 때 발생. |
| **InvocationTargetException** | 메서드 호출 중 예외가 발생하면 이 예외가 래핑되어 던져짐. |

## 04 면접에서 나올 수 있는 질문

### 1. **리플렉션이란 무엇이며, 어떻게 사용되는지 설명해주세요.**

리플렉션은 자바에서 **런타임에 클래스 정보를 동적으로 조회**하고 **객체 생성**, **메서드 호출**, **필드 수정** 등을 할 수 있게 해주는 기능입니다. 이를 통해 컴파일 타임에 알 수 없는 클래스를 런타임에 조작할 수 있습니다. 

### 2. **리플렉션의 장점과 단점은 무엇인가요?**

리플렉션의 장점은 **동적 객체 조작**, **유연한 코드 작성**, 그리고 **프레임워크 활용**입니다. 예를 들어, Spring 프레임워크는 의존성 주입을 위해 리플렉션을 사용합니다. 

하지만 단점으로는 **성능 저하**와 **보안 문제**가 있습니다. 리플렉션을 사용하면 **정적 방식보다 느리며**, **JIT 최적화**가 어려워 성능에 영향을 미칩니다. 또한, **private 필드와 메서드에 접근**할 수 있어 보안 취약점이 발생할 수 있습니다.
