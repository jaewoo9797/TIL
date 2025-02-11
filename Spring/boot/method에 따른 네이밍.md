프로젝트를 시작하면서 각 레이어별 메서드의 네이밍에 대해서 일관적이고 통용되는 규칙을 이용하여 개발해야 합니다. 이에 각 레이어별 어떤 규칙들이 존재하고 구성해야하는지 정리합니다.

기본

1. Controller

- HTTP GET 요청을 처리하는 메서드의 네이밍 규칙: get(XXX)
- HTTP POST 요청을 처리하는 메서드의 네이밍 규칙: add(XXX)
- HTTP PUT 요청을 처리하는 메서드의 네이밍 규칙: update(XXX)
- HTTP DELETE 요청을 처리하는 메서드의 네이밍 규칙: delete(XXX)

2. Service

- HTTP GET 요청을 처리하는 메서드의 네이밍 규칙: find(XXX)
- HTTP POST 요청을 처리하는 메서드의 네이밍 규칙: add(XXX)
- HTTP PUT 요청을 처리하는 메서드의 네이밍 규칙: update(XXX)
- HTTP DELETE 요청을 처리하는 메서드의 네이밍 규칙: delete(XXX)

## Controller

- 컨터롤러의 메소드명은 해당 메소드가 수행하는 작업을 설명하는 동사로 시작합니다.
- 주로 HTTP 요청 메서드와 관련된 동사를 사용합니다.
- 예를 들어, 데이터를 조회하는 메서드의 경우 get으로 시작하고, 데이터를 생성하는 메서드의 경우 create로 시작합니다.
- 예시: getUserById, createUser, updateUser, deleteUser 등

## Service

- 서비스의 메서드 명은 해당 메서드가 제공하는 기능이나 업무를 명확하게 설명하는 동사로 시작합니다.
- 비즈니스 로직이나 특정 업무 처리를 담당하는 메서드의 이름을 지을 때 주로 사용됩니다.
- 예를 들어, 주문을 처리하는 메서드의 경우 processOrder와 같이 설명적인 동사를 시작합니다.
- 예시: processORder, calculateTotalPrice, cancelReservation 등

## Repository

- 레포지터리의 메서드명은 데이터베이스와 관련된 작업을 수행하는데, 주로 데이터베이스에서 데이터를 조회, 삽입, 갱신, 삭제하는 동작을 나타냅니다.
- 주로 CRUD기능과 관련된 동사를 사용합니다.
- 예를 들어, 사용자를 검색하는 메서드의 경우 findByUsername과 같이 시작합니다.

## API 디자인

1. 기본 URL에는 동사가 아닌 명사를 사용하며, 리소스마다 2개의 기본 URL을 유지하자.

```
/dogs(Collection),  /dogs/1234(Element)
```

2. HTTP 동사를 사용해 집합이나 개별 요소를 오퍼레이션하자.

3. 복수형 명사와 구체적인 이름을 사용하라.
   단수보다 복수 형태를 사용하는 편이, 그리고 추상적인 이름보다 구체적인 이름을 사용하는 편이, 직관적인 API다.

4. 자원 간의 관계를 간단하게 하여 URL 계층이 깊어지는것을 피하자.

- 자원간의 관계, 매개변수 및 속성과 같은 복잡한 것은 쿼리파라미터를 이용하자.

```
예) GET /dogs?color=red&state=running&location=park
```

5. 오류 처리를 명확하게 해라.

- HTTP 상태코드를 정하고, 개발자들을 위한 오류 메시지 정의, 상세 정보 링크 등을 넣어주면 좋다.

6. 버전 관리를 해라.

- 접두사 "v"로 버전을 지정하고 1계층에 두자.
- 인터페이스로서 구현이 아님을 강조하기 위해 간단한 정수를 사용하자. 버전 일련번호는 소수점 쓰지마라.
- 필요시 헤더에 버전을 디자인할 수도 있다.

7. 부분적 응답과 페이징 처리를하라.

- 리턴해 달라는 필드를 지정하려면 쉼표로 구분된 목록을 사용하라.
  예) /dogs?fields=name,color,location
- 페이징을 할 경우 상대 위치(offset)와 범위(limit)를 사용, 기본값은 limit=10&offset=0
  예) /dogs?limit=25&offset=50

8. 데이터베이스에 없는 자원에 대한 응답일 경우 동사를 사용하라.

- 리소스가 아닌 응답을 전송하는 경우 명사가 아니라 동사를 사용하는 것이 알기 쉽다.
- 계산, 번역, 변환 등의 경우처럼 알고리즘 계산이나, 번역, 환율 변환 등에 요청이 올 경우 명사가 아니라 동사를 사용하라.

9. 검색 팁

- 전체 검색은 동사"search" 와 쿼리 매개 변수"q"를 사용하자.
  예) /search?q=fluffy+fur



- (web api design: 개발자에게 사랑받는 API 만들기기)[https://www.mimul.com/blog/web-api-design-from-apigee/?fbclid=IwAR1Q5QmdYTLpgc9c5w9IKQM2i49JBsNuEwyJHntYjWytEYQK89m2fJUMPAE]
