# RequestMapping 
컨트롤러에서 HTTP를 매핑하기 위한 `RequestMapping`을 알아보겠습니다.

`@RequestMapping` 주석을 사용하여 요청을 컨트롤러 메서드에 매핑할 수 있습니다. URL, HTTP 메서드, 요청 매개변수,
헤더 및 미디어 유형별로 일치시키는 다양한 속성을 가지고 있습니다. 클래스 수준에서 `@RequestMapping` 공유 매핑을 표현
하거나 메서드 수준에서 특정 엔드 포인트 매핑으로 좁히는 데 사용할 수 있습니다.

`@RequestMapping` 의 shortcut 표현
- `GetMapping`
- `PostMapping`
- `PutMapping`
- `DeleteMapping`
- `PathchMapping`

이러한 shortcut 표현의 커스텀 어노테이션이 제공되는 이유는 대부분의 컨트롤러 메서드가 특정 HTTP 메서드에
매핑되어야 하기 때문입니다. 그렇다면 `@RequestMapping`을 알아야하는 이유는 무엇일까요?

우선적으로는 shortcut 어노테이션이 제공되어도 클래스 수준에서는 여전히 @RequestMapping 어노테이션을 이용하여 공유 매핑
포인트가 필요합니다. 예를들어 한 컨트롤러에서 특정 도메인의 요청을 받는다고 가정하겠습니다.

- GET `/user`
- POST `/user`
- Get `/user/{id}`
- Get `/user?name=xxx&age=xxx`
- Patch `/user/{id}`
- DELETE `/user/{id}`

위와 같이 하나의 컨트롤러에서도 여러 개의 매핑이 필요해질 수 있으며, 중복되는 도메인 url이 존재합니다. 중복되는 부분을 공유
매핑 하여 공통적으로 적용하는데 `@RequestMapping`을 사용할 수 있습니다. 물론 공통적인 url을 클래스 수준에서 선언하지 않고 
메서드 레벨에서 선언해도 문제될 것은 없지만, 중복을 제거하는 것이 소프트웨어 원칙에 적합하다고 생각합니다.

## 주의사항
`@RequestMapping`은 동일한 요소 (클래스, 인터페이스 또는 메서드)에 선언된 다른 `@RequestMapping` 어노테이션과 함께 사용할 수 
없습니다. 동일한 요소가 여러개의 `@RequestMapping`이 감지되면 경고가 기록되고 첫 번째 매핑만 사용됩니다. 이는 shortcut 
어노테이션에서도 동일하게 적용됩니다.

## Short cut 어노테이션
메서드 레벨에서 사용하는 `@ReqeustMapping` 의 short cut 어노테이션에 대해서 알아보자. 기존 `@RequestMapping`을 사용하여 
모든 요청을 매핑할 수 있다. 
```java
@RequestMapping(value = "/user", method= RequestMethod.GET)
@RequestMapping(value = "/user", method= RequestMethod.POST)
```

위와 같이 할 경우에 생기는 문제점을 생각해보자. 나는 먼저 `method = ReqeustMethod ...` 이런식으로 매핑해주는 것이 
반복적인 코드가 반복적으로 발생하고, 각각의 매핑의 정보가 길어진다는 문제점이 있는것 같다. 

이를 short cut 어노테이션을 사용해서 해결할 수 있다. 각각의 어노테이션에는 구성은 다음과 같이 유사하게 내부에 `@RequestMapping`
어노테이션을 포함하고 있다.

```java
@Target({ElementType.METHOD})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@RequestMapping(
    method = {RequestMethod.GET}
)
public @interface GetMapping {
```

`method = {RequestMethod.GET}` 를 직접 선언해주었던 부분을 포함하고 있기에, 메서드 레벨에서 `@GetMapping` 을 사용하면
`@RequestMapping(value = ..., method = RequestMethod.GET)` 를 사용하는 것과 같다. 이와 같이 공통되는 부분을 
포함하여 제공되는 short cut 어노테이션을 사용하여 얻을 수 있는 이점은 내가 느끼기에 두 가지인것 같다.
- 가독성. 한 눈에 어떤 의미와 의도를 가지는지 알 수 있다.
- 공통되고 반복적인 코드가 사라짐

## Consumable Media Types
```java
@PostMapping(path = "/pets", consumes = "application/json") 
public void addPet(@RequestBody Pet pet) {
	// ...
}
```
consumes 속성은 콘텐츠 유형을 제한하는 것입니다. 클래스 수준에서 공유 소비 속성을 선언할 수 있고, 메서드 수준에서 
클래스 수준 선언을 오버라이딩하여 소비할 수 있습니다. 즉, consumes 속성은 요청 헤더 Content-type의 값을 제한하겠다는 의미입니다.

```java
@RequestMapping(value = "/reply/replyList.wow",
			consumes = {MediaType.APPLICATION_JSON_VALUE,MediaType.APPLICATION_XML_VALUE} )
```
위와 같이 consumes 속성을 지정할 경우 "application/json", "application/xml" 타입이 아닌 제한하겠다는 의미이다.

## Producible Media Types
```java
@GetMapping(path = "/pets/{petId}", produces = "application/json")
@ResponseBody
public Pet getPet(@PathVariable String petId) {
	// ...
}
```
produces 속성은 서버에서 데이터를 전달할 때 해당 데이터의 유형을 정해주는 속성이다. 

> `MediaType` provides constants for commonly user media types, such as `APPLICATION_JSON_VALUE` AND `APPLICATION_XML_VALUE`.

## 정리
정리하자면, `@RequestMapping` 어노테이션을 이용하여 HTTP URL과 method 와 속성을 이용하여 서버에서 클라이언트의 요청을
적절한 컨트롤러로 매핑하도록 사용한다. 반복적으로 method 속성을 지정해주는 반복 코드를 short cut 어노테이션을 이용해서
생산성을 향상시켜준다는 것도 알 수 있었다. 각 메서드 수준에서 short cut 어노테이션을 사용하는 것이 보다 가독성이 좋고
간편하는점에서 사용하는 것을 알 수 있었다. 

또한 요청 타입을 제한하고 반환하는 타입을 지정해줄 수 있는 consumes속성과 produces 속성도 간단하게 알아보았다. 이는 
파일이나 이미지 등을 처리할 때도 사용할 수 있으니 기본적인 유형을 살펴봄으로 이 후 학습을 진행할 때에 이해하는데 도움을 
받을 수 있을 것 같다.