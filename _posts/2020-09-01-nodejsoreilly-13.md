---
title: "[Nodejs]O'REILLY 정리 - 13"
layout: single
author_profile: true
read_time: true
comments: true
share: true
related: true
categories:
- o'reilly
- nodejs
tags:
- o'reilly
- tags
---

# 이벤트와 EventEmitter
Node 코어 개체들의 내부를 살펴보면 EventEmitter를 발견하는 경우가 많은데, 개체가 이벤트를 발생시켜서 이벤트가 on으로 처리될 때마다 EventEmitter가 동작하는 것을 볼 수 있다.   

EventEmitter 개체는 Node 개체들에 비동기 이벤트 처리를 제공한다. 핵심 기능을 보기위한 테스트 애플리케이션은 먼저, Events 모듈을 포함시킨다:   

```python
var events = require('events');
```

다음으로, EventEmitter 인스턴스를 생성한다:   

```python
var em = new events.EventEmitter(); 
```

새롭게 생성한 EventEmitter 인스턴스를 사용해서 수행할 수 있는 두 가지 주요작업은 이벤트에 이벤트 처리기를 연결하는 것과 실제로 이벤트를 발생시키는 것이다. on 이벤트 처리기는 특정 이벤트가 발생될 때 호출된다. 메서드의 첫 번째 매개변수는 이벤트의 이름이며, 두 번째는 이벤트를 처리하기 위한 함수다 :   

```python
em.on('someevent', function(data){ ...});
```

해당 이벤트는 개체에서 어떤 기준에 따라 emit 메서드를 통해 발생된다 :   

```python
if(somecriteria){
	em.emit('event');
}
```

**예제 3-12** EventEmitter 기능에 대한 기본적인 테스트   
```python
var eventEmitter = require('events').EventEmitter;
var counter = 0;

var em = new eventEmitter();
setInterval(function(){em.emit('timed',counter++);},3000);

em.on('timed', function(data){
	console.log('timed ' +data);
});
```
예제 3-12 에서는 매 3초마다 timed라는 이벤트를 발생시키는 EventEmitter 인스턴스를 생성한다. 이 이벤트에 대한 이벤트처리기 함수는 카운터와 함께 콘솔에 출력한다.   
애플리케이션을 실행하면 애플리케이션이 종료될 때까지 timed 이벤트 메시지가 콘솔에 출력된다.   

애플리케이션 전체에서 EventEmitter 인스턴스를 사용하지 않고 EventEmitter 기능을 기존 개체에 추가하려면 **util.inherits** 메서드를 사용한다:   

`util.inherits(someobj, EventEmitter);`

util.inherits를 사용함으로써 개체 내의 메서드에서 **emit** 메서드를 호출할 수 있으며, 개체 인스턴스에 이벤트 처리기 코드를 작성할 수 있게 된다 :   
```python
someobj.prototype.somemethod = function(){ this.emit('event') };
...
someobjinstance.on('event', function() { } );
```

**예제 3-13** EventEmitter로부터 상속받는 이벤트 기반 개체 생성

```python
var util = require('util');
var eventEmitter = require('events').EventEmitter;
var fs = require('fs');

function inputChecker (name, file) {
   this.name = name;
   this.writeStream = fs.createWriteStream('./' + file + '.txt',
      {'flags' : 'a',
       'encoding' : 'utf8',
       'mode' : 0666});
};

util.inherits(inputChecker,eventEmitter);

inputChecker.prototype.check = function check(input) {
  var command = input.toString().trim().substr(0,3);
  if (command == 'wr:') {
     this.emit('write',input.substr(3,input.length));
  } else if (command == 'en:') {
     this.emit('end');
  } else {
     this.emit('echo',input);
  }
};
// testing new object and event handling
var ic = new inputChecker('Shelley','output');

ic.on('write', function(data) {
   this.writeStream.write(data, 'utf8');
});

ic.on('echo', function( data) {
   console.log(this.name + ' wrote ' + data);
});

ic.on('end', function() {
   process.exit();
});

process.stdin.resume();
process.stdin.setEncoding('utf8');
process.stdin.on('data', function(input) {
    ic.check(input);
});
```
예제 3-13은 애플리케이션 내에서는 새로운 개체인 inputChecker가 생성된다. 생성자는 사람 이름과 파일 이름 두 값을 받아서 사람 이름을 개체 변수로 할당하고, 파일 시스템 모듈의 createWriteStream 메서드를 사용하여 쓰기 가능한 스트림에 대한 참조도 생성한다.
EventEmitter 기능은 process.stdin.on 이벤트 처리기 메서드도 포함되어 있는데, process.stdin은 EventEmitter를 상속받는 수많은 Node 개체 중 하나이기 때문이다.   

util.inherits를 다루는 것 처럼, 새로운 개체의 생성자를 EventEmitter에 연결시킬 필요가 없다. 예제의 **on**과 **emit**은 개체 인스턴스 속성이 아닌 prototype 메서드로 구성되어 있기 때문이다.   

on 메서드는 사실 동일한 매개변수를 받는 **EventEmitter.addListener** 메서드를 단축시킨 것에 불과하다.

## TIP
**읽기 및 쓰기 가능 스트림**   
Node 파일 시스템 모듈(fs)은 파일을 열어서 읽고 쓰기, 특정 파일에 새로운 활동이 발생했는지를 감시하기, 디렉터리 조작하기 등을 가능하게 해준다. 또한 스트림을 일고 쓸 수 있는 기능을 제공한다.   

읽기 가능 스트림은 **fs.createWriteStream**에 파일에 대한 이름 및 경로, 기타 옵션을 넘겨서 생성한다. 쓰기 가능한 스트림 역시 파일명 및 경로를 **fs.createWriteStream**에 넘겨서 생성한다.   

**읽기 및 쓰기가 자주 발생하는 이벤트에서 파일을 읽고 쓸 때는 전통적인 읽기/쓰기 메서드 대신 읽기 및 쓰기 가능 스트림을 사용하는 것이 좋다**. 스트림은 백그라운드에서 열리며, 읽기 및 쓰기는 큐로 들어가 순서대로 처리된다.