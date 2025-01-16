# 정적 팩토리 메소드

스프린트 미션 1의 다른 스프린터 분들의 코드를 보는중 많이 본 리뷰 중 하나가 `정적 팩토리 메소드`를 이용해 객체를 생성 하도록 리뷰가 공통적으로 많이 받은 것을 보았다.
`of` 또는 `from` 의 정적 메서드를 본적만 있지 정확하게 모르는 개념이였다. 
이번 기회에 관련 `레퍼런스`를 찾아보고, 주로 참고로 많이 나오는 `이펙티브 자바` 책을 참고해서 공부했다.

## 이펙티브 자바 - 생성자 대신 정적 팩토리 메서드를 고려하라

- `from` : 매개변수를 하나 받아서 해당 타입의 인스턴스를 반환하는 형변환 메서드
- `of` : 여러 매개변수를 받아 적합한 타입의 인스턴스를 반환하는 집계 메서드
- `valueOf` : from 과 of 의 더 자세한 버전
- `instance` 혹은 `getInstance` : (매개변수를 받는다면) 매개변수로 명시한 인스턴스를 반환하지만, 같은 인스턴스임을 보장하지 않는다.
- `create` 혹은 `newInstance` : instance 혹은 getInstance와 같지만, 매번 새로운 인스턴스를 생성해 반환함을 보장한다.
- `getType` : getInstance와 같으나, 생성할 클래스가 아닌 다른 클래스에 팩터리 메서드를 정의할 때 쓴다.
- `newType` : newInstance와 같으나, 생성할 클래스가 아닌 다른 클래스에 팩터리 메서드를 정의할 때 쓴다.
- `type` : getType과 newType의 간결한 버전

## 2. 인스턴스에 대해 통제 및 관리가 가능하다.

제목만으로 처음에는 이해가 어렵지만, 다음과 같다. 필요에 따라 항상 새로운 객체를 생성해서 반환할 수도 있고, 객체 하나만 만들어두고 이를 공유하여 재사용하게 하여 불필요한 객체를 생성하는 것을 방지 할 수 있는것이다.
싱글톤에서도 이와 같이 이용할 수 있다.
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


## 내 스프린트 미션 사이드 프로젝트에 어떻게 적용할 것인가? 


### 장점
- 이름을 가질 수 있다.
- 매번 호출될 때 마다 객체를 생성할 필요가 없다. -> 캐싱해서 관리할 수 있다. 하나의 인스턴스로 관리할 수 있다.
- 상속받은 어떠한 객체라면 `반환 타입`으로 설정할 수 있다. 구체적인 타입을 만들기 전에 코드 작성할 수 있다.
- 반환되는 객체는 입력 파라미터에 따라 구현체가 다를 수 있다.

학습을 진행하면서 내 코드에 적용해 볼만한 것을 생각해보았다. 
```java
public class Student {
    
    private String name;
    private int score;
    
    // 생성자는 하나 존재, 접근 제어자 : private
    private Stucent(String name, int score) {
        this.name = name;
        this.score = score;
    }
    
    // 객체 생성을 해주는 정적 팩토리 메서드
    public static Student of(String name, int score) {
        validateName(name);
        return new Student(name, score);
    }
    
    private void validateName(String name) {
        //... 검증 메소드
    }
}
```
정적 팩토리 메서드를 사용하여 객체를 생성하는 간단한 클래스 구조이다. 내가 정적 팩토리 메서드의 유용한 부분이라고 생각하는 부분은, 객체의 생성자는 정말로 생성만
담당하는 기능을 가지고 있는 것이다. 이전에는 객체 생성할 때 검증 메서드를 실행하도록 중간에 끼워넣었었다. 

이처럼 생성자는 생성을 하는 역할만 담당하는 SRP를 만족하게 한다.

## static factory method naming best practice
가장 고민이 되는 부분이다. 정적 팩토리 메서드의 장점 중 하나인 `이름을 가질 수 있다` 를 어떻게 살릴 수 있을까를 많이 생각했다.

단지 `of` 또는 `from`으로 메서드를 만든다면 이 장점이 죽어버린다. 그래서 많은 레퍼런스들을 찾아보려고 했는데, 결과적으로는 정해진 것은
없고 알아서 잘 만들고 같이 작업하는 사람들이랑 이야기해서 결정을 해야한다는 것이였다. 그래서 내가 생각하기에 스프린트1 미션을 진행하면서 
부족하지만 의미를 잘 나타내는 정적 팩토리 메서드 이름을 지어주려고 노력했다. 

ex
```java
public static Channel createOfChannelNameAndUser(
        String channelName,
        User creator
) {

    return new Channel(channelName, creator);
}
```