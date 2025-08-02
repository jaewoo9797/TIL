# 파라미터로 넘어온 참조에 의한 값 전달
스프린트 미션 1을 진행하며, 파라미터로 넘어온 값의 재할당 불가, `null` 체크를 고민하는 도중 `재할당`에 대해서 공부한 내용
```java
    private boolean isNotCreator(final User user) {
        Preconditions.checkNotNull(user);
        return !creator.equals(user);
    }
```

## 파라미터에 값이 재할당이 되지 않도록 명시적으로 표현하기 위해 `final` 키워드를 붙여주는 것에 대해서 고민.

`Java` 는 참조에 의한 값 전달 (**callByValue**) 방식을 사용한다. `Java`에는 `C` 언어처럼 포인터라는 개념을 사용하지 않는다.
`*` 를 이용해서 reference address 를 접근하는 것과 다르다.

자바에서 객체를 참조주소로 값을 식별하는게 아닌가? 그러니까 참조 주소를 값으로 호출하는 것이 아닌가에 대한 모호함

중요한건 파라미터에 `final` 키워드를 붙여야 하는가에 대한 것이다. 값 재할당을 불가능 하도록 명시적으로 해주었는데 코드를 읽는데 가독성을 해치는 것 같다.

## Java call-by-value
- `Java`에서는 모든 메서드 호출이 값에 의한 호출 방식으로 이루어진다.
- 객체를 메서드에 전달할 때, 객체의 **참조값(Reference Address)** 이 복사되어 전달된다.
- 중요한 점은 **참조값 자체도 하나의 값**으로 처리된다는 것이다.
```java
Dog myDog = new Dog("alex");

void foo(Dog dog) {
    dog = new Dog("flex");
}

foo(myDog);
// myDog = "alex"
```
`foo` 메서드 호출 시, `myDog` 객체의 참조값이 복사되어 `dog` 매개변수로 전달된다. 

그래서 메서드의 매개변수 dog의 참조값에 새로운 참조값을 할당하는 것이지, `myDog`에 영향을 주는 것이 아니다.

따라서 메서드가 종료된 후, 원래의 `myDog`는 여전히 `Dog("alex") 객체를 참조한다.

### Java의 참조와 C 언어의 포인터의 차이
모호했던 부분을 정리하면 `Java`에는 포인터라는 개념이 없고, 객체는 **참조값**을 통해 관리되고, `C` 에서 포인터는 메모리 주소를 직접 다루고 `*`을 이용해 직접 접근한다는 것이다.
- `C`에서 포인터는 메모리 주소를 직접 다루고 `*`를 통해 값을 변경할 수 있다.

```
void foo(int* x) {
    *x = 20;
}
int main() {
    int a = 10;
    foo(&a); // 주소 전달
    printf("%d", a); // 20
}
```
정리하면, `C`에서는 `*`로 메모리 값을 직접 변경할 수 있지만, `Java`에서는 객체의 참조값을 조작하지 않는다.
- `Java`에서는 객체의 참조값이 복사되어 전달되므로, 참조값 자체를 재할당해도 호출한 곳에는 영향을 주지 않는다.

## `final` 키워드을 사용하지 않게된 이유
메서드 파라미터로 넘어온 객체의 참조값은 복사된 것으로 호출한 곳의 객체에 영향을 주지 않는다. 그렇기에 `final` 키워드를 사용하지않고도
`Java`의 특성상 재할당되지 않는다. `final`을 붙일 경우 명시적으로 표현할 수 있고, 재할당할 경우 컴파일 단계에서 에러를 확인할 수 있지만,
코드의 간결성과 가독성을 해칠 수 있기에 나는 사용하지 않기로 했다.

[참조 사이트](https://softwareengineering.stackexchange.com/questions/48413/in-java-should-i-use-final-for-parameters-and-locals-even-when-i-dont-have-t)