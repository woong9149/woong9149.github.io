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