# DTO 명명 규칙과 패키지 구조 설계

DTO(Data Transfer Object)는 계층 간의 데이터 전송을 위해 사용되는 객체입니다. DTO 명명 규칙은 코드의 가독성을 높이고, 유지보수를 용이하게 하기 위해 중요합니다.

명확한 명명 규칙을 따르면 팀원 간의 커뮤니케이션이 원활해지고, 코드의 일관성을 유지할 수 있습니다.

왜냐하면 명명 규칙이 없으면 각자 다른 방식으로 이름을 지어 코드의 가독성이 떨어지기 때문입니다.

이 글에서는 DTO 명명 규칙과 중요성과 실제 예제를 통해 명명 규칙을 어떻게 적용할 수 있는지 설명합니다.

## 명명 규칙의 예제

DTO 명명 규칙은 일반적으로 클래스 이름 뒤에 `Request`나 `Response`를 붙이는 방식으로 이루어 집니다. 예를 들어, 상품 조회 API의 경우 `PRoductRequest`와 `ProductResponse`로 명명할 수 있습니다.

```java
public class ProductRequest {
  private String productId;
  private String productName;
}

public class ProductResponse {
  private String productId;
  private String productName;
  private double price;
}
```

## 패키지 구조 설계의 중요성

패키지 구조 설계는 코드의 모듈화와 재사용성을 높이기 위해 중요합니다. 패키지를 잘 설계하면 코드의 가독성을 높이고, 유지보수를 용이하게 할 수 있습니다.

왜냐하면 패키지 구조가 잘못 설계되면 코드가 복잡해지고, 유지보수가 어려워지기 때문입니다.

- 레이어 기반 패키지 구조
- 피처 기반 패키지 구조

레이어 기반 패키지 구조는 계층별로 패키지를 나누는 방식이며, 피처 기반 패키지 구조는 기능별로 패키지를 나누는 방식입니다.

### 레이어 기반 패키지 구조

일반적으로 컨트롤러, 서비스, 리포지터리, DTO 등의 패키지로 나눕니다.

```
com.example.project
 ├── controller
 │   └── ProductController.java
 ├── service
 │   └── ProductService.java
 ├── repository
 │   └── ProductRepository.java
 └── dto
     ├── ProductRequest.java
     └── ProductResponse.java
```

### 피처 기반 패키지 구조

피처 기반 패키지 구조는 기능별로 패키지를 나누는 방식입니다. 일반적으로 각 기능별로 컨트롤러, 서비스, 리포지터리, DTO를 포함한 패키지를 만듭니다.

```
com.example.project
 ├── product
 │   ├── controller
 │   │   └── ProductController.java
 │   ├── service
 │   │   └── ProductService.java
 │   ├── repository
 │   │   └── ProductRepository.java
 │   └── dto
 │       ├── ProductRequest.java
 │       └── ProductResponse.java
 └── order
     ├── controller
     │   └── OrderController.java
     ├── service
     │   └── OrderService.java
     ├── repository
     │   └── OrderRepository.java
     └── dto
         ├── OrderRequest.java
         └── OrderResponse.java
```

## 결론

DTO 명명 규칙과 패키지 구조 설계는 코드의 가독성을 높이고, 유지보수를 용이하게 하기 위해 중요합니다. 명확한 명명 규칙을 따르면 팀원 간의 커뮤니케이션이 원활해지고, 코드의 일관성을 유지할 수 있습니다.

레이어 기반 패키지 구조와 피처 기반 패키지 구조를 이해하고 적용하면 코드의 모듈와와 재사용성을 높일 수 있습니다.

- (DTO 명명 규칙과 패키지 구조 설계계)[https://f-lab.kr/insight/dto-naming-conventions-and-package-structure-20240906]
