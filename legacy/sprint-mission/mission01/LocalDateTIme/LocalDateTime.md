# Local Date Time 

객체의 생성시간과 수정시간을 관리하는 타입으로 `Long`을 사용한다

LocalDateTime으로 시간을 생성한 후 Long 타입으로 바꾸는 방법이 있는지 찾아보았다.

`java.time.LocalDateTime.getLong(TemporalField field)` 

Long은 타임스탬프이기 때문에 실제 날짜나 시간 데이터와는 형태가 다릅니다.

따라서 우리가 사용하기 좋은 구조로 데이터를 파싱하는 과정이 필요하다.

```java
// LocalDateTime -> Long
LocalDateTime.now().atZone(ZoneId.SystemDefault()).toInstant().toEpochMilli();

// Long -> LocalDateTime
LocalDateTime.ofInstant(Instant.ofEpochMilli(LocalDateTime time), ZoneId.systemDefault());
```