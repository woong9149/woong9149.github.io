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
*(예를들어, 구매할 상품의 개수를 변경하면수량과 총 금액이 모두 변경되어야한다. 그렇지 않으면, '주문 총 금액은 개별 상품의 주문 개수 X 가격 의 합이다s.' 라는 도메인 규칙을 어기고 데이터 일관성이 깨진다.) *
