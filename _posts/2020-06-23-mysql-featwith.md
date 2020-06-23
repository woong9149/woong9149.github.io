---
title: "[MySQL] 임시테이블 생성 -1. WITH절"
layout: single
author_profile: true
read_time: true
comments: true
share: true
related: true
categories:
- mysql
- database
- 임시 테이블
- with
- recursive
tags:
- mysql
- database
- 임시 테이블
- with
- recursive
---

# CTE(Common Table Expression) -공통 테이블 표현식
CTE는 간단하게 말하자면, 한 쿼리문 내에서 여러번 참조될 수 있도록 만들어진 임시 테이블이라고 볼 수 있다.    
with 절을 단지 임시테이블을 만들어 쓰기 위한 것으로만 알고 접근했었는데, with절 내에도 recursive의 사용여부에 따라 재귀와 비재귀로 나뉜다. 

## 문법

```python
WITH[RECURSIVE]
  cte1 AS (SELECT a, b FROM table1)
SELECT b, d FROM cte1 ;
```

WITH절에 포함된 서브쿼리 (SELECT a, b FROM table1)의 결과집합에 이름(cte1)을 붙여 메모리에 저장하고, WITH 절 이후에 오는 외부 쿼리에서 테이블 처럼 사용할 수 있다.   

## WITH RECURSIVE ~
재귀 호출을 통해 서브쿼리 내에서 임시테이블(cte1)을 사용하려면 **RECURSIVE**를 사용해야 한다. 가상의 테이블을 구성하면서 그 자신(가상의 테이블)을 참조하여 값을 결정할 때 유용하다.    

**WITH RECURSIVE ~ 규칙**   
* RECURSIVE를 사용할 때는 서브쿼리내에서 **UNION [ALL]** 이 사용되어야 한다.   
* 한 개 이상의**Non-Recursive** 문장이 포함되어 있어야 한다.   
* 반복되는 Recursive문은 반드시 **정지조건**(아래 예문에서 where이하)이 필요하다.   

```python
WITH RECURSIVE test AS
(
#non-recursive 문(첫번째 루프에서만 실행됨)
SELECT 1 AS num FROM emp #첫 루프에서 emp 테이블의 행 수 만큼 자체 반복됨.
UNION ALL
#recursive 문(읽어 올 때마다 행의 위치기 기억되어 다음번 읽어 올 때는 다음 행으로 이동함)
SELECT num+1 AS num FROM test WHERE num<10
)
SELECT * FROM test;
```

## 효과
* 복잡한 쿼리문에서 **코드의가독성**을 위해 사용된다.   
* 동일한 SQL이 반복되어 사용될 때 **성능을 높이기 위해** 사용된다.(장시간 걸리는 쿼리의 결과를 임시테이블에 저장해놓고 저장해놓은 데이터를 엑세스 하기때문에 성능이 향상됨)   
* 하지만, 동시에 여러개의 with절이 실행되어 임시 테이블이 견딜 수 있는 수준이 넘어가면 모두 느려질 수도 있다.(MySql8과 PostgreSQL에서는 CTE를 **Materializing**해서, 테이블이 cache처럼 임시로 저장되기때문)
* **VIEW**와 비교해서 더 간단하게 생성해서 사용할 수 있다.   

<br/>
+추가로 with절 이외에 종료시점을 정할 수 있는 임시테이블을 만드는 방법도 있다.(https://superkong1.tistory.com/35),   
+참조:https://unrealengine.tistory.com/163