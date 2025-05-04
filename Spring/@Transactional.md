# 00. Transactional의 기본 개념

> Transaction = 데이터베이스의 상태를 변경시키는 작업의 단위
> 

데이터베이스에서 데이터를 다룰 때 (데이터 추가, 갱신, 삭제 등) 처리하던 작업이 오류가 발생했을 때 모든 일련의 작업들을 원 상태로 되돌린다.

모든 일련의 작업들이 성공해야만 최종적으로 데이터베이스에 데이터를 갱신, 삭제, 추가가 된다.

이러한 트랜잭션은 여러 상황에 따라 여러 개가 만들어진다. 하나의 트랜잭션이 Commit 되거나 Rollback 될 수 있다.

# 01. Spring에서 트랜잭션 관리의 기본 구조

Spring에서는 Transaction 처리를 **프로그래밍적 트랜잭션 관리**와 **선언적 트랜잭션 관리**을 통해 지원한다.

그 중 어노테이션 `@Transactional` 을 선언해 트랜잭션을 처리하는 방식을 **선언적 트랜잭션 관리**라고 부른다.

### 프로그래밍 방식 트랜잭션 관리

TransactionTemplate 또는 PlatformTransactionManager를 사용하여 개발자가 직접 트랜잭션을 제어하는 방식이다. 이 방식은 복잡한 트랜잭션 제어나 커스터 마이징이 필요한 경우 유용하다. 

### 선언적 트랜잭션 관리

선언적 트랜잭션 관리는 `@Transactional` 어노테이션을 사용하여 트랜잭션을 관리하는 방식이다. 

이 방식은 AOP(Aspect-Oriented Programming)를 활용하여 트랜잭션 관련 코드를 비즈니스 로직과 분리하게 된다. 

즉, 개발자는 트랜잭션 시작, 커밋, 롤백 등의 코드를 직접 작성하지 않고도 트랜잭션을 적용할 수 있다.

### 스프링의 Transaction Management

Spring은 DataSource와 PlatformTransactionManager를 자동으로 빈으로 등록한다.

```java
public interface PlatformTransactionManager {
    TransactionStatus getTransaction(@Nullable TransactionDefinition var1) throws TransactionException;
 
    void commit(TransactionStatus var1) throws TransactionException;
 
    void rollback(TransactionStatus var1) throws TransactionException;
}
```

예를 들어 JDBC를 사용하는 경우 DataSouceTransactionManager, ORM 프레임워크를 사용하는 경우엔 Hibernate 또는 JpaTransactionManager 가 필요하다. 스프링은 코드의 변경 없이 기술에 따라 구현체를 바꿔 사용할 수 있도록 `PlatformTransactionManager` 인터페이스를 통해 서비스를 추상화하였다.

![image](https://github.com/user-attachments/assets/2d9dc2f7-7dac-4224-95a8-8fa7b3e5257f)


(기본 구현체는 DataSourceTransactionManager이고, SpringDataJpa 사용 시 JpaTrancationManager로 설정을 바꾼다.)

# 02. @Transactional의 기본 사용

### 클래스 vs. 메서드 단위 선언 차이

```java
@Service
@Transactional
public class UserService {
		// UserService 내 모든 메서드에 @Transactional 적용
}
```

```java
@Service
public class UserService {
    @Transactional // 해당 메서드에만 적용
    public void registerUser(User user) {
        // 사용자 생성 로직
    }
}
```

- 클래스 단위에 선언 시, 해당 클래스에 속한 메서드에 공통적으로 적용
- 메서드 단위에 선언 시, 해당 메서드에만 적용

### 동작 원리

- 클래스 또는 메서드에 @Transactional이 선언된다면 *해당 클래스에* 트랜잭션이 적용된 프록시 객체가 생성
- 프록시 객체는 @Transactional 이 포함된 메서드 호출 시, 트랜잭션을 시작하며 Commit 또는 Rollback을 수행
- CheckedException 또는 예외가 없을 경우 **Commit**
- UncheckedException 발생하는 경우 **Rollback**

![image 1](https://github.com/user-attachments/assets/6c0bcdd9-9993-45e2-b1f3-6522ea814e39)


### 전파 방식

| 전파 타입 (`Propagation`) | 설명 | 사용 예시 |
| --- | --- | --- |
| `REQUIRED` (기본값) | 현재 트랜잭션이 존재하면 참여하고, 없으면 새로 생성합니다. | 대부분의 서비스 메서드에 사용됩니다. |
| `REQUIRES_NEW` | 항상 새로운 트랜잭션을 생성하며, 기존 트랜잭션이 있으면 일시 중단합니다. | 독립적인 트랜잭션이 필요한 로깅이나 감사 작업에 사용됩니다. |
| `SUPPORTS` | 트랜잭션이 존재하면 참여하고, 없으면 트랜잭션 없이 실행합니다. | 트랜잭션이 필수가 아닌 조회 작업에 사용됩니다. |
| `NOT_SUPPORTED` | 트랜잭션이 존재하면 일시 중단하고, 트랜잭션 없이 실행합니다. | 트랜잭션이 필요 없는 작업(예: 캐시 조회)에 사용됩니다. |
| `NEVER` | 트랜잭션이 존재하면 예외를 발생시키며, 항상 트랜잭션 없이 실행합니다. | 트랜잭션이 있으면 안 되는 작업에 사용됩니다. |
| `MANDATORY` | 반드시 트랜잭션이 존재해야 하며, 없으면 예외를 발생시킵니다. | 트랜잭션 내에서만 호출되어야 하는 내부 메서드에 사용됩니다. |
| `NESTED` | 현재 트랜잭션이 존재하면 중첩 트랜잭션을 생성하고, 없으면 새로운 트랜잭션을 생성합니다. | 부분 롤백이 필요한 복잡한 작업에 사용됩니다. |

### rollback 조건 (예외에 따른 롤백)

- rollbackFor: 지정한 예외 발생 시 트랜잭션 롤백

```java
  @Transactional(rollbackFor = CustomException.class)
```

- noRollbackFor: 지정한 예외가 발생해도 트랜잭션을 롤백하지 않음

```java
  @Transactional(noRollbackFor = SpecificException.class)
```

## 주의 사항

### 1) Private 은 트랜잭션 처리 불가

private 메서드는 프록시 객체에서 호출할 수 없다.

프록시 객체는 타겟 객체/인터페이스를 상속 받아서 구현하게 된다. 자식인 프록시 객체 입장에서 부모의 private 메서드는 호출할 수 없는 것이다.

즉, @Transactional이 붙는 메서드, 클래스는 프록시 객체에서 접근 가능한 레벨로 지정해야 한다.

### 2) 트랜잭션은 객체 외부에서 처음 진입하는 메서드를 기준으로 동작

### 3) 내부 메서드 호출 시 주의

같은 클래스 내에서 this를 사용하여 다른 메서드를 호출하면 프록시를 거치지 않으므로 트랜잭션이 적용되지 않는다.

# 04. 트랜잭션 전파와 내부 호출 문제

```java
@Slf4j
@Service
public class SimpleService {

    public void outerMethod() {
        log.info("==== outerMethod start ====");
        log.info("==== outerMethod transaction Active : {}", 
                TransactionSynchronizationManager.isActualTransactionActive());
        innerMethod();
        log.info("==== outerMethod end ====");
    }

    public void innerMethod() {
        log.info("==== innerMethod start ====");
        log.info("==== innerMethod transaction Active : {}", 
                TransactionSynchronizationManager.isActualTransactionActive());
        log.info("==== innerMethod end ====");
    }
}
```

### **outerMethod (@Transactional) & innerMethod (@Transactional)**

```java
==== outerMethod start ====
==== outerMethod transaction Active : true
==== innerMethod start ====
==== innerMethod transaction Active : true
==== innerMethod end ====
==== outerMethod end ====
```

outer, inner 메서드 **모두 트랜잭션 적용**

스프링에서 트랜잭션 전파(Transaction Propagation) 타입의 기본값은 REQUIRED

→ outerMethod의 트랜잭션에 innerMethod의 트랜잭션이 참여하는 구조

### **outerMethod (@Transactional) & innerMethod**

```java
==== outerMethod start ====
==== outerMethod transaction Active : true
==== innerMethod start ====
==== innerMethod transaction Active : true
==== innerMethod end ====
==== outerMethod end ====
```

outer, inner 메서드 **모두 트랜잭션 적용**

outerMethod는 @Transactional 어노테이션을 통해 트랜잭션이 시작되어 있는 상태이다.

outerMethod가 종료되기 전에(트랜잭션이 닫히기 전에) innerMethod가 호출되었다.

### **outerMethod  & innerMethod (@Transactional)**

```java
==== outerMethod start ====
==== outerMethod transaction Active : false
==== innerMethod start ====
==== innerMethod transaction Active : false
==== innerMethod end ====
==== outerMethod end ====
```

outer, inner 메서드 **모두 트랜잭션이 적용되지 않는다.**

흔히 표현하는 **'프록시 객체 내부 메서드 호출 시 트랜잭션 적용이 안되는'** 문제

테스트 코드에서 호출된 simpleService.outerMethod()는 사실 **프록시 객체의 outerMethod를 호출한 것**이다.

**이 때, 프록시 객체 내부에서 실제 simpleService의 outerMethod()를 호출한다. (target.outerMethod())**

outerMethod 내에서 innerMethod를 호출하므로 다음과 같이 동작한다.

```java
public void outerMethod() {
    log.info("==== outerMethod start ====");
    log.info("==== outerMethod transaction Active : {}",
            TransactionSynchronizationManager.isActualTransactionActive());
    this.innerMethod(); // 주목
    log.info("==== outerMethod end ====");
}
```

즉, 트랜잭션 관련 코드가 존재하지 않는 **실제 객체의 innerMethod가 호출되어 트랜잭션이 적용되지 않는다.**

- 이를 해결하기 위해서 **@Transactional을 외부 메서드에 붙이는 것이 권장**된다.
- 내부메서드에 @Transactional을 붙여야 한다면 **별도의 클래스를 생성하여 해당 메서드를 사용**해야한다.

# 05. 트러블슈팅

추후 추가 예정

# 06. 자주 나오는 질문 정리

### @Transactional 에 대해 설명해주세요.

해당 어노테이션은 스프링 프레임워크에서 트랜잭션을 지원하는 데 사용되는 어노테이션입니다.

이 어노테이션을 사용하면 해당 메서드 또는 클래스의 메서드들이 트랜잭션 경계 내에서 실행됩니다.

즉, 여러 개의 연관된 데이터베이스 작업이 하나의 트랜잭션으로 묶여서 실행되거나, 전체가 성공 또는 실패할 때 롤백되는 등의 트랜잭션 처리를 할 수 있습니다.

### @Transactional 의 동작 과정에 대해 설명해주세요.

1. 프록시(Proxy) 객체 생성
    - Spring은 `@Transactional` 어노테이션이 붙은 클래스나 메서드를 감지하여 해당 객체의 프록시를 생성합니다.
2. 트랜잭션 시작
    - 프록시 객체는 메서드 실행 전에 `TransactionManager`를 통해 트랜잭션을 시작합니다.
    - 트랜잭션의 전파 방식(`propagation`)에 따라 새로운 트랜잭션을 생성하거나 기존 트랜잭션에 참여합니다.
3. 비즈니스 로직 실행
    - 트랜잭션이 시작된 상태에서 실제 비즈니스 로직이 실행됩니다.
4. 트랜잭션 종료
    - 메서드 실행이 정상적으로 완료되면 트랜잭션을 커밋합니다.
    - 실행 중 예외가 발생하면 트랜잭션을 롤백합니다.
        - 기본적으로 `RuntimeException`이나 `Error`가 발생하면 롤백됩니다.
        - `Checked Exception`의 경우, `rollbackFor` 속성을 사용하여 롤백을 지정할 수 있습니다.

### @Transaction에 readOnly 속성을 사용하는 이유에 대해 설명해주세요.

트랜잭션 안에서 수정/삭제 작업이 아닌 `ReadOnly` 목적인 경우에 주로 사용하며, 영속성 컨텍스트에서 엔티티를 관리 할 필요가 없기 때문에 readOnly를 추가하는 것으로 메모리 성능을 높일 수 있고, 데이터 변경 불가능 로직임을 코드로 표시할 수 있어 가독성이 높아진다는 장점이 있습니다.

readOnly 속성이 없는 보통의 트랜잭션은 데이터 조회 결과 엔티티가 영속성 컨텍스트에 관리되며, 이는 1차 캐싱부터 변경 감지(Dirty Checking)까지 가능합니다. 하지만, 조회시 스냅샷 인스턴스를 생성해 보관하기 때문에 메모리 사용량이 증가합니다.

### **A라는 Service 객체의 메소드가 존재하고, 그 메소드 내부에서 로컬 트랜잭션 3개(다른 Service 객체의 트랜잭션 메소드를 호출했다는 의미)가 존재한다고 할 때, @Transactional을 A 메소드에 적용하면 어떤 요청 흐름이 발생하는지 설명해주세요.**

트랜잭션 전파 수준에 따라 달라지는데, 만약 기본 옵션인 Required를 가져간다면 로컬 트랜잭션 3개가 모두 부모 트랜잭션인 A에 합류하여 수행됩니다. 

그래서 부모 트랜잭션이나 로컬 트랜잭션 3개나 모두 같은 트랜잭션이므로 어느 하나의 로직에서 문제가 발생하면 전부 롤백이 됩니다.

### 참고 자료

https://jiwondev.tistory.com/154

https://velog.io/@songheechoi/Spring-%EC%9E%98-%EC%82%AC%EC%9A%A9%ED%95%98%EA%B8%B0-Transactional%EC%97%90-%EB%8C%80%ED%95%98%EC%97%AC

https://velog.io/@kkambbak1/Transactional%EC%9D%98-%EB%8F%99%EC%9E%91%EC%9B%90%EB%A6%AC

https://velog.io/@jinyeong-afk/%EA%B8%B0%EC%88%A0-%EB%A9%B4%EC%A0%91-Spring-Transactional-%EB%8F%99%EC%9E%91-%EC%9B%90%EB%A6%ACfeat.-AOP

https://velog.io/@zxzz45/%EA%B8%B0%EC%88%A0-%EB%A9%B4%EC%A0%91%ED%8A%B8%EB%9E%9C%EC%9E%AD%EC%85%98

https://hungseong.tistory.com/81
