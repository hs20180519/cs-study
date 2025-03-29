# 00. Spring Boot 실전 Response & Exception 처리 패턴

서비스의 안정성과 사용자 경험을 위해 API 응답(Response)과 예외(Exception) 처리는 모든 서비스에서 필수적인 영역입니다. 실무에서는 일관된 응답 구조와 에러 핸들링 전략을 통해 프론트엔드, 백엔드 간 명확한 소통과 유지보수를 쉽게 만듭니다.


# 01. Response 구조 설계

### 공통 응답 클래스 (ApiResponse)

```java
public class ApiResponse<T> {

    private String code;
    private String message;
    private T data;

    public ApiResponse(String code, String message, T data) {
        this.code = code;
        this.message = message;
        this.data = data;
    }

    public static <T> ApiResponse<T> success(T data) {
        return new ApiResponse<>("SUCCESS", "요청이 성공적으로 처리되었습니다.", data);
    }

    public static <T> ApiResponse<T> fail(String code, String message) {
        return new ApiResponse<>(code, message, null);
    }
}
```

- 모든 API의 성공/실패 응답을 이 클래스로 통일
- `success()`, `fail()` 메서드로 편하게 사용 가능


# 02. ErrorCode Enum 설계

### ErrorCode Enum

```java
public enum ErrorCode {

    // 400 BAD REQUEST
    INVALID_INPUT_VALUE(400, "C001", "잘못된 입력 값입니다."),
    USER_ALREADY_EXISTS(400, "C002", "이미 존재하는 사용자입니다."),

    // 404 NOT FOUND
    USER_NOT_FOUND(404, "U001", "사용자를 찾을 수 없습니다."),

    // 500 INTERNAL SERVER ERROR
    INTERNAL_SERVER_ERROR(500, "S001", "서버 에러가 발생했습니다.");

    private final int status;
    private final String code;
    private final String message;

    ErrorCode(int status, String code, String message) {
        this.status = status;
        this.code = code;
        this.message = message;
    }

    public int getStatus() { return status; }
    public String getCode() { return code; }
    public String getMessage() { return message; }
}
```

- 모든 에러 코드를 Enum으로 관리
- status, code, message를 함께 정의하여 재사용성 확보



# 03. BaseException 정의

### 커스텀 Exception의 부모

```java
public class BaseException extends RuntimeException {

    private final ErrorCode errorCode;

    public BaseException(ErrorCode errorCode) {
        super(errorCode.getMessage());
        this.errorCode = errorCode;
    }

    public ErrorCode getErrorCode() {
        return errorCode;
    }
}
```

- 모든 Custom Exception은 BaseException을 상속하여 통일



# 04. 커스텀 Exception 작성

### 예: UserNotFoundException

```java
public class UserNotFoundException extends BaseException {

    public UserNotFoundException() {
        super(ErrorCode.USER_NOT_FOUND);
    }
}
```

- 상황별로 커스텀 예외를 만들어서 사용



# 05. GlobalExceptionHandler

###  전역 예외 처리기

```java
@RestControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(BaseException.class)
    public ResponseEntity<ApiResponse<Void>> handleBaseException(BaseException ex) {
        ErrorCode errorCode = ex.getErrorCode();
        return ResponseEntity
                .status(errorCode.getStatus())
                .body(ApiResponse.fail(errorCode.getCode(), errorCode.getMessage()));
    }

    @ExceptionHandler(Exception.class)
    public ResponseEntity<ApiResponse<Void>> handleException(Exception ex) {
        return ResponseEntity
                .status(HttpStatus.INTERNAL_SERVER_ERROR)
                .body(ApiResponse.fail("S000", "예상치 못한 서버 오류가 발생했습니다."));
    }
}
```

- BaseException을 상속한 모든 예외는 여기서 처리
- 예상치 못한 Exception은 `500`으로 처리



# 06. Controller 예시

```java
@RestController
@RequestMapping("/api/users")
public class UserController {

    @GetMapping("/{id}")
    public ResponseEntity<ApiResponse<UserDto>> getUser(@PathVariable Long id) {
        UserDto user = userService.findUserById(id);

        if (user == null) {
            throw new UserNotFoundException();
        }

        return ResponseEntity.ok(ApiResponse.success(user));
    }
}
```

- 비즈니스 로직에서 커스텀 예외를 던지고, GlobalExceptionHandler가 처리
- 클라이언트에는 항상 동일한 구조의 응답 제공



# 07. 정리

| 구성 요소 | 설명 |
|------------|------|
| ApiResponse | 공통 응답 객체 |
| ErrorCode | 상태 코드, 에러 코드, 메시지 관리 |
| BaseException | 공통 Exception 부모 클래스 |
| CustomException | 상황별 예외 정의 |
| GlobalExceptionHandler | 전역 예외 처리기 |
| ResponseEntity | 상태 코드 + ApiResponse 조합으로 반환 |

이 구조를 사용하면 API 응답이 항상 통일되고, 클라이언트와 협업도 쉬워지며 유지보수성이 올라갑니다.

> 실무에서는 여기에 추가로 Validation, BindingResult, 로깅, 에러 코드 자동화 등을 얹어 사용하는 경우도 많습니다.



# 08. 좋은 에러 메시지를 만드는 6가지 원칙
https://toss.tech/article/how-to-write-error-message

