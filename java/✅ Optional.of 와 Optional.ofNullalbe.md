# Optional `of` , `ofNullable` 정적 메소드 어느걸 써야하지?
## 동기

순수 자바로 스프린트 미션1을 진행 중, 데이터를 저장하고 있는 자료구조에서 값을 반환해줄 때 찾는 값이 없으면 `null` 을 반환한다. 프로젝트가 예상하지 못한 에러로 다운되지 않도록 특정 메서드 반환 객체를 `Optional` 객체로 감싸서 리턴하기로 결정했다.

자료 구조를 저장하고 있는 `Repository` 객체에서 특정 객체를 찾는 메서드를 호출한다. 값을 찾지 못할 때 `null`을 반환하는 것을 처리해 주어야 한다.

```
@Override
public Optional<T> findById(final ID id) {
    var findEntity = store.get(id);
    return Optional.ofNullable(findEntity);
}
```

## 언제 `Optional`의 정적 메소드 `of`와 `ofNullable`을 사용해야 되나?

`Optional` 객체의 정적 메소드인 `of`와 `ofNullable`을 사용할 수 있다. 비슷해 보이는 두 API 중 어떤것을 사용해야하는지 고민하게 되었다.

공식 JAVA API 문서

`of`

```
public static <T> Optional<T> of(T value)
Returns an Optional with the specified present non-null value.

Type Parameters:
T - the class of the value

Parameters :
value - the value to be present, which must be non-null

Throws : 
NullPointerException - if value is null
```

`non-null`이 무슨 의미인지 몰라서, 이 키워드로 검색을 해보았다. 이는 무조건 값이 있어야 하는 것으로, `null`이면 안되는 값을 넣어준다는 것을 알 수 있었다. 매개변수로 들어오는 값이 있어야 하는 것이다. 그렇지 않으면 이 메소드는 `NullPointerException`을 발생시킨다는 것을 알려준다.

`ofNullable`

```
public static <T> Optional<T> ofNullable(T value)
Return an Optional describing the specified value, if non-null, otherwise returns an empty Optional.

Parameters :
value - the possibly-null value to describe

Returns :
an Optional with a present value if the specified value is non-null, otherwise an empty Optional
```

파라미터로 null 값이 들어오면 `Optional` 객체의 `empty()` 메서드를 통해 빈 `Optional`을 반환한다.

참고 : [java API](https://docs.oracle.com/javase/8/docs/api/java/util/Optional.html#of-T-)

`non-null`이란? 객체가 `null`이 아니다 라는 의미이다. 즉 어떤 값이나 객체가 메모리 상에 유요하게 존재하고 사용할 준비가 된 상태를 말한다.

## 요약

-   `Optional.of`는 값이 절대 `null`이 아니라고 보장할 때 사용한다고 생각된다. 만약 값이 null일 가능성이 있다면 `NullPointerException`을 발생시킬 것인지 빈 `Optional`을 반환할 것인지 결정해야 한다.
-   `null`이 파라미터로 들어올 때 예외를 터트리는게 아니라 `Optional` 객체를 반환하고 싶다면, `ofNullable()` 을 사용한다.
-   `non-null`은 객체가 `null`이 아니며, 유효하게 존재함을 뜻한다.
-   `ofNullable`은 파라미터 값이 없다면 `empty()`을 통해 반환한다.

## 정리

코드로 돌아가 보면, `of`메서드를 사용한다면, 이렇게 할 것 같다.

```
import java.util.Optional;

@Override
public Optional<T> findById(final ID id) {
    var findEntity = store.get(id);

    if (findEntity != null) {
        return Optional.of(findEntity);
    }
    return Optional.empty();
}
```

구현할 수 있는 방법 중 하나라고 생각한다. 내가 이렇게 구현하지 않은 이유는 다음과 같다. if문을 사용하면서 기존 코드보다 한 번더 생각을 해야되는 품이 들어간다고 생각하기 때문이다.  
어떤게 정답이라고 할 수는 없지만, 단지 내 생각은 `ofNullable`을 사용하는게 조금 더 직관적이라고 생각한다.