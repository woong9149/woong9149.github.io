---
title: "[JavaScript] Array APIs 정리"
layout: single
author_profile: true
read_time: true
comments: true
share: true
related: true
---

# JavaScript Array APIs
## 1. join(separator? : string): string;
배열의 모든 요소를 연결해 하나의 문자열을 만든다.   
```python
const fruits = ['apple', 'banana', 'orange'];
const result = fruits.join(',');
console.log(result); //apple,banana,orange
```

## 2. split(separator: string | RegExp, limit?: number): string[ ]
String 객체를 지정한 구분자를 이용하여 여러 개의 문자열로 나누어, 배열에 담아서 반환한다

```python
const fruits = "apple,banana,orange";
const result = fruits.split(',');
console.log(result);//['apple', 'banana', 'orange'];
```

## 3. reverse( ): T[ ];
배열의 순서를 반전한다.  첫 번째 요소는 마지막 요소가 되며, 마지막 요소는 첫 번째 요소가 된다.

```python
const array = [1, 2, 3, 4, 5];
const result = array.reverse();
console.log(result); // 5, 4, 3, 2, 1
console.log(array); //5, 4, 3, 2, 1
```

rever()를 사용하면 반환된 배열과 원본 배열 모두 순서가 반전된다.