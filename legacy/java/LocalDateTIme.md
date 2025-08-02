## LocalDateTime

시간관련 자바 객체인 LocalDateTime

- LocalDate: 년, 월, 일 정보
- LocalTime: 시, 분, (초), (나노초) 정보
- LocalDateTime: 년, 월, 일, 시, 분 (초), (나노초) 정보

LocalDate + LocalTime -> LocalDateTime

```java
LocalDateTIme newLocalDateTime = LocalDateTime.of(localDate, localTime);
```

LocalDateTime -> LocalDate & LocalTime

```java
LocalDate newLocalDate = newLocalDateTime.toLocalDate();
LocalTime newLocalTime = newLocalDateTime.toLocalTime();
```

Java8 Date/Time API의 `LocalDate`, `LocalTime`, `LocalDateTime`

이 3개의 클래스는 모두 타임존 개념을 가지고 있지 않기 때문에 타임존에 따른 시간 변환이 불가능하다는 공통점을 가지고 있습니다.

자바 8이전의 Date, Calendar 클래스들은 여러 가지 문제점을 가지고 있었습니다.

- 불변성 부족: Date 클래스의 인스턴스는 변경 가능했습니다. 이로 인해 날짜 객체가 예기치 않게 변경될 위험이 있었습니다.
- 스레드 안전하지 않음: 여러 스레드에서 동시에 Date 객체를 수정할 때 동기화 문제가 발생할 수 있었습니다.
- 표준화된 시간대 처리 부재: Date는 시간대를 고려하지 않아 국제화된 애플리케이션에서는 문제가 발생하기 쉬웠습니다.
- 복잡한 API: Calendar 클래스는 사용하기 복잡했으며, 직관적이지 않은 메서드를 많이 포함하고 있었습니다.

자바8에서는 이러한 문제들을 해결하기 위해 java.time 패키지를 도입했습니다. 이 패키지는 불변성을 제공하며, 사용하기 쉽고, 스레드 안전하며, 시간대를 적절히 처리할 수 있도록 설계되었습니다.
