---
title: "[MySQL] case when절"
layout: single
author_profile: true
read_time: true
comments: true
share: true
related: true
categories:
- MySQL
- CASE
- TIL
tags:
- MySQL
- CASE
- TIL
---

MySQL의 CASE문은 프로그래밍 언어의 if~else 문, switch과 유사하다.    

# 문법
```python
CASE (컬럼명)  
	WHEN condition1 THEN result1   
	WHEN condition2 THEN result2   
	WHEN conditionN THEN resultN   
	ELSE result   
END;
```

> 1. CASE와 WHEN 사이에 특정 컬럼명이 들어갈 수 있으며,  condintion과 일치하면 해당 절의 result값을 반환한다.   
> 2. 특정 컬럼이 condition들 중 일치하는 값이 없으면 ELSE 절의 result값을 반환한다.   
> 3. 특정 컬럼과 일치하는 condition이 없고, ELSE 절이 없으면  NULL을 반환한다.   
> 4. condition은 필수로 있어야하고, 나열된 순서로 취급된다(출력순서가 같음).   
> 5. result역시 필수로 있어야하고 특정컬럼과 일치하는 condition을 만나면 실행을 멈추고, 그에 해당하는 result값을 반환한다(condition과 result는 각각 하나씩만 쌍을 이룬다).   
> 6. CASE와 WHEN 사이에 특정 컬럼명 없이 boolean값이나 boolean을 판별하는 구문을 넣어서 true이면 result 값을 반하고, false면 다음 절로 넘어간다   
>7. CASE 이하  WHEN ~ THEN절은 다수가 존재할 수 있다.

**예제)**
```python
SELECT CustomerName, City, Country FROM Customers
ORDER BY (CASE
WHEN City IS NULL THEN Country
ELSE City
END);
```
   
SELECT절 뿐만 아니라 WHERE절이나 ORDER BY 절에도 쓰일 수 있다.   

**출력)**   
<img width="755" alt="스크린샷 2020-05-15 오전 12 09 29" src="https://user-images.githubusercontent.com/42554237/81951708-77311480-9640-11ea-8717-ed670d9ac4ed.png">

 
**적용 예제)**   
author테이블   
<img width="332" alt="스크린샷 2020-05-15 오전 12 11 57" src="https://user-images.githubusercontent.com/42554237/81952191-2241ce00-9641-11ea-85bc-b3a24d1340dd.png">

```python
SELECT name,
	(CASE  profile WHEN 'CEO' THEN '사장'
   			ELSE '직원'
	   END) AS role
 FROM author;
```

**출력)**   
<img width="169" alt="스크린샷 2020-05-15 오전 12 14 47" src="https://user-images.githubusercontent.com/42554237/81952182-1f46dd80-9641-11ea-806d-084c82e3518a.png">