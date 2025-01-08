# Mockito Intro

기본적인 소개

일단 공식 레퍼런스는 들어가서 소개만 보았는데, 보다 빠르게 습득하기 위해서 한국어로 된 문서를 조금 읽어보면서 실습해보면 적응 후 살펴보는게 좋다고 생각해 다음 글을 참조해 정리한다.

[Mockito 소개](https://www.nextree.io/mockito/)

새로운 API client 서비스를 만들어 테스트하거나, 외부 서비스의 API client 혹은 DB와의 연결이 포함된 비즈니스 로직을 테스트해야 한다면 웹 통신과 DB 연결을 위한 소프트웨어가 필요할 것이다.

이런 복잡한 연관성을 가진 프로그램을 테스트하려면 Mockito를 사용할 때다.

## Mockito를 사용한 테스트 코드 예시

의존성 주입을 위해 사용했던 `@Autowired` 애노테이션의 용법과 동일하게, 모조 객체를 만들어 사용하고 싶은 클래스를 `@Mock` 애노테이션 혹은 `@MockBean` 애노테이션을 사용하여 필드 주입하면
된다.

### 0. Mockito Dependency 추가

```text
dependencies {
    testImplementation 'org.mockito:mockito-core:4.8.0'
}
```

### 1. `@Mock` 기본 예제

REST API Test

```java

@RequestMapping("test")
@RestController
@RequiredArgsConstructor
public class TestResource {

    @GetMapping("{id}")
    public String mockTest(
            @PathVariable("id")
            String id
    ) {
        return id + " actualResult";
    }
}
```

```java

@Slf4j
@ExtendWith(SpringExtension.class)
@AutoConfigureWebTestClient
@RequiredArgsConstructor
public class MockitoTester {

    private WebTestClient webTestClient;

    @Mock
    private TestResource testResource;

    @BeforeEach
    public void beforeEach() {
        this.webTestClient = WebTestClient.bindToController(this.testResource).build();
    }
}
```

`@AutoConfigureWebTestClient`은 `WebTestClient` 인터페이스를 사용할 수 있게 해주는 어노테이션.

`WebTestClient`는 API client service에서 사용하는 `WebClient`와 같은 역할을 해주는 인터페이스로,
HTTP connection을 내부적으로 수행하여 mock request와 response를 제공해 테스트를 가능하게 해줍니다.

given 

먼저 Mockito의 `when` 과 `then` 메서드를 통해 테스트 대상의 given을 설정한다.

