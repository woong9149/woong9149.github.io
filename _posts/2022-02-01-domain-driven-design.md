---
title: 도메인 주도 설계(Domain Driven Design) 정리-1
categories:
- TIL
- 도메인주도설계
- DDD
tags:
- TIL
- 도메인주도설계
- DDD
---

# 아키텍쳐 구성

**Presentaion Layer** - 사용자의 요청을 처리하고 사용자에게 정보를 보여준다. 여기서 사용자는 소프트웨어를 사용하는 사람뿐만 아니라 외부 시스템도 사용자가 될 수 있다.  
**Application Layer** - 사용자가 요청한 기능을 실행한다. 업무 로직을 직접 구현하지 않으며 도메인 계층을 조합해서 기능을 실행한다.  
**Domain Layer** - 시스템이 제공할 도메인의 규칙을 구현한다.   
**Infrastructure Layer** - 데이터베이스나 메시징 시스템과 같은 외부 시스템과의 연동을 처리한다.   

# Entity와 Value
도출한 모델은 크게 엔티티(Entity)와 벨류(Value)로 구분할 수 있다.  

**Entity** - Entity의 가장 큰 특징은 식별자를 갖는 것이다. 식별자는 Entity 별로 고유해서 각 Entity는 서로 다른 식별자를 갖는다.  
*예) 주문 도메인에서 각 주문은 주문 번호를 갖는데 이 주문번호는 각 주문마다 서로 다르다. 따라서 주문번호가 주문의 식별자가 된다. 주문 도메인 모델에서 주문에 해당하는 클래스가 Order라면, Order가 엔티티가 되며, 주문번호를 속성으로 갖게된다.*   

**Entity의 식별자 생성**   
흔히 식별자는   
* 특정 규칙에 따라 생성
* UUID 사용
* 값을 직접 입력
* 일련번호 사용(시퀀스나 DB의 자동 증가 컬럼 사용)   

**Value Type** - Value Type은 개념적으로 완전한 하나를 표현할 때 사용한다.   
*예) Order 도메인에서 price와 amounts는 int 타입의 숫자를 사용할 수 있지만, 이들이 의미하는 값은 '돈'이다. 따라서 '돈'을 의미하는 Money 타입을 만들어 사용하면 코드를 이해하는 데 도움이 된다.*   
또 다른 장점은 Value Type을 위한 기능을 추가할 수 있다는 것이다.   
Value 객체의 데이터를 변경할 때는 기존 데이터를 변경하기보다는 변경한 데이터를 갖는 새로운 Value 객체를 생성하는 방식이 좋다. 이처럼 데이터 변경 기능을 제공하지 않는 타입을 불변(Immutable)으로 구현하는 가장 중요한 이유는 보다 안전한 코드를 작성할 수 있기 때문이다.(참조 투명성과 관련)   

**도메인 모델에 set 메서드 넣지 않기**   
set 메서드는 도메인의 핵심 개념이나 의도를 코드에서 사라지게 한다. 그리고 도메인 객체를 생성할 때 완전한 상태가 아닐 수도 있게 만든다.   
도메인 객체가 불완전한 상태로 사용되는 것을 막으려면 생성 시점에 생성자를 통해 필요한 데이터를 모두 받아야 한다. 생성자로 필요한 것을 모두 받기 때문에, 생성자를 호출하는 시점에 필요한 데이터가 올바른지 검사도 할 수 있다.   
set 메서드를 구현해야 할 특별한 이유가 없다면 Value Type은 불변으로 구현하는 것이 장점을 살릴 수 있는 방법이다.
