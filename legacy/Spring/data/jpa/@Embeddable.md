# `@Embedded`, `@Embeddable`

JPA Provides `@Embeddable` annotation to declare that a class will be embedded by other entities

해당 어노테이션이 붙은 클래스가 다른 엔티티에 내장될 것임을 선언합니다.

```java
@Embeddable
public class ContactPerson {

  private String firstName;

  private String lasgName;

  private String phone;
}
```

`@Embedded` 어노테이션은 엔티티에 `@Embeddable` 어노테이션을 사용한 클래스를 내부에 내장합니다.

```java
@Entity
public class Company {

  @Embedded
  private ContactPerson contactPerson;
}
```

기본적인 어노테이션의 사용은 쉽지만 다음의 궁금증이 생긴다. 컬럼 속성을 어떻게 지정할지, 또는 안할지. 어디에서 컬럼 속성을 지정해주어야 할지.

## 속성 재정의

속성을 재정의해서 테이블에 매핑하기 위해서 사용하는 어노테이션이 존재합니다.
`@AttributeOverrides` 와 `@AttributeOverride`를 사용해서 내장된 유형의 열 속성을 재정의할 수 있습니다.

### @Embedded

```java
@Embedded
@AttributeOverrides({
  @AttributeOverride(name = "firstName", column = @Column(name = "contact_first_name")),
  @AttributeOverride(name = "lastName", column = @Column(name = "contact_last_name")),
  @AttributeOverride(name = "phone", column = @Column(name = "contact_phone"))
})
private ContactPerson contactPerson;
```

살펴보면 내장된 객체의 속성의 이름을 컬럼 어노테이션을 이용해서 테이블 컬럼명을 설정하는 것을 알 수 있다.

출처 : [Baeldung](https://www.baeldung.com/jpa-embedded-embeddable)

## 객체지향 설계를 위한 값 타입 활용 전략

어플리케이션에서는 여러 엔티티에서 공통으로 사용되는 데이터가 존재합니다. 예를들어 게시판의 이름, 사용자의 이름, 채팅방의 이름. 또는 배송 정보와 같이 공통으로 사용되는 데이터가 존재합니다.

JPA의 임베디드 타입 기능은 이런 중복된 컬럼들을 하나의 값 타입으로 묶어 재사용함으로써, 보다 객체지향적인 모델링이 가능합니다. 이를 통해 코드의 중복을 줄이고, 유지보수가 용이한 설계를 구현할 수 있습니다.

- 클래스로 정의하여 사용할경우 다양한 부수적인 효과를 적용시킬 수 있습니다
- 간단한 예로 객체의 메서드로 응집도 높고 다양한 함수를 정의할 수 있습니다.

컬럼의 기본적인 공통되는 제약이나 속성들을 지정해주고, 매핑정보를 재정의하여 컬럼명을 재정의할 수 있습니다.

```java
@Embeddable
public class Name {
  
  @Column(name = "first_name", nullable = false)
  private String first;

  @Column(name = "last_name")
  private String last;

  public String getFullName() {
    return this.first + this.last;
  }
}

@Entity
public class Member {
  ...
  @Embedded
  @AttributeOverrides({
    @AttributeOverride(name = "first", column = @Column(name = "member_first_name", length = 5)),
    @AttributeOverride(name = "last", column = @Column(name = "member_last_name")),
  })
  private Name name;
}
```

또한 [내장 컬렉션](https://www.baeldung.com/jpa-attributeoverride)으로 사용할 수 있는 방법도 존재합니다.

JPA 의 임베디드 타입기능을 활용하면, 중복되는 값 컬럼들을 하나의 값 객체로 묶어 재사용함으로써 객체지향적인 모델링이 가능해집니다. 또한 @AttributeOverride 를 통해 각 엔티티에 맞게 매핑 정보를 유연하게 재정의 할 수 있어 데이터베이스 설계와 유지보수가 더욱 용이해집니다.

참조 : [JPA Embedded Type 소개](https://cheese10yun.github.io/jpa-embedded/)
## 패스워드를 내장 객체로 활용하기

참조 : [Embedded를 이용한 Password 처리](https://cheese10yun.github.io/spring-jpa-best-04/)