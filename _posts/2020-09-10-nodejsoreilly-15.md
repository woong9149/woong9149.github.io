---
title: "[Nodejs]O'REILLY 정리 - 15"
layout: single
author_profile: true
read_time: true
comments: true
share: true
related: true
categories:
- o'reilly
- nodejs
- TIL
tags:
- o'reilly
- nodejs
- TIL
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

promise 개체는 Node 버전 0.1.30에서 제거되었다. **Ryan Dahl**이 작성한 제거 사유는 다음과 같다:   
> 많은 사람들(나 자신을 비롯한)은 개체를 생성할 필요가 없는 파일 시스템 동작에 대한 저수준 인터페이스만을 원하는 반면, promise와 유사하지만 어떻게든 다른 것을 원하는 사람들도 많은 것 같다. 따라서 promise 대신 최종 인수 콜백을 사용하도록 하고, 보다 나은 추상화 계층을 구축하는 것은 사용자 라이브러리에 맡기도록 하겠다.   

**모든 비동기 메서드는 마지막 인수로 콜백 함수를 사용**하는 특징을 가지고 있다. 이 **콜백 함수의 첫번째 인수는 항상 error 개체**다.

**예제 5-2** 최종 콜백 기능의 기반 구조
```python
var obj = function(){};

obj.prototype.doSomething = function(arg1, arg2_){
    var arg2 = typeof(arg2_) ==='string' ? arg2_ : null;

    var callback_ = arguments[arguments.length - 1];
    callback = (typeof(callback_) == 'function' ? callback_ : null);

    if(!arg2)
        return callback(new Error('second argument missing or not a string'));

        callback(arg1);
}

var test = new obj();

try {
    test.doSomething('test',3.55,function(err,value){
        if(err) throw err;

        console.log(value);
    })
} catch (err) {
    console.error(err);
}
```

첫 번째 핵심 기능은 **마지막 인수가 콜백 함수임을 보증**하는 것이다. 사용자의 의도를 결정할 수는 없지만, 마지막 인수가 함수인지는 확인할 수 있으므로 이를 수행해야 한다. 두 번째 핵심기능은 오류가 발생한 경우 새로운 Node Error 개체를 생성하고 콜백 함수의 결과로 반환하는 것이다. 마지막 핵심 기능은 오류가 발생하지 않은 경우 메서드의 결과를 전달하여 콜백 함수를 호출하는 것이다. 요컨대, 다음 세가지 핵심 기능만 제공된다면 나머지 다른 것들은 변경하는 것이 가능하다 :   
* 마지막 인수가 함수임을 보증한다.   
* 오류가 발생한 경우 Node Error 를 생성하여 반환한다.   
* 오류가발생하지 않으면 메서드의 결과를 전달하여 콜백 함수를 호출한다.   

예제 5-1의 코드 실행 시, 애플리케이션은 콘솔에 다음과 같은 에러 메시지를 출력한다 :    
`[Error: second argument missing or not a string]`   
코드에서 메서드 호출을 다음과 같이 변경하면,   
`test.doSomething('test','this',function(err,value){`   
결과로 콘솔에 test가 출력된다.   

Node 설치 위치의 lib 디렉터리에 있는 코드를 살펴보면 최종 콜백 패턴이 여기저기에서 반복되어 나타나는 것을 볼 수 있다. 기능이 변경될 수는 있겠지만, 이 패턴은 동일하게 유지된다.   
이 접근방법은 매우 간단하며 비동기 메서드로부터의 일관된 결과를 보장한다.   

## 순차 기능, 중첩 콜백, 예외 처리
다음과 같은 코드는 클라이언트 자바스크립트 애플리케이션에서 별로 특이한 것은 아니다 :   
```python
val1 = callFunctionA();
val2 = callFunctionB(val1);
val3 = callFunctionC(val2);
```
이 함수들은 이전 함수의 결과를 바로 뒤에 있는 함수로 전달하면서 순서대로 호출된다. 모든 함수가 동기 방식이므로, 함수 호출이 순서를 벗어나서 예기치 않은 결과를 가져오는 것을 걱정 할 필요가 없다.

**예제 5-3** 순차적 동기 애플리케이션   
```python
var fs = require('fs');

try{
	var data = fs.readFileSync('./apples.txt','utf8');
	console.log(data);
	var adjData = data.replace(/[A|a]pple/g,'orange');
	
	fs.writeFileSync('./oranges.txt',adjData);
}catch(err){
console.error(err);
}
```
예제 5-3은 이러한 순차 프로그래밍에서 상대적으로 흔히 볼 수 있는 케이스를 보여준다. 애플리케이션에서는 파일을 열어서 데이터를 가져온 후 "apple"을 모두 "orange"로 바꿔 데이터를 수정하기 위해 Node 파일 시스템메서드의 동기 버전(Sync)을 사용하고 있다.   

문제가 발생했을 때 모듈 함수에서 내부적으로 오류를 처리한다고 확신할 수 없으므로, 깔끔하게(최소한 유익하게) 예외 처리를 할 수 있도록 모든 함수 호출을 try 블록으로 감쌌다 :   
```
{[Error : ENOENT, no such file or directory './apples.txt']
	errno : 34,
	code : ' ENOENT',
	path: './apples.txt',
	syscall:'open'}
```
별로 사용자에게 친절하지는 않지만, 최소한
```
node.js:201
	throw e ; // process.nextTick error, or 'error' event on first tick
```
으로 시작하는 에러를 띄우는 거보다는 훨씬 낫다.   

이러한 동기 순차 애플리케이션 패턴을 비동기 구현으로 변환하려면 몇 가지 수정이 필요하다. 먼저, 모든 함수를 대응되는 비동기 버전으로 바꿔야 한다. 하지만 각 함수들이 호출될 때 차단이 발생하지 않는다는 사실 역시도 고려해야 하는데, 이는 함수들이 서로 독립적으로 호출되는 경우에는 적합한 순서를 보장할 수 없다는 것을 의미한다. 각 함수가 적합한 순서대로 호출되도록 보장할 수 있는 유일한 방법은 **중첩 콜백(nested callback)** 을 사용하는 것 뿐이다.   
**예제 5-4** 예제 5-3의 애플리케이션을 비동기 중첩 콜백으로 변환   
```python
var fs = require('fs');

try {
    fs.readFile('./apples2.txt','utf8',function(err,data){
        
        if (err) throw err;

        var adjData = data.replace(/[A|a]pple/g,'orange');

        fs.writeFile('.oranges.txt',adjData,function(err){
            if (err) throw err;
        });
    });
} catch (err) {
    console.error(err);
}
```
예제 5-4는 예제 5-3 애플리케이션의 비동기 버전이다. 모든 파일 시스템 함수 호출은 비동기 버전으로 교체되었으며, 중첩 콜백을 통해 함수들이 적합한 순서대로 호출된다.   

예제 5-4에서 입력 파일을 열어서 읽는 동작이 끝나는 순간 마지막 매개변수로 전달된 콜백 함수가 호출된다. 이 콜백 함수에서는 오류가 null이 아닌지를 확인하고, null인 경우에는 바깥쪽에 있는 예외 처리 블록에서 catch하도록 예외를 throw 한다.   
오류가 발생되지 않았으면 데이터가 처리되고 비동기 **writeFile 메서드**가 호출된다. 이 메서드의 콜백 함수는 매개변수를 하나만 가지는데, 바로 **error** 개체다. 이 개체가 null이 아니면 바깥쪽 예외 블록에서 처리되도록 throw한다.   
만약 오류가 발생되면 다음과 유사하게 나타날 것이다:   
```
/home/examples/public_html/node/read2/js:11
	if (err) throw err;
Error: ENOENT, no such file or directory './boogabooga/oranges.txt'
```
에러 스택 추적을 원할 경우 Node error 개체의 stack 속성을 출력하면 된다 :   
```python
catch(err) {
	consol;e.log(err.stack);
}
```

순차 함수 호출을 하나 더 포함시키면 콜백 중첩 수준이 하나 더 추가된다.   

**예제5-5** 디렉터리 내의 변경할 파일 목록을 가져오기   
```python
var fs = require('fs');

var writeStream = fs.createWriteStream('./log.txt',
                                        {'flag' : 'a',
                                        'encoding' : 'utf8',
                                        'mode':0666
                                        });
try {
    //파일 목록 가져옴
    fs.readdir('./data/', function(err,files){
        //각 파일에 대해
        files.forEach(function(name){
            //내용을 수정
            fs.readFile('./data/' + name, 'utf8', function(err,data){
                if (err) throw err;
                var adjData = data.replace(/somecompany\.com/g,'burningbird.net');

                //파일에 기록
                fs.writeFile('./data/' + name, addData, function(err){
                    if(err) throw err;

                    //로그 기록
                    writeStream.write('changed '+ name + '\n', 'utf8', function(err){
                        if (err) throw err;
                    })
                })
            })
        })
    })
} catch (err) {
    console.error(utill.inspect(err));
}
```
예제 5-5 에서는 디렉터리의 파일 목록에 접근한다. 각 파일에서는 문자열 **replace** 메서드를 사용하여 일반화해놓은 도메인명을 특정 도메인명으로 바꾸고, 결과는 원래 파일에 다시 쓰여진다. 쓰기 스트림을 사용해서 변경된 각 파일의 로그가 유지된다.   
애플리케이션이 다음으로 넘어가기 전에 각 파일을 하나씩 처리하고 있는 것  처럼 보이지만, 비동기 메서드가 사용되어 여러번 실행 후 log.txt 파일을 확인해보면 파일들이 매번 다르게 마치 무작위 순서처럼 처리 되었다는 것을 알 수 있다.   

만약 모든 파일들이 수정되었는지를 확인하고 싶다면 또 다른 문제가 발생한다. forEach 메서드는 반복자(iterator) 콜백 함수를 비동기로 호출하므로 차단이 일어나지 않는다. forEach를 사용한 뒤 `console.log('all done');` 를 추가 하더라도 애플리케이션이 모두 완료되었다는 것을 의미 하지는 않는다.   
이 문제를 해결하려면, 각 로그 메세지에 증가되는 카운터를 추가한 다음 파일 배열의 길이와 비교하여 "all done" 메시지를 출력한다 : 
```python
//디렉터리에 접근하기 전
 var counter = 0;
 ...
 
	 writeStream.write('changed ' + name + '\n', 'utf8', function(err){
		 if (err) throw err;
		 console.log('finished ' + name);
		 counter++;
		 if (counter >= files.length)
			 console.log('all done');
	 })
```
모든 파일이 업데이트되고 난 후에 "all done" 메시지가 표시되는 것을 알 수 있다.   

만약 애플리케이션이 하위 디렉터리를 만나게 되었을 때 에러가 발생하는 것을 방지 하기 위해서는 **fs.stats** 메서드를 사용하여야 한다. fs.stats 메서드가 반환하는 개체에는 파일인지 아닌지를 비롯한 개체에 대한 정보가 담겨있다. 물론 fs.stats 메서드 역시 비동기 메서드이므로, 콜백 중첩이 더 필요하다.

**예제 5-6** 각 디렉터리 개체가 파일인지 확인하기 위해 stats를 추가
```python
var fs = require('fs');

var writeStream = fs.createWriteStream('./log.txt',
{'flags' : 'a',
'encoding' : 'utf8',
'mode' : 0666});

try {
    //파일 목록을 가져옴
    fs.readdir('./data/', function(err,files){
        //각 파일에 대해
        files.forEach(function(name){
            //개체가 파일인지 확인
            fs.stat('./data/' + name, function(err, stats){
                if (err) throw err;

                if (stats.isFile())
                //내용 수정
                fs.readFile('./data/' + name, 'utf8', function(err,data){
                    if (err) throw err;
                    var adjData = data.replace(/somecompany\.com/g, 'burningbird.net');

                    //파일에 기록
                    fs.writeFile('./data/' + name, adjData, function(err){
                        if (err) throw err;
                        //로그 기록
                        writeStream.write('changed ' + name + '\n', 'utf8', function(err){
                            if (err) throw err;
                        })
                    })
                })
            })
        })
    })
} catch (err) {
    console.log(err);
}
```
예제 5-6에서는 원하는 목적의 애플리케이션이 구현은 되었지만, 가독성과 유지보수가 어려워졌다. 이러한 중첩 콜백의 형태는 **콜백 스파게티**나 **죽음의 피라미드**라고 불린다.   중첩 콜백이 계속 오른쪽으로 밀려가기 때문에 올바른 콜백에 코드를 제대로 작성하고 있는지를 보장하기가 점점 어렵게 된다. 일련의 메서드 호출을 중첩 콜백에 의존하지 않고 구현하기 위해, 비동기 제어 흐름을 제공하는 서드파이 모듈을 찾아볼 필요가 있다.   

각 메서드에 대해 명명된 함수를 콜백 함수로 제공하는 방법으로 피라미드를 평평하게 만들 수 있고, 디버깅도 간단해지지만, 이 방법은 모든 프로세스가 완료된 시점을 결정하는 것과 같은 다른 문제들 중 일부를 해결하지 못하기 때문에, 서드 파티 라이브러리가 필요하다.

## 비동기 패턴 및 제어 흐름 모듈
예제 5-6의 애플리케이션은 비동기 패턴의 예로, 각 함수들이 순서대로 호출되어 결과를 다음 함수로 전달하며 에러가 발생하는 경우에만 전체 체인이 중단된다. 비동기 패턴은 여러가지가 존재하는데, 어떤 패턴들은 다른 패턴들을 변형한 형태이지만 사용하는 용어는 완전히 동일하지는 않다.   
**Async**라는 Node 모듈은 비동기 제어 흐름 패턴들에 대해 가장 광범위한 지원을 제공하고 있다.

**waterfall**   
함수가 순서대로 호출되고, 모든 함수의 결과는 마지막 콜백에 배열로 전달된다(다른 곳에서는 series나 sequence로 불리기도 한다).   

**series**   
함수들이 순서대로 호출되며, 선택적으로 결과가 마지막 콜백에 배열로 전달된다.   

**parallel**   
함수들이 병렬로 실행되며, 실행이 완료되면 결과들이 마지막 콜백으로 전달된다(병렬 패턴에 대한 해석 중 일부에서는 결과 배열을 패턴 내에 포함하지 않기도 한다).   

**whilst**   
한 함수를 반복적으로 호출하되, 사전 준비 테스트가 false를 반환 하거나 오류가 발생하는 경우에만 마지막 콜백이 호출된다.   

**queue**   
지정된 동시 제한 수까지 함수를 병렬로 호출하고, 함수 중 하나가 완료되면 새로운 함수가 큐에 들어간다.   

**until**  
한 함수를 반복적으로 호출하되, 후처리 테스트가 false를 반환하거나 에러가 발생하는 경우에만 마지막 콜백이 호출된다.   

**auto**   
함수가 요구사항을 기반으로 호출되며, 각 함수는 이전 콜백의 결과를 받는다.   

**iterator**   
각 함수가 다음 함수를 호출하며, 각각 다음에 있는 반복자에 접근할 수 있다.   

**apply**   
이전에 적용된 인수를 가지고 다른 제어 흐름 함수와 결합되는 연속 함수다.   

**nextTick**   
Node의 process.nextTick 을 기반으로 이벤트 루프의 다음번 루프에서 콜백을 호출한다.   

제어 흐름 모듈들 전부가 가능한 모든 패턴들을 처리할 수 있는 기능을 제공하고 있는 건 아니지만, 가장 흔히 사용되는 패턴인 **series**(sequene나 waterfall 이라고 부르기도 함. Async 에서는 waterfall을 series와 구분하고 있음)와 **parallel**은 대부분의 모듈에서 제공한다.   

유지보수가 활발한 제어 흐름 모듈 중에서 가장 인기있는 것은 Step 과 Async 이다. 서로 비동기 제어 흐름에 대한 자신만의 고유한 관점을 나타내고 있으며, 둘 다 매우 유용하고 거의 필수적이라고 할 수 있는 서비스를 제공하고 있다.   

**Step**   
Step은 순차 및 병렬 실행의 흐름을 간단하게 제어할 수 있게 해주는 데 초점을 맞춘 유틸리티 모듈로, 다음과 같이 npm을 사용하여 설치할 수 있다 :   
`npm install step`   
Step 모듈은 단 한개의 개체만을 제공한다. 이 개체를 사용하여 순차적으로 실행하려면 비동기 함수 호출을 함수로 감싸서 개체에 매개변수로 전달한다. 

**예제 5-7** Step 을 사용하여 순차적으로 비동기 작업들을 수행   
```python
var fs = require('fs'),
  step = require('step');

  try {
    step(
      function readData(){
        fs.readFile('./data/data1.txt', 'utf8', this);
      },
      function modify(err,text){
        if (err) throw err;
        return text.replace(/somecompany\.com/g, 'burningbird.net');
      },
      function writeData(err, text){
        if (err) throw err;
        fs.writeFile('./data/data1.txt', text, this);
      }
    );
  } catch (err) {
    console.error(err);
  } 
```
예제 5-7에서는 파일의 내용을 읽고, 내용을 수정한 후 다시 파일에 기록하기 위해 Step을 사용하고 있다.   
Step 순서 상 첫 번째 함수인 **readData**는 파일의 내용을 문자열로 읽은 후 두 번째 함수로 전달한다. 두 번째 함수는 해당 문자열을 **replace**를 사용하여 수정하고, 결과를 세 번째 함수에 전달한다. 세 번째 함수에서는 수정한 문자열을 다시 원래 파일에 기록한다.   
첫 번째 함수에서는 비동기**fs.readFile**을 감싸고 있다. 하지만 콜백 함수를 마지막 인수로 전달하는 대신, 코드에서는 this 컨텍스트로 전달하고 있다. 함수가 종료되면 함수의 데이터와 발생 가능한 에러는 다음 함수인 modify로 전송된다. **modify** 함수는 비동기 함수가 아니며, 문자열 중 부분문자열을 다른 것으로 바꾸는 역할만을 수행한다. 이 함수는 this 컨텍스트를 필요로 하지 않으며, 함수 끝에서 결과를 반환할 뿐이다.   
마지막 함수는 새롭게 수정된 문자열을 받아서 원래 파일에 다시 기록한다. 이 함수는 비동기 함수이므로 콜백 함수 자리에 this를 받는다. 마지막 함수의 마지막 매개변수로 this를 넣지 않으면 발생한 오류가 throw 되지 않아 바깥쪽 루프에서 잡히지 않는다.   다음과 같이 수정한 코드에서는 boogabooga 하위 디렉터리가 존재하지 않는 경우,   
```python
function writeFile(err,text){
	if (err) throw err;
	fs.writeFile('./boogabooga/data/data1.txt);
}
```
쓰기가 실패했다는 것을 절대 알 수 없게 된다.   

두 번째 함수는 비동기가 아님에도 불구하고, Step 내에서 첫번째 함수를 제외한 모든 함수는 일관성을 위해 첫 번째 매개변수로 error 개체를 요구한다. 동기 함수에서는 이 매개변수가 기본적으로 null이다.
**예제 5-8** 그룹화된 비동기 프로세스를 처리하기 위해 Step의 group() 기능을 사용
```python
var fs = require('fs'),
  Step = require('step'),
  files,
  _dir = './data/';

  try {
      Step(
        function readDir(){
          fs.readdir(_dir, this);
        },
        function readFile(err, results){
          if (err) throw err;

          files = results;
          var group = this.group();
          results.forEach(function(name){
            fs.readFile(_dir + name , 'utf8', group());
          });
        },
        function writeAll(err,data){
          if (err) throw err;
          for(var i = 0; i < files.length; i++){
            var adjdata = data[i].replace(/somecompany\.com/g, 'burningbird.net');
            fs.writeFile(_dir + files[i], adjdata, 'utf8', this);
          }
        }
      );
  } catch (err) {
    console.error(err);
  } 
```
예제 5-8에서는 지정된 하위 디렉터리 내의 파일 목록을 가져오기 위해 **readdir** 비동기 함수를 추가했다. 파일들의 배열은 예제 5-6에서처럼 **forEach** 명령으로 처리되지만, readFile에 대한 호출이 콜백 함수나 this로 끝나지 않는다. Step에서는 group 개체를 생성하도록 호출하면 그룹 결과를 위한 매개변수를 준비해두라고 알리게 된다. readFile 비동기 함수 내에서 group 개체를 호출하면 각 콜백이 순서대로 호출되며, **결과가 배열로 그룹화되어 다음 함수로 전달**된다.   
파일명들을 저장하기 위해 readdir의 결과는 전역변수 files에 할당된다. 마지막 Step 함수에서는 for 루프를 돌면서 데이터를 수정하고 files 변수에서 파일 명을 얻어낸다. 파일명과 수정한 데이터는 마지막으로 writeFile을 비동기로 호출하는 데 사용된다.   

각 파일에 대한 변경사항을 하드코딩하고자 한다면, 다른 접근방법으로 Step의 parallel 기능을 사용할 수 있다.   
**예제 5-9** Step의 group 기능을 사용하여 파일 그룹을 읽고 쓰기   
```python
var fs = require('fs'),
  Step = require('step'),
  files;

try {
  Step(
    function readFiles(){
      fs.readFile('./data/data1/txt', 'utf', this.parallel());
      fs.readFile('./data/data2/txt', 'utf', this.parallel());
      fs.readFile('./data/data3/txt', 'utf', this.parallel());
    },
    function writeFiles(err, data1, data2, data3){
      if (err) throw err;
      data1 = data1.replace(/somecompany\.com/g, 'burningbird.net');
      data2 = data2.replace(/somecompany\.com/g, 'burningbird.net');
      data3 = data3.replace(/somecompany\.com/g, 'burningbird.net');

      fs.writeFile('./data/data1.txt',data1, 'utf8',this.parallel());
      fs.writeFile('./data/data2.txt',data2, 'utf8',this.parallel());
      fs.writeFile('./data/data3.txt',data3, 'utf8',this.parallel());
    }
  );
} catch (err) {
  console.log(err);
} 
```
예제 5-9는 몇 개의 다른 파일들에 대해 readFile을 수행하고, 마지막 매개변수로**this.parallel()** 을 넘긴다. 이는 결과적으로 첫 번째 함수 내의 각 readFile에 대해 **next함수**로 전달되는 매개변수가 된다.**parallel 함수 호출은 두 번째 함수 내의 writeFile 함수에서도 각 콜백이 순서대로 처리되도록 보장하기 위해 사용된다**.    
예제 5-9의 애플리케이션은 동작은 되지만 보기에 좋지않다. 병렬 기능은 일련의 비동기 함수 여러개를 비동기로 실행하고 사후 콜백으로 처리되는 데이터용으로 사용하도록 남겨두는 것이 더 바람직하다.

## Async(by Caolan McMahon)
Async 모듈은 forEach, map, filter 등을 독자적으로 변형한 것처럼 컬렉션을 관리하는 기능을 제공한다. 또한 memoization을 비롯한 몇 가지 유틸리티 함수도 제공한다.   
> Async와 Async.js 모듈 두 개가 존재하므로, 둘을 혼동하지 않도록 주의해야한다. 지금 다루는 것은  Caolan McMahon이 만든 Async 모듈이다.   

다음과 같이 npm을 사용해서 Async를 설치한다 :   
`npm install async`   
Async는 serial, parallel, waterfall을 비롯한 다양한 비동기 패턴에 대한 제어 흐름 기능을 제공한다. Step 처럼 중첩 콜백 피라미드를 쉽게 다룰 수 있게 해주는 도구들을 제공하지만, 접근방법은 상당히 다르다. 예를 들면, **각 함수와 콜백 사이에 직접 삽입할 필요가 없다. 대신에 프로세스의 일부로 콜백을 포함시키게 된다.**   

**예제 5-10** 파일의 내용을 비동기로 읽고, 수정하고, 쓰기 위해 async.waterfall을 사용   
```python
var fs = require('fs'),
  async = require('async');

  try {
    async.waterfall([
      function readData(callback){
        fs.readFile('./data/data1.txt', 'utf8', function(err, data){
          callback(err,data);
        });
      },
      function modify(text, callback){
          var adjdata = text.replace(/somecompany\.com/g, 'burningbird.net');
          callback(null,adjdata);
      },
      function writeData(text, callback){
        fs.write('./data/data1.txt', text, function(err){
          callback(err,text);
        });
      }
    ], function(err,result){
      if (err) throw err;
      console.log(result);
    });
  } catch (err) {
    console.log(err);
  } 
```
예제5-10에서는 fs.readFile을 사용하여 데이터 파일을 열고 읽어서 비동기 문자열 치환을 수행한 후, fs.writeFile을 사용하여 문자열을 다시 파일에 기록하는 데 **async.waterfall**을 사용했다.   

**async.waterfall** 메서드는 두 개의 매개변수를 받는데, **작업들의 배열**과 **선택적인 콜백함수**다. 각 비동기 작업 함수는 async.waterfall 메서드 배열의 구성요소이며, 각 함수는 마지막 매개변수로 콜백을 필요로 한다. 이 콜백 함수는 물리적으로 함수를 중첩시킬 필요 없이 비동기 콜백 결과를 체인화할 수 있게 해준다. 하지만 각 함수의 콜백은 각 함수에서 에러를 테스트할 필요가 없다는 것을 제외하고는 중첩 콜백을 사용해서 일반적으로 처리하는 것과 동일하게 처리된다.   
콜백은 첫 번째 매개변수로 error 개체를 찾아본다. 콜백 함수에 error 개체를 전달하면, 이 시점에 프로세스가 종료되고 최종 콜백 루틴이 호출된다. 최종 콜백에서는 에러를 테스트해보고 바깥쪽에 있는 예외 처리 블록에 에러를 throw 하게 된다.   

readData 함수는 먼저 에러를 확인하는 fs.readFile 호출을 감싸고 있다. 에러가 발견되면 에러를 throw하고 프로세스를 종료한다. 에러가 발견되지 않으면 마지막 동작으로 콜백을 호출한다. 이는 Async로 하여금 다음 함수를 호출하고 관련된 데이터를 전달하라는 방아쇠다. 다음 함수는 비동기가 아니므로 처리를 수행한 후 null인 error 개체와  수정된 데이터를 전달한다. 마지막 함수인 writeData는 이전 콜백에서 전달된 데이터를 사용하여 자신의 콜백 루틴에서 에러를 테스트하는 비동기 writeFile을 호출한다.   

> 예제5-10 에서는 명명된 함수를 사용하고 있지만, 공식 문서에서는 익명함수를 권장한다. 둘 다 동일하게 잘 수행되기는 하지만, 명명 함수를 사용하는 것이 디버깅과 에러 처리를 단순화시킬 수 있는 장점이 있다.

**예제 5-11** 디렉터리에서 개체를 얻어 파일을 찾은 후 파일을 읽고 수정해서 다시 쓴 다음 로그를 기록
```python
var fs = require('fs'),
  async = require('async'),
  _dir = './data/';

  var writeStream = fs.createWriteStream('./log.txt',
  {'flags':'a',
    'encoding' :'utf8',
    'mode' : 0666});

    try {
      async.waterfall([
        function readDir(callback){
          fs.readdir(_dir, function(err,files){
            callback(err,files);
          })
        },
        function loopFiles(files, callback){
          files.forEach(function(name){
            callback(null,name);
          });
        },
        function checkFile(file, callback){
          fs.stat(_dir + file, function(err, stats){
            callback(err, stats,file);
          })
        },
        function readData(stats, file, callback){
          if (stats.isFile())
            fs.readFile(_dir + file, 'utf8', function(err, data){
              callback(err,file,data);
            })
        },
        function modify(file, text, callback){
          var adjdata = text.replace(/somecompany\.com/g, 'burningbird.net');
          callback(null, file, adjdata);
        },
        function writeData(file, text, callback){
          fs.writeFile(_dir + file, text, function(err){
            callback(err,file);
          })
        },
        function logChange(file, callback){
          writeStream.write('changed ' + file + '\n', 'utf8', function(err){
            callback(err,file);
          });
        }
      ], function(err,result){
          if (err) throw err;
          console.log('modified ' + result);
      });
    } catch (err) {
      console.log(err);
    } 
```
**fs.readdir** 메서드는 디렉터리 개체의 배열을 얻는데 사용된다. Node의 **forEach** 메서드(Async의 forEach가 아님)가 각 개체에 접근하는 데 사용된다. **fs.stats** 메서드는 각 개체의 stats를 얻어내는 데 사용된다. **stats**는 파일인지 검사하는 데 사용되며, 파일이 발견되면 파일을 열고 데이터에 접근한다. 데이터를 수정한 후 **fs.writeFile**을 통해 파일에 다시 기록한다. 해당 동작은 로그 파일에 기록디며, 콘솔로 되풀이 된다.   

일부 콜백에는 더 많은 데이터가 전달되는 것에 유의한다. 대부분의 함수들은 텍스트뿐만 아니라 파일 이름이 필요하므로, 마지막 여러 개의 메서드들은 이를 전달받는다. **각 함수의 첫 번째 매개변수가 error 객체(error 개체가 없을 경우 null)이고 마지막 매개변수가 콜백 함수인 한, 데이터 양에 관계없이 메서드에 전달 가능하다.**   

Async가 각 콜백 내에서 error 개체를 테스트하고, 에러가 발견되면 처리를 중단하고 최종 콜백 함수를 호출하므로 각 비동기 작업 함수에서는 에러를 확인할 필요가 없다. 이전에 Step을 사용할 때처럼 배열 항목을 처리하기 위해 특별한 처리를 해야 할 필요도 없다.

**예제 5-12** 세 개의 파일을 병렬로 열고, 내용을 읽음

```python
var fs = require('fs'),
  async = require('async');

  try {
    async.parallel({
        data1 : function (callback){
          fs.readFile('./data/data1.txt', 'utf8', function(err,data){
            callback(err,data);
          });
        },
        data2 : function (callback){
          fs.readFile('./data/data2.txt', 'utf8', function(err,data){
            callback(err,data);
          });
        },
        data3 : function readData(callback){
          fs.readFile('./data/data3.txt', 'utf8', function(err,data){
            callback(err,data);
          });
        }, function(err,result){
          if (err) throw err;
          console.log(result);
        }
    })
  } catch (err) {
    console.log(err);
  } 
```
예제 5-12 는 **async.parallel**을 사용하여 세 파일의 내용을 병렬로 읽는다. 하지만 이 예제는 함수 배열 대신에 Async가 지원하는 다른 접근방법을 사용하고 있는데, 개체의 속성으로 나열된 각 비동기 작업 개체로 개체를 전달한다. 세 작업이 모두 완료되면 결과가 콘솔에 출력된다.   

결과는 개체의 배열로 반환되며, 각 결과는 각각의 속성에 연결되어 있다. 예제에서 세 개의 데이터 파일이 다음과 같은 내용을 가지고 있을 경우,

```
data1.txt : apples
data2.txt : oranges
data3.txt : peaches
```
예제 5-12를 실행한 결과는 다음과 같다 :   
`{data1 : 'apples\n', data2 : 'oranges\n', data3 : 'peaches\n'}`   

Async 제어 흐름 메서드를 사용해서 작업할 때는 각 비동기 작업에 콜백을 전달하고, 작업이 완료되면 error 개체(또는 null)와 필요한 데이터를 전달하여 이 콜백을 호출하기만 하면 된다는 것을 기억해야 한다.
