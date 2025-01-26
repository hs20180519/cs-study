# 01. 개요

- 요약: **불변 데이터**를 객체 간에 전달하는 작업을 간단하게 만들어주는 것
- 객체 간 불변 데이터를 전달하는 것은 많은 java 애플리케이션에서 가장 일반적이고 단조로운 작업 중 하나
- java 14 이전: 보일러플레이트 필드와 메서드가 포함된 클래스를 생성해야 했음
    
    → 사소한 실수에 취약하고 의도를 흐릴 수 있음
    
    - 보일러플레이트(bolierplate): 여러 가지 상황에서 큰 수정 없이 재사용할 수 있는 코드

## 기존 데이터 클래스의 문제점

```java
public class Person {

    private final String name;
    private final String address;

    public Person(String name, String address) {
        this.name = name;
        this.address = address;
    }

    @Override
    public int hashCode() {
        return Objects.hash(name, address);
    }

    @Override
    public boolean equals(Object obj) {
        if (this == obj) {
            return true;
        } else if (!(obj instanceof Person)) {
            return false;
        } else {
            Person other = (Person) obj;
            return Objects.equals(name, other.name)
              && Objects.equals(address, other.address);
        }
    }

    @Override
    public String toString() {
        return "Person [name=" + name + ", address=" + address + "]";
    }

    // standard getters
}
```

### 문제점 1. boilerplate 코드가 많음

- 각 데이터 클래스에 동일한 과정(equals, hashCode, toString 메서드, 생성자 코드 작성 등)을 반복해야 함
- IDE에서 자동 생성 기능을 제공하지만, 클래스에 새 필드가 추가됐을 경우 자동으로 업데이트는 불가능함

### 문제점 2. 클래스의 목적이 모호해짐

- 추가 코드로 인해 해당 클래스가 단순히 이름과 주소가 있는 두 개의 String 필드가 있는 데이터 클래스라는 사실이 모호해짐

→ **Java 14**부터 Record가 도입되면서 불필요한 코드를 제거하고 적은 코드로도 명확한 의도를 표현할 수 있게 됨

## record의 목표

- 데이터를 간결하게 표현
- 개발자가 동작을 확장하는 것보다 불변 데이터를 모델링하는 데 집중
- 데이터 지향 메소드를 자동으로 구현
- 명확한 의도 표현

**💡 불변 객체의 장점**

- 상태 변경이 불가능하여 멀티스레드 환경에서 안전성이 높음
- 데이터 무결성을 유지할 수 있음
- 코드의 가독성과 유지보수성 up

# 02. 특징

- 멤버 변수는 private final로 선언됨
- 필드별 getter 자동 생성
    - getXXX() 형식이 아닌, xxx() 형식
    - e.g. getName() → name()
- 모든 멤버변수를 인자로 하는 public 생성자 자동 생성
    - 생성자 내부에서 필드를 수정할 수 없음
    
    ```java
    public record Person(String name, int age) {}
    ```
    
    위 record는 다음과 같은 생성자가 자동으로 만들어진다:
    
    ```java
    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }
    ```
    
    다음과 같은 필드 변경이 불가능하다:
    
    ```java
    public record Person(String name, int age) {
        public Person {
            name = name.toUpperCase();  // 컴파일 오류 발생!
            age = age + 1;  // 컴파일 오류 발생!
        }
    }
    ```
    
- equals, hashCode, toString 메서드 자동 생성
- 기본생성자는 제공하지 않음
- 레코드 내부에 멤버 변수 선언 불가능하나, static 변수/메소드는 가능
- 상속 불가능

## 컴팩트 생성자(Compact Constructor)

- 레코드의 생성자가 private 필드를 초기화하는 것 이상의 작업을 수행하도록 하려면 레코드에 대한 컴팩트 생성자라고 불리는 사용자 정의 생성자를 정의할 수 있음
- 클래스 생성자와 달리 생성자 매개 변수 부분이 없음
- 유효성 체크에 적합

```java
public record Person(String name, String address) {
		public Person {
				// null check
				Objects.requireNonNull(name);
				Objects.requireNonNull(address);
		}
}
```

## vs. lombok

### Record가 적합한 경우

- 불변 데이터 클래스를 간단하게 정의할 경우
- **객체의 상태가 변경되지 않아야** 하며, 데이터를 **단순히 전달**하거나 **읽기 전용**으로 사용할 경우
- 클래스의 역할이 명확하고, 확장이 필요없는 경우

### lombok이 적합한 경우

- 복잡한 데이터 모델을 정의하거나, 객체의 상태가 변경될 필요가 있을 경우
- 다양한 패턴(e.g. builder)을 사용해야 하거나, 기존 클래스에 다양한 유틸리티 메서드를 추가해야 할 경우
- 프로젝트에서 많은 수의 데이터 클래스를 관리해야 하며, 이 클래스들이 불변일 필요는 없을 경우

# 03. Record 활용하기

## DTO(Data Transfer Object)

- record는 한 번 값이 정해지고 나면 setter를 통해 값을 변경할 수 없음
    
    → 자바 내부에서 데이터 가공 시 중간에 변질될 우려가 없음
    

### 기존 DTO 클래스

```java
@Getter
@Setter
@NoArgsConstructor
public class QuestionSaveRequestDto {

    @NotBlank(message = "제목은 필수 입력 사항입니다.")
    @Size(max = 200, message = "제목이 너무 길어요.")
    private String title;
    @NotBlank(message = "내용은 필수 입력 사항입니다.")
    private String content;

    @Builder
    public QuestionSaveRequestDto(String title, String content) {
        this.title = title;
        this.content = content;
    }

    public Question toEntity(Member member) {
        return Question.builder()
                .title(title)
                .content(content)
                .member(member)
                .build();
    }
}
```

### Record로 바꾼 DTO

```java
public record QuestionSaveRequestDto(
        @NotBlank(message = "제목은 필수 입력 사항입니다.")
        @Size(max = 200, message = "제목이 너무 길어요.")
        String title,

        @NotBlank(message = "내용은 필수 입력 사항입니다.") 
		String content
) {
    public Question toEntity(Member member) {
        return Question.builder()
                .title(title())
                .content(content())
                .member(member)
                .build();
    }
}
```

**❓ Entity로는 활용할 수 없을까?**

A. 없다. 왜냐면:

- spring boot JPA는 프록시 생성을 위해 entity에 의존하는데, 이 때 entity에는 인수 생성자, non-final 필드, setter, non-final 클래스가 없음
    
    → entity는 불변일 수 없음
    
- 쿼리 결과를 매핑할 때 객체를 인스턴스화 할 수 있도록 매개변수가 없는 생성자가 필요함
    
    → record는 매개변수가 없는 생성자를 제공하지 않음(setter를 사용할 수 없기 때문에 모든 필드의 값을 생성 시에 입력해야 함)
    
- 접근자 메소드인 getter가 필수 명명 규칙을 따르지 않음

# 04. 자주 나오는 질문

## Record에 대해서 설명해 주세요.

record는 불변성을 가지는 데이터 객체를 간결하게 표현하기 위한 기능으로, java 14부터 도입되었습니다. 필드 생성자, getter, toString, equals, hashCode 메서드를 자동으로 생성하며, 주로 DTO와 같은 데이터 전달 목적으로 사용됩니다.

# 05. 참고자료

https://velog.io/@pp8817/record

https://colevelup.tistory.com/28

https://s7won.tistory.com/2

https://velog.io/@hyeok_1212/Java-Record-%EC%82%AC%EC%9A%A9%ED%95%98%EC%8B%9C%EB%82%98%EC%9A%94
