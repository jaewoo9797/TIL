### 위클리 페이퍼

이론 수업에서 배운 내용에 관련된 특정 주제에 대하여 심화학습을 하고, 학습한 내용을 문서로 작성하는 과제

## 2주 차 주제

1.  객체지향 프로그래밍에서 `단일 책임 원칙(SRP)` 과 `개방-폐쇄 원칙(OCP)`에 대해서 설명하고, 각각의 원칙을 적용한 코드 예시를 들어주세요.
2.  `Stream API`의 `map`과 `flatMap`의 차이점을 설명하고, 각각의 활용 사례를 예시 코드와 함께 설명해 주세요.

## 단일 책임 원칙과 개방-폐쇄 원칙

내가 생각하는 `SRP`는 `객체의 책임을 분리하는 행위`이다. 객체는 자신의 정보를 관리하는 주체로서 정보 전문가이다. 그렇기에 자신의 정보를 관리하는데 다른 객체에 의존하지 않고 스스로 행위를 하며, 다른 객체와 메시지를 주고받으며 협력한다. 예를 들어 User라는 객체의 책임을 생각해 보자. User 객체는 자신의 정보를 저장하는 책임을 가진다. 그리고 다른 객체와 협력하기 위한 메서드라는 행위를 통해 자신의 정보를 주거나, 다른 객체에게 요청을 보내기도 한다. 

그렇다면, 유저가 어느 방에 참여한 방의 관리는 유저의 책임일까? 내 생각에는 '아니다'. 왜냐하면 유저는 자신의 참여한 방의 정보만을 저장하는 책임을 지니고 있다고 생각하기 때문이다. 그렇다면 참여한 방의 정보는 누가 관리해야 하느냐. 나는 참여한 방을 관리하는 `ParticipatedRoom` 객체를 만들었다. 이 객체는 유저가 참여한 방을 새로 생성하여 추가하거나, 수정하여 관리하는 책임을 지닌다. 이제 `User` 객체는 자신이 참여한 방을 관리하는 책임에서 벗어나 정보만을 저장하는 단일 책임 원칙이 지켜진다고 생각한다.

만약 유저가 참여한 방의 관리의 책임을 분리하지 않았을 경우와 비교를 해보겠다. 책임을 분리하지 않았을 경우에 유저는 방을 참여, 수정, 삭제, 추가하는 기능들을 모두 구현하고 있어야 할 것이다.

```java
class User {
    
   private List<Room> participatedRoom;
   
   public void exitRoom(String roomName) {
     paticipatedRoom.removeIf(it -> it.getName().equals(roomName));
   }
   
   public void participated(Room room) {
     participated.add(room);
   }
   ...
}
```

`User` 객체의 참여 방의 자료구조가 비효율적이라서 변경될 경우 로직을 수정해주어야 한다. 유저는 자신이 참가한 채널에 대한 정보를 알고 싶거나 나가고 싶을 뿐이지, 구체적인 구현은 궁금하지 않다. 그리고 참가한 방의 유형이 여러 개가 생길 경우 모든 로직을 구현해주어야 하고 이는 개발비용을 증가시킨다.

따라서, 유저의 참가 채널을 관리하는 객체를 만들고 두 책임 간의 연결을 끊어주려고 하였다.

```java
class User {
    private final ParticipatedRoom participatedRoom;
    
    public Room findParticipatedRoomByName(String channelName) {
      return participatedRoom.findByName(channelName);
    }
    //...
}

class ParticipatedRoom {
	private final List<Room> participatedRoom;
    
    public List<Room> findAllParticipated() {
      // room 상태가 활성화 필터링 로직.. 등등
      var foundRoom = ...
      return Collections.unmodifiedList(foundRoom);
    }
    //..etc
}
```

개방 폐쇄 원칙(OCP) 은 말 그대로 확장에는 열려 있으나 변경에는 닫혀 있는 것이다. 주로 추상화를 의미하며, 기능을 구현하는 객체들이 구현 방식을 각자 다르게 함으로써 OCP원칙을 충족시킬 수 있다. 내 생각에는 OCP는 SOLID 중 리스코프 치환 법칙으로 해결하는 하나의 방법이 되지 않을까 생각한다. 예를 들어 동물이라는 추상적인 개념에는 동일한 동작을 수행해야 하는 책임이 있을 수 있다. 밥을 먹는다. 운다. 잔다 등등.. 이러한 행위를 추상화하여 구체적인 행위는 이를 구현하는 객체들에 따라 다르게 구현하여서 확장할 수 있지 않을까 생각한다. 

### `Stream API` `map`과 `flatMap`의 차이점을 설명, 사용 사례

주로 자료구조를 사용할 때 `Stream API`를 사용했었다. `map`은 각각의 자료에 특정 조작을 한다고 생각한다. A 가 들어가서 B로 나오도록 하는 메서드로 이해하면 간단하다. `flatMap`은 스트림안에 스트림이 있을 경우 평탄하게 만들어주는 API이다. 리스트 안에 리스트가 있다고 생각해보면 편하다. 그냥 `map`을 사용할 경우 밖의 리스트의 요소들에 스트림이 감싸져서 나오는 것을 평탄하게 해준다고 생각한다. 많이 사용해보지 않으면 어렵다. 

코드로 사용사례 살펴보기

```java
class Student {
  private String name;
  
  public String getName() {
    return name;
  }
  
  public static void main(String[] args) {
    List<Student> students = new ArrayList<>();
    
    List<String> names = students.stream()
            .map(this::getName)
            .toList();
  }
}

  public static void main(String[] args) {
        List<String> strs = new ArrayList<>(List.of("abc", "def"));
        strs.stream()
                .flatMap(s -> Arrays.stream(s.split("")))
                .forEach(it -> System.out.print(it+" "));
    	// a b c d e f
  }
```

### 정리하면서 든 생각

추상적인 개념이라 개발자마다 생각하는 방식이 다르고, 객체의 책임을 어디서부터 어디까지 결정할지는 개개인의 역량에 달려있다. 아직 부족한 코드이고 계속적으로 고민하고 더 나은 코드는 무엇인가 생각하겠다. 자주 스스로 생각해 보면서 구현해 보는 연습과 노력이 많이 필요할 것 같다. 추상화하는 역량도 매우 부족하다고 느꼈고 개념을 머리로는 느낌적인 느낌으로는 알고 있다고 생각했는데, 글로 정리하려다 보니 생각보다 뚜렷하게 적을 수 없었다. 아직 학습이 부족합을 느낀다.
