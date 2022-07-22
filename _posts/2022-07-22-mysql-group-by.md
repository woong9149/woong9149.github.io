---
title: "[Mysql] GROUP BY"
---

최근 쿼리작업을 하면서 최신순으로 ORDER BY를 한 후 GROUP BY를 해야할 경우가 있는데,
ORDER BY를 한 후 GROUP BY를 할 때는 고려해야할 사항들이 있다.

```python
SELECT *
FROM TABLE
ORDER BY update_datetime GROUP BY username;
```
은 쿼리 에러가 발생한다.   

ORDER BY와 GROUP BY는 같은 계층에서 사용할 수 없기 때문에, Sub Query에서 먼저 Order by를 해줘야 한다.   

```python
SELECT *
FROM (SELECT *
      FROM TABLE
      ORDER BY update_datetime
      LIMIT 18446744073709551615)
GROUP BY username;;
```
