# Defining Query Methods

## Paging, Iterating Large Results, Sorting & Limiting

```java
Page<User> findByLastname(String lastname, Pageable pageable);

Slice<User> findByLastname(String lastname, Pageable pageable);

List<User> findByLastname(String lastname, Sort sort);

List<User> findByLastname(String lastname, Sort sort, Limit limit);

List<User> fidnByLastname(String lastname, Pageable pageable);
```

## Limiting Query Results
쿼리 메소드 `First`, `Top` 키워드를 사용하여 결과를 제한할 수 있다.
숫자가 누락된 경우 결과 크기는 `1`로 가정합니다.

```java
List<User> findByLastname(Limit limit);

User findFirstByOrderByLastnameAsc();

User findTopByOrderByAgeDesc();

Page<User> queryFirst10ByLastname(String lastname, Pageable pageable);

Slice<User> findTop3ByLastname(String lastname, Pageable pageable);

List<User> findFirst10ByLastname(String lastname, Sort sort);

List<User> findTop10ByLastname(String lastname, Pageable pageable);
```


## JPQL
기본값으로, 쿼리는 JPQL을 이용한다.
```java
@Query("SELECT u FROM User u WHERE u.status = 1")
Collection<User> findAllActiveUsers();
```

## Native
@Query 속성으로 `nativeQuery`값을 true로 설정하고 쿼리를 정의한다.
```java
@Query(
  value = "SELECT * FROM USERS u WHERE u.status = 1",
  nativeQuery = true
)
Collection<User> findAllActiveUsersNative();
```

## Order in a Query
파라미터로 Sort 객체를 메서드 파라미터로 제공할 수 있다. 이는 자동으로 `ORDER BY`로 변환되어 데이터베이스로 전달된다.

```java
userRepository.findAll(Sort.by(Sort.Direction.ASC, "name"));
```

JPQL에서 파라미터로 Sort 유형의 객체를 추가하기만 하면 된다.
```java
@Query(value = "SELECT u FROM User u")
List<User> findAllUsers(Sort sort);
```

이는 Native 쿼리를 사용할 때는 사용할 수 없다. 

## Joining Tables in a Query

```java
@Query(
  value = "SELECT new ResultDTO(c.id, o.id, p.id, c.name, c.email, o.orderDate, p.productName, p.price) "
  + " from Customer c, CustomerOrder o, Product p " 
  + " where c.id = o.customer.id "
  + " and o.id = p.customerOrder.id "
  + " and c.id=?1 "
)
List<ResultDTO> findResultDTOByCustomer(Long id);
```

파라미터로 넘어온 값을 쿼리에 적용
```java
@Query("SELECT u FROM User u WHERE u.status = ?1")
User findUserByStatus(Integer status)

@Query("SELECT u FROM User u WHERE u.status = ?1 and u.name = ?2")
User findUserByStatusAndName(Integer status, String name);
```

명명된 파라미터 
```java
@Query("SELECT u FROM User u WHERE u.status = :status and u.name = :name")
User findUserByStatusAndNameNamedParams(
  @Param("status") Integer status,
  @Param("name") String name
);
```

## Collection Parameter
쿼리에 `IN` 키워드에 컬렉션으로 넘겨 값을 반환 받을 수 있다.

```java
@Query(value = "SELECT u FROM User u WHERE u.name IN :names")
List<User> findUserByNameList(@Param("names") Collection<String> names);
```

## Update Query
`@Query`와 `@Modifying` 어노테이션을 사용하여 데이터베이스 값을 수정할 수 있다.
```java
@Modifying
@Query("update User u set u.status = :status where u.name = :name")
int updateUserSetStatusForName(@Param("status") Integer status, @Param("name") String name);
```

INSERT 경우 : 

INSERT 는 JPA 인터페이스의 일부가 아니기 때문에 네이티브 쿼리를 사용해야 한다.
```java
@Modifying
@Query(
  value = 
    "insert into Users(name, age, email, status) values (:name, :age, :email, :status)",
  nativeQuery = true
)
void insertUser(@Param("name") String name, @Param("age") Integer age, @Param("status") Integer status, @Param("email") String email);
```
