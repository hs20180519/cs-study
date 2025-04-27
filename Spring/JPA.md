
## 01. 개념

### 개념

- 객체와 관계형 데이터베이스를 매핑하기 위한 자바 표준 라이브러리로, **JPA (Java Persistence API)** 는 자바 ORM(Object-Relational Mapping) 표준 인터페이스이다.
- JPA는 **EJB(Entity Bean)** 의 복잡함과 무거움을 대체하기 위해 등장하였으며, **자바 EE 5 (2006년)** 부터 표준으로 포함되었다.
- JPA는 **인터페이스만 제공**하며, 실제 구현체는 따로 존재한다.
    - **Hibernate** (가장 널리 쓰이며, JPA 표준의 사실상 기준 역할
        - Spring Boot를 쓰면 **기본 JPA 구현체로 Hibernate가 설정**
        - 2차 캐시(Second-level Cache) 지원
        - 엔티티를 저장할 때 **배치 처리(batch insert/update)** 지원
    - EclipseLink (JPA 공식 참조 구현)
    - OpenJPA (Apache)

### 사용법

- Maven 또는 Gradle로 JPA 의존성 추가 (Hibernate 포함)
- `persistence.xml` 혹은 `application.yml` 설정
- `@Entity`로 엔티티 클래스 지정
- `@Id`, `@GeneratedValue` 등으로 매핑

```java
@Entity
public class Member {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String name;
}
```

Spring Boot 환경 설정 예시 (`application.yml`):

```yaml
spring:
  datasource:
    url: jdbc:mysql://localhost:3306/example
    username: root
    password: password
  jpa:
    hibernate:
      ddl-auto: update
    show-sql: true
    properties:
      hibernate:
        format_sql: true

```

## 02. JPA 기본 구조와 개념

### 요약

- JPA는 객체와 관계형 데이터베이스 간의 매핑을 담당한다.
- JPA의 핵심 동작 흐름:
    1. `EntityManager`를 통해 엔티티를 관리
    2. `영속성 컨텍스트(Persistence Context)`에 의해 엔티티의 상태를 추적
    3. 트랜잭션 커밋 시 변경 사항을 자동으로 감지하여 DB에 반영 (Dirty Checking)
    4. `JPQL`을 사용하여 객체 지향적으로 데이터 조회
- 엔티티 생명주기: 비영속 → 영속 → 준영속 → 삭제

### 1. Persistence Context (영속성 컨텍스트)

- **영속성**: 자바 객체를 데이터베이스에 저장해 프로그램이 종료되어도 데이터를 유지하는 것.
- **Persistence Context**: 1차 캐시처럼 동작하는 공간으로, Entity를 관리하는 메모리 영역이다.
- 한 번 조회한 Entity는 Persistence Context에 저장되고, 다시 조회할 때는 DB가 아니라 메모리에서 바로 가져온다.

### 2. Entity (엔티티)

- **Entity**: 데이터베이스 테이블과 매핑되는 자바 객체를 의미한다.

```java
@Entity
public class User {
    @Id
    private Long id;
    private String name;
}
```

- `@Entity` 어노테이션 사용

### 3. EntityManager (엔티티 매니저)

- **EntityManager**: Entity를 관리하는 주요 인터페이스이다.
- EntityManager는 내부적으로 **Persistence Context**를 관리한다.
- EntityManager를 통해 Entity의 **저장**, **조회**, **수정**, **삭제**를 수행할 수 있다.
- 직접 SQL을 작성하지 않고, EntityManager의 메서드를 호출하면 JPA가 자동으로 SQL을 생성하고 실행한다.
- 즉, **자바 객체 ↔ 데이터베이스 데이터 변환**을 자동으로 처리해준다.

```java
EntityManager em = ...; // 주입받거나 직접 생성
User user = em.find(User.class, 1L); // id=1인 User 조회
em.persist(user); // User 저장
em.remove(user); // User 삭제

```

## 3. JPQL (Java Persistence Query Language)

**1. JPQL이란?**

- **JPQL**은 **JPA를 사용할 때 엔티티(Entity) 객체를 대상으로 쿼리를 작성하는 언어**이다.
- 즉, **DB 테이블**을 직접 다루는 것이 아니라, **자바 객체(Entity)**를 기준으로 쿼리를 짠다.

**2. SQL과 JPQL의 차이**

- **SQL**은 데이터베이스 안의 **테이블**과 **컬럼**을 기준으로 작성한다.
- **JPQL**은 **Entity 클래스 이름**과 **필드 이름**을 기준으로 작성한다.

| 구분 | SQL | JPQL |
| --- | --- | --- |
| 대상 | 테이블 | 엔티티 |
| 컬럼 | 컬럼 이름 | 필드 이름 |
| 목적 | 데이터베이스 조회 | 객체 조회 후 데이터베이스 접근 |

**3. 예시**

- **SQL**

```sql
SELECT * FROM user WHERE name = 'kim';
```

: `user`라는 테이블에서 `name`이라는 컬럼이 `'kim'`인 데이터를 가져온다.

- **JPQL**

```java
String jpql = "SELECT u FROM User u WHERE u.name = 'kim'";
List<User> users = em.createQuery(jpql, User.class).getResultList();
```

- `User`라는 **Entity 클래스**를 대상으로, 그 안의 `name`이라는 **필드**가 `'kim'`인 객체들을 조회한다.
    - 이 때, `User`는 **자바 클래스** 이름이고, `u.name`은 **자바 필드** 이름

1. **장점**
- JPA는 **DB 독립성**을 제공한다.
- SQL은 데이터베이스 종류에 따라 문법이 달라질 수 있지만, JPQL은 **하나의 문법**으로 다양한 데이터베이스(MySQL, Oracle 등)에서 쓸 수 있다.
- JPA가 JPQL을 적절한 SQL로 변환해서 DB에 날려주기 때문에, 개발자는 DB에 종속되지 않고 **자바 코드만 신경** 쓰면 된다.

## 4. 영속성 컨텍스트 (Persistence Context)

### 개념

- **영속성**이란 자바 객체를 데이터베이스에 저장해서 "계속 살아있게" 하는 것이다.
- **영속성 컨텍스트**는 JPA가 엔티티 객체를 저장하고 관리하는 **1차 캐시(메모리 저장소)** 역할을 한다.
- **EntityManager**가 내부적으로 관리하며, 모든 데이터 변경은 컨텍스트를 통해 이루어진다.

### 장점

- **DB 접근을 최소화**하여 성능 향상
- **트랜잭션 내 데이터 일관성**을 유지
- **객체 변경만으로 DB 자동 반영** (Dirty Checking)
- 복잡한 SQL 없이 **비즈니스 로직 중심 개발**이 가능

### 주요 기능

#### 1. 엔티티 생명 주기 관리

| 상태 | 설명 |
| --- | --- |
| 비영속 (new) | 영속성 컨텍스트와 관련 없는 상태 |
| 영속 (managed) | 컨텍스트에 저장되어 관리되는 상태 |
| 준영속 (detached) | 컨텍스트에서 분리된 상태 |
| 삭제 (removed) | 삭제가 예정된 상태 |

#### 2. 1차 캐시와 동일성(identity) 보장

- 영속성 컨텍스트에 저장된 엔티티는 메모리에 캐시된다.
- **같은 트랜잭션 내에서 같은 ID를 조회하면 항상 같은 객체를 반환**한다.

```java
Member m1 = em.find(Member.class, 1L);
Member m2 = em.find(Member.class, 1L);
System.out.println(m1 == m2); // true

```

- `==` 비교로도 동일성 확인이 가능하다.

#### 3. 변경 감지 (Dirty Checking)

- 영속 상태의 엔티티가 변경되면, 별도로 `update`를 호출하지 않아도 트랜잭션 커밋 시점에 자동으로 **UPDATE SQL**이 발생한다.

```java
member.setName("변경된 이름"); // 트랜잭션 커밋 시점에 UPDATE
```

- **객체만 수정하면 DB가 자동으로 반영**된다.

#### 4. 용어

**지연 로딩 (Lazy Loading)**

- 연관된 엔티티는 처음부터 로딩하지 않고, 실제로 필요한 시점에 DB에서 조회한다.
- 메모리와 성능 최적화를 위해 기본적으로 사용된다.

**Flush (플러시)**

- 영속성 컨텍스트에 쌓인 변경 내용을 데이터베이스에 **반영**하는 과정
- 트랜잭션 커밋 시 자동 호출되며, 필요하면 명시적으로 `em.flush()`로 강제할 수도 있다.

**Detach, Clear, Close(컨텍스트 제어)**

| 메서드 | 설명 |
| --- | --- |
| `detach(entity)` | 특정 엔티티만 관리 대상에서 분리 |
| `clear()` | 모든 엔티티를 영속성 컨텍스트에서 제거 |
| `close()` | EntityManager 자체를 종료 |

## 5. JPA 엔티티 생명주기

| 단계 | 설명 |
| --- | --- |
| 비영속 | 메모리에만 존재. 아직 관리 안 됨 |
| 영속 | 영속성 컨텍스트에 저장되어 관리됨 |
| 준영속 | 영속성 컨텍스트에서 분리된 상태 |
| 삭제 | 삭제가 예정된 상태 (트랜잭션 커밋 시 DB 삭제) |

```
[비영속]
   ↓ em.persist()
[영속]
   ↓ em.detach() 또는 em.clear()
[준영속]
   ↓ em.remove()
[삭제]
   ↓ COMMIT
[DB에 반영]
```

#### 1. 비영속 (Transient)

- 아직 **EntityManager가 관리하지 않는** 상태
- 자바 메모리에만 존재하는 객체

```java
Member member = new Member();
member.setName("kim");  // 단순한 메모리 객체

```

#### 2. 영속 (Persistent)

- `em.persist(member);`를 호출하면 **영속성 컨텍스트가 관리**하는 상태가 된다.
- 1차 캐시에 등록되며, 트랜잭션 커밋 시 자동으로 DB에 INSERT된다.

```java
em.persist(member); // member 객체는 "영속" 상태
```

#### 3. 준영속 (Detached)

- 영속성 컨텍스트에서 **분리(detach)** 된 상태.
- 더 이상 EntityManager가 관리하지 않는다.
- 데이터 변경해도 DB에 반영되지 않는다.
- `em.clear()` 호출 시 전체 엔티티가 준영속 상태로 된다.

```java
em.detach(member);

```

#### 4. 삭제 (Removed)

- `em.remove(member);`를 호출하면 **삭제 예정 상태**가 된다.
- 트랜잭션을 커밋하면 실제로 DB에서 DELETE 쿼리가 날아간다.

```java
em.remove(member); // 삭제 예정 상태
```

## 06. N+1 문제

- **문제 정의**: 연관된 엔티티를 조회할 때 쿼리가 1 + N번 실행되는 문제
- 예시:

```java
List<Team> teams = em.createQuery("select t from Team t", Team.class).getResultList();
for (Team team : teams) {
    System.out.println(team.getMembers());
}
```

- 해결 방법:
    - **Fetch Join** 사용:

```java
select t from Team t join fetch t.members
```

- **EntityGraph** 또는 **BatchSize** 설정
- **LAZY 로딩 전략**을 명시하고 상황에 따라 직접 로딩

# 06. N+1 문제

- N+1 문제란, 하나의 메인 쿼리(1번)로 엔티티를 조회하며, 연관된 엔티티를 각각 조회하느라 추가로 N번 쿼리가 발생하는 문제를 말한다.

### 예시

```java
List<Team> teams = em.createQuery("select t from Team t", Team.class).getResultList();
for (Team team : teams) {
    System.out.println(team.getMembers());
}

```

- 첫 번째 쿼리 (1번):
    
    ```sql
    SELECT * FROM team;
    ```
    
- 그 다음, 각 팀마다 멤버를 가져오기 위해 추가 쿼리 (N번)발생
    
    ```sql
    SELECT * FROM member WHERE team_id = ?;
    SELECT * FROM member WHERE team_id = ?;
    ...
    ```
    

### 패치 타입(FetchType)이란?

- 엔티티 연관 관계를 로딩할 때 언제 데이터를 가져올지를 정하는 방법
1. **EAGER(즉시 로딩)**
    - 조회할 때 **연관된 엔티티도 즉시 함께 조회**한다.
- 조인 쿼리가 자동으로 발생한다.
1. **LAZY(지연 로딩)**
    - **필요할 때까지 가져오지 않고 기다린다**. (프록시 객체만 넣어둠)
    - 실제로 사용되는 순간 별도 쿼리를 날린다.

**JPA 기본 전략**

- `@ManyToOne`, `@OneToOne` → 기본이 **EAGER**
- `@OneToMany`, `@ManyToMany` → 기본이 **LAZY**

### N+1 문제 해결 방법

**1.Fetch Join 사용**

- 연관된 엔티티를 함께 한 번에 가져오는 방법.
- JPQL 작성할 때 `join fetch` 키워드 사용.

```java
List<Team> teams = em.createQuery(
    "select t from Team t join fetch t.members", Team.class)
    .getResultList();

```

**2.EntityGraph 사용**

- JPQL 없이도 어떤 연관 필드를 즉시 로딩할지 명시할 수 있는 방법.
- 주로 EAGER 로딩을 사용 시, 특정 연관 필드만 즉시 로딩시키기 위해 사용된다

```java
@EntityGraph(attributePaths = {"members"})
List<Team> findAll();
```

**3.Batch Size 설정**

- **Batch Size** : 데이터베이스에서 여러 개의 쿼리나 데이터를 한 번에 묶어서 처리하는 방법
- 여러 건의 LAZY 연관 엔티티를  **IN 절**로 한 번에 조회해서 한꺼번에 로딩하는 방법

```
hibernate.default_batch_fetch_size=100
```

```java
@BatchSize(size = 100)
private List<Member> members;
```

## 07. Identifier + Auto Increment 조합 사용 시 문제점

- `@GeneratedValue(strategy = GenerationType.IDENTITY)` 사용 시:
    - DB에서 ID를 직접 생성하므로, **JPA는 INSERT 이후에야 ID 값을 알 수 있다.**
    - 따라서 `persist()` 호출 시 쿼리가 **즉시 실행**되어야 하며, **배치 삽입이 불가능**하다.
    - 영속성 컨텍스트에 쌓아뒀다가 flush 시 일괄 처리하는 기능을 사용할 수 없음.
- 대안:
    - `SEQUENCE` 전략 사용 (`GenerationType.SEQUENCE`)
        - DB에서 시퀀스를 먼저 조회해 ID 확보 → 영속성 컨텍스트에서 관리 가능
        - 배치 처리 및 ID 선점 가능

## 08. 면접에서 자주 나오는 질문 / 답변

**Q1. JPA와 Hibernate의 차이는 무엇인가요?**

A. JPA는 자바 ORM의 **표준 인터페이스**, Hibernate는 그 **구현체**이다. JPA를 통해 코드를 작성하면 Hibernate뿐 아니라 다른 구현체로도 교체 가능하다.

**Q2. 영속성 컨텍스트란 무엇인가요?**

A. 엔티티를 저장하고 관리하는 JPA 내부의 메모리 저장소이다. 엔티티의 변경 사항을 감지(Dirt Checking)하고, 1차 캐시, 동일성 보장, 지연 로딩 등을 제공한다.

**Q3. FetchType.EAGER와 FetchType.LAZY의 차이는?**

A. EAGER는 즉시 로딩, LAZY는 지연 로딩이다. LAZY를 기본으로 설정하고, 필요한 시점에 Fetch Join 등을 통해 조회하는 것이 성능상 유리하다.

**Q4. JPA를 사용할 때의 장점은 무엇인가요?**

A. 생산성 향상, 객체 지향적인 개발, SQL 작성 최소화, 트랜잭션 관리의 단순화, 유지보수 편의성 등이다.

**Q5. @Transactional이란?**

A. 해당 메서드 또는 클래스의 트랜잭션 범위를 선언한다. 메서드 실행 중 예외가 발생하면 롤백, 정상 완료 시 커밋을 수행한다.

## 09. 참고자료

- [JPA 공식 문서](https://jakarta.ee/specifications/persistence/)
- [Hibernate 공식 문서](https://hibernate.org/)
- 김영한 - 자바 ORM 표준 JPA 프로그래밍
