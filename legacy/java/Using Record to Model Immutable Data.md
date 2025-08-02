# Using Record to Model Immutable Data

불변 객체를 생성할 때 일정한 형식을 가진다.

```java
public class Point {
    private final int x;
    private final int y;

    public Point(int x, int y) {
        this.x = x;
        this.y = y;
    }
}
```

메소드를 `toString()`, `equals()`, `hashCode()`를 작성해야 한다. 

다양한 추가 코드를 작성해야하지만, JDK에 레코드 타입이 추가되며 단일 줄의 코드로 모든 것을 제공한다.
컴파일러가 생성한다.

레코드 선언 방법
```java
public record Point(int x, int y) {}
```
- 두 개의 필드를 가진 불변 클래스이다.
- 초기화 하기 위한 생성자가 존재한다.
- `toString()`, `equals()`, `hashCode()` 메서드가 컴파일러에 의해 생성됨
- `Serializable` 인터페이스를 구현함.

`Serializable`는 객체를 직렬화 하고 역직렬화하는 과정을 지원하는 인터페이스이다. 이 개념은 객체 데이터를 네트워크로 전송하거나 파일에 저장할 때 사용된다.

직렬화 : 객체를 바이트 스트림으로 변환하는 과정이다.
역직렬화 : 바이트 스트림을 원래의 객체로 복원하는 과정이다.

레코드는 IDE의 코드 자동생성 기능을 사용하지 않고도 컴파일러가 자동의로 메서드를 업데이트하기 때문에 
버그의 위험이 줄어든다.

## Declaring the Components of Record

레코드는 컴파일러가 각각의 요소에 접근하기 위한 방법을 제공한다.

각 요소와 같은 이름으로 메소드를 생성한다. 그리고 값을 리턴한다.
```java
public int x() {
    return this.x;
}

public int y() {
    return this.y;
}
```

## 레코드에 추가할 수 없는 사항
1. 레코드에 인스턴스 필드를 추가할 수 없다.
2. 필드 생성자를 정의할 수 없다.
3. 인스턴스 생성자를 추가할 수 없다.
정적 필드 (`static`)와 정적 생성자를 만들 수 있다.

## Using the Compact Constructor
```java
public record Range(int start, int end) {
    
    public Range{
        if (end <= start) {
            throw new IllegalArgumentException("End cannot be lesser than start");
        }
    }
}
```
매개 변수 블록을 선언할 필요가 없다. 이렇게 할 경우 컴파일러엥서 추가한 코드로 수행되는 생성자에 할당할 수 있다.

매개변수에 새로운 값을 할당할 수 있으며, 컴파일러 생성 코드가 자동으로 할당하여 동일한 결과가 도출된다.

```java
public Range {
    if (start < 0) {
        start = 0;
    }
    if (end < 0) {
        end = 0;
    }
}
```

또한 다음과 같이 생성자를 직접 정의할 수 있다.
```java
public record Range(int x, int y) {
    
    public Range(int start, int end) {
        if (end <= start) {
            throw new IllegalArgumentException("end cannot be lesser than start");
        }
        
        if (start < 0) {
            this.x = 0;
        } else {
            this.x = start;
        }
        
        if (end <0) {
            this.y = 0;
        } else {
            this.y = end;
        }
    }
}
```

If the components of your record are not immutable, you should consider making defensive copies of them in both the canonical constructor and the accessors.

## Defining any Constructor
레코드에 모든 생성자를 추가할 수도 있다.

```java
import java.util.List;

public record State(String name, String capitalCity, List<String> cities) {

    public State {
        cities = List.copyOf(cities);
    }
    
    public State(String name, String capitalCity) {
        this(name, capitalCity, List.of());
    }
    
    public State(String name, String capitalCity, String... cities) {
        this(name, capitalCity, List.of(cities));
    }
}
```

cities가 수정될 수 없음을 보장해야한다. 

`List.copyOf()` : 인수로 얻는 컬렉션에서 null 값을 허용하지 않는다. 또한 불변 복사본을 리턴한다.

## Getting the State of a Record
필드의 값을 가져오기 위한 접근자를 추가할 필요가 없다. 미리 살펴보았듯이 필드와 같은이름으로 접근자가 생성되기 때문이다.

하지만, 자신의 접근자를 재정의 해야하는 경우도 존재한다. 예를 들어 수정할 수 없는 도시 목록의 방어 사본을 생성하지 않았다면,

내부 상태를 변경할 수 없도록 접근자에서 이를 수행해야 한다.

```java
public record State(String name, String capitalCity, List<String> cities) {

    public State {
        // List.copyOf returns an unmodifiable copy,
        // so the list assigned to `cities` can't change anymore
        cities = List.copyOf(cities);
    }
}
```
이를 정의하지 않앗다면

```java
import java.util.List;

public List<String> cities() {
    return List.copyOf(cities);
}
```

## 추가적으로 canonical constructor 살펴보기

## 레코드 사용 사례 살펴보기
1. 애플리케이션의 객체 모델에 데이터를 전달한다. 레코드는 불변의 데이터를 전달하는 역할로 설계되었다.
