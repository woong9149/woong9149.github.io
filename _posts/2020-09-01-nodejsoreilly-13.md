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