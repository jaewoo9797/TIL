SQL 1번으로 `N명` 의 `member`를 조회하였는데, 조회된 member 의 결과 N 번의 수만큼
`member` 엔티티 안에 연관관계로 있는 `order` 엔티티까지 추가로 N + 1 조회 SQL을 실행하는
문제가 N+1 문제이다.

# 👀 Lazy Loading 의 N + 1 문제를 해결하는 방법에 대해서 알아보자.

N + 1 자체의 문제는 한쪽 테이블을 조회하고 싶은데 연결된 다른 테이블까지 따로 조회되는 문제입니다.
미를 미리 두 테이블을 JOIN 하여 한 번에 모든 데이터를 가지고 올 수 있다면 N + 1 문제가 발생
하지 않을 것입니다. 이런 방식을 Fetch Join 이라고 부릅니다.

## 1. Fetch Join 으로 해결하기

`Featch Join` : 연관된 엔티티나 컬렉션을 한 번에 같이 조회할 수 있습니다. 이를 통해 조회를
하면, 연관된 엔티티는 프록시가 아닌 실제 엔티티를 조회하게 되므로 연관관계 객체까지
한 번의 쿼리로 가져올 수 있습니다.

```java
@Query("select DISTINCT o from OWNER o join fetch o.pets")
```

단점

- 쿼리 한 번에 모든 데이터를 가져오기 때문에 JPA 가 제공하는 Paging API 사용 불가능
- 1:N 관계가 두 개 이상인 경우 사용 불가
- 패치 조인 대상에게 별칭 부여 불가능
- 번거롭게 쿼리문을 작성해야 한다.

## 2. Entity Graph

`@EntityGraph` 의 attributePaths 는 같이 조회할 연관 엔티티명을 적으면 됩니다. ,(콤마)를 통해
여러 엔티티를 정의할 수 있습니다.

fetch join 과 동일하게 JPQL을 사용해 Query 문을 작성하고 필요한 연관관계를
Entity Graph에 설정해야 합니다.

```java
@EntityGraph(attributesPaths = {"pets"})
@Query("select DISTINCT o from OWNER o")
```

## 3. Fetch Join VS Entity Graph

Fetch Join과 EntityGraph 의 출력 쿼리를 보면, Fetch Join 의 경우 Inner join 을 하는 반면,
EntityGraph 는 outer join 의 쿼리를 실행합니다. 기본적으로 outer join 보다 inner join 이 성능
최적화에 더 유리합니다. 보통 그래서 Fetch Join 을 더 많이 사용됩니다.

JPQL을 사용할 경우 그냥 쿼리 뒤에 join fetch 만 붙여주면 되는데 번거롭게 어노테이션과
그 속성을 추가할 필요가 없기 때문이기도 합니다.

## 4. 사용 시 주의할 점

fetch join, entity graph 는 공통적으로 카타시안 곱이 발생하여 중복이 생길 수 있다.

- cross join 이 발생하는 조건
  - join 명령을 했을 때 명확한 join 규칙이 주어지지 않았을 때
  - join 이후 on 절이 없을 때, db는 두 테이블의 결합한 결과는 내보내야하고, 조건이 없으니 M \* N 경우의 수를 출력한다.

1. JPQL 에 DISTINCT 를 추가하여 중복제거

```java
@Query("select DISTINCT o from owner o join fetch o.pets")
List<Owner> findAllJoinFetch();
```

```java
@EntityGraph(attributePaths = {"pets"})
@Query("select DISTINCT o from Owner o")
List<Owner> findAllEntityGraph();
```

2. OneToMany 필드 타입을 Set으로 선언하여 중복 제거

```java
import java.util.LinkedHashSet;

@OneToMany(mappedBy = "owner", fetch = FetchType.EAGER)
private Set<Pet> pets = new LinkedHashSet();
```

## 5. 컬렉션을 패치 조인하여 페이징 불가능 -> Batch_Size로 해결

중복 Distinct 처리는 SQL 쿼리 성능에 좋지 않다. 그리고 Collections 타입의 OneToMany
연관관계의 타입들은 paging을 사용시 paging out of memory warnig 오류가 발생한다.

즉, Collection 타입의 필드들을 조회 시, fetch join을 할 때 페이징은 불가능하다.

이를 해결하는 방법으로는 in 쿼리를 사용하여 필드를 제한해서 조회하는 Batch_Size 를 사용하는 것이다.

엔티티의 연관관계를 고려해서 최적화하고 N + 1에 대해 학습을 진행하도록 설계받음

레포지터리로 테스트를 진행하면서 조회 최적화 등 학습을 진행해보려고 한다.

1. 연관관계 매핑 최적화

- OneToMany 등 적절한 사용과 fetch 전략 설정 이해
- 양방향 연관관계와 단방향 연관관계의 차이를 실습

2. 조회 최적화

- JPQL과 Native Query 를 통한 데이터 조회
- @EntityGraph 를 활용한 페치 조인
- Hibernate의 2차 캐시 활용

3. N + 1 문제 이해 및 해결:

- N + 1 문제를 디버깅하는 방법
- 페치 조인과 Batch Size 설정을 통한 해결

4. 테스트 환경 구성

- H2 데이터베이스를 활용한 테스트
- JUnit 과 AssertJ를 이용한 테스트 코드 작성

5. 실험 및 학습

- 각 연관관계 매핑에서 N + 1 문제가 어떻게 발생하는지 실험.
- 페치 조인과 batch fetching 이 어떻게 동작하는지 확인

## Article 연관관계 조회

```java
/**
     * 연관관계 테스트
     */
    // 1. Article 엔티티에서 user 필드를 통해 User 데이터를 저장하고 조회하는 테스트
    // 2. fetch 전략 Lazy, Eager 동작 방식 확인
    @Test
    void 글을_저장하고_조회후_유저의_정보에_대해서_접근() {
        //given
        articleRepository.save(article);

        // when
        entityManager.flush();
        entityManager.clear();

        Article fetchedArticle = articleRepository.findById(article.getId()).orElseThrow();

        // then
        assertThat(fetchedArticle.getUser().getUsername()).isEqualTo("김길동");
        System.out.println(fetchedArticle.getUser().getUsername());
    }
```

1. Article 과 연관관계를 맺는 User - Lazy

- 조회 쿼리가 2 개가 나간다. 하나의 article을 조회하는데 2개의 쿼리

2. User - Eager

- 조회 쿼리가 한 개로 join을 통해서 조회해온다.

3. Comment - User - Article

- 하나의 댓글에 3번의 조회 쿼리가 나간다.

이번 테스트를 통해서 LAZY, EAGER 에서 조회하는 쿼리 메소드의 차이에 대해서 학습하였다. 개념과 결과에
대해서는 학습을 진행하였는데, 아직 내 프로젝트에 적용하는 방법이 아직은 살짝 복잡하다고 생각되고, 반복을 통해
익숙해져야 한다는 필요성을 느꼈다.

## 하나하나 정리해보자

일단 `Article` 객체로 기준으로 생각을 해보자.

- 글에 들어갈 경우 해당하는 댓글을 조회해서 화면에 같이 보여주지 않을까? 그런데, 댓글이 많을
  경우에는 어떻게 처리해주어야 할까?
- 글의 목록을 조회할 때도 댓글의 수를 조회해야하는데, 이건 어떻게 처리해야할까?
- 댓글이 생성될 때 article 에도 데이터가 업데이트 되어주어야 하겠구나
- 이와 같은 이유로 양방향 연관관계를 지어주어야 하는 이유라고 생각한다.
- 글의 목록을 조회하는 메서드와 해당 게시글의 상세정보로 들어가는 메서드를 분리해야하겠지?

차근차근 하나하나 이것저것 다 해보면서 학습을 진행하며 정리를 하겠다.
