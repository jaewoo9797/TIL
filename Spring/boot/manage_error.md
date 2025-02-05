스프링 부트에서 계층적인 예외 관리를 설계하기

1. 최상위 예외 클래스: 모든 비즈니스 예외의 공통 부모 클래스 (`BusinessException`)를 만든다.
2. 도메인별 예외 클래스: `UserException`, `OrderException`등으로 비즈니스 도메인별로 예외 클래스를 정의한다.
3. 세부적인 예외 클래스: `UserNameInvalidException`, `UserEamilInvalidException`같은 세부적인 예외를 정의한다.
4. Global Exception Handler: `@RestControllerAdvice`를 활용해 공통적으로 예외를 처리한다.

## ✔️ 예제: 계층적 예외 설계 및 핸들링

**최상위 예외 클래스** (`BusinessException`)

<br/>

```java
public abstract class BusinessException extends RuntimeException {
  private final ErrorCode errorCode;

  protected BusinessException(String message, ErrorCode errorCode);

  protected BusinessException(ErrorCode errorCode) {
    super(errorCode.getMessage());
    this.errorCode = errorCode;
  }

  public String getErrorCode() {
    return errorCode;
  }

}
```

- 모든 비즈니스 예외의 부모 클래스.
- `errorCode`를 추가하여 특정 오류 코드 관리

**도메인별 예외 클래스** (`UserException`)

```java
public abstract class UserException extends BusinessException {
  protected UserException(String message, ErrorCode errorCode) {
    super(message, errorCode);
  }
}
```

- `UserException`은 `BusinessException`을 상속받고, 유저 관련 예외의 부모 역할

**세부 예외 클래스** (유저)

```java
public class UserNameInvalidException extends UserException {
  public UserNameInvalidException(String message) {
    super(message, ErrorCode.INVALID_VALUE)
  }
}

public class UserEmailInvalidException extends UserException {
  public UserEmailInvalidException() {
    super(..., ...)
  }
}
```

- `UserException`을 상속받아 세부적인 유저 관련 예외를 정의
- `errorCode`를 설정하여 클라이언트가 명확한 에러를 받을 수 있도록 함.
