---
title: "[Mysql] GROUP BY"
categories:
- TIL
- MYSQL
- DB
- 데이터베이스
- QUERY
tags:
- TIL
- MYSQL
- DB
- 데이터베이스
- QUERY
---

최근 쿼리작업을 하면서 최신순으로 ORDER BY를 한 후 GROUP BY를 해야할 경우가 있었는데,
ORDER BY를 한 후 GROUP BY를 할 때는 고려해야할 사항들이 있다.

```python
SELECT
	column1,
	column2,
	column3
FROM 
	TABLE
ORDER BY 
	update_datetime 
GROUP BY 
	column1;
```
위 쿼리는 쿼리 에러가 발생한다.   

ORDER BY와 GROUP BY는 같은 계층에서 사용할 수 없기 때문에, Sub Query에서 먼저 Order by를 해줘야 한다.   

```python
SELECT 
	*
FROM (
	SELECT
		column1,
		column2,
		column3
	FROM 
		TABLE
	ORDER BY 
		update_datetime
	LIMIT 100) sub
GROUP BY
sub.column1;
```

Sub Query에서 Order by를 해줄 때는  `LIMIT`을 추가해줘야 원하는 정렬상태의 데이터를 얻을 수 있다.   
	SQL standard에 따르면 DB Table내에서 데이터의 순서는 아무 의미가 없는 것이기 때문에    
	Order by가 적용되지 않고, LIMIT 을 함께 사용해야 데이터의 본질적인 구조와 정렬을 바꿀 수 있다는 것 이다.   
	Sub Query의 결과가 메인 쿼리에서 DB Table로서 사용되기때문에 똑같이 적용 되는 것 같다. LIMIT의 최대는 `64bit unsigned -1 (2^64-1)`, 즉  `LIMIT 18446744073709551615`  까지    가능하기 때문에, 실제 서비스단에서 사용할 때는 `LIMIT 18446744073709551615`을 사용하는게 원하는 결과를 얻기 위한 방법일 것 같다.
	
<br/>
**정리**   
> 1. Order by 후 Group by를 해야될 때는 Order by가 필요한 부분을 Sub Query로 둔다.
> 2. Sub Query에서 Limit 구문까지 적용한다.
> 3. Order by한 상위 계층에서 Group by를 한다.

<br/>
**참고)**   
- https://jaenjoy.tistory.com/39
- https://mariadb.com/kb/en/why-is-order-by-in-a-from-subquery-ignored/
