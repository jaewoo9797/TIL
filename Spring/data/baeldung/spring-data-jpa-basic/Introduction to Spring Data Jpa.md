# Introduction to Spring Data JPA

기존에 DAO를 직접 구현해야 했던 부분을 JpaRepository를 상속을 받아 사용하면, 애플리케이션에서 데이터베이스의 데이터를 조작할 수 있다.

인터페이스를 확장함으로써 표준 DAO에서 표준 데이터 접근을 위한 가장 관련성 높은 CURD 방법을 얻을 수 있다.

## 자동적으로 쿼리를 커스텀
⭐ 새로운 Repository를 구현하면, 인터페이스에서 정의된 모든 메서드를 분석하고 **메서드 이름에서 쿼리를 자동으로 생성한다.**


예시
```java
public interface IFooDAO extends JpaRepository<Foo, Long> {

  Foo findByName(String name);
}
```

엔티티의 이름 필드를 이용해서 데이터를 찾는 쿼리를 자동으로 생성된다.


## 수동적으로 사용자 지정 쿼리

`@Query` 어노테이션을 사용해서 쿼리를 정의할 수 있다.
```java
@Query("SELECT f FROM Foo f WHERE LOWER(f.name) = LOWER(:name)")
Foo retrieveByName(@Param("name") String name);
```

좀 더 세밀하게 쿼리를 생성 제어하려면 좋은 방법이다.

## 간단한 JPA 설정
### Dependencies

- Maven 
```
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter</artifactId>
    <version>3.1.0</version>
</dependency>
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-jpa</artifactId>
    <version>3.1.0</version>
</dependency>
```

- gradle
```
dependencies {
  implementation 'org.springframework.boot:spring-boot-starter-data-jpa'
      runtimeOnly 'com.h2database:h2'
}
```

h2 데이터 베이스는 인메모리 데이터베이스로, 종속성을 추가할 경우 Boot가 자동으로 DataSource를 구성한다.

MySQL 데이터베이스에서 JPA를 사용하려면 `mysql-connector-java` 종속성과 DataSource 구성도 정의해주어야 한다.   
이 작업은 @Configuration 클래스에서 수행하거나 Boot 속성을 사용하여 수행한다.

```java
@Bean
public DataSource dataSource() {
  DriverManagerDataSource dataSource = new DriverManagerDataSource();

  dataSource.setDriverClassName("com.mysql.cj.jdbc.Driver");
  dataSource.setUsername("mysqluser");
  dataSource.setPassword("mysqlpass");
  dataSource.setUrl(
    "jdbc:mysql://localhost:3306/myDb?createDatabaseIfNotExist=true"
  );

  return dataSource;
}
```
속성 파일 properties 을 사용하여 설정

```
spring:
  datasource:
    driver-class-name:com.mysql.cj.jdbc.Driver
    username: ...
    ...
```

이 속성 파일을 이용해서 스프링부트에서 데이터 소스를 자동으로 구성한다.

또한, 기본 연결 풀이 Tomcat에서 HikariCP로 변경되었다.

[JPA 예제 github](https://github.com/eugenp/tutorials/tree/master/persistence-modules/spring-jpa)

### 엔티티 클래스

```java
@Entity
public class Publishers {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private int id;
    private String name;
    private String location;
    private int journals;
    // constructors, getters and setters
}
```

### Repository
```java
public interface PublisherRepository extends JpaRepository<Publishers, Integer> {
}
```

내장하고 있는 메서드
- save() 
- findById()
- findAll()
- findById()

특정 명명 규칙을 따름으로써 쿼리를 만들 수 있다.

```java
public interface PublisherRepository extends JpaRepository<Publishers, Integer> {
    List<Publishers> findAllByLocation(String location);
}
```

또한 @Query 어노테이션을 사용

```java
@Query("SELECT p FROM Publishers p WHERE p.journals > :minJournals AND p.location = :location")
List<Publisher> findPublishersWithMinJournalsInLocation(Integer minJournals,String location);
```

### findByXxx
만약 찾으려는 레코드가 없을 경우 에러를 발생시킬 수 있다.   
find 메서드는 리턴 값이 null일 수 있기 때문에 Optional로 객체를 감싸서 리턴한다. 이에 이를 사용하는 서비스 레이어에서는 null을 조작할 필요가 있다.

```java
// get()을 통해 가져오는 것은 위험하다.
public Foo findById(int id) {
  return fooRepository.findById(id).orElseThrow(() -> new RuntimeException());
}
```
예외를 던지기 전에 특정 작업을 실행할 수도 있다. 예를 들어 스트림의 map을 이용하여 반환하는 값을 DTO로 변환하는 작업을 할 수 있다.   
또한, 예외를 던질때 사용자 정의 에러를 발생시켜서 advice 클래스에서 예외를 캐치하여 일정한 반환값을 반환할 수도 있다.

# CrudRepository, JpaRepository

- CrudRepository
- JpaRepository
- PagingAndSortingRepository

## JpaRepository
`JpaRepository`는 다음의 레포지터리를 확장했습니다.    `CrudRepository`, `PagingAndSortingRepository`,

- Crud : CURD 기능을 제공
- PagingAndSorting : 페이지네이션과 정렬의 메소드를 제공한다.
- Jpa : Persistence context 플러쉬, 레코드 삭제와 같은 메서드를 일괄적으로 제공한다.

### **JpaRepository contains the full API of CrudRepository and PagingAndSortingRepository**
전체 기능이 필요하지 않을 때는 CurdRepository를 사용할 수 있다.

### CrudRepository interface
```java
public interface CrudRepository<T, ID extends Serializable> extends Repository<T, ID> {
  <S extends T> S save(S entity);

  T findOne(ID primarykey);

  Iterable<T> findAll();

  Long count();

  void delete(T entity);

  boolean exists(ID primaryKey);
}
```

### PagingAndSortingRepository
```java
public interface PagingAndSortingRepository<T, ID extends Serializable> extends CrudRepository<T, ID> {

  Iterable<T> findAll(Sort sort);

  Page<T> findAll(Pageable pageable);
}
```

`Pageable` 객체를 사용할 때 최소한 다음 사항을 지정해야 합니다.

- Page size
- Current page number
- Sorting

만약, 첫 페이지를 lastname으로 정렬한 결과 집합을 오름차순으로 표시하고 각각 5개 이하의 레코드를 가지고 있다고 가정한다.   
```java
Sort sort = new Sort(new Sort.Order(Direction.ASC, "lastName"));
Pageable pageable = new Pageable(0, 5, sort);
```

### JpaRepository
```java
public interface JpaRepository<T, ID extends Serializable> extends PagingAndSortingRepository<T, ID> {

  List<T> findAll();

  List<T> findAll(Sort sort);

  List<T> save(Iterable<? extends T> entities);

  void flush();

  T saveAndFlush(T entity);

  void deleteInBatch(Iterable<T> entity);
}
```

- flush() : 모든 작업을 데이터베이스로 플러시한다.
- saveAndFlush() : 엔티티를 저장하고 즉시 변경 사항을 플러시한다.
- deleteInBatch() : 반복 가능한 엔티티를 삭제한다.

## 😥 Spring Data Repositories의 단점
1. 코드와 라이브러리와 페이지, 페이지 기능 과 같은 특정 추상화와 결합한다. -> 일부 내부 구현 세부 사항이 노출되지 않도록 주의해야 한다.
2. 메서드에 대해 더 세밀한 제어를 얻고자 하는 상황에 직면한다. 예를 들어 저장 및 삭제 메서드를 포함하지 않는 `ReadOnly` 레포지터리를 생성하는 것과 같은 상황이 발생할 수 있다.
