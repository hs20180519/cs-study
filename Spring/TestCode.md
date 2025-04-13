# 00. 테스트 코드

왜 테스트 코드를 작성해야 할까?

1. 개발 단계 초기에 문제를 발견할 수 있다.
2. 나중에 코드를 리팩토링하거나 라이브러리 업그레이드 시, 기존 기능이 잘 작동하는 지 확인 가능하다.
3. 기능에 대한 불확실성을 낮춘다.

테스트 코드를 쓰지 않는다면?

```
1. 코드를 작성한 뒤 프로그램을 실행하여 서버를 켠다.
2. API 프로그램으로 HTTP 요청 후 결과를 확인
3. 결과가 예상과 다르면, 다시 프로그램을 종료한 뒤 코드를 수정하고 반복한다.
```

테스트 코드는 위의 과정을 수동으로 거치는 대신, 자동으로 검증할 수 있게 해준다.

# 01. 단위 테스트

> 단위 테스트(Unit Test)는 소프트웨어의 가장 작은 단위인 모듈, 함수, 클래스, 메서드 등을 개별적으로 분리하여 테스트하는 방법입니다. 이를 통해 각 단위가 의도한 대로 동작하는지 확인할 수 있습니다.
> 

### 예시

```java
@ExtendWith(MockitoExtension.class)
class UserServiceTest {

    @Mock
    private UserRepository userRepository;

    @InjectMocks
    private UserService userService;

    @Test
    void testFindUserById() {
        // Given
        Long userId = 1L;
        User mockUser = new User(userId, "Alice");
        when(userRepository.findById(userId)).thenReturn(Optional.of(mockUser));

        // When
        User result = userService.findUserById(userId);

        // Then
        assertEquals("Alice", result.getName());
    }
}

```

### @ExtendWith(MockitoExtension.class)

JUnit 5에서 Mockito를 사용하기 위해 필요한 어노테이션이다.

Mockito의 기능을 JUnit 5 테스트에 통합하여, Mock 객체를 자동으로 초기화하고 주입할 수 있도록 도와준다.

### @Test

테스트 메서드를 나타낸다.

JUnit 5에서 테스트 메서드를 정의할 때 사용하며, 해당 메서드는 테스트 실행 시 자동으로 호출된다.

### @Mock

Mock 객체를 생성한다.

테스트 대상 클래스의 의존성을 가짜(Mock) 객체로 대체하여, 테스트의 독립성을 유지하고 외부 요소의 영향을 제거한다.

```java
  @Mock
  private UserRepository userRepository;
```

### @InjectMocks

Mock 객체를 테스트 대상 클래스에 주입한다.

`@Mock` 으로 생성된 Mock 객체들을 테스트 대상 클래스의 의존성으로 주입하여, 해당 클래스의 메서드를 테스트할 수 있다.

```java
  @InjectMocks
  private UserService userService;
```

## BDD 스타일 - Behavior Driven Development

- Given: 주어진 상황을 설정하는 부분
- When: 테스트할 기능을 호출하고 실행하는 부분
- Then: 기대한 결과를 검증하는 부분

# 02. 웹 계층 테스트

> 웹 계층 테스트는 스프링 MVC의 컨트롤러를 중심으로 한 테스트로, 실제 웹 서버를 실행하지 않고도 HTTP 요청과 응답을 시뮬레이션하여 컨트롤러의 동작을 검증한다. 이를 통해 빠르고 효율적인 테스트가 가능하다.
> 

### 예시

```java
@WebMvcTest(controllers = HomeController.class)
class HomeControllerTest {

    @Autowired
    private MockMvc mockMvc;

    @MockBean
    private HomeService homeService;

    @Test
    void testHome() throws Exception {
        // Given
        String expectedResponse = "Hello, World!";
        when(homeService.getGreeting()).thenReturn(expectedResponse);

        // When & Then
        mockMvc.perform(get("/"))
               .andExpect(status().isOk())
               .andExpect(content().string(expectedResponse));
    }
}

```

### @WebMvcTest(controllers = `컨트롤러클래스명.class`)

`컨트롤러 클래스` 에 대한 테스트를 수행한다.

### @MockBean

HomeController가 의존하는 HomeService를 모킹하여 테스트에 주입한다.

# 03. 통합 테스트

> 통합 테스트(Integration Test)는 애플리케이션의 여러 구성 요소가 함께 동작할 때의 상호작용을 검증하는 테스트이다. 스프링 부트에서는 `@SpringBootTest` 어노테이션을 사용하여 전체 애플리케이션 컨텍스트를 로드하고, 실제 환경과 유사한 조건에서 테스트를 수행할 수 있다.
> 

### 예시

```java
@SpringBootTest
@AutoConfigureMockMvc
class UserControllerIntegrationTest {

    @Autowired
    private MockMvc mockMvc;

    @Test
    void testGetUser() throws Exception {
        mockMvc.perform(get("/users/1"))
               .andExpect(status().isOk())
               .andExpect(jsonPath("$.name").value("John Doe"));
    }
}

```

### @SpringBootTest

애플리케이션의 모든 빈(Bean)을 로드하여 전체 컨텍스트에서 테스트를 수행한다.

- 전체 애플리케이션 컨텍스트를 로드하므로 실제 환경과 유사한 테스트가 가능하다.
- 전체 애플리케이션 컨텍스트를 로드하므로 테스트 실행 시간이 길어질 수 있다.

### @AutoConfigureMockMvc

`MockMvc`를 자동으로 구성하여, 웹 계층의 테스트를 지원한다.

```java
  @SpringBootTest
  @AutoConfigureMockMvc
  class MyIntegrationTest {
      @Autowired
      private MockMvc mockMvc;
  }

```

### @Transactional

테스트 메서드 실행 후 트랜잭션을 롤백하여 데이터베이스 상태를 초기화한다.

# 04. CI/CD에서 테스트 활용

1. 테스트 자동화
2. 테스트 커버리지 측정
    
    예를 들어, 테스트 커버리지가 80% 이하일 경우, 테스트 실패하게 하기
    

참고

https://cookie-dev.tistory.com/20?utm_source=chatgpt.com

# 05. 자주 나오는 질문 정리

### @MockBean 과 @Mock의 차이는 무엇인가요?

- **`@MockBean`**: 스프링 컨텍스트에 Mock 객체를 주입합니다. 주로 `@SpringBootTest`나 `@WebMvcTest`와 함께 사용되며, 스프링의 의존성 주입을 통해 Mock 객체를 테스트 대상에 주입합니다.
- **`@Mock`**: Mockito에서 제공하는 어노테이션으로, 스프링 컨텍스트와 무관하게 단위 테스트에서 Mock 객체를 생성할 때 사용됩니다.

### 테스트에서 외부 API 호출은 어떻게 처리하나요?

외부 API 호출은 테스트의 독립성을 해칠 수 있으므로, `MockRestServiceServer`나 `WireMock`과 같은 도구를 사용하여 Mocking하는 것이 좋습니다. 이를 통해 외부 시스템에 의존하지 않고도 테스트를 수행할 수 있습니다.

### 테스트 코드 작성 시 `Given-When-Then` 패턴이란 무엇인가요?

`Given-When-Then` 패턴은 테스트 코드를 구조화하는 방법으로, 다음과 같습니다.

- **Given**: 테스트의 초기 상태 설정
- **When**: 테스트 대상 동작 실행
- **Then**: 예상 결과 검증
