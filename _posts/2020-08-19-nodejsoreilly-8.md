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