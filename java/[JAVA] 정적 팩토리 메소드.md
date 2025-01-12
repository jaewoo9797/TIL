# 정적 팩토리 메소드

스프린트 미션 1의 다른 스프린터 분들의 코드를 보는중 많이 본 리뷰 중 하나가 `정적 팩토리 메소드`를 이용해 객체를 생성 하도록 리뷰가 공통적으로 많이 받은 것을 보았다. `of` 또는 `from` 의 정적 메서드를 본적이 있지만, 정확하게 어떤 개념을 이요해서 사용하는 것인지 잘 모르지만, 찾아보지는 않았었다. 이번 기회에 관련 레퍼런스를 찾아보고, 주로 참고로 많이 나오는 `이펙티브 자바` 책을 참고해서 공부했다.

## 이펙티브 자바 - 생성자 대신 정적 팩토리 메서드를 고려하라

- `from` : 매개변수를 하나 받아서 해당 타입의 인스턴스를 반환하는 형변환 메서드
- `of` : 여러 매개변수를 받아 적합한 타입의 인스턴스를 반환하는 집계 메서드
- `valueOf` : from 과 of 의 더 자세한 버전
- `instance` 혹은 `getInstance` : (매개변수를 받는다면) 매개변수로 명시한 인스턴스를 반환하지만, 같은 인스턴스임을 보장하지 않는다.
- `create` 혹은 `newInstance` : instance 혹은 getInstance와 같지만, 매번 새로운 인스턴스를 생성해 반환함을 보장한다.
- `getType` : getInstance와 같으나, 생성할 클래스가 아닌 다른 클래스에 팩터리 메서드를 정의할 때 쓴다.
- `newType` : newInstance와 같으나, 생성할 클래스가 아닌 다른 클래스에 팩터리 메서드를 정의할 때 쓴다.
- `type` : getType과 newType의 간결한 버전

## 블로그

정적 팩토리 메서드 : 개발자가 구성한 Static Method를 통해 간접적으로 생성자를 호출하는 객체를 생성하는 디자인 패턴 ?(디자인 패턴 정적팩터리 패턴과는 다르다고 했는데? -이펙티브 자바)

inpa dev 블로그

## 2. 인스턴스에 대해 통제 및 관리가 가능하다.

제목만으로 처음에는 이해가 어렵지만, 다음과 같다. 필요에 따라 항상 새로운 객체를 생성해서 반환할 수도 있고, 객체 하나만 만들어두고 이를 공유하여 재사용하게 하여 불필요한 객체를 생성하는 것을 방지 할 수 있는것이다.

```java
class Singleton {
  private static Singleton INSTANCE;

  private Singleton() {}

  // static factory method
  public static synchronized Singleton getInstance() {
    if (instance == null) {
      INSTANCE = new Sinleton();
    }
    return INSTANCE;
  }
}
```

caching 한다면 필요한 인스턴스만 뽑아 재사용하여 메모리를 절약할 수 있게 된다.
