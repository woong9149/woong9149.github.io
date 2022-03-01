---
title: 도메인 주도 설계(Domain Driven Design) 정리-2
---

아키텍처를 설계할 때 사용되는 전형적인 영역이 'Presentaion', 'Application', 'Domain', 'InfraStructure' 의 네 영역이다.    
**Presentaion Layer** 는  사용자의 요청을 받아 Application Layer에 전달하고 그 처리 결과를 다시 사용자에게 보여주는 역할을 한다. HTTP 요청을 Application Layer가 필요로 하는 형식으로 변환해서 전달하고 그 응답을 다시 HTTP 응답으로 변환해서 전송한다.( 예를들면, 웹 브라우저가 HTTP request parameter로 전송한 데이터를 Application Layer가 요구하는 형식의 객체 타입으로 변환해서 전달하고, 그 리턴 결과를 JSON 형식으로 변환해서 HTTP 응답으로 웹 브라우저에 전송하는 식.)   
**Application Layer**은 Presentaion Layer를 통해 전달 받은 사용자의 요청을 바탕으로 시스템이 사용자에게 제공해야 할 기능을 구현한다. (예를들어, '주문 등록', '주문 취소', '상품 상세 조회' 와 같은 기능을 구현한다.) 기능 구현을 위해 Domain Layer의 Domain Model을 사용한다. Application Layer는 로직을 직접 수행하기보다는 Domain Model에 로직 수행을 위임한다.   
**Domain Layer**는 Domain Model을 구현한다. Domain Model은 Domain의 핵심 로직을 구현한다. 주문 도메인의 경우 '배송지 변경', '결제 완료', '주문 총액 계산' 과 같은 핵심 로직을 도메인 모델에서 구현한다.   
**InfraStructure Layer**는 구현 기술에 대한 것을 다룬다. 이 영역은 DB 연동을 처리하고, 메시징 큐에 메시지를 전송하거나 수신하는 기능을 구현한다. SMTP를 이용한 메일 발송 기능을 구현하거나 HTTP 클라이언트를 이용해서 REST API를 호출하는 것도 구현한다. 즉, 논리적은 개념을 표현하기보다는 실제 구현을 다루는 영역이다. Domain Layer, Application Layer, Presentation Layer는 구현 기술을 사용한 코드를 직접 만들지 않는 대신, InfraStructure Layer에서 제공하는 기능을 사용해 필요한 기능을 개발한다.   
# 계층 구조 아키텍처
네 영역을 구성할 때 많이 사용하는 아키텍처가 **Presentaion -> Application -> Domain -> InfraStructure** 구조이다. Presentaion Layer와 Application Layer는 Domaion Layer 를 사용하고, Domain Layer는 InfraStructure Layer를 사용하므로 계층 구조를 적용하기에 적당하다. 도메인의 복잡도에 따라 Application Layer와 Domain Layer를 분리하기도 하고 한 계층으로 합치기도 한다.   
계층 구조는 그 특성상 상위 계층에서 하위 계층으로의 의존만 존재하고 하위 계층은 상위 계층에 의존하지 않는다.( -> 방향이 의존하는 방향) 계층 구조를 엄격하게 적용하면 상위 계층은 바로 아래의 계층에만 의존을 가져야 하지만 구현의 편리함을 위해 계층 구조를 유연하게 적용한다. (예를들어, Application Layer는 바로 아래 계층인 Domain Layer에 의존하지만 외부 시스템과의 연동을 위해 더 아래 계층인 InfraStructure Layer에 의존하기도 한다.   
Presentaion Layer, Application Layer, Domain Layer가 상세한 구현 기술을 다루는 InfraStructure Layer에 종속되기 때문에 생기는 두 가지 문제점이 있다.
* 테스트의 어려움
* 기능 확장의 어려움
이를 보완하기 위해 DIP의 적용이 필요하다.   

# DIP
고수준 모듈이란 의미 있는 단일 기능을 제공하는 모듈이고, 저수준 모듈은  하위 기능을 실제로 구현한 것을 말한다. 예를 들어, '가격 할인 계산'을 해야한다면 그 기능을 가지고 있는 객체는 고수준 모듈이고, 고객 정보를 구하고 할인 금액을 계산해서 구하는 등 그 기능을 실제로 구현한 것이 저수준 모듈이다.   
고수준 모듈이 제대로 동작하려면 저수준 모듈을 사용해야 하는데, 고수준 모듈이 저수준 모듈을 사용하면 계층 구조의 두 가지 문제점이 발생한다. DIP는 추상화한 Interface를 통해 저수준 모듈이 고수준 모듈에 의존하도록 바꿈으로써 계층 구조의 문제점을 해결한다.
