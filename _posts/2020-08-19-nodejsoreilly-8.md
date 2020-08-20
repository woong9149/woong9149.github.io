---
title: "[Nodejs]O'REILLY 정리 - 8"
layout: single
author_profile: true
read_time: true
comments: true
share: true
related: true
categories:
- nodejs
- o'reilly
- TIL
tags:
- nodejs
- o'reilly
- TIL
---

# 타이머: setTimeout, clearTimeout, setInterval, clearInterval
클라이언트 JavaScript에서 타이머 함수는 전역 window 객체에 속해있다. 타이머 함수는 JavaScript에 속한 것이 아니지만, JavaScript 개발에서 매우 자주 사용되는 항목이기에 Node 개발자들이 Node 코어 API에 포함시켰다.   
Node에서 동작하는 타이머 함수는 브라우저에서 동작하는 것과 유사하다. 실제로 Node의 타이머 함수 동작은 Chrome에서의 동작과 동일한데, Node가 Chrome의 V8 JavaSript 엔진을 기반으로 하기 때문이다.   

Node의 **setTimeout** 함수는 첫 번째 매개변수로**콜백 함수**, 두 번째 매개변수로 **지연 시간**(밀리초 단위) 그리고 **선택적인 인수**들을 받는다 :   
```python
//파일을 열고 내용을 읽어 HTTP 응답 개체로 보내기 위한 타이머
function on_OpenAndReadFile(filename, res){
    console.log('opening ' + filename);
    //파일을 열고 내용을 읽는다
    fs.readFile(filename, 'utf8', function(err,data){
        if(err){
            res.write('Could not find or open file for reading \n');
        }else{
            res.write(data);
        }
        //응답 완료
        res.end();
    })
	}

    setTimeout(on_OpenAndReadFile, 2000, filename, res);

```
이 코드에서는 약 2,000 밀리초가 지난 후 콜백 함수인 onOpenAndReadFile이 호출되어 파일을 열고 읽어서 HTTP 응답으로 보낸다.

> Node 문서에서 기술하였듯이, 콜백 함수가 정확하게 n 밀리초에 호출된다는 보장은 없다. 이는 브라우저에서 setTimeout을 사용하는 것과 차이가 없다. 환경에 대한 절대적인 제어권을 가지고 있지 않으므로, 여러가지 요인들로 타이머가 약간 지연될 수 있다.

**clearTimeout** 함수는 이전에 설정된 setTimeout을 지운다. 반복적인 타이머가 필요하다면, **setInterval**을 사용해서 매 n 밀리초 마다 함수를 호출한다(n 값은 이 함수의 두 번째 매개변수임). setInterval은 **clearInterval**로 지운다.

# Nodejs 공식문서의 "시간 연속체 제어하기"
### "지정한 시기에" 실행 ~ `setTimeout()`

 `setTimeout()`은 지정한 밀리초 이후 코드 실행을 스케줄링하는 데 사용할 수 있다. 이 함수는 브라우저 JavaScript API의 `window.setTimeout()`과 비슷하지만, 코드의 문자열을 실행하려고 전달할 수 없다.   
 `setTimeout()`은 첫 인자로 실행할 함수를 받고 두 번째 인자로 지연시킬 밀리 초를 숫자로 받는다. 부가적인 인자를 더 전달할 수도 있는데 이는 함수로 전달될 것이다.
 
 예제
```python
 function myFunc(arg) {
	 console.log(`arg was => ${arg`});
 }
 
 setTimeout(myFunc, 1500, 'funky');
```
myFunc()는 setTimeout() 호출로 인해 약 1500밀리 초(1.5초) 정도에 실행될 것이다.    
설정한 타임아웃 간격은 정확한 밀리 초 후 실행되도록 할 수 없다. 이벤트 루프를 블로킹하거나 보유하고 있는 다른 실행 코드가 타임아웃의 실행을 다시 뒤로 밀기 때문이다. 단, **선언된 타임아웃 간격보다 더 빨리 실행되지는 않는다는 것만 보장**한다.   

 `setTimeout()`은 설정한 타임아웃을 참조하는 **Timeout 객체를 반환**한다. 여기서 반환된 객체로 실행 동작을 변경할 수도 있고, 타임아웃을 취소할 수도 있다.
 
### "바로 다음에" 실행 ~ `setImmediate()`
`setImmediate()`는 현재 이벤트 루프 주기 끝에 코드를 실행한다. 이 코드는 현재 이벤트 루프의 모든 I/O 작업 후 다음 이벤트 루프에 스케줄링 된 모든 타이머 이전에 실행된다. 이 코드 실행은 `setImmediate()` 함수 호출 뒤에 오는 모든 모드는 `setImmediate()` 함수 인자 이전에 실행된다는 의미로 "바로 다음에" 실행한다고 생각할 수 있다.
`setImmediate()`의 첫 인자는 실행할 함수, 두 번째 인자는 첫 번째 인자인 함수가 실행될 때 인자로 전달된다.   
예제
```python
console.log('before immediate');

setImmediate((arg) => {
	console.log(`executing immediate: ${arg}`);
}, 'so immediate');

console.log('after immediate');
```
 `setImmediate()`에 전달한 위 함수는 실행할 수 있는 코드를 모두 실행한 후에 실행하고, 콘솔 출력은 다음과 같다.
```
before immediate
after immediate
executing immediate: so immediate
```
setImmediate() 는 바로 스케줄링 된 것을 취소할 수 있는**Immediate 객체**를 반환한다.
   
**Note**:  `setImmediate()` 를 `process.nextTick()` 과 혼동하면 안된다. 첫 째로 `process.nextTick()` 은 모든 스케줄링된 I/O 이전뿐만 아니라 설정한 모든 Immediate 이전에 실행 된다는 점 둘 째로 `process.nextTick()`은 취소할 수 없으므로 일단 `process.nextTick()`으로 코드를 실행하도록 스케줄링하면 일반 함수처럼 실행을 멈출 수 없다는 점이 그 차이 점이다.


# "무한루프" 실행 ~ `setInterval()`
여러 번 실행해야 하는 코드 블록이 있다면 `setInterval()` 을 사용할 수 있다. `setInterval()`은 두 번째 인자로 지정한 밀리 초 단위의 지연시간으로 무한대로 실행할 함수를 인자로 받는다. `setTimeout()` 처럼 지연시간 다음에 부가적인 인자를 지정할 수 있고 이는 함수 호출에 전달될 것이다. 또한 `setTimeout()` 처럼 작업이 이벤트 루프에서 진행 중일 수 있으므로 지연시간이 보장되지 않는다. 그러므로 대략적인 지연시간으로 생각해야한다.   

예제
```python
function intervalFunc(){
	console.log('Cant stop me now !');
}

setInterval(intervalFunc,1500);
```
예제에서 `intervalFunc()` 는 중단하기 전까지는 15000밀리 초(1.5초)마다 실행 될 것이다.   
`setTimeout()` 처럼 `setInterval()`도 설정한 인터벌을 참조하고 수정하는 데 사용할 수 있는 Timeout 객체를 반환한다.


# 취소하기
Timeout 이나 Immediate 객체를 취소하고 싶다면 어떻게 해야할까? `setTimeout()`, `setImmediate()`, `setInterval()` 은 설정한 Timeout 이나 Immediate 객체를 참조하는 타이머 객체를 반환한다. 각각의 clear 함수에 이 객체들을 전달해서 해당 객체의 실행을 완전히 중단할 수 있다. 각각의 함수는 `clearTimeout()`, `clearImmediate()`, `clearInterval() `이다.

예제
```python
const timeoutObj = setTimeout(() => {
	console.log('timeout beyond time');
},1500);

const immediateObj = setImmediate(() => {
	console.log('immediately executing immediate');
})

const intervalObj = setInterval( () => {
	console.log('interviewing the interval');
}, 500);

clearTimeout(timeoutObj);
clearImmediate(immediateObj);
clearInterval(intervalObj);
```

# 타임아웃 감춰두기
여기서 `Timeout` 객체는 `setTimeout`과 `setInterval `이 반환했다는 점을 기억해야 한다.  `Timeout`  객체는  `Timeout` 의 동작을 강화하는 두 가지 함수 `unref()`와 `ref()`를 제공한다. `set` 함수로 스케줄링 된 `Timeout` 객체가 있다면 이 객체에서 `unref()`를 호출할 수 있다. 이는 동작을 다소 변경하는데, **실행할 코드가 이것밖에 남지 않았다면 `Timeout` 객체를 호출하지 않는다.** `Timeout` 객체는 프로세스를 유지하지 않고 실행을 기다린다.   

비슷하게 `unref()` 가 호출된 Timeout 객체에서 ref()를 호출하면 `unref()`의 동작을 제거해서 Timeout 객체의 실행을 보장할 수 있다. 하지만 선능 문제로 초기 동작을 완전히 똑같이 복구하는 것은 아니다.   

예제
```python
const timerObj = setTimeout( () => {
	console.log('will i run?');
});

//이 부분만 있다면 이 타임아웃이 프로그램을 종료되지 않게 하고 있으므로, 위 타임아웃이 실행되지 않게 한다.
timerObj.unref();

//immediate 안에서 ref()를 실행해서 다시 실행 상태로 만들 수 있다.
setImmediate(() => {
	timerObj.ref();
});
```