# Constructor Dependency Injection in Spring

스프링 생성자 DI에 대해서 알기위해서 기본적인 설정

- spring-boot-starter-web 의존성 추가
- 구성 파일 설정 : POJO 파일 또는 XML 파일

`Constructor Dependency Injection` : 스프링에서 DI 기법인 생성자 기반 종석성 주입 -> **인스턴스화 시점에 필요한 구성 요소를 클래스에 전달**하는 것을 의미한다.


## Annotaion Based Configuration

```java
@Configuration
@ComponentScan("com.packageName.constructordi")
public class Config {

  @Bean
  public Engine engine() {
    return new Engine("v8", 5);
  }

  @Bean 
  public Trasmission transmission() {
    return new Transmission("sliding");
  }
}
```

스프링 런타임에 빈을 제공하며, 패키지 `com.xxx.xxx"` 추가 빈을 찾기 위해 컨텍스트 스캔을 수행해야 함을 알립니다.

```java
@Component
public class Car {

  @Autowired
  public Car(Engine engine, Transmission transmission) {
    this.engine = engine;
    this.transmission = transmission;
  }
}
```

보통 초기화할 때 `Lombok`을 사용하여 초기화하는 방법을 사용해왔다. 이 때 생성자가 여러 개가 존재한다면 @Autowired 어노테이션을 사용하는 것 같다.

스프링은 패키지 스캔을 수행하는 동안(`@Component` 어노테이션으로 스프링이 관리해야할 객체라는 것을 알림) Car 클래스를 만나 `@Autowired` 주석이 달린 생성자를 호출하여 인스턴스를 초기화 한다.

Config 클래스의 `@Bean` 어노테이션의 메서드를 호출하면 인스턴스를 얻을 수 있다.

마지막으로 POJO 구성을 사용하여 ApplicationContext를 부트스트랩해야 한다.

```java
ApplicationContext context = new AnnotationConfigApplicationContext(Config.class);
Car car = context.getBean(Car.class);
```

## Implicit Constructor Injection
단일 생성자가 있는 클래스는 @Autowired 주석을 생략할 수 있습니다. 

자바에서는 생성자를 클래스 파일 내에 작성하지 않으면 기본생성자가 디폴트로 존재한다. 하지만, 보이지는 않는다.    
만약, 생성자를 한 개라도 작성하게 되면, 생략되어있는 기본생성자는 컴파일할 때 추가하지 않는다. 이에 딱 하나의 생성자만 작성하여 DI하는것 같다.

## Pros and Cons
생성자 의존성 주입의 장단점 ( 필드 주입과 비교하여 )

1. 테스트 가능성

*필드 주입 예제*
```java
public class UserService {

  @Autowired
  private UserRepository userRepository;
}
```

필드 주입을 할 경우 인스턴스를 생성할 때 사용자 리포지터리 상태를 초기화할 수 없다. 또한, 한 번 초기화 되면 변하지 않아야 하는 필드가 재할당할 수 있게 된다.   
이는 생성자 호출에 비해 덜 안전할 것이다. 정리하면, 불안전한 인스턴스 초기화(`NullpointException`), 객체의 불변성을 만족하지 못한다.

> OOP 관점에서 객체 인스턴스를 생성하기 위해 생성자를 사용하는 것이 더 자연스럽다.

**단점**   
bean에 대해 의존성 관리를 복잡할 경우이다. 

# Ioc(Inversion of Control), DI(Dependency Injection)
`IoC` : 소프트웨어 엔지니어링에서 객체의 제어를 컨테이너나 프레임워크로 전달하는 원칙이다. 

이는 프레임워크가 프로그램의 흐름을 제어하고 사용자 지정 코드에 호출할 수 있도록 한다. 

**장점**
- 어떤 작업을 수행하는 것의 구현으로부터 분리한다.
- 다양한 구현 간 전환(갈아끼기)를 더 쉽게 해준다.
- 프로그램의 테스트에서 구성 요소를 분리, 종속성을 mocking해서 더 쉽게 할 수 있다.

## DI
코드로 먼저 살펴본다.   
여기에는 두 개의 객체가 존재한다. 그리고 객체들이 협력하는 방식이다. 
```java
public class Store {
    private Item item;
 
    public Store() {
        item = new ItemImpl1();    
    }
}

public class Store {
    private Item item;
    public Store(Item item) {
        this.item = item;
    }
}
```

초기화 시 구체적인 클래스를 지정하지 않고 생성할 수 있게된다.

## 필드 주입
```java
public class Store {
    @Autowired
    private Item item; 
}
```

필드에 직접 주입하는 경우 단점
- This method uses **relection** to inject the dependencies, which is costier than constructor-based or setter-based injection

### DI
What is Dependency Injection ?

DI is ...   
제어 반전의 한 측면으로, 객체를 수동으로 생성하는 것이 아니라 대신, 프레임워크가 생성하는 방법을 설명하는 것. IoC 컨테이너가 필요한 클래스를 인스턴스화한다.

### Bean
What is Bean

**The Spring Beans are Java Objects that are initialized by the Spring IoC container.**

