혼자서 Jpa 학습을 진행하면서 성능 최적화를 위해 고민하게 되었다.
특정 조회 요청에서는 데이터베이스에 많은 데이터가 존재하는 컬럼에 접근하게되는데
모든 데이터 셋을 모두 가지고 가려고 하려면, 데이터베이스에서 한 번 계산이
이루어지고, 가지고온 데이터를 애플리케이션 메모리에 올리면서, 그리고 가지고 온 데이터를 response 응답하면서 직렬화와 네트워크 처리에서 많은 리소스를 소모할 것으로 예상된다.

스프링 부트 데이터의 `Paging` and `Sorting` 섹션을 보면서 정리해보겠다.

# Spring Paging and Sorting

스프링 부트 레포지터리 `Paging`과 `Sorting` 추상화 객체의 사용.

## Paging

큰 결과 데이터 셋의 모든 데이터를 반환하는 것보다 스프링부트는 특정 URL 파라미터로 페이지 사이즈와 시작 번호를 받는 것을 권장한다.

JpaRepository 는 PagingAndSortingRepository 를 구현하는 인터페이스로 페이징 기능을 사용할 수 있다. 한 번의 조회를 통해 20개의 엔티티를 조회해와 제공해준다. 만약 한 번의 조회로 얻어오고 싶은 데이터 셋의 개수를 설정하고 싶다면 size parameter 를 설정하면 된다.

```http
http://localhost:8080/people?size=5
```

레포지터리에서 해당 페이지네이션을 메서드 시그니처에 파라미터로 추가하면 된다. List 대신 Page, Slice 를 반환한다.

```java
Page findByNameStartWith(@Param("name") String name, Pageable p);
```

응답 결과를 보면 링크와 함께 현재 보고 있는 페이지의 정보를 반환해준다.

```json
{
  "_links" : {
    "self" : {
      "href" : "http://localhost:8080/persons{&sort,page,size}",
      "templated" : true
    },
    "next" : {
      "href" : "http://localhost:8080/persons?page=1&size=5{&sort}",
      "templated" : true
    }
  },
  "_embedded" : {
  	… data …
  },
  "page" : {
    "size" : 5,
    "totalElements" : 50,
    "totalPages" : 10,
    "number" : 0
  }
}
```

하단에 존재하는 Page 정보가 포함되어 있다. 이 정보를 통해 사용자가 데이터를 볼 때 전체 위치를 반영하여 UI 도구를 쉽게 구성할 수 있다.

만약 다음 페이지를 보고 싶다면,

```curl
"http://localhost:8080/person?page=1&size=5"
```

다음과 같이 요청하면 된다.

## Sorting

스프링 데이터는 sorting parameters를 사용해 정렬하는 것을 추천한다. 특정 속성으로 정렬한 결과를 얻고 싶을 때, ULR parameter에 sort 하려는 속성의 이름을 넣어주면 된다. 또한 , 를 사용하여 오름차순 내림차순을 정의할 수 있다.

```curl
http://localhost:8080/people/search/nameStartsWith?name=K&sort=name,desc"
```

더 많은 속성을 이용해서 sorting 하려면 추가적으로 `sort=PROPERTY` 파라미터로 필요한만큼 추가해주면 된다.

## 리소스 , 표현

페이지네이션을 설계할 때 첫 번째 질문은 페이지를 실제 리소스로 간주할 것인지, 단지 리소스의 표현으로 간주할 것인지이다.

페이지 자체를 리소스로 취급하면 더 이상 호출 간에 리소스를 고유하게 식별할 수 없는 것과 같은 문제가 발생한다. persist 계층에서 페이지가 적절한 엔티티가 아니라 필요할 대 생성되는것을 생각하면 페이지는 표현의 일부이다.

- URI 경로 : /foo/page/1
- URI 쿼리 : /foo?page=1

페이지는 리소스가 아니라는 점을 명심하고 URI 쿼리에 피이지 정보를 담아 보낸다.

## 컨트롤러

```java
@GetMapping(params = {"page", "size" })
public List<Foo> findPaginated(
  @RequestParam("page") int page,
  @RequestParam("size") int size,
  UriComponentsBuilder uriBuilder,
  HttpSerlvetResponse response
) {
  Page<Foo> resultPage = service.findPaginated(page, size);

  eventPublisher.publishEvent(new PaginatedResultsRetrievedEvent<Foo>(
    Foo.class, uriBuilder, response, page, resultPage.getTotalPages(), size
  ));

  return resultPage.getConent();
}
```

또는 page, size, sort 매개변수를 자동으로 매핑하는 Pageable 객체를 사용할 수 있다.
