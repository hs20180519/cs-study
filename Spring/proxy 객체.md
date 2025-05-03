# 00. 개요

proxy ‘대신하다’

JPA가 지원하는 지연 로딩은,

일반적으로 가장 많이 사용되는 JPA 구현체인 Hibernate가 사용하는 프록시 객체로 구현된다

## JPA Proxy란?

- 지연 로딩(lazy loading): 연관된 엔티티의 실제 데이터가 필요할 때까지 조회를 미루는 것
- 그럼 해당 엔티티를 연관관계로 가지고 있는 엔티티의 필드에 null을 넣어 두어야 할까?
    
    → 지연 로딩을 사용하는 연관관계 자리에 프록시 객체를 주입하여, 실제 객체가 들어있는 것처럼 동작하도록 함
    
- 엔티티 매니저의 `getReference` 메소드로도 프록시를 호출할 수 있음
    - 데이터베이스 조회 x, 실제 엔티티 객체 생성 x
    - 데이터베이스 접근을 위임한 프록시 객체 반환
    
    ![Image](https://github.com/user-attachments/assets/b2b05379-b807-4cd5-9fb0-306bd04d5985)
    

# 01. 동작원리

- 프록시는 **실제 객체의 상속본**
- 프록시 객체는 실제 객체에 대한 참조(target)을 보관
- 프록시 객체의 메소드를 호출하면 프록시 객체는 실제 객체의 메소드를 호출함
    
    → 프록시 객체가 실제 객체 타입 자리에 들어가도 문제없이 사용할 수 있는 이유
    

![Image](https://github.com/user-attachments/assets/e267d3ea-3b1a-4648-9aaa-a953b0ae0baf)

![Image](https://github.com/user-attachments/assets/3299b109-a1d5-4d51-9912-0689fbc440db)

## 프록시의 초기화

- 최초 지연 로딩 시점에는 참조 값이 없음 (데이터베이스에 접근하지 않았기 때문)
- 실제 객체의 메소드를 호출할 필요가 있을 때 데이터베이스를 조회하여 참조값을 채우게 됨
    
    → 이것이 프록시 객체의 초기화(한 번만 일어남)
    

### 예제

```java
@Entity
@NoArgsConstructor(access = AccessLevel.PROTECTED)
@AllArgsConstructor
@Getter
public class Member {
		@Id
		@GeneratedValue(strategy = GenerationType.IDENTITY)
		private Long id;
		
		@Column
		private String name;
		
		@JoinColumn(name = "team_id")
		@ManyToOne(fetch = Fetch.LAZY)
		private Team team;
		
}

// ----------------------------------------

@Entity
@NoArgsConstructor(access = AccessLevel.PROTECTED)
@AllArgsConstructor
@Getter
public class Team {
		@Id
		@GeneratedValue(strategy = GenerationType.IDENTITY)
		private Long id;
		
		@Column
		private String name;
		
}

```

- `Member`의 연관관계 `Team`은 lazy loading으로 설정되어 있음
    
    → `Member`를 조회하면 `team` 필드 자리에는 프록시가 들어갈 것
    
- 이 때 프록시 team의 getName 메소드를 호출하면:
    1. select 쿼리 실행
    2. 프록시 초기화

```java
Team team = member.getTeam();
System.out.println(team.getName()); // 프록시 초기화 시점
```

### 주의할 점

- 프록시가 **실제 객체를 참조하게 되는 것**이지, 실제 객체로 교체되는 것이 아님
- 프록시를 초기화하지 못하는 경우도 있음
- 프록시의 초기화는 영속성 컨텍스트의 도움을 받기 때문에,
- 영속성 컨텍스트의 관리를 받지 못하는 상황(준영속 상태인) 프록시를 초기화하거나
- OSIV 옵션이 꺼져 있을 때
- 트랜잭션 밖에서 프록시를 초기화하려는 경우 `LazyInitializationException`이 발생할 수 있음

→ 프록시를 초기화할 때는 프록시가 **영속 상태**여야 한다

</br>
**❓OSIV(Open Session In View)?**

- 영속성 컨텍스트를 뷰까지 열어두는 기능
- 뷰까지 영속성 컨텍스트가 살아있다면 뷰에서도 지연 로딩을 사용할 수 있음
- JPA는 기본적으로 켜져 있음
    
    [JPA - OSIV(Open Session In View) 정리](https://ykh6242.tistory.com/entry/JPA-OSIVOpen-Session-In-View%EC%99%80-%EC%84%B1%EB%8A%A5-%EC%B5%9C%EC%A0%81%ED%99%94)

</br>

### id를 조회할 때의 프록시

- 엔티티의 식별자를 조회할 때는 프록시를 초기화하지 않음

```java
Team team = member.getTeam();
System.out.pinrln(team.getId()); // 프록시 초기화가 일어나지 않음
```

**프록시 초기화 과정**

- `ByteBuddyInterceptor` 클래스 상위의 추상 클래스인 `AbstractLazyInitializer`가 초기화를 담당함
- 엔티티의 식별자를 조회하는 메소드를 호출하면 최종적으로는 `AbstractLazyInitializer`의 `getIdentifier`를 호출

```java
// AbstarctLazyInitializer

@Override
public final Serializable getIdentifier() {
    if (isUninitialized() && isInitializeProxyWhenAccessingIdentifier() ) {
    // 프록시가 아직 초기화되지 않았고
    // 식별자에 접근할 때 초기화하는 옵션이 켜져 있다면 초기화
        initialize(); 
    }
    return id;
}
```

+) 식별자에 접근할 때 초기화하는 옵션: `hibernate.jpa.compliance.proxy=true`(default: false)

- 주의점
    - 식별자 메소드가 `get + 필드명` 규칙을 따르지 않으면 `getIdentifier` 메소드를 호출하지 못하고 프록시가 초기화됨
    - id는 프록시가 아니라 프록시 내부 인터셉터에 들어 있음
        
        → 프록시 객체가 가진 필드값은 모두 `null`이므로, `user.id` 처럼 직접 접근하면 `null` 조회됨
        

### 프록시 초기화 여부 확인하기

- `EntityManagerFactor.getPersistenceUnitUtil().isLoaded()`

### 프록시 강제 초기화하기

- `org.hibernate.Hibernate.initialize()`

# 02. 특징

## 프록시 객체의 비교

- 요약: 비교할 때 `==` 말고 `instance of` 사용해야 함

```java
Team team = member.getTeam();
Team sameTeam = member.getTeam();
assertThat(team).isEqualTo(sameTeam); // error
```

→ 주소값이 같은 객체임에도 불구하고 equals 메소드를 통해 동등성을 비교하는 isEqualTo를 통과하지 못함

```java
// team 객체에 자동생성되는 equals 메소드

@Override
public boolean equals(Object o) {
    if (this == o) {
        return true;
    }
    if (o == null || getClass() != o.getClass()) {
        return false;
    }
    Team team = (Team) o;
    return Objects.equals(id, team.id);
}
```

- team.equals(sameTeam) 호출 시:
    - 프록시 `team`이 초기화 (`getId()`를 호출한 것이 아니기 때문)
    - 프록시 내부의 실제 `Team` 객체의 `equals()` 메소드 실행
    - 위 코드에서 `this.getClass()`는 `Team.class`이고, `o.getClass()`는 프록시 객체임
        
        → `getClass() != o.getClass()`
        
- 그래서 보통 하던 것처럼 `getClass`를 비교하도록 `equals`를 override하면 안 된다는 것
- 프록시는 원본 객체의 상속하기 때문에, `instanceof`는 통과하므로 동등성 비교가 가능해짐
- `Objects.equals(id, team.id)`
    - team.id는 null이므로 getId()로 실제 값을 얻어야 함

```java
@Override
public boolean equals(Object o) {
    if (this == o) {
        return true;
    }
    if (!(o instanceof Team)) {
        return false;
    }
    Team team = (Team) o;
    return Objects.equals(id, team.getId());
}
```

## 같은 영속성 컨텍스트 내 동일성 보장

- 프록시로 만들어진 엔티티에 대한 조회가 들어온다면, 영속성 컨텍스트는 실제 엔티티와 프록시 객체 중 어떤 객체를 반환해야 할까?
- 영속성 컨텍스트는 동일성을 보장함

**프록시가 최초로 생성된 경우**

- `getReference()` 또는 지연 로딩으로 프록시가 생성되면
- 영속성 컨텍스트는 이후 해당 엔티티에 대한 모든 조회 요청에 대해 프록시를 그대로 반환함
- 프록시가 초기화되더라도 실제 엔티티 객체로 교체되지 않고, 참조값만 가짐
- 객체 자체는 여전히 프록시

→ 프록시로 한번 만들어진 객체는 초기화를 하더라도 실제 엔티티로 교체되지 않고, 참조값만 가짐

**실제 엔티티가 최초로 저장된 경우**

- `find()`나 `persist()`를 통해 실제 엔티티 객체가 영속성 컨텍스트에 먼저 등록되면
- 이후 지연 로딩이나 프록시 생성 요청이 들어와도 프록시가 아닌 실제 엔티티를 반환

→ 이미 엔티티가 등록되어 있는 경우에는 프록시가 사용되지 않음

# 03. 자주 나오는 질문

## JPA에서 Proxy란 무엇인가요?

JPA에서 Proxy는 지연 로딩을 지원하기 위해 사용하는 가짜 객체입니다. 실제 엔티티를 대신해서 먼저 만들어지는 객체이고, 실제 데이터는 필요할 때 DB에서 불러오며 초기화됩니다. 초기화되더라도 객체 자체는 여전히 프록시 객체이고, 실제 엔티티 객체로 교체되지는 않습니다.

## Hibernate에서 Proxy는 어떻게 생성되고 사용되나요?

hibernate에서 프록시는 getReference() 메소드나 fetch=FetchType.LAZY 설정을 통해 생성됩니다. 처음에는 이 프록시 객체만 영속성 컨텍스트에 등록되고, 실제 필드들은 모두 null인 상태입니다.

다만, 프록시의 getId()처럼 프록시 내부에 있는 값만 쓸 경우에는 초기화 없이도 동작하며 다른 필드에 접근할 때 프록시가 초기화되면서 실제 DB를 조회합니다. 

초기화되더라도 프록시 객체는 실제 엔티티로 바뀌지 않고 내부적으로 값을 채운 상태로 유지됩니다.

# 참고자료

https://tecoble.techcourse.co.kr/post/2022-10-17-jpa-hibernate-proxy/
