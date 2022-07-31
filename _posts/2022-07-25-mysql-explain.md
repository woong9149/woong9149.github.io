---
title: "[Mysql] Explain"
categories:
- TIL
- MySQL
- Explain
tags:
- TIL
- MySQL
- Explain
---

# Explain
Explain 구문은 Mysql이 명령문을 어떻게 실행하는지에 대한 정보를 보여주는데, 어떤 테이블을 어떻게 참고해서 몇개의 row를 가져올지 등등 Explain 해서 알 수 있는 정보들을 바탕으로 Mysql Opimizer는 최적의 방법으로 쿼리를 실행하게 된다.   
따라서, 쿼리의 성능 개선이 필요할 경우 Explain을 통해 개선이 필요한 부분을 쉽게 찾을 수 있다. **SELECT, DELETE, INSERT, REPLACE, UPDATE** 구문 모두 적용 가능하지만, 대게 SELECT 쿼리에서 JOIN, Sub Query 등으로 쿼리 실행에 오랜 시간이 걸릴 때 Explain을 사용하면 효과적으로 문제되는 부분을 파악할 수 있다.

**Explain 사용법**   
실행하고자 하는 쿼리의 제일 앞에 Explain 키워드를 쓴다.

```python
EXPLAIN SELECT
		tbl1.column1,
		tbl1.column2,
		tbl2.column3
	FROM
		TABLE tbl1
		INNER JOIN TABLE2 tbl2 ON tlb2.foreigner, tbl1.serial
```

JSON format으로 출력해서 보고싶을 때는,    
```python
EXPLAIN format = json
SELECT * FROM table;
```
을 사용할 수 있다.

**Explain 결과**   
Explain을 실행했을 때 얻을 수 있는 결과 값들은 id,	select_type,	table,	partitions,	type,	possible_keys,	key,	key_len,	ref,	rows,	filtered,	Extra 등이 있다.

**id** - 실행 계획에 있는 쿼리 안의 각 SELECT 문에 대한 식별자이다. SELECT 문의 실행 순서를 의미하며, 단순히 JOIN만 실행할 때는 항상 1이지만 Sub Query나 UNION이 있을 때는 다른 식별자로 구분되어 표시된다.   

**select_type** - SELECT문의 타입을 나타낸다.
- SIMPLE : 단순 SELECT문을 의미한다. UNION 또는 Sub Query를 사용하지 않은 경우를 의미하는 타입이다.
- PRIMARY: 가장 바깥쪽의 SELECT문을 의미한다.
- DERIVED: FROM 절 안에 있는 Sub Query를 의미하는 타입이다.
- SUBQUERY: SELECT문 하위의 첫번째 Sub Query 즉, 가장 바깥에 있는 Sub Query문을 의미하는 타입이다.
- DEPENDENT SUBQUERY
- UNCACHEABLE SUBQUERY
- UNION: UNION문에서 두번째 SELECT문 을 의미하는 타입이다. (A UNION B 에서 B)
- DEPENDENT UNION

**table** - 실행할 쿼리에서 참조하는 테이블을 나타낸다.   

**partitions** - 테이블이 partitioning 되어 있을 경우 일치하는 테이블을 나타낸다. partitioning 되어 있지 않았을 경우에는 NULL로 표시된다.   

**type** - 실행 계획에 있는 쿼리의 테이블들을 MYSQL이 어떤 식으로 조인되어 있는지를 나타낸다. 쿼리 튜닝을 하는데 있어서 중요한 지표가 되며 아래 유형들에서 system이 가장 좋은 유형, all로 갈 수록 가장 나쁜 유형이다.   
- system: 테이블의 row가 1개 밖에 없는 경우(=시스템 테이블)의 특별한 접근 방식   
- const: 쿼리 실행시 테이블에 조건을 만족하는 row가 최대 1개인 경우로, 결과 row가 하나만 있기 때문에 결과 컬럼은 상수 취급된다.
- eq_ref: primary key나 unique not null column으로 생성된 인덱스를 사용해 조인을 하는 경우로, 결과 row별로 조인되는 row가 하나만 있기 때문에 매우 빠르다.(ststem, const 유형 다음으로 빠름)   
- ref: eq_ref 유형과는 다르게 인덱스로 사용된 키가 primary key나 unique not null column가 아닌 경우이다. 그렇기때문에 접근가능한 row가 여러개일 수 있다.   
- fulltext: fulltext 인덱스를 사용하여 JOIN된 유형이다.   
- ref_or_null: ref와 JOIN 유형이 비슷하지만, MySQL이 NULL을 포함하는 row에 대한 추가 검색을 수행한다는 점이 차이인 유형이다.   
- index_merge
- unique_subquery
- index_subquery
- range
- index
- all: JOIN시 테이블의 모든 row를 스캔하는 유형이다. **모든 row를 스캔하기 때문에 가장 나쁜 유형**이며, 테이블의 상수 값 또는 컬럼 값을 기반으로 테이블에서 row 검색을 가능하게 하는 **인덱스를 추가하여 방지**할 수 있다.     

**possible_keys** - 선택할 수 있는 인덱스   
**key** - 실제로 선택한 인덱스   
**key_len** - 선택한 키의 길이   
**ref** - 인덱스와 비교한 열   
**rows** - 검사할 행의 추정치   
**filtered** - 테이블 조건으로 필터링된 행의 비율   
**Extra** -  추가 정보
