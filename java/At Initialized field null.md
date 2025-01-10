# 생성자 초기화 시 필드 값 `null`

## 동기
객체를 초기화 할때 null을 허용하는 필드의 경우 어떻게 초기화하는게 좋을까 고민하게 되었다.

```java
public abstract class AbstractUUIDEntity {

    private final UUID id;

    private final Long createAt;

    protected Long updateAt;

    private Status status;
    
    protected AbstractUUIDEntity() {
        this.id = UUID.randomUUID();
        this.createAt = createUnixTimestamp();
        this.status = REGISTERED;
    }
}
```
수정한 날짜는 초기화 시 필요없는 값이기 때문에 별다른 조치를 해주지 않았는데, 다른 사람이 보았을 때,
이 객체의 updateAt 값은 null의 가능성이 있다는 것을 알 수 없다는 생각이 들었다. 그래서 객체를 사용할 때 
수정 날짜를 가져오려고 할 때 `NullPointerException`을 직면할 수 있다.

## 어떻게 해주었나 ?

`null` 일 수 있는 값을 가지고 오는 get 메서드의 반환 값에는 `Optional` 객체로 감싸서 리턴해주었고, 
코드를 읽는 사람이 바로 업데이트 날짜는 null 값이 들어있을 수도 있겠구나 생각이 들 수 있도록
필드에 null값을 명시적으로 대입해 주었다.

```java
public abstract class AbstractUUIDEntity {

    private final UUID id;

    private final Long createAt;

    protected Long updateAt = null;

    private Status status;

    protected AbstractUUIDEntity() {
        this.id = UUID.randomUUID();
        this.createAt = createUnixTimestamp();
        this.status = REGISTERED;
    }

    public Optional<Long> getUpdateAt() {
        return Optional.ofNullable(updateAt);
    }
    
    // ... other method
}
```
- 클래스 설계와 초기화와 관련하여 명시적으로 값을 초기화하여 의도를 드러내는 것이 중요하다. (이펙티브 자바)
- 코드 가독성과 의도 전달을 강조하며, 모호한 코드보다는 명확한 코드가 협업과 유지 보수에 유리하다. "명시적인 것이 암묵적인 것보다 낫다" (클린코드)
- 초기화 상태를 명확히 하는 것이 코딩 표준에 포함된다. (oracle java coding standard)
- 자바 코드 정적 분석 도구 : sonarQube에서 명시적으로 null을 권장

*값 초기화를 명확히 함으로써 코드 의도를 명확히 할 수 있다*에 따라서 생성자와 필드에 초기값을 넣어줄 수 있다.
 내가 선택한 방법은 필드에 초기화하는 방법이다. 클래스를 설계할 때 가장 먼저 마주치는 것은 `field`이기 때문에 바로 눈에 띄도록 하는게 좋다고 생각했다.

ps. `constructor` 는 `field` 아래 쪽에 작성하는 것이 권장 사항으로 알고 있다. 