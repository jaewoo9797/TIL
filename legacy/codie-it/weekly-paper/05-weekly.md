# 5주차 위클리 페이퍼

- 웹 API의 발전 과정에서 SOAP에서 REST로의 전환이 일어난 이유와 그 장단점에 대해 설명하세요
- Spring Boot 에서 `@RestController`로 들어온 HTTP 요청이 처리되어 응답으로 변환되는 전체 과정을 설명하세요. 특히 HTTP 메시지 컨버터가 동작하는 시점과 역할을 포함해서 설명하세요

<br/>

## 1. SOAP , REST

웹 API의 발전과정 중 `SOAP` 란 무엇이고, `REST`란 무엇인지 알아본 후 전환이 일어난 이유와 장단점에 대해서 알아보자.

<br/>

### SOAP

SOAP (Simple Object Access Protocol) 는 다른 언어로 다른 플랫폼에서 빌드된 애플리케이션이 통신할 수 있도록 설계된 최초의 표준 프로토콜입니다.

프로토콜이기 때문에 복잡성과 오버헤드를 증가시키는 빌트인 룰을 적용하므로, 페이지 로드 시간이 길어질 수 있습니다. 그러나 이러한 표준은 빌트인 컴플라이언스를 제공한다는 의미이므로, 기업에서 선호하는 방식이기도 합니다.

빌트인 컴플라이언스 표준에는 보안과 안정적인 데이터베이스 트랜잭션의 기본 속성이 ACID이 포함됩니다.

데이터에 대한 요청이 SOAP API로 전송되는 경우 HTTP, SMTP, TCP 등의 다양한 애플리케이션 레이어 프로토콜을 통해 처리될 수 있습니다. 그러나 요청이 수신되면, 인간과 기계가 모두 읽을 수 있는 마크업 언어인 XML 문서 형식으로 반환 SOAP 메시지가 반환됩니다. SOAP API에 대한 완료된 요청은 브라우저에서 캐시할 수 없으므로, API로 재전송하지 않는 한 이후에 액세스 할 수 없습니다.

<br/>

### REST

REST는 웹 서비스와 모바일 애플리케이션 경량화의 필요에 맞춘 아키텍처 원칙 세트입니다. 가이드라인이기 때문에 이러한 권장 사항의 구현 여부는 개발자에게 달려 있습니다.

데이터 요청이 REST API 로 전송될 때는 일반적으로 하이퍼텍스트 전송 프로토콜(HTTP)을 통해 이루어집니다. 이러한 요청을 수신하면 REST용으로 설계된 API(RESTful API 또는 RESTful 웹 서비스)가 HTML, XML, 일반 텍스트, JSON과 같은 다양한 형식으로 메시지를 반환할 수 있습니다.

<br/>

무엇이 RESTful에 속하는 것인지 갖추어야 할 요소

- 클라이언트, 서버, 리소스로 구성된 클라이언트-서버 아키텍처가 필요합니다.
- 요청이 통과하는 서버에 클라이언트 콘텐츠가 저장되지 않는 스테이트리스(stateless) 클라이언트 - 서버 커뮤니케이션이 필요합니다. 대신 세션의 상태에 대한 정보가 클라이언트에 저장됩니다.
- 일부 클라이언트-서버 간 상호 작용의 필요성을 제거할 캐시 가능 데이터가 필요합니다.
- 애플리케이션 요구 사항별로 다른 형식이 아닌, 표준화된 형식으로 정보를 전송할 수 있도록 구성 요소 간 통합된 인터페이스가 필요합니다.
- 클라이언트-서버 간의 상호 작용을 계층적으로 조정할 수 있도록 계층화된 시스템 제약이 필요합니다.
- 실행 가능한 코드를 전송해 서버가 클라이언트의 기능을 확장할 수 있게 해주는 코드 온디맨드가 필요합니다.

<br/>

## Summary

REST 와 SOAP는 각기 다른 두 가지의 온라인 데이터 전송 방식입니다.

둘 다 웹 애플리케이션 간 데이터 통신을 허용하는 애플리케이션 프로그래밍 인터페이스(API)를 구축하는 방법을 정의합니다. REST는 아키텍처 원칙 세트이고, SOAP는 W3C에서 유지관리하는 공식 프로토콜입니다.

그래서 두 차이점은 무엇인가?  
REST는 유연한 구현을 제공하는 가이드라인 세트고, SOAP는 XML 메시징과 같은 특정 요건이 있는 프로토콜입니다.

REST API는 경량화되어 있기 때문에 사물 인터넷, 모바일 애플리케이션 개발, 서버리스 컴퓨팅과 같이 보다 새로운 컨텍스트에 이상적입니다.

SOAP 웹 서비스는 많은 기업에서 필요로 하는 기본 보안과 트랜잭션 컴플라이언스를 제공하지만, 이로 인해 좀 더 무거운 경향이 있습니다.

<br/>

## 2. 유사점 SOAP, REST

애플리케이션을 빌드하려면 다양한 프로그래밍 언어, 아키텍처 및 플랫폼을 사용할 수 있습니다. 데이터 형식이 서로 다르기 때문에 이러한 다양한 기술간에 데이터를 공유하는 것은 어렵습니다. SOAP 와 REST는 모두 이 문제를 해결하기 위해 개발되었습니다.

<br/>

- 둘 다 애플리케이션 간 데이터를 요청을 작성, 처리 및 응답하는 방식에 대한 규칙과 표준을 설정함.
- 둘 다 표준화된 인터넷 프로토콜인 HTTP를 사용하여 정보를 교환함.
- 둘다 안전하고 암호화된 통신을 위해 SSL/TLS를 지원함.

<br/>

## 3. 언제 구분해서 사용하는가.

SOAP, REST 중 하나를 선택하기 전 시나리오와 API 사용자의 요구사항을 고려한다.

<br/>

### 전체 애플리케이션 설계

모바일 앱 및 하이브리드 애플리케이션과 같은 최신 애플리케이션은 REST API에서 더 잘 작동합니다. REST는 마이크로서비스 및 컨테이너와 같은 최신 아키텍처 패턴을 사용하여 애플리케이션을 설계할 수 있는 확장성과 유연성을 제공합니다. 그러나 이미 SOAP API가 있는 레거시 시스템을 통합하거나 확장해야 하는 경우 SOAP를 계속 사용하는 것이 더 나을 수 있다.

<br/>

### 보안

퍼블릭 API는 보안 요구 사항이 낮고 누구나 사용할 수 있도록 하는 높은 유연성을 요구합니다. 따라서 PUBLIC API를 빌드할 때는 REST가 더 나은 선택입니다. 반대로 기업 내부 요구사항을 위한 일부 PRIVATE API는 SOAP의 WS-Security를 통해 더 엄격한 보안 조치를 취하는 것이 도움이 될 수 있습니다.

<br/>

## 4. SOAP API와 REST API 작동 방식

SOAP는 시스템 간 엄격한 통신 계약이 필요한 오래된 기술입니다. 시간이 지남에 따라 기술 변화를 수용하기 위해 새로운 웹 서비스 표준이 추가되엇지만 이로 인해 추가 오버헤드가 발생합니다. REST는 SOAP 이후에 개발되었으며 본질적으로 많은 단점을 해결합니다.

<br/>

### SOAP API

SOAP는 엄격한 통신 규칙을 정의하는 프로토콜입니다.  
**표준**

- 웹 서비스 보안 WS-Security 은 고유 식별자로 토큰을 사용하는 것과 같은 보안 조치를 지정함
- 웹 서비스 주소 지정 WS-Addressing 에는 라우팅 정보를 메타데이터로 포함해야 함
- WS-ReliableMessaging 은 SOAP 메시징의 오류 처리를 표준화함
- 웹 서비스 기술 언어 WSDL는 SOAP 웹 서비스의 범위와 기능을 설명함

SOAP API로 요청을 보낼 때는 HTTP 요청을 SOAP 봉투에 포장해야합니다. 항상 SOAP 응답으로 XML 문서를 반환합니다.

<br/>

### REST API

REST API 작동 방식에는 6가지 조건을 부과하는 소프트웨어 아키텍처 스타일입니다.

1. 클라이언트-서버 아키텍처 발신자와 수신자는 기술, 플랫폼, 프로그래밍 언어 등과 관련하여 서로 독립적입니다.
2. 계층화. 서버에는 클라이언트 요청을 완료하기 위해 함께 작업하는 여러 중개자가 있을 수 있지만 클라이언트에게는 보이지 않습니다.
3. 균일한 인터페이스. API는 전체적으로 완전히 사용할 수 있는 표준 형식으로 데이터를 반환합니다.
4. 무상태. API는 이전 요청과 독립적으로 모든 새 요청을 완료합니다.
5. 캐싱 가능. 모든 API 응답은 캐싱할 수 있습니다.
6. 온디맨트 코드. 필요한 경우 API 응답에 코드 스니펫이 포함될 수 있습니다.

GET및 POST 와 같은 HTTP 동사를 사용하여 REST 요청을 보냅니다. REST API응답은 일반적으로 JSON 이지만 데이터 형식이 다를 수 있습니다.

<br/>

## 5. 주요 차이점

SOAP 는 프로토콜이고 REST는 아키텍처 스타일입니다.

<br/>

### 설계

SOAP API는 함수 또는 작업을 노출하는 반면 REST API는 데이터 기반입니다.

다른 애플리케이션에서 조작할 수 있는 직원 데이터가 포함된 애플리케이션을 예로 들어보겠습니다. 애플리케이션의 SOAP API는 CreateEmployee라는 함수를 노출할 수 있습니다. 직원을 만들려면 요청을 보낼 때 SOAP 메시지에 함수 이름을 지정해야 합니다.

그러나 REST API는 /employees 라는 URL을 노출할 수 있으며 해당 URL에 대한 POST 요청은 새 직원 레코드를 생성합니다.

<br/>

### 유연성

SOAP는 엄격하며 애플리케이션 간 XML 메시징만 허용합니다. 또한 애플리케이션 서버는 각 클라이언트의 상태를 유지해야 합니다. 즉, 새요청을 처리할 때 이전 요청을 모두 기억해야 합니다.

REST는 더 유연하며 애플리케이션에서 일반 텍스트, HTML, XML 등 JSON으로 데이터를 전송할 수 있습니다. 또한 REST 는 무상태이므로 모든 이전 요청과 독립적으로 처리합니다.

<br/>

### 성능

SOAP 메시지는 더 크고 복잡하기 때문에 전송 및 처리 속도가 느려집니다. 이로 인해 페이지 로드 시간이 늘어날 수 있습니다.

REST 는 메시지 크기가 작기 때문에 SOAP보다 빠르고 효율적입니다. REST 응답도 캐싱할 수 있으므로 서버는 자주 액세스하는 데이터를 캐시에 저장하여 페이지 로드 시간을 더욱 단축할 수 있습니다.

<br/>

### 확장성

SOAP 프로토콜을 사용하려면 애플리케이션이 요청 간에 상태를 저장해야 하므로 대역폭과 메모리 요구 사항이 증가합니다. 결과적으로 애플리케이션 비용이 많이 들고 확장하기가 어려워집니다.

SOAP와 달리 REST는 무상태 및 계층화된 아키텍처를 허용하므로 확장성이 뛰어납니다. 예를 들어 애플리케이션 서버는 요청을 다른 서버로 전달하거나 콘텐츠 전송 네트워크와 같은 중개자가 요청을 처리하도록 허용할 수 있습니다.

<br/>

### 보안

SOAP를 HTTPS와 함께 사용하려면 추가 WS-Security 계층이 필요합니다. 추가 헤더 콘텐츠를 사용하여 지정된 서버의 프로세스만 SOAP 메시지 컨텐츠를 읽도록 합니다. 이로 인해 통신 오버헤드가 추가되고 성능에 부정적인 영향을 미칩니다.

REST는 추가 오버헤드 없이 HTTPS를 지우너합니다.

<br/>

### 신뢰성

SOAP에는 오류 처리 로직이 내장되어 있으며 더 높은 신뢰성을 제공합니다. 반면 REST는 통신 장애 발생 시 다시 시도해야 하며 안정성이 떨어집니다.

<br/>

출처

- [AWS - SOAP, REST](https://aws.amazon.com/ko/compare/the-difference-between-soap-rest/)
- [text](https://www.redhat.com/ko/topics/integration/whats-the-difference-between-soap-rest)

---

# Spring Boot 에서 `@RestController`로 들어온 HTTP 요청이 처리되어 응답으로 변환되는 전체 과정을 설명하세요. 특히 HTTP 메시지 컨버터가 동작하는 시점과 역할을 포함해서 설명하세요

HTTP 요청 -> (내가 모르는 어떤 것) -> @RestController -> HTTP 메시지 컨버터 + (내가 모르는 어떤 것)

클라이언트의 요청이 어떤 과정을 거쳐서 애플리케이션에 들어와 처리된 후 클라이언트에게 응답이 가는지 알아보자.

## Web Server 와 Apache Server

클라이언트에서 미리 정해진 HTTP 규격에 맞게 요청을 보냅니다. 이것을 해석하고 클라이언트가 원하는 데이터를 반환해주어야 합니다. 여기서 HTTP를 해석하고, 그에 맞는 데이터 형식으로 보내주는 것이 Web Server 가 할 일입니다.

Apache HTTP Server는 1995년에 Apache 라는 비영리 재단에서 발표된 오픈 소스 Web Server입니다. 그 이외에도 최근에 많이 쓰이고 있는 Nginx 도 Web Server의 일종으로 볼 수 있습니다.

## Web Application Server

정적 데이터를 반환해주는 Web Server에서 더 많은 기능이 요구됩니다. 동적으로 데이터가 변하거나, 특정 데이터만 요청하거나 하는 등 더 많은 기능이 요구됩니다. 이를 해결하기 위해서 Web Application Server를 사용합니다.

![Web-Application-Server](https://github.com/user-attachments/assets/bb4d1067-932d-4c3c-82b1-c71164fad6e8)

WAS (Web APplication Server)는 일부 Web Server 기능과 Web Container로 함께 구성됩니다. 앞단의 Web Server는 HTTP 요청을 받아서 Web Container로 넘겨주게 됩니다. Web Container는 이를 내부 프로그램 로직의 처리에 따라서 데이터를 만들어서 Web Server로 다시 전달하게 됩니다.

Java 에서는 이를 Servlet을 통해서 처리하기 때문에 Servlet Container라고도 부릅니다.

스프링부트가 요청에 따라서 데이터를 처리 및 DB에 영향을 주는 것은 Apache Tomcat 가 내장이 되어있기 때문입니다.

Tomcat은 Servlet Containe 개념으로 개발이 되었으며 데이터의 동적인 처리를 할 수 있습니다. 이 때, 외부 요청을 처리하는 Web Server의 기능을 일부 내장하고 있기 때문에 WAS로 간주하기도 합니다.

![Image](https://github.com/user-attachments/assets/37f585aa-58a3-4ab4-b822-fc3b442be929)

## Apache Tomcat 은 Servlet Container

Spring Boot 에서는 Servlet 을 단위로하여 Client의 요청을 처리하게 됩니다. Servlet은 Java에서 Thread 기반으로 Client 요청에 대해서 동적으로 변하게 작동하는 구성요소입니다.

서블렛을 요청에 연결하고, 수명관리를 해주는 것이 바로 Servlet Container 입니다.

Apache Tomcat은 기본적으로는 Servlet Container이나 자체적으로 Web Server가 내장이 되어 있습니다. 이 때문에 HTTP 요청을 받을 수 있어서 Apache Tomcat은 WAS 기능을 일부 가지고 있는 Servlet Container 라고 볼 수 있습니다.

## Spring Boot 에서의 Apache Tomcat

Spring Boot 에서는 HTTP 요청을 받고 Servlet을 관리할 수 있기 때문에 기본적으로 Apache Tomcat을 내장하여 사용하고 있습니다. 먼저, Gradle 에서 의존성을 살펴보게 되면, Tomcat이 내장되어 있음을 확인할 수 있습니다.

![Image](https://github.com/user-attachments/assets/3b54c0b8-9d4e-4128-9826-17f7412e12d1)

## Web Server와 WAS 의 상호 관계

유연성이 더 좋은 WAS가 Web Server 보다 더 나은 것이라고 생각할 수 도 있습니다. 하지만 실제로는 Appache HTTP Server나 Nginx 와 같은 Web Server 를 WAS와 함께 쓰고 있습니다.

이유는 정적인 응답을 처리할 때 문제입니다. WAS 및 Web Server 둘 다 정적인 파일을 처리할 수 있습니다. 하지만 WAS 에서 응답을 처리할 때에는 외부 프로그램, Servlet Container 등의 존재 때문에 부하가 많이 걸리게 됩니다. 이 때문에 상대적으로 가벼운 Web Server가 정적 요청에는 유리합니다.

두 번째로는 Load Balancing 의 적용입니다. 하나의 WAS에 너무 많은 요청일 몰리게 되면, 데이터를 처리할 것이 많아져서 CPU에 부하가 오게 됩니다. 이를 여러 WAS로 구성을 하여, 요청을 분산시킬 수가 있습니다. 요청을 분산시키는 기능이 필요하게 되는데, 이를 Load Balancing 이라고 합니다.

[text](https://tecoble.techcourse.co.kr/post/2021-05-24-apache-tomcat/)

## Servlet

Servlet Container 를 알아보기 전에 서블릿에 대해서 알아보자.

일반적으로 웹 프로그래밍을 한다고 하면 정의된 클라이언트의 요청에 대해 상응하는 결과를 return 해 주어야 하는데 웹 페이지 혹은 결과값을 동적으로 생성해 주기 위한 역할을 하는 자바 프로그램을 서블릿이라고 합니다.

java EE 로 웹 서비스를 직접 구현할때에는 서블릿을 만들기 위해 Servlet 인터페이스의 구현체를 직접 만들어 사용했지만, 스프링 MVC 에서는 Dispatcher Servlet 이라는 모든 요청을 담당하는 서블릿을 두고 컨트롤러에 위임을 하여 요청을 처리합니다.

이와 같이 프론트 컨트롤러 디자인 패턴이 적용된 Spring MVC 를 통해 개발자는 별도의 서블릿 개발 없이, Controller의 구현만으로도 동적인 response를 클라이언트에게 줄 수 있다.

Spring MVC에서 제공해주는 DisplatcherServlet은 FrameworkServlet.java > HttpServlet.java > Servlet.java 를 상속받아 구현한 서블릿입니다.

클래스 내부에 여러 핸들러, 어댑터, 리졸버 등을 가지고 클라이언트의 요청에 따라 개발자가 정의해 둔 내용을 응답해 줄 수있도록 front-controller의 역할을 하고 있다.

![Image](https://github.com/user-attachments/assets/6423bd04-a4a5-4e7b-8e8c-678d17f3fee4)

스프링 부트는 기본적으로 내장 Tomcat을 실행하여 HTTP 요청을 처리한다.
클라이언트가 HTTP 요청을 보내면 실행된 Tomcat 서버로 요청을 받아서 서블릿 컨테이너에 존재하는 서블릿에게 요청을 처리하여 반환한다. 이 때 서블릿 컨테이너 안에는 스프링부트의 DispatcherServlet 이 존재하며 프론트 컨트롤러 역할을 한다.

[Servlet Container](https://mossgreen.github.io/Servlet-Containers-and-Spring-Framework/)

## 스프링 부트의 동작 구조

클라이언트의 요청이 들어오면 서블릿이 이를 처리해야 하는데, 서블릿은 서블릿 컨테이너에서 관리하고, 톰캣이 서블릿 컨테이너의 역할과 WAS 의 역할을 담당한다.

### Dispatcher Servlet 이란

클라이언트의 요청을 받아서 서블릿 컨테이너에서 관리되는 DispatcherServlet이 요청을 처리하고 반환한다.

![Image](https://github.com/user-attachments/assets/5e1c84f1-8476-4eed-8389-5c0bbcb21a9a)

1. 클라이언트의 요청 : 클라이언트에서 HttpServletRequest 이 들어오면, 서블릿 컨테이너는 DispatcherServlet으로 이를 전달한다.

2. Handler 조회: DispatcherServlet은 핸들러 매핑을 통해 요청 URL에 매핑된 핸들러(Controller를 탐색한다.)

3. HandlerAdapter 조회 :조회한 핸들러를 실행할 수 있는 핸들러 어댑터를 조회한다.

4. Handler Adapter 실행 : 핸들러 어댑터를 통해 핸들러를 호출한다.

5. Handler(Controller) 실행 : 핸들러를 실행하여 컨트롤러에서 요청을 처리하고, 응답을 다시 핸들러 어댑터로 반환

6. Model And View 반환

7. @Controller 사용 시

- View Resolver를 찾고 실행한다.
- View Resolver는 View 논리이름을 물리 이름으로 바꾸고, 랜더링 역할을 담당하는 View 객체를 반환한다.
- View 를 렌더링 하여 클라이언트에 반환

8. @RestController 사용 시

- View와 ViewResolver를 거치지 않는다.
- Controller로 부터 반환 받은 데이터를 MessageConverter를 거쳐서 Json 형식으로 변환한다.
- Json을 ResponseBody로 응답

- 모든 HTTP 요청은 Dispatcher Servlet을 통해 들어온다. Dispatcher Servlet은 요청을 핸들러에게 전달하기 전에 HandlerMapping, HandlerAdapter, Interceptior 를 거친다.

```java
public class DispatcherServlet extends FrameworkSerlvet {

  protected void doService(HttpServletRequest request, HttpServletResponse response) throws Exception {
    //...request에 속성을 설정

    try {
      this.doDispatch(request, response);   // 여기가 Dispatcher 가 처리하는 핵심 메서드
    } finally {

    }
  }

  protected void doDispatch(HttpServletRequest request, HttpServletResponse response) throws Exception {
    HttpServletRequest processRequest = request;
    HandlerExcutionChain mappedHandler = null;
    boolean multipartRequestParsed = false;
    WebAsyncManager asyncManager = WebAsyncUtils.getAsyncManager(request);

    try {
      try {
        ModelAndView mv = null; // HandlerAdaptor 실행 결과를 담을 객체
        Exception dispatchException = null;

        try {
          //.. multipart 검사
          mappedHandler = this.getHandler(processRequest); // 핸들러를 찾는다. iterator
          if (mappedHandler == null) {
            // 리턴
          }

          HandlerAdapter handlerAdaptor = this.getHandlerAdpater(mappedHandler.getHandler());
          // 사전에 지역변수로 선언해둔 ModelAndView 객체에 결과 담기
          mv = handlerAdaptor.handle(processRequest, response, mappedHandler.getHandler());

          //
        }
      }
    }
  }
}
```

## Handler Adaptor

```java
public interface HandlerAdpator {

  @Nullable
  ModelAndView handle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception;
}
```

HandlerAdpator는 인터페이스로 handle 메서드가 호출된다. 이를 구현한 추상 클래스인 `AbstractHandlerMethodAdaptor` 의 시그니처를 살펴본다.

```java
public abstract class AbstractHandlerMethodAdapter extends WebContentGenerator implements HandlerAdapter, Ordered {

  @Nullable
  public final ModelAndView handle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
    return this.handleInternal(request, response, (HandlerMethod) handler);
  }

  @Nullable
  protected abstract ModelAndView handleInternal(HttpServletRequest request, HttpServletResponse response, HandlerMethod handlerMethod) throws Exception;
}
```

내부적으로 `handleInternal` 추상 메서드를 호출한다. HandlerAdapter를 통해서 HandlerExcutionChanin을 처리하는데, 내부적으로 인터셉터를 가지고 있어 공통적인 전/후처리 과정이 처리된다.

대표적으로 컨트롤러 메서드 호출 전에는 적합한 파라미터를 만들어 넣어주어야 하며 (ArgumentResolver), 호출 후에는 메시지 컨버터를 통해 ResponseEntity의 Body를 찾아 Json 직렬화하는 등 (ReturnValueHandler)이 필요하다.

이러한 처리를 핸들러 어댑터가 요청을 처리하기 위한 handleInternal 추상 메서드이다.

처리를 위한 구현체인 `RequestMappingHandlerAdapter` 의 처리를 살펴보자.

```java
public class RequestMappingHandlerAdapter extends AbstractHandlerMethodAdaptor implements BeanFacoryAware, InitializingBean {

  //..
  protected ModelAndView handleInternal(HttpServletRequest request, HttpServletResponse response, HandlerMethod handlerMethod) throws Exception {
    this.checkRequest(request);
    ModelAndView mav;
    if (this.synchronizeOnSession) {
      //...
    }
    // 주요 메서드
    mav = this.invokeHandlerMethod(request, response, handlerMethod);
  }
}
```

request 가 지원하는 메서드인지 확인하고, `invokeHandlerMethod` 를 통해서 처리한다. 이 메서드를 살펴보면 컨트롤러의 파라미터를 처리하는 ArgumentResolver와 returnValueHandler를 검사하고 처리한다.

```java
@Nullable ModelAndView invokeHandlerMethod(HttpServletRequest request, HttpServletResponse response, HandlerMethod handlerMethod) throws Exception {
  // ...
  // 실행가능한 ServletInvocableHandelrMethod 로 만들어준다.
  ServletInvocableHandlerMethod invocableMethod = this.createInvocableHandlerMethod(handlerMethod);
  // 다양한 설정..
  // method Argument 처리와 인자값을 처리한다.
  invocableMethod.invokeAndHandle(webRequest, mavContainer, new Object[0]);


}
```

invokeAndHandle 메서드 안에서 invokeForRequest 에서 먼저 메소드 호출위해 필요한 인자값을 처리한다. @RequestHeader, @CookieValue 및 @PathValriable 등도 모두 스프링이 만들어둔 ArgumentResolver에 의해 처리가 되는데, 이러한 인자값을 만드는 작업이 getMethodArgumentValues 내에서 처리가 된다. 그리고 `doInvoke`에서 만들어진 인자값을 통해 컨트롤러의 메서드를 호출한다.

```java
public void invokeAndHandle(ServletWebRequest webRequest, ModelAndViewContinaer mavContainer, Object... providedArgs) throws Excption {

  Object returnValue = this.invokeForRequest(webRequest, mavContainer, providedArgs);
  //.. 컨트롤러에서 성공적으로 작업을 처리한 후에 ResponseEntity를 반환했따면 invokeAndHandle의 returnValue로 해당객체가 반환된다.

  try {
    this.returnValueHandlers.handleReturnValue(returnValue, this.getRetrunValueType(returnValue), mavContainer, webRequest);
  } catch (Exception ex) {
    ...
  }
}
```

컨트롤러의 응답에 대한 후처리를 하기 위해서 returnValueHandlers를 통해 처리된다. 응답을 처리하기 위한 인터페이스로 HandelrMethodReturnValueHandler 를 이용한다.

이때 응답으로 ResponseEntity 객체를 반환하는 경우에 컴포지트 객체가 갖는 HandlerMethodReturnValueHandler 구현체 중에서 HttpEntityMethodPRocessor가 사용된다.

HttpEntityMethodProcess 내부에서는 Response를 set해주고, 응답 가능한 MediaType인지 검사한 후에 적절한 `MessageConverter를` 선택해 응답을 처리하고 결과를 반환한다.
