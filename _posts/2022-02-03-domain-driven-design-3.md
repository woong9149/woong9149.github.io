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
