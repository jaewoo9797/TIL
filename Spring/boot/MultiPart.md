# MultiPart 
`MultipartResolver` 가 활성화된 후, `multipart/form-data`가 포함된 `POST` 요청의 내용이 구문 분석되어
정규 요청 매개변수로 접근할 수 있습니다.

`Multipart` : HTTP에서 request는 header와 body 부분으로 나누어져있다. header의 content-type은 body에 대한
데이터를 정의한다. 서버는 content-type의 값을 보고 body를 알맞은 형태로 해석한다. 이 요청 헤더는 content-type의 한
종류로서 웹 클라이언트가 요청을 보낼 때, HTTP 요청의 바디부분을 여러부분으로 나눠서 보내는 방식이다.

`Multipart/form-data`에 대해 알아보자.

우리가 일반적으로 폼 데이터를 전송하면 `application/x-www-form-urlencoded` 형식으로 전송됩니다. `HTTP body`에
바로 전송하고자 하는 데이터가 들어가는 형태입니다.

`name=kim&age=26`과 같은 `key-value` 쌍이 body에 들어가는 것이다. 이렇게 동일한 타입의 `문자 데이터`를 
전송하는 것은 전혀 무리가 없습니다.

하지만 이런 `key-value` 형태의 문자데이터와 바이너리 형태의 파일 데이터가 함께 전송되어야 하는 경우엔 어떨까요
`body`의 어디쯤에 여기부터는 파일이 전송되는 것이라고 알려주어야 할텐데 일반적인 `application/x-www-form-urlencoded` 타입으로는 불가능
합니다.

전송되는 폼 데이터를 구분해주어야 합니다. 여기서 구분되는 한 단위를 `part`라고 하고 동시에 여러 단위의
`part`를 나눌 수 있기에 `multipart`라는 이름이 붙은 것입니다.

## 1. Using @ModelAttribute
컨트롤러에서 `Multipart` 을 받는 방법
```java
@PostMapping(path = "/employee", consumes = { MediaType.MULTIPART_FORM_DATA_VALUE})
public String saveEmployee(@ModelAttribute Employee employee) 
```
- consumes attribute value is set to multipart/form-data
- @ModelAttribute has captured all the form data into the Employee POJO, including the uploaded file


## 2. Using @RequestPart
이 어노테이션은 매개변수로 json, xml 등 다중 속성 데이터를 연결하여 페이로드로 전송합니다.

두 개의 인자를 가지는 메서드 (모두 @RequestPart)
1. 직원 객체 Employee
2. MultipartFile 

```java
@PostMapping(path = "/request-part/employee", consumes = {MediaType.MULTIPART_FORM_DATA_VALUE})
public ResponseEntity<Object> saveEmployee(
        @RequestPart Employee employee,
        @RequestPart MultipartFile document        
){
    employee.setDocument(document);
    employeeService.save(demploye);
    return ResponseEntity.ok().build();
}
```

## 3. Using @RequestParam
@RequestParam을 사용하여 파일과 함께 키/값 쌍으로 전송되는 간단한 데이터에 유요합니다.
```java
@RequestMapping(path="/request-param/employee", method=POST, consumes = {MediaType.MULTIPART_FORM_DATA_VALUE})
public ResponseEntity<Object> saveEmployee(@ReuqestParam String name, @RequestPart MultipartFile document)
```


**출처**

- [Spring docs](https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-controller/ann-methods/multipart-forms.html)
- [baeldung 파일 컨트롤러 예제](https://www.baeldung.com/sprint-boot-multipart-requests)