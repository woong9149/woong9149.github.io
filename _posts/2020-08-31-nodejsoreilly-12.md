---
title: "[Nodejs]O'REILLY 정리 - 12"
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
- nodejs
---

# Utilities 모듈과 개체 상속
Utilities 모듈은 여러 가지 유용한 함수들을 제공한다. 이 모듈은 다음과 같이 포함시킬 수 있다:   
`var util = require('util');`   
Utilities 를 사용해서 개체가 배열이거나(**util.isArray**) 정규식인지(**util.isRegExp**)를 테스트하고 문자열을 형식화(**util.format**)할 수 있다. 모듈에 새롭게 실험적으로 추가된 것에는 읽기 가능한 스트림으로부터 데이터를 뽑아내어 쓰기 가능한 스트림으로 보내는 기능도 있다:   
`util.pump(process.stdin, process.stdout);`   

개체를 문자열로 표현한 것을 얻기 위해서 **util.inspect**를 많이 사용하게 되는데, 이를 사용하면 개체에 대한 추가 정보를 발견하는 데 매우 유용하다. 첫 번째 필수적인 인수는 개체이고, 두 번째 선택적 인수는 열거가 불가능한 속성을 출력할지의 여부, 세 번째 선택적 인수는 개체를 재귀 탐색할 횟수(depth), 네 번째 선택적 인수는 출력을 ANSI 색상으로 꾸밀지의 여부다. depth(기본값은 2번)를 null 값으로 할당하면, 개체를 완전히 조사할 때까지 필요한만큼 무한하게 재귀 탐색을 수행한다.  depth에 null을 사용하면 상당히 많은 출력 결과를 얻게 되기 때문에 조심해야 한다.   

util.inspect를 사용한 간단한 애플리케이션은,   
```python
var util = require('util');
var jsdom = require('jsdom');

console.log(util.inspect(jsdom, true, null, true));
```
애플리케이션을 실행 시 다음과 같이 결과를 파일로 파이프시킨다:   
`node inspectjsdom.js > jsdom.txt`   

Utilities 모듈에서 제공하는 메서드들은 여러 개가 있지만, 가장 많이 사용하게 되는 것이 바로 **util.inherits** 이다. util.inherits 함수는 두 개의 인수 constructor와 superConstructor를 받는다. 그 결과로 **constructor(생성자)** 는 **superconstructor(상위생성자)** 로 부터 기능을 상속 받는다.

**예제 3-11** util.inherits 메서드를 통한 개체 상속   
```python
var util = require('util');

//원래 개체를 정의
function first(){
    var self = this;
    this.name = 'first';
    this.test = function(){
        console.log(self.name);
    };
}

first.prototype.output = function(){
    console.log(this.name);
}

function second(){
    second.super_.call(this);
    this.name = 'second';
}
util.inherits(second,first);

var two = new second();

function third(func){
    this.name = 'third';
    this.callMethod = func;
}

var three = new third(two.test);

//모두 "second"를 출력해야 함
two.output();
two.test();
three.callMethod();
```

각각 first, second, third라는 3개의 개체를 생성한다. first 개체는 test와 output이라는 두 개의 메서드를 가진다. test 메서드는 개체 내에 직접 정의된 반면, output 메서드는 이후 **prototype** 개체를 통해서 추가되었다. 개체에 메서드를 정의하는 데 두 기법을 모두 사용한 이유는 util.inherits로 상속 시에 중요한 사항을 보여주기 위한 것이다(실제 상속은 JavaScript에서 해주는 것이지만, **util.inherits에 의해 활성화된다**).   

second 개체에는 다음 줄이 포함되어 있다:   
`second.super_.call(this);`   
second 개체 생성자에서 이 줄을 제거하면 second 개체의 output에 대한 호출은 성공하지만 test에 대한 호출은 오류가 발생해서 'test가 정의되지 않았다'는 메시지와 함께 Node 애플리케이션이 강제 종료된다.   

call 메서드는 두 개체 간의 생성자를 연결하여 생성자뿐만 아니라, 상위 생성자도 호출되게 해준다. **상위 생성자는 상속된 개체의 생성자**다.   

상위생성자를 호출해야 하는 이유는 **first가 생성되기 전까지는 test 메서드가 존재하지 않기 때문**이다. 하지만 output 메서드의 경우에는 call 메서드가 필요 없는데, first 개체의 prototype 개체 상에 직접 정의되었기 때문이다. second 개체가 first로부터 속성들을 상속시에 새롭게 추가된 메서드도 상속받는다.

util.inherits의 내부를 살펴보면 **super_** 가 정의된 곳을 볼 수 있다 :   
```python
exports.inherits = function(ctor, superCtor){
	ctor.super_ = superCtor;
	ctor.prototype = Object.create(superCtor.prototype, {
		constructor: {
			value: ctor,
			enumerable: false,
			writable: true,
			configurable: true
		}
	});
}
```

super_는 util.inherits가 호출될 때 second 개체의 속성으로 할당된다:   

`util.inherits(second, first);`    

세 번째 개체인 third는 name 속성을 가지고 있다. third는 first나 second로부터 상속받지 않았지만, 인스턴스화될 때 함수가 전달되어야 한다. 이 함수는 third 개체가 가지고 있는 **callMethod** 속성에 할당된다. 코드에서 이 개체의 인스턴스를 생성 시에 two라는 개체 인스턴스의 test 메서드가 생성자로 전달된다:   

`var three = new third(two.test);`   

three.callMethod가 호출되면, 얼핏 생각하기에 "third"가 출력될 것 같지만, 실제로는 "second"가 출력되는데, 이것은 first 개체의 **self 참조**가 관련되어 있다.   

**JavaScript에서 this는 개체 컨텍스트**로, 메서드가 전달되거나 이벤트 핸들러로 넘겨질 때 변경될 수 있다. **개체 메서드에 대한 데이터를 보존 가능한 유일한 방법은 this를 개체 변수(이 경우 self)로 할당한 다음, 개체의 함수 내에서 해당 변수를 사용하는 것**이다.   

이 애플리케이션을 실행하면 다음과 같이 출력된다:   
```
second
second
second
```