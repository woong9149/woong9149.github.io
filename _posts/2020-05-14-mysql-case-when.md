---
title: "[MySQL] case when"
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
CASE   
		WHEN condition1 THEN result1   
		WHEN condition2 THEN result2   
		WHEN conditionN THEN resultN   
		ELSE result   
END;
```

> 주어진 조건이 condintion과 일치하면 해당 절의 result값을 반환한다.   

> 주어진 condition들 중 일치하는 값이 없으면 ELSE 절의 result값을 반환한다.   

> 주어진 조건에 충족하는 condition이 없고, ELSE 절이 없으면  NULL을 반환한다.   

> conditiond은 필수로 어야하고, 나열된 순서로 취급된다(출력순서도 같음).   

> result역시 필수로 있어야하고 주어진 조건이 일치하는 condition을 만나면 실행을 멈추고 그에 해당하는 result값을 반환한다(condition과 result는 각각 하나씩만 쌍을 이룬다).

> WHEN~THEN절은 다수가 존재할 수 있다.