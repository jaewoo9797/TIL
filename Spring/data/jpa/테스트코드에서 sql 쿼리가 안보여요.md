연관관계의 조회 방식인 `LAZY`, `EAGER` 에 대해서 테스트 코드를 실행시키면서 
쿼리를 보려고했는데, 쿼리 정보가 안나오는 상황이 발생했다.

# 이유
JPA 테스트 환경에서는 영속성 컨텍스트와 트랜잭션 롤백 설정 때문에 쿼리가 실행되지 않거나 
로그에 나타나지 않는 경우가 발생할 수 있다. 특히, 테스트가 기본적으로 `read-only` 로 동작하거나
`@Transactional` 로 인해 롤백 처리되면서 실제 쿼리 실행이 지연되는 경우가 있다.

## 문제 원인
1. 영속성 컨텍스트의 특징 :
   - 영속성 컨텍스트는 기본적으로 **쓰기 지연** (write-behind) 전략을 사용한다.
   - `flush()` 호출 전에는 쿼리가 데이터베이스로 날아가지 않습니다. 
   - 테스트 환경에서 조회 쿼리가 발생하지 않는 이유는 `flush()` 가 호출되지 않거나 롤백 처리가 이루어짐
2. Spring Boot 테스트와 롤백:
   - `@DataJpaTest` 를 사용할 경우 기본적으로 테스트는 트랜잭션 안에서 실행되고, 테스트 완료 후 롤백된다.
   - 이 과정에서 쿼리가 최종적으로 실행되지 않을 수 있다.

## 해결방법
쿼리를 명확히 확인하기 위한 방법
1. `flush()` 강제 호출
   - `EntityManager`를 이용해 강제로 `flush()` 를 호출하면 영속성 콘텍스트의 변경 사항이 데이터 베이스로 커밋된다.
```java
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
}
```
2. Spring Transaction 설정 변경
   - 테스트에서 트랜잭션 롤백을 비활성화하여 실제 쿼리가 실행되도록 설정한다.
```java
@Test
@Transactional(propagation = Propagation.NOT_SUPPORTED) // 트랜잭션 비활성화
void 글을_저장하고_조회후_유저의_정보에_대해서_접근() {
    // given
    articleRepository.save(article);

    // when
    Article fetchedArticle = articleRepository.findById(article.getId()).orElseThrow();

    // Lazy Loading 트리거
    String username = fetchedArticle.getUser().getUsername();

    // then
    assertThat(username).isEqualTo("김길동");
}
 ```

2번으로 설정했더니, 프록시 user 객체에 에러가 발생한다. 프록시를 생성할 수 없다.

3. 테스트에서 트랜잭션 완전 종료
    - 기본 동작을 무시하고 트랜잭션을 강제로 종료한다.
    - 예를 들어 @Commit 어노테이션을 추가하면 롤백 없이 커밋이 발생한다.

두 가지 방법으로 좁혀질 것 같다. 하나는 `EntityManager`를 이용하거나, `@Commit` 어노테이션을 
사용하는 방법

아 커밋도 사용할 수 없었다. `insert` 쿼리만 나가고 조회는 영속성 컨텍스트를 이용한다. 
즉, Jpa 의 영속성 컨텍스트에 트랜잭션 범위 내에서 저장한 후 트랜잭션이 끝나는 시점에 insert 쿼리가 나가서
@Commit 도 적용할 수 없었다. : 먼저 조회가 된 이후 insert 쿼리가 날아가서 `select` 쿼리는 볼 수 없었다.

## 결론
`EntityManager`를 필드에 추가해서 사용하는 방법을 써야한다.

# 추가 정보
테스트 설정정보를 활성화 시켜주어야한다. 테스트 코드에 테스트 폴더에 존재하는 application 파일 
활성화를 시켜주어야 한다. 이는 설정정보가 잘못된 것인줄 알아서 알게된 정보이다. 테스트 폴더에 application.yml 파일을
따로 생성해서 테스트에 대한 설정정보를 주는 방법이다.

테스트 클래스 단에서 어노테이션을 추가한다.
```java
@ActiveProfiles("test")
@DataJpaTest
@Import(value = {JpaAuditingConfiguration.class})
@TestPropertySource(locations = {"classpath*:application.yml"})
class CommentRepositoryTest {
```
`@TestPropertySource(locations = {"classpath*:application.yml"})` 이건 없어도 됨

## 결론
이 문서에 목적은 연관관계 매핑이 된 엔티티의 데이터에 접근할 때 조회 쿼리를 보려는 것이다. 그런데, 조회 쿼리가
테스트 코드에서 작성한 테스트에서는 출력되지 않았던 것이 문제였다. 이는 Jpa 영속성 컨텍스트의 특징으로
발생한 문제였다. 