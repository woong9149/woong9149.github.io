---
title: "[Nodejs]O'REILLY 정리 - 6"
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

# process
각 Node 애플리케이션은 Node의 process 개체 인스턴스이며, 그에 따라 일정 기능들을 내장하고 있다.

process 개체의 메서드 및 속성 대부분은 애플리케이션 및 환경에 대한 식별과 정보를 제공한다. **process.execPath**메서드는 Node 애플리케이션의 실행 경로를 반환한다. **process.version**은 Node의 버전을 제공하고,**process.platform**은 서버 플랫폼을 식별하는데 사용된다:   
```python
console.log(process.execPath); // 출력 : /usr/local/bin/node
console.log(process.version); // 출력 : v10.16.3
console.log(process.platform); // 출력 : darwin
```

process개체는 STDIO 스트림인 **stdin**, **stdout**, **stderr**도 래핑하고 있다. stdin과 stdout은 둘 다 비동기이며, 각각 읽기와 쓰기가 가능하다. 하지만 stderr은 동기이며, 블로킹되는 스트림이다.   
stdin과 stdout을 사용해서 데이터를 읽고 쓰는 방법을 보기위해, 예제 3-1의 Node 애플리케이션은 stdin으로 데이터를 수신해서 stdout으로 데이터를 그대로 전달한다. stdin 스트림은 기본적으로 일시 중지되어 있으므로, 데이터를 보내기 전에 resume을 호출해야한다.   
예제 3-1
```python
process.stdin.resume();
process.stdin.on('data', function(chunk){
	process.stdout.write('data: '+chunk);
})
```
Node를 사용해서 애플리케이션을 실행한 후 터미널에 값을 입력하면, Enter를 누를 때마다 입력한 내용이 그대로 되풀이 된다.   

process에서 유용한 또 하나의 메서드는 **memoryUsage**로, Node 애플리케이션이 얼마나 많은 메모리를 사용하고 있는지를 알려준다. 이 메서드는 성능 튜닝에 유용하며 애플리케이션에 대한 일반적인 호기심을 충족시켜주기도 한다. 응답 결과는 다음과 같은 구조를 가진다:   
`{res: 7450624, heapTotal: 2783520, heapUsed: 1375720 }`   
heapTotal 과 heapUsed 속성은 V8 엔진의 메모리 사용량을 가리킨다.   
process에서 마지막으로 다룰 메서드는 **process.nextTick**이다. 이 메서드는 Node 이벤트 루프의 다음 틱(루프)에서 호출될 콜백 함수를 연결해준다.   

어떤 이유로 함수를 비동기로 지연시키고 싶다면 **process.nextTick**을 사용하면 된다. 콜백 함수를 가진 새로운 함수를 만들고 있는 중일 때 콜백이 정말로 비동기인지를 확인하고 싶은 경우가 적절한 예다.
```python
var asynchFunction = function (data, callback) {
	process.nextTick(function() {
		callback(data);
	});
};
```
콜백 함수를 그냥 호출해버렸다면 동기로 동작했을 것이다. 하지만 이제는 콜백 함수가 이벤트 루프의 다음 틱이 될 때까지 호출되지 않는다.   
**process.nextTick** 대신 setTimeout의 지연 값을 0 밀리초로 호출해도 된다:   
```python
setTimeout(function(){
	callback(val);
}, 0);
```

하지만 **setTimeout**은**process.nextTick** 만큼 효율적이지 않다. 둘을 테스트해보면 **setTimeout**을 0 밀리초 지연값으로 호출했을 때보다 **process.nextTick**이 훨씬 더 빨리 호출된다. **process.nextTic**k은 **상당히 연산이 복잡하고 시간을 많이 소모하는 동작을 수행하는 함수를 가진 애플리케이션을 실행중인 경우에도 사용할 수 있다.** **process.nextTick**을 통해 호출하도록 프로세스를 나누면, **Node 애플리케이션이 시간을 많이 소비하는 프로세스가 완료될 때까지 기다리지 않고 다른 요청을 받을 수 있게 된다.**   

물론 예기치 않은 결과를 받게 되어 순차적으로 실행되도록 해야 할 필요가 있는 경우에는 프로세스를 나누고 싶지 않을 것이다.