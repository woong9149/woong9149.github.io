---
title: "[Mysql] Explain"
categories:
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
**select_type** - SELECT 유형   
**table** - 출력 행에 대한 테이블   
**partitions** - 일치하는 파티션   
**type** - 조인 유형   
**possible_keys** - 선택할 수 있는 인덱스   
**key** - 실제로 선택한 인덱스   
**key_len** - 선택한 키의 길이   
**ref** - 인덱스와 비교한 열   
**rows** - 검사할 행의 추정치   
**filtered** - 테이블 조건으로 필터링된 행의 비율   
**Extra** -  추가 정보
