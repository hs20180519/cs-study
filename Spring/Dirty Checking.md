# 00. 개요

- Dirty: 엔티티 데이터의 변경된 부분
- Dirty를 체크한다 → 변경된 부분을 체크한다
- JPA에서 영속성 컨테이너가 관리하는 엔티티의 상태를 감지해서, 변경된 부분이 있다면 자동으로 트랜잭션이 끝나는 시점에 데이터베이스에 반영하는 기능
- 개발자가 update에 관련된 쿼리를 작성하지 않아도 되므로, 코드의 복잡성을 줄일 수 있다.

### 동작과정

1. 엔티티 조회
2. 조회된 상태대로 스냅샷 생성
3. 트랜잭션이 끝나는 시점에 스냅샷과 비교
4. 변경사항이 있다면 update 쿼리를 데이터베이스로 전달

### 변경된 데이터를 데이터베이스에 반영하는 시점

1. 트랜잭션 커밋
2. EntityManager flush()
3. JPQL

# 01. 더티 체킹 조건

### 1. 영속성 컨텍스트에서 관리되는 엔티티

- 영속성 컨텍스트는 엔티티를 처음 조회할 때 시작되며,(”변화가 있다”의 기준)
- 이후 변경을 감지하기 시작함
- 준영속/비영속 상태의 엔티티는 더티 체킹의 대상이 아님
    - 준영속: 영속성 컨텍스트와 분리된 엔티티
    - 비영속: 데이터베이스에 반영되기 전 처음 생성된 엔티티

**+) 영속성 컨텍스트?**

- 엔티티를 영구 저장하는 환경
- 애플리케이션과 데이터베이스 사이에서 객체를 보관하는 가상의 데이터베이스와 같은 역할

### 2. Transaction이 commit되었을 떄

- 트랜잭션이 커밋되기 전까지 영속성 컨텍스트는 변경사항을 추적하기만 하고, DB에 반영하지 않음

## 예제

### entityManager

```java
    public void updateNicknameWithEntityManager(int id, String nickname) {
        EntityManager em = entityManagerFactory.createEntityManager();
        EntityTransaction tx = em.getTransaction();

        tx.begin(); // 트랜잭션 시작
        User user = em.find(User.class, id);
        user.changeNickname(nickname); // 엔티티 변경
        tx.commit(); // 트랜잭션 커밋
    }
```

```java
    @Test
    void 엔티티매니저로_확인() {
        //given
        User user = userRepository.save(new User("test1", "test1@test.com"));

        //when
        String updateNickname = "test2";
        userService.updateNicknameWithEntityManager(user.getId(), updateNickname);

        //then
        User saved = userRepository.findAll().get(0);
        assertThat(saved.getNickname()).isEqualTo(updateNickname);
    }
```

<img width="564" alt="Image" src="https://github.com/user-attachments/assets/4440e26c-2e23-4c0b-abfd-54ddb009c94d" />

### Spring Data JPA

```java
    @Transactional
    public void updateNickname(int id, String nickname) {
        User user = userRepository.findById(id)
                .orElseThrow();

        user.changeNickname(nickname);
    }
```

```java
    @Test
    void Jpa로_확인() {
        //given
        User user = userRepository.save(new User("test1", "test1@test.com"));

        //when
        String updateNickname = "test2";
        userService.updateNickname(user.getId(), updateNickname);

        //then
        User saved = userRepository.findAll().get(0);
        assertThat(saved.getNickname()).isEqualTo(updateNickname);
    }
```

<img width="567" alt="Image" src="https://github.com/user-attachments/assets/d7b7799a-2f2e-4d3e-9be5-82c3d5728fd6" />

```java
    public void updateNickname(int id, String nickname) {
        User user = userRepository.findById(id)
                .orElseThrow();

        user.changeNickname(nickname);
    }
```

<img width="670" alt="Image" src="https://github.com/user-attachments/assets/770b03bf-a81f-48b5-8299-d5e440c615ab" />

# 02. @DynamicUpdate: 변경된 부분만 update하기

- Dirty Checking을 실행하면 SQL에서는 변경된 엔티티의 모든 내용을 update 쿼리로 만들어 전달함
- 이렇게 생성되는 update 쿼리는 기본적으로 엔티티의 **전체 필드**를 업데이트
    
    **장점**
    
    - 생성되는 쿼리가 같아, spring boot 실행 시점에 미리 만들어 재사용할 수 있음
    - 데이터베이스 입장에서도 쿼리 재사용이 가능함
        - 동일한 쿼리를 받으면 이전에 파싱된 쿼리를 재사용
    
    **단점**
    
    - 필드가 20~30개가 넘으면 전체 필드 update 쿼리가 부담스러울 수 있음
- @DynamicUpdate로 변경된 필드만 반영되도록 할 수 있음

```java
@Entity
@Getter
@DynamicUpdate
@NoArgsConstructor
public class User {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private int id;
    private String nickname;
    private String email;
```

<img width="626" alt="Image" src="https://github.com/user-attachments/assets/70b92851-d941-4fe8-bd71-349e47621501" />

# 03. 자주 나오는 질문

## JPA의 더티 체킹이 무엇인가요?

JPA의 더티 체팅이란 영속성 컨테이너가 관리하는 엔티티의 상태를 감지해서, 트랜잭션이 끝나는 시점에 변경된 부분이 있을 경우 자동으로 데이터베이스에 반영하는 기능입니다.

영속성 컨텍스트는 엔티티를 처음 조회할 때 스냅삿을 생성하며, 트랜잭션이 커밋되는 시점에 스냅샷과 비교하여 update 쿼리를 만듭니다.

더티 체킹은 영속성 컨텍스트가 관리하는 엔티티에만 적용되며, 준영속 및 비영속 엔티티에는 적용되지 않습니다.

# 참고자료

https://jojoldu.tistory.com/415
