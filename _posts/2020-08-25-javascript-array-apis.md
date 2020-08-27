---
title: "[JavaScript] Array APIs 정리"
layout: single
author_profile: true
read_time: true
comments: true
share: true
related: true
categories:
- javascript
- array
tags:
- javascript
- array
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

reverse() 를 사용하면 반환된 배열과 원본 배열 모두 순서가 반전된다.


## 4. slice(start?: number, end?: number): T[ ];
어떤 배열의 start 부터 end 까지(end 미포함)에 대한 얕은 복사본을 새로운 배열 객체로 반환한다. 원본 배열은 바뀌지 않는다.
```python
const array = [1, 2, 3, 4, 5];
const result = array.slice(2,5)
console.log(result); //[3, 4, 5]
console.log(array); //[1, 2, 3, 4, 5];
```

**splice와 의 차이**
`splice(start: number, deleteCount?: number): number[ ]`   
splice( )는 원본 배열의 요소를 삭제 또는 교체하거나 새 요소를 추가하여 배열의 내용을 변경하는 것이다.   


## 5. find(predicate( value:T, index: number, obj: T[ ]) => unknown, thisArg?: any): T | undefined;
또는   
`find<S extends T>(predicate: (this: void, value: T, index: number, obj: T[ ]) => value is S, thisArg?: any): S | undefined; `    
주어진 판별 함수를 만족하는 첫 번째 요소의 값을 반환한다. 그런 요소가 없으면 undefined를 반환한다.

```python
const Student = {
    constructor(name, age, enrolled, score){
        this.name = name;
        this.age = age;
        this.enrolled = enrolled;
        this.score = score;
    }
}

const students = [
    new Student('A', 29, true, 45),
    new Student('B', 28, false, 80),
    new Student('C', 30, true, 90),
    new Student('D', 40, true, 66),
    new Student('E', 18, true, 88),
];

//점수가 90점인 학생을 출력
const result = students.find((student) => student.score === 90);
console.log(result); // Student {name: "C", age: 30. enrolled: true, score: 90 }
```


## 6. filter(callbackfn: (value: T, index: number, array: T[ ]) => unknown, thisArg?: any): T[ ];
또는   
`filter<S extends T>(callbackfn: (value: T, index: number, array: T[]) => value is S, thisArg?: any): S[];`   
 주어진 함수의 테스트를 통과하는 모든 요소를 모아 새로운 배열로 반환한다.   
 
 ```python
const Student = {
    constructor(name, age, enrolled, score){
        this.name = name;
        this.age = age;
        this.enrolled = enrolled;
        this.score = score;
    }
}

const students = [
    new Student('A', 29, true, 45),
    new Student('B', 28, false, 80),
    new Student('C', 30, true, 90),
    new Student('D', 40, true, 66),
    new Student('E', 18, true, 88),
];

//enrolled가 true인 학생을 출력
const result = students.filter((student) => student.enrolled);
console.log(result);
```


## 7. map\<U>(callbackfn: (value: T, index: number, array: T[ ]) => U, thisArg?: any): U[ ];
배열 내의 모든 요소 각각에 대하여 주어진 함수를 호출한 결과를 모아 새로운 배열을 반환 한다.   

```python
const Student = {
    constructor(name, age, enrolled, score){
        this.name = name;
        this.age = age;
        this.enrolled = enrolled;
        this.score = score;
    }
}

const students = [
    new Student('A', 29, true, 45),
    new Student('B', 28, false, 80),
    new Student('C', 30, true, 90),
    new Student('D', 40, true, 66),
    new Student('E', 18, true, 88),
];

//모든 학생의 score를 출력
const result = students.map((student) => student.score);
console.log(result);
```


## 8. some(callbackfn: (value: T, index: number, array: T[ ]) => unknown, thisArg?: any): boolean;
배열 안의 어떤 요소라도 주어진 판별함수를 통과하는지를 테스트 한다. boolean 값을 반환한다.
```python
const Student = {
    constructor(name, age, enrolled, score){
        this.name = name;
        this.age = age;
        this.enrolled = enrolled;
        this.score = score;
    }
}

const students = [
    new Student('A', 29, true, 45),
    new Student('B', 28, false, 80),
    new Student('C', 30, true, 90),
    new Student('D', 40, true, 66),
    new Student('E', 18, true, 88),
];

//some은 하나라도 만족하면 true 반환
const result = students.some((student) => student.score < 50);
console.log(result);

// every는 모두 충족해야 true 반환
const result2 = students.every((student) => student.score >= 50);
console.log(result2);
```


# 9. reduce(callbackfn: (previousValue: T, currentValue: T, currentIndex: number, array: T[ ]) => T, initialValue?: T): T;
배열의 각 요소에 대해 주어진 리듀서(reducer) 함수를 실행하고, 하나의 결과값을 반환한다.   => 원하는 시작점 부터 모든 요소를 반복하면서 어떤 값을 누적할 때 사용할 수 있다. 

```python
const Student = {
    constructor(name, age, enrolled, score){
        this.name = name;
        this.age = age;
        this.enrolled = enrolled;
        this.score = score;
    }
}

const students = [
    new Student('A', 29, true, 45),
    new Student('B', 28, false, 80),
    new Student('C', 30, true, 90),
    new Student('D', 40, true, 66),
    new Student('E', 18, true, 88),
];

// 평균 score 출력
const result = students.reduce((prev, curr) => prev + curr.score, 0);
console.log(result / students.length);
```
`.reduceRight()` 는 마지막 요소부터 누적한다.      


## 10. sort(compareFn?: (a: T, b: T) => number): this;
배열의 요소를 적절한 위치에 정렬한 후 그 배열을 반환한다. 정렬은 stable sort가 아닐 수 있다. 기본 정렬 순서는 문자열의 유니코드 코드 포인트를 따른다.
```python
const Student = {
    constructor(name, age, enrolled, score){
        this.name = name;
        this.age = age;
        this.enrolled = enrolled;
        this.score = score;
    }
}

const students = [
    new Student('A', 29, true, 45),
    new Student('B', 28, false, 80),
    new Student('C', 30, true, 90),
    new Student('D', 40, true, 66),
    new Student('E', 18, true, 88),
];

//작은 것 부터 정렬
const result = students
    .sort((a,b) => b - a)
console.log(result);
```


## 11. api를 여러개 묶어서 사용
여러 api를 묶어서 사용할 수 있다.
```python
const result = students
    .map((student) => student.score)
    .filter((score) => score >= 50)
    .join();
console.log(result);
```
함수형 프로그래밍 이라고도 한다.