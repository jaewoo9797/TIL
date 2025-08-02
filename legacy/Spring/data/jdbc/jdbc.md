# JDBC

간단 예제

```java
public void insert(User user) throws SQLException{
  Connection con = null;
  PreparedStatement pstmt = null;
  try {
    con = ConnectionManager.getConnection();
    String sql = "INSERT INTO USERS VALUES (?, ?, ?, ?)";
    pstmt = con.preparedStatement(sql);
    pstmt.setString(1, user.getId());
    pstmt.setString(2, user.getPassword());
    pstmt.setString(3, user.getName());
    pstmt.setString(4, user.getEmail());

    pstmt.executeUpdate();
  } finally {
    if (pstmt != null) {
      pstmt.close();
    }

    if (con != null) {
      con.close();
    }
  }
}
```

<br/>

데이터베이스에 데이터를 추가하는 예제다. 처음 자바를 공부할 때가 생각난다. 이게 뭐지 싶었는데, 지금보니 처음봤을 때에 비해서 쉽게 느껴진다. 코드조각이 어렵지 않지만, try-catch-resourse 를 적용할 수는 없을까? sql 변수를 관리할 방법이 없을까? 이런 고민이 든다. 일단 JDBC에 대해서 조금 더 살펴보겠다.

```java
public List<User> selectAll() throws SQLException {
  Connection con = null;
  PreparedStatement patmt = null;
  ResultSet rs = null;

  try {
    con = ConnectionManager.getConnection();
    String sql = "SELECT id, password, name, email FROM USER";
    pstmt = con.prepareStatement(sql);
    rs = pstmt.executeQuery();

    List<User> users = new ArrayList<>();

    while (rs.next()) {
      String id = rs.getString("id");
      String password = rs.getString("password");
      String name = rs.getString("name");
      String email = rs.getString("email");

      users.add(new User(id, password, name, email));
    }

    return users;
  } finally {
    if (rs != null) {
      rs.close();
    }

    if (pstmt != null) {
      pstmt.close();
    }

    if (con != null) {
      con.close();
    }
  }
}
```

데이터 베이스에서 데이터를 조회하는 로직입니다.

JDBC 만을 이용해서 위처럼 데이터를 저장 조회를 할 때 커넥션 맺기, 끊기 등 공통적인 작업들이 존재합니다.

## Spring JDBC Template

```java
public class UserDao {

  @Autowired
  private JdbcTemplate jdbcTemplate;

  public void insert (User user) {
    String sql = "INSERT INTO USERS VALUES (?, ?, ?, ?)";
    jdbcTemplate.update(sql, user.getUserId(), user.getPassword(), user.getName(), user.getEmail());
  }
}
```

<br/>

`JdbcTemplate`를 이용하여 데이터를 추가하는 로직을 구현한 코드입니다. 상대적으로 많은 부분이 제거되었고, `JdbcTemplate`이 제거된 작업에 대한 책임을 가져간 것으로 보입니다.

스프링은 데이터베이스와의 연동을 쉽게 도와주는 여러 가지 도구와 방식을 제공합니다. `JDBC`는 자바에서 데이터베이스에 접속할 수 있게 도와주는 API입니다. `JdbcTemplate`은 이러한 `JDBC`를 좀 더 편리하게 사용할 수 있도록 스프링에서 제공하는 템플릿 클래스입니다.

`JdbcTemplate`은 스프링 `JDBC`의 핵심이며 다른 고수준의 기능들도 결국 내부에서는 이 `JdbcTemplate`을 활용합니다.

### Query (SELECT)

`JdbcTemplate`을 이용하여 `SELECT` 쿼리를 실행하는 여러가지 방법을 제공합니다.

- queryForObject
- query
- queryForList
- queryForRowSet
- queryForMap

### RowMapper

```java
Customer customer = jdbcTemplate.queryForObject(
  "select id, first_name, last_name from customers where id = ?",
  (resultSet, rowNum) -> {
    Customer customer = new Customer(
      resultSet.getLong("id");
      resultSet.getString("first_name");
      resultSet.getString("last_name");
    );
    return customer;
  }, id);
```

`RowMapper`를 전달하여 조회결과를 매핑할 수 있습니다. 3번째 매개변수는 ?에 매핑되는 값입니다.

### Querying for a List

`query` 메서드를 이용하여 여러 개의 객체를 조회할 수 있습니다. 두 번째 매개변수에 `RowMapper`를 전달하여 조회 결과를 매핑할 수 있습니다.

queryForObject와 마찬가지로 세 번째 매개변수를 이용하여 쿼리문에 바인딩할 파라미터를 전달할 수 있습니다. `RowMapper`의 경우 별도로 선언하여 사용할 수 있습니다.

```java
private final RowMapper<Customer> rowMapper = (resultSet, rowNum) -> {
      Customer customer = new Customer(
      resultSet.getLong("id");
      resultSet.getString("first_name");
      resultSet.getString("last_name");
    );
    return customer;
}

List<Customer> customers =
        jdbcTemplate.query("select id, first_name, last_name from customers where first_name = ?", rowMapper, firstName);
```

## update (Insert, Update, Delete)

update, batchUpdate, execute 메서드를 이용해서 쿼리를 실행할 수 있습니다.

```java
jdbcTemplate.update("insert into customers (first_name, last_name) values (?, ?)", customer.getFirstName(), customer.getLastName());

jdbcTemplate.update("delete from customers where id = ?", Long.valueOf(id));
```

## KeyHolder

`JdbcTemplate`을 사용하여 데이터베이스에 새로운 정보를 추가하고, 그 때 생성된 primary key를 가져오기 위해서 KeyHolder를 사용할 수 있습니다.

```java
KeyHolder keyHolder = new GeneratedKeyHolder();
jdbcTemplate.update(connection -> {
  PreparedStatuement ps = connection.preparedStatement(
    "insert into customers (first_name, last_name) values (?, ?)",
    new String[]{"id"});
  ps.setString(1, customer.getFirstName());
  ps.setString(2, customer.getLastName());
  return ps;
}, keyHolder);

Long id = keyHlolder.getKey().longValue();
```

- Spring 에서 제공하는 JDBC Core 패키지에서 가장 핵심이 되는 클래스입니다.
- 리소스의 생성과 해제를 처리해서 연결을 닫는 것을 까먹고 못하게 되는 등의 오류를 방지한다.
- SQL 쿼리를 실행한다.
- SQL 문을 실행하여 DB에서 데이터를 변경한다.
- SQL 쿼리의 실행 결과로 Result Set을 생성한다.
- JDBC 예외를 잡아서 일반적인 예외 계층 구조로 변환한다.

## NamedParameter Jdbc Template

- '?' place holder 대신에 이름을 지어준 파라미터로 대체한다.
- JDBC Template 클래스를 래핑하며, Jdbc Template 클래스의 대부분의 기능을 사용할 수 있다.

`Named Parameter`를 사용하면 파라미터의 순서를 기억할 필요 없이, 파라미터에 이름을 지어서 쿼리를 실행할 수 있다.

SqlParameterSource 인터페이스를 제공한다. 이는 파라미터 값들의 소스이다.

- MapSqlParameter Source

```java
public int userMapSqlParameterSource(String firstName) {
  String sql = "select count(*) from customers where first_name = :first_name";
  MapSqlParameterSource namedParameters = new MapSqlParameterSource("first_name", firstName);
  return namedParameterJdbcTemplate.queryForObject(sql, namedParameters, Integer.class);
}
```

`MapSqlParameterSource`는 생성자, addValue 메서드를 통해 파라미터 이름과 값을 짝지어서 지정할 수 있다.

```java
public int userMapSqlParameterSource(String firstName) {
  String sql = "select count(*) from customers where first_name = :first_name";
  MapSqlParameterSource namedParameters = new MapSqlParameterSource().addValue("first_name", firstName);
  return namedParameterJdbcTemplate.queryForObject(sql, namedParameters, Integer.class);
}
```

- BeanPropertySqlParameterSource

자바 빈 객체의 프로퍼티 이름과 값을 매핑하여 파라미터 맵을 생성한다. 그런데 보통 디비 테이블 컬럼과 자바 빈 객체의 프로퍼티의 이름이 상이한 경우가 존재하여 편하지만 제한사항이 존재한다.

## SimpleJdbcInsert

- 스프링 프레임워크에서 제공하는 JDBC 확장 클래스 중 하나
- JDBC로 SQL 쿼리를 실행하는 것보다 더 쉽고 간편하게 DB에 데이터를 '추가'하도록 도와준다.

[Spring jdbc blog](https://engineerinsight.tistory.com/58)
