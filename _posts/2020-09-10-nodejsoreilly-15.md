---
title: "[Nodejs]O'REILLY 정리 - 15"
layout: single
author_profile: true
read_time: true
comments: true
share: true
related: true
---

# 제어흐름, 비동기 패턴, 예외 처리
새로운 서버 기능 전체가 아니더라도, 비동기 이벤트와 콜백, EventEmitter와 같이 생소한 개체들에 대해 논의하다 보면 Node가 두려워질 수도 있다. 하지만 요즘 나온 자바스크립트 라이브러리들을 사용해본 적이 있다면 Node에 들어간 기능의 상당 부분, 최소한 비동기 개발에 대해서는 경험을 해본 것이다.   

예를 들어, 자바스크립트에서 **타이머**를 사용해본 적이 있거나 **Ajax**로 개발해본 적이 있다면 비동기 함수를 사용해본 것이다. 심지어 상당히 오래된 **onclick** 이벤트 핸들러 역시 사용자가 언제 마우스를 클릭할지, 키보드를 누를지 알 수 없으므로 비동기 함수라 할 수 있다.   

**어떤 이벤트나 결과를 기다리는 동안 제어 쓰레드를 차단하지 않는 메서드는 비동기 함수**다. onclick 처리의 경우 애플리케이션에서는 사용자의 마우스 클릭을 기다리는 동안 다른 처리를 차단하지 않는다. 마찬가지로, 타이머가 작동하는 동안이나 Ajax 호출에서 서버가 반환하기를 기다리는 동안에도 다른 기능들을 전혀 차단하지 않는다.   

## Promise 사용, Promise 대신 Callback 사용
초기의 Node에서는 비동기 기능이 1970년대에 등장한 개념인 Promise를 사용하여 만들어졌다. **Promise는 어떤 비동기 결과를 상징하는 개체**로 future, delay, deferred라고 부르기도 한다. CommonJS 디자인 모델에서는 promise 개념을 포괄하고 있다.   

초기 Node 구현에서 promise는 **success**와 **error**라는 두 개의 이벤트만을 발생시키는 개체였다. 사용법은 간단한데, **비동기 동작이 성공하면 success 이벤트**가 발생되고, **실패한 경우에는 error 이벤**트가 발생된다. 해당 이벤트 이외의 다른 이벤트들은 발생되지 않으며, 개체는 success나 error 중 하나(모두는 안됨)를 한 번만 발생시킨다.

```python
var fs = require('fs');

function test_and_load(filename){
    var promise = new process.Promise();
    fs.stat(filename).addCallback(function(stat){

        //파일이 아닌 것은 걸러냄
        if(!stat.isFile()){
            promise.emitSuccess();
            return;
        }

        //파일인 경우 읽어 들임
        fs.readFile(filename).addCallback(function(data){
            promise.emitSuccess(data);
        }).addErrback(function(error){
            promise.emitError(error);
        });

    }).addErrback(function(error){
        promise.emitError(error);
    });
    return promise;
}
```
**각 개체는 promise 개체를 반환한다**. 성공적인 결과를 처리하는 코드는 promise 개체의 **addCallback** 메서드에 함수로 전달되며 data라는 매개변수 하나를 가진다.    
에러를 처리하는 코드는 promise 개체의 **addErrback** 메서드에 함수로 전달되며, error라는 유일한 매개변수를 받는다 :   
```python
var file = require('file');
var promise = File.read('mydata.txt');
propmise.addCallback(function(data){
	//데이터 처리 로직
	
});
promise.addErrback(function(err){
	//오류 처리 로직
})
```
promise 개체는 이벤트가 종료될 때 적절한 기능(결과를 조작할 수 있거나 에러가 처리되는 것중 하나)이 수행되었다는 것을 보증한다.