# [JAVA] final 필드 생성 시 초기화 된 객체의 비교

객체를 비교할 때 우리는 `hashcode` ,`equlas` 메소드를 재정의 한다. 사이드 프로젝트로 외부 라이브러리와 프레임워크를 사용하지 않는 POJO(순수자바) 프로젝트를 진행하는 도중, 필드가 초기화 시에 fianl로 변경할 수 없는 객체를 만들었다.

객체를 테스트 하기위한 코드를 작성 중에, 나는 final값으로 생성 시 초기화 되는 UUID 값을 어떻게 테스트할 수 있을까 고민하게 되었습니다. 내가 생성한 객체가 하나의 UUID 값을 가진다면 정말로 두 객체가 같은 객체인 것을 보장하고 싶었기 때문입니다.

## 프로젝트를 진행하며 고민하게 된 동기
데이터를 저장하기 위해 객체를 생성한다. 자료구조에 저장된 데이터를 찾고 수정하기 위해 식별값 `id`를 통해 동일한 데이터를 가지고 왔는지 검사를 한다.

저장할 객체가 가지는 공통된 특성을 반복적으로 구현하는 것은 `DRY원칙`에 따라서 지양한다. 이에 공통 코드를 추상 클래스로 모두 모아 ㅓㅈ장할 객체의 인스턴스 필드를 한 곳으로 모으고 관련 메서드를 `template method` 로 구현했다.

```java

public abstract class AbstractUUIDEntity {

    private final UUID id;

    private final Long createAt;

    protected Long updateAt = null;

    private Status status;

    protected AbstractUUIDEntity() {
        this.id = UUID.randomUUID();
        this.createAt = createUnixTimestamp();
        this.status = REGISTERED;
    }
    //... getter 

    public void updateStatusAndUpdateAt() {
        this.status = MODIFIED;
        this.updateAt = createUnixTimestamp();
    }

    private long createUnixTimestamp() {
        return LocalDateTime.now().atZone(ZoneId.systemDefault()).toInstant().toEpochMilli();
    }

    @Override
    public boolean equals(Object o) {
        if (this == o) {
            return true;
        }
        if (o == null || getClass() != o.getClass()) {
            return false;
        }
        AbstractUUIDEntity that = (AbstractUUIDEntity) o;
        return id.equals(that.id);
    }

    @Override
    public int hashCode() {
        return id.hashCode();
    }
}
```

객체를 식별하기 위한 `id`를 `UUID` 객체를 이용해서 비교한다. 이를 위해 `hashcode`, `equals` 메서드를 재정의 했다. 이를 상속받은 객체를 만들고 비교할 때 `id`값이 같다면 동일한 객체인 것을 알 수 있다.

레포지터리를 구현하면서 `id`를 이용해서 찾아오는 로직을 구현하고 있는데, 헷갈리는 부분이 생겼다. 스프링 부트를 사용하면 객체를 비교하는 방법이 간단하지만, 프레임워크 도움 없이 내가 구현한 코드가 정상적으로 원하는 대로 결과를 뱉어내는가?
 테스트 코드를 작성하며 생각한대로 **정말로 같은 객체라는 결과**가 나오는 것을 눈으로 확인해야겠다고 생각했다.
(당연히 재정의 했다면 같다고 나올 것이지만)

## 검증
작성한 코드로 돌아가 보니, `hashcode` 메소드와 `equals` 메소드를 재정의 하지 않았었다. 😥

코드를 작성하다 보니 깜빡 잊고 누락하는 경우가 있다. 이에 테스트 코드를 작성하는 것이지 않나 생각이 다시 한 번 들었다.

생성 시에 값이 내부적으로 초기화되는 객체를 동일한 UUID를 가지게 보장하는 방법에 대해서 고민하고 막막했다. 이에 생성AI를 이용해서 물어보았더니, 자바의 리플렉션 기능을 사용하라고 해서 잘 모르지만 테스트를 해야하기 때문에 복사해서 테스트를 진행했다.

```java
 @Test
    @DisplayName("동일한 UUID를 가진 객체간의 동등성 비교를 할 경우 True를 반환하는지 테스트")
    void givenCreateAbstractEntityAndEqualEntityWhenIsEqualsThenReturnTrueTest() throws Exception {
        // Create two separate AbstractUUIDEntity objects
        AbstractUUIDEntity entity1 = new AbstractUUIDEntity() {};
        AbstractUUIDEntity entity2 = new AbstractUUIDEntity() {};

        // Generate a UUID to assign to both entities
        UUID sameUUID = UUID.randomUUID();

        // Use reflection to set the same UUID for both entities
        setFinalField(entity1, "id", sameUUID);
        setFinalField(entity2, "id", sameUUID);

        // Test equality (should be equal because UUIDs are the same)
        assertEquals(entity1, entity2, "Entities with the same UUID should be equal");

        // Test inequality with a new entity
        AbstractUUIDEntity entity3 = new AbstractUUIDEntity() {};
        assertNotEquals(entity1, entity3, "Entities with different UUIDs should not be equal");

    }
    private void setFinalField(Object object, String fieldName, Object value) throws Exception {
        // Get the field by name
        Field field = object.getClass().getSuperclass().getDeclaredField(fieldName);
        field.setAccessible(true); // Make the field accessible
        field.set(object, value); // Set the new value
    }
```

결과는 예상하지 못하게 두 객체가 다르다고 나왔다.
```text
Entities with the same UUID should be equal
필요: com.sprint.mission.discodeit.entity.common.AbstractUUIDTest$1@1d073097
실제: com.sprint.mission.discodeit.entity.common.AbstractUUIDTest$2@1d073097
```

객체 생성을 추상클래스로 코드가 제공되었는데, 이게 문제인가 해서 테스트를 위한 더미 객체를 만들고 상속받은 후 테스트를 진행하니 같은 객체라고 테스트가 통과하는 것을 확인할 수 있었다.
리플렉션에 대해서 더 학습하는 것은 필요성을 못느꼈고, 일단은 넘어가기로 결정햇다.  결과에 대해 코멘트를 달아주시면 감사합니다.