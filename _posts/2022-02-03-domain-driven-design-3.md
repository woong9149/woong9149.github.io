---
title: 도메인 주도 설계(Domain Driven Design) 정리-3
categories:
- TIL
- DDD
- 도메인주도설계
tags:
- TIL
- DDD
- 도메인주도설계
---

# Aggregate
Aggregate는 관련된 객체를 하나의 군으로 묶어주기 때문에 좀 더 상위 수준에서 도메인 모델 간의 관계를 파악할 수 있다. 주요 도메인 개념간의 관계를 파악하기 어렵다는 것은 곧 코드를 변경하고 확장하는 것이 어려워 진다는 것을 의미하기 때문에 상위 수준에서 도메인을 이해하는 것은 중요하다. 또한  Aggregate 단위로 일관성을 관리하기 때문에 Aggregate는 복잡한 도메인을 단순한 구조로 만들어준다. 복잡도가 낮아진다는 것은 개발 시간을 줄여주는 중요한 요소이다.   
Aggregate는 관련된 모델을 하나로 모은 것이기 때문에 한 Aggregate에 속한 객체는 유사하거나 동일한 라이프사이클을 갖는다. 도메인 규칙에 따라  최초 Aggregate 생성 시점에 일 부 객체를 만들 필요가 없는 경우도 있지만, Aggregate에 속한 구성요소는 대부분 함께 생성하고 함께 제거한다.   
Aggregate는 독립된 객체 군이기 때문에 한 Aggregate에 속한 객체는 다른 Aggregate에 속하지 않고, 각 Aggregate는 자기 자신을 관리할 뿐 다른 Aggregate를 관리하지 않는다. 대부분의 Aggregate는 한 개의 Entity 객체만 가지고 두 개 이상의 Entity로 구성되는 Aggregate는 드물다.

**Aggregate Root**   
Aggregate는 여러 객체로 구성되기 때문에 한 객체만 상태가 정상이어서는 안 된다. 도메인 규칙을 지키려면 Aggregate에 속한 모든 객체가 정상 상태를 가져야 한다.   
Aggregate에 속한 모든 객체가 일관된 상태를 유지하려면 Aggregate 전체를 관리할 주체가 필요한데 이 책임을 지는 것이 바로 Aggregate의 Root Entity다. Aggregate Root Entity는 Aggregate의 대표 Entity로 Aggregate에 속한 객체는 Aggregate Root Entity에 직접 또는 간접적으로 속한다.   
*(예를들어, 구매할 상품의 개수를 변경하면수량과 총 금액이 모두 변경되어야한다. 그렇지 않으면, '주문 총 금액은 개별 상품의 주문 개수 X 가격 의 합이다.' 라는 도메인 규칙을 어기고 데이터 일관성이 깨진다.)* 

예를들어,  주문 Aggregate 에서 루트 역할을 하는 엔티티는 Order이다. OrderLine, ShippingInfo, Orderer 등 주문 Aggregate에 속한 모델은 Order에 직접 또는 간접적으로 속한다.   

**도메인 규칙과 일관성**   
Aggregate Root의 핵심 역할은 Aggregate의 일관성이 깨지지 않도록 하는 것이다. 이를 위해 Aggregate Root는 Aggregate가 제공해야 할 도메인 기능을 구현한다.    
예를 들어, Order Aggregate는 배송지 변경, 상품 변경과 같은 기능을 제공하는데    
Aggregate Root인 Order가 이 기능을 구현한 메서드를 제공한다. Aggregate Root가 제공하는 메서드는 도메인 규칙에 따라 Aggregate에 속한 객체의 일관성이 깨지지 않도록 구현해야 한다.   
예를 들어, 배송이 시작되기 전까지만 배송지 정보를 변경할 수 있다는 규칙이 있다면,    
Aggregate Root인 Order의 changeShippingInfo() 메서드는 이 규칙에 따라 배송 시작 여부를 확인하고 변경이 가능한 경우에만 배송지 정보를 변경해야 한다.   Aggregate Root가 아닌 다른 객체가 Aggregate에 속한 객체를 직접 변경하면 안된다. 이는 Aggregate Root가 강제하는 규칙을 적용할 수 없어 모델의 일관성을 깨는 원인이 된다.    
불필요한 중복을 피하고 Aggregate Root를 통해서만 도메인 로직을 구현하게 만들려면 도메인 모델에 대해 다음의 두가지를 습관적으로 적용해야 한다.
* 단순히 필드를 변경하는 set 메서드를 public 으로 만들지 않는다.
* Value 타입은 불변으로 구현한다.   

public한 set 메서드는 중요 도메인의 의미나 의도를 표현하지 못하고 도메인 로직이 도메인 객체가 아닌 응용 영역이나 표현 영역으로 분산되게 만드는 원인이 된다. 도메인 로직이 한곳에 응집되어 있지 않게 되므로, 코드를 유지보수할 때에도 분석하고  수정하는 데 더 많은 시간을 들이게 된다.    
또한, Value 타입은 불변으로 구현한다. Value 객체의 값을 변경할 수 없으면 Aggregate Root에서 Value 객체를 구해도 값을 변경할 수 없기 때문에 Aggregate 외부에서 Value 객체의 상태를 변경할 수 없게 된다. Value 객체가 불변이면 Value 객체의 값을 변경하는 방법은 새로운 Value 객체를 할당하는 것 뿐이다. Value 타입의 내부 상태를 변경하려면 Aggregate Root를 통해서만 가능하다. 그러므로, Aggregate Root가 도메인 규칙을 올바르게만 구현하면 Aggregate 전체의 일관성을 올바르게 유지할 수 있다.

# Repository와 Aggregate
aggregate는 개념상 한 개의 도메인 모델을 표현하므로 객체의 영속성을 처리하는 repository는 aggregate 단위로 존재한다.  새로운 aggregate를 만들면 저장소에 aggregate를 영속화하고, aggregate를 사용하려면 저장소에서 aggregate를 읽어야 하므로 repository는 적어도 save(aggregate 저장), findById(id로 aggregate를 구함) 두 메서드를 제공해야 한다. 이 두 메서드 외에 필요에 따라 다양한 조건으로 aggregate를 검색하는 메서드나 aggregate를 삭제하는 메서드를 추가할 수 있다.   
어떤 기술을 이용해서 repository를 구현하느냐에 따라 aggregate의 구현도 영향을 받는다. 예를들어, ORM 기술 중의 하나인 JPA/Hibernate를 사용하면 데이터베이스 관계형 모델에 객체 도메인 모델을 맞춰야 하는 경우도 있다. 하지만 aggregate를 영속화 할 저장소로 무엇을 사용하든지 간에 aggregate의 상태가 변경되면 모든 변경을 원자적으로 저장소에 반영해야 한다.    
aggregate에서 두 개의 객체를 변경했는데, 저장소에는 한 객체에 대한 변경만 반영되면 데이터 일관성이 깨지므로 문제가 된다.
aggregate는 개념적으로 하나이므로 repository는 aggregate 전체를 저장소에 영속화해야 한다. 예를 들어, Order aggregate와 관련된 테이블이 세 개라면 repository를 통해서 Order aggregate를 저장할 때 aggregate root와 매핑되는 테이블 뿐만 아니라 aggregate에 속한 모든 구성요소를 위한 테이블에 데이터를 저장해야 한다.
동일하게 aggregate를 구하는 repository 메서드는 완전한 aggregate를 제공해야 한다. repository가 완전한 aggregate를 제공하지 않으면, 필드나 값이 올바르지 않아 aggregate의 기능을 실행하는 도중에 NullPointException과 같은 문제가 발생하게 된다.
