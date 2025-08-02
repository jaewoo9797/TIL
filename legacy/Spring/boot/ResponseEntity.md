# ResponseEntity
웹 서비스에서는 많은 정보를 송수신하게 됩니다. 각각의 다른 웹 서비스들이 대화하려면, 서로 정해진 약속에 맞게 데이터를 가공해서 보내야합니다. 보내는 요청 및 데이터의 형식을 우리는 HTTP이라고 합니다. Spring에서도 마찬가지로 HTTP에 맞게 데이터를 송수신해야합니다. 요청에 대한 응답을 HTTP 형식으로 코드로 직접 작성하는 것은 쉬운일이 아닙니다. 이를 만들어주는 ReponseEntity를 통해서, 빠르게 쉽게 규격에 맞는 HTTP 응답을 생성해 봅시다.

Spring에서 데이터를 송수신하는 방법
HTTP란
HTTP는 HyperText Transfer Protocol의 약자로, Cliend와 Server 사이에 요청과 응답을 처리하기 위한 규약입니다. 해당 규약을 지키게 된다면 살펴보는 것만으로도 어떤 요청을 하는지에 대해서 간략하게 알 수가 있습니다. 

HTTP는 요청과 응답 모드 크게 세 가지 요소로 구성됩니다.
- start Line
- Header
- Body

1. Start Line은 method, URL, 그리고 version으로 이루어져있으며, 서버에서 요청을 받아들이는 첫 줄입니다.
2. Headers는 요청에 대한 접속 운영체제, 브라우저, 인증 정보와 같은 부가적인 정보를 담고 있습니다.
3. Body는 요청에 관련된 json, html과 같은 구체적인 내용을 포함합니다.

Spring에서는 HTTP Response를 만드는 것이 주요한 관심사입니다. 200, 404 등 각각의 응답의 상태 코드뿐만 아니라, Body에 들어갈 내용도 넣어주어야 합니다. 세 가지 요소를 채운 객체를 만드는 것은 까다로울 것입니다. 데이터를 받아서 자동으로 구성해주는 것이 바로 @ResponseBody와 ResponseEntity 입니다.

@ResponseBody
@ResponseBody 어너테이션은 HTTP 규격에 맞는 응답을 만들어주기 위한 것입니다. HTTP 요청을 객체로 변환하거나, 객체를 응답으로 변환하는 HttpMessageConverter를 사용합니다. HTTPMessageConverter는 해당 Annotation이 분은 대상을 response body에 직렬화를 하는 방식으로 작동됩니다. 따라서 Controller에서 반환할 객체나 Method에 @ResponseBody를 붙히는 것만으로 HTTP 규격에 맞는 값을 반환할 수 있습니다.

Annotation을 추가하는 것으로 간단하게 처리를 할 수 있다는 점이 @ResponseBody의 장점입니다. 또한, 만약 해당 메서드를 가진 Controller에 @RestController 가 붙으면 @ResponseBody를 생략하여 더 간결하게 코드를 작성할 수 있습니다. 

하지만 단점으로는 HTTP 규격 구성 요소 중 하나인 Header에 대해서 유연하게 설정을 할 수 없다는 점입니다. 또한 Status도 메서드 밖에서 Annotation을 사용하여 따로 설정해주어야 한다는점이 있습니다. 이는 @ResponseBody만 사용시에 별도의 뷰를 제공하지 않고, 데이터만 전송하는 형식이기 때문입니다. 이와 같은 점들을 해결해줄 수 있는 것이 ResponseEntity라는 객체입니다.

ResponsEntity
ResponseEntity도 마찬가지로 HTTP응답을 빠르게 만들어주기 위한 객체입니다. @ResponseBody와 달리 Annotationdl 아닌 객체로 사용이 됩니다. 즉, 응답으로 변환될 정보를 담은 요소들을 객체로 만들어서 반환해줍니다. 객체의 구성요소에서 HttpMessageConverter는 응답이 되는 본문을 처리해 준 뒤에, RESTTemplate에 나머지 구성 요소인 Status를 넘겨주기 됩니다. 이를 통하여 앞서 언급한 문제점들을 해결할 수 있는데, 선언된 구조부터 살펴보도록 하겠습니다.

```java
public class ResponseEntity extends HttpEntity {
  private final Object status;
}
```
먼저, ResponseEntity의 구조를 보게되면, 다음과 같이 Status 만 필드 값으로 가지고 있습니다. ResponseEntity에서 직접적으로 StatusCode를 지정할 수 있다는 것을 의미합니다. 나머지 부분은 HttpEntity에 구현이 되어있는데, 이는 RequestEntity와 여러 설정들을 공유하기 때문입니다. 다음은 HttpEntity의 구현 부분을 보도록 하겠습니다.

```java
public class HttpEntity<T> {

  private static HttpEntity<?> EMPTY = new HttpEntity<>();

  private final HttpHeaders headers;

  @Nullable
  private final T body;
}
```

이와 같이 ResponseEntity는 HttpEntity를 상속하여 구현이 됩니다. HttpEntity 에서는 Generic 타입으로 Body가 될 필드 값을 가질 수가 있습니다. Generic 타입으로 인하여 바깥에서 Wrapping 될 타입을 지정할 수가 있습니다. Wrapping 된 객체들은 자동으로 HTTP 규격에서 Body에 들어갈 수 있도록 변환이 됩니다. 또한, 필드 타입으로 HttpHeaders를 가지고 있는데, 이는 ResponseBody 와 다르게 객체 안에서 Header를 설정해줄 수 있음을 암시합니다.

Constructor 보다는 Builder
이렇게 ResponseEntity를 사용할때, 생성자를 사용하기보다는 builder를 활용하는 것을 권장하고 있습니다. 그 이유는 숫자로 된 상태코드를 넣을 때, 잘못된 숫자를 넣을 수 있는 실수 때문입니다.

Spring 또한 HTTP에 맞는 응답을 클라이언트에게 제공을 해주어야 합니다. Header 설정에는 유연성을 가진 ResponseEntity를 사용하여, 빠르면서도 실수를 줄이는 HTTP 응답을 구현할 수 있습니다.

(ResponseEntity-Spring Boot에서 Response를 만들자)[https://tecoble.techcourse.co.kr/post/2021-05-10-response-entity/]