---
title: "[Mysql] Explain"
---

# Explain
Explain 구문은 Mysql이 명령문을 어떻게 실행하는지에 대한 정보를 보여주는데, 어떤 테이블을 어떻게 참고해서 몇개의 row를 가져올지 등등 Explain 해서 알 수 있는 정보들을 바탕으로 Mysql Opimizer는 최적의 방법으로 쿼리를 실행하게 된다.   
따라서, 쿼리의 성능 개선이 필요할 경우 Explain을 통해 개선이 필요한 부분을 쉽게 찾을 수 있다. SELECT, DELETE, INSERT, REPLACE, UPDATE 구문 모두 적용 가능하지만, 대게 SELECT 쿼리에서 JOIN, Sub Query 등으로 쿼리 실행에 오랜 시간이 걸릴 때 Explain을 사용하면 효과적으로 문제되는 부분을 파악할 수 있다.

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
