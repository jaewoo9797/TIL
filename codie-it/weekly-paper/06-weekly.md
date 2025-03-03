# 6번째 위클리 페이퍼

## 1. SQL 에서 DDL과 DML의 차이점을 설명하고, 각각의 대표적인 명령어들의 용도를 설명하세요

### DDL 이란

DDL (Data Definition Language) 는 **데이터 정의언어**로 컴퓨터 사용자 또는 응용 소프트웨어가 컴퓨터의 데이터를 정의하는 컴퓨터 언어 또는 컴퓨터 언어 요소이다.

SQL 컨텍스트에서 데이터 정의 또는 데이터 설명언어 (DDL) 는 테이블, 인덱스 및 사용자와 같은 데이터베이스 객체를 만들고 수정하기 위한 구문이다.

DDL 은 선언적 구문을 사용하여 열과 데이터 유형을 정의합니다. 테이블이나 다른 요소의 정의를 추가, 변경 또는 삭제하여 데이터베이스의 스키마를 수정합니다.

### DML 이란

### DDL과 DML의 차이점

### DDL 의 대표적인 용어들

- CREATE : 새로운 데이터베이스 관계(테이블), View, 인덱스, 저장 프로시저 만들기
- DROP : 이미 존재하는 데이터베이스 관계, 뷰, 인덱스, 저장 프로시저를 제거
- ALTER : 이미 존재하는 데이터베이스 개체에 대한 변경, RENAME 의 역할
- TRUNCATE : 관계 (테이블)에서 데이터를 돌이킬 수 없는 제거

**CREATE 문**

```sql
CREATE TABLE [테이블 이름] ( [열 정의] ) [테이블 매개변수]
```

열 정의 : [열 이름] [데이터 유형] {NULL | NOT NULL} {열 옵션}
기본 키 정의 : PRIMARY KEY
제약 조건 {CONSTRAINT} [제약조건 정의]

**ALTER 문**

```sql
ALTER objectType objectname parameters
```

**DROP 문**

```sql
DROP objectType 객체 이름.
```

**TRUNCATE 문**

```sql
TRUNCATE TABLE 테이블 이름;
```

| Command  | Description                               | Syntax                                                   |
| -------- | ----------------------------------------- | -------------------------------------------------------- |
| CREATE   | Create database or its objects            | CREATE TABLE table_name (column1 data_type,...);         |
| DROP     | Delete Objects from the database          | DROP TABLE table_name;                                   |
| ALTER    | Alter the structure of the database       | ALTER TABLE table_name ADD COLUMN column_name data_type; |
| TRUNCATE | Remove all records from table             | TRUNCATE TABLE table_name;                               |
| COMMENT  | Add comments to the data dictionary       | COMMENT 'comment_text' ON TABLE table_name;              |
| RENAME   | Rename an Object existing in the database | RENAME TABLE old_table_name TO new_table_name;           |

### DML 의 대표적인 용어들

### 출처

- [WIKI - DDL](https://ko.wikipedia.org/wiki/%EB%8D%B0%EC%9D%B4%ED%84%B0_%EC%A0%95%EC%9D%98_%EC%96%B8%EC%96%B4)
- [text](https://en.wikipedia.org/wiki/Data_definition_language)
