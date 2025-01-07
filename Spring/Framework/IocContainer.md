# The IoC Container

## Introduction to the Spring IoC Container and Beans

스프링 프레임워크 IoC(제어의 역전)의 basic은 패키지 `org.springframework.beans`와 `org.springframework.context`에 있다. 익히 알고 있듯이 컨텍스트를 관리하는 `ApplicationContext` 이는 모든 유형의 객체를 관리하는 `BeanFactory` 인터페이스의 하위 인터페이스이다. 그리고 이 컨테이너에 의해 관리되는 **객체**를 우리는 `Bean이라고` 부른다. 이 빈들로 스프링 컨테이너에 의해 인스턴스화, 조립, 관리된다.

## Container Overview
`ApplicationContext` : 인터페이스는 스프링 IoC 컨테이너를 나타내며, 빈을 인스턴스화, 구성 및 조립하는 역할을 한다.

구성 메타 데이터는 다음으로 표현될 수 있다.
- 주석이 달린 클래스
- 팩토리 메서드가 포함된 구성 클래스
- 외부 XML 파일
- Groovy 스크립트

설정된 컨텍스트 정보를 사용하는 방법

```java
ApplicationContext context = new ClassPathXmlApplicationContext("servicse.xml", "daos.xml");
var petStoreService = context.getBean("petStore", PetStoreService.class);

var userList = service.getUsernameList();
```

실제로 애플리케이션 코드에는 getBean() 메서드에 대한 호출이 전혀 없으므로 Spring API에 의존해서는 안된다.