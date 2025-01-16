# UUID

미션에 요구사항으로 각 객체를 구별하기 위한 `ID` 값으로 `UUID` 객체를 사용하라는 요구사항으로 인해 학습해보고자 한다.

일단 API 명세서를 살펴본다.

## API Reference
```java
public final class UUID extends Object implements Serializable, Comparable<UUID> {
  ...
}
```

이 클래스는 고유 식별자(UUID)로 불변 범용 식별자이다. 128비트 값을 나타낸다.

`UUID` 에는 네 가지 기본 유형 존재
- 시간 기반
- DCE 보안
- 이름 기반
- 무작위로 생성된 UUID

이러한 유형의 버전 값은 각각 1,2,3,4 이다.

UUID 생성자
```java
UUID(
  long mostSigBits,
  long leastSigBits
)
```
매개변수의 의미

1. mostSigBits : UUID의 상위 64비트를 나타낸다.
2. leastSigBits : UUID의 하위 64비트를 나타낸다.

UUID는 총 128비트(16바이트)로 구성되며 이를 두 개의 비트로 나눠 관리한다.
```java
UUID uuid = new UUID(0x123456789ABCDEF0L, 0xFEDCBA9876543210L);
System.out.println(uuid.toString());
```

일반적으로 생성하는 방법

- `UUID.randomUUID()` : 기본적으로 버전 4 UUID를 생성하며, 내부적으로 `SecureRandom`이나 `ThreadLocalRamdom`을 사용해 채운다.

값을 확인 하는 방법

```java
UUID uuid = UUID.randomUUID();
long mostSigBits = uuid.getMostSignificantBits();
long leastSigBits = uuid.getLeastSignificantBits();
```

## UUID 정의
네임 스페이스 : Java.Util

변경할 수 없는 UUID(범용 고유 식별자)를 나타내는 클래스입니다.

UUID.toString()을 할 경우 '-' dash 가 존재한다. 이를 제거하고 사용하길 원하면 뒤에 replace("-","") 메서드를 사용한다.

### ps
듣기로는 `UUID` 값을 얻어서 같은 값을 얻을 확률은 로또를 3번이나 1등 당첨되는 확률보다 낮다고 한다🫢