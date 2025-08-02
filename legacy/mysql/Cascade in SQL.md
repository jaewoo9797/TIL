# Cascade in SQL

> One crucial feature for maintaining **data integrity** in SQL is the **CASCADE** operation, which simplifies handling relationships bwtween parent and child tables.

CASCADE 는 부모 테이블에서 변경 사항이 발생할 때 자동으로 트리거되어 관련 자식 테이블에 업데이트 또는 삭제가 전파되는 작업을 의미합니다.

By using `ON DELETE CASCADE` 또는 `ON UPDATE CASCADE` 를 사용하여 **참조 무결성**을 보장하고 일관된 관계를 유지하며 수동 데이터베이스 관리 작업의 복잡성을 줄일 수 있습니다.

- 데이터 관리 간소화 : 참조하는 테이블 간의 변경 사항을 자동화합니다.
- 참조 무결성 보장 : 부모 테이블과 자식 테이블 간의 일관된 관계를 유지합니다.
- 데이터 베이스 설계 최적화 : 종속된 자료 관리를 위한 수동 작업을 줄입니다.

### 출처

- [geeks - CASCADE IN SQL](https://www.geeksforgeeks.org/cascade-in-sql/)
- [postgre SQL - Constraints ](https://www.postgresql.org/docs/current/ddl-constraints.html)
- [Neon - PostgreSQL DELETE CASCADE](https://neon.tech/postgresql/postgresql-tutorial/postgresql-delete-cascade)
