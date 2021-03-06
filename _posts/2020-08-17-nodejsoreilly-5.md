---
title: "[Nodejs]O'REILLY 정리 - 5"
layout: single
author_profile: true
read_time: true
comments: true
share: true
related: true
---

# 전역 개체(global,process)와 Buffer
몇몇 개체는 모든 Node 애플리케이션에서 **사용자가 모듈을 포함시킬 필요 없이 사용 가능**할 수 있다. Node.js 웹사이트에서는 **이러한 항목들을 global이라는 설명 레이블로 분류**하고 있다. 지금까지 사용해본 전역 요소에는 애플리케이션에 모듈을 포함시키기 위한 **require**가 있다. 또 다른 전역 요소인 **console**도 콘솔에 메시지를 기록하는 데 많이 사용했다. 나머지 전역 요소들은 Node를 내부적으로 구현하는 데 필수 불가결하지만, 우리가 직접 접근하거나 알아둬야 할 필요는 없다. 그렇지만, 다음 항목들은 Node의 핵심 동작 방식을 정의하는 데 도움이 되므로 좀 더 자세히 살펴볼 필요가 있다.
* **global 개체** : 전역 네임스페이스
* **process 개체** : STDIO(표준 IO) 스트림에 대한 래퍼와 같은 필수 기능과 동기 함수를 비동기 콜백으로 전환해주는 기능을 제공
* **Buffer 클래스** : 원시 데이터 저장 및 조작을 제공하는 전역 개체
* **자식 프로세스**
* **도메인 해석**(domain resolution)과 **URL 처리에 유용한 모듈**

## global
global은 전역 네임스페이스 개체다. 전역 속성 및 메서드에 대한 접근을 제공하고 이름으로 명시적인 참조를 할 필요가 없다는 점에서 브라우저 환경에서의 window와 유사하다고 볼 수 있다. REPL에서 `console.log(global);` 로 global 개체를 출력하면 모든 전역 개체들에 대한 인터페이스뿐만 아니라, 현재 실행중인 시스템에 대한 정보도 출력된다.
    
global이 브라우저에서 window 개체와 유사하다고 했지만, 사용 가능한 메서드나 속성이 다르다는 것 외에도 중요한 차이점이 존재한다. 브라우저에서 window 개체는 진정한 전역개체다. 클라이언트 JavaScript에서 전역 변수를 선언하면 웹 페이지와 모든 단일 라이브러리에서 접근가능하다. 하지만 **Node 모듈에서 최상위 영역에 변수를 생성(즉 함수 외부에 있는 변수)하면, 모듈에 대해서는 전역이 되지만 모든 모듈에 대해서는 아니다.**   
require를 비롯한 다른 개체나 메서드드로 global 개체의 인터페이스에 속한 것이다.    
모듈은 자신만의 고유한 네임스페이스 내에 존재하므로, 한 모듈 내에서 최상위 변수를 정의하면 다른 모듈에서는 해당 변수를 사용할 수 없다. 여기서 더욱 중요한 것은 애플리케이션에서 모듈을 포함하려고 할 때 모듈에서 명시적으로 내보내어진 것만 가능하다는 것이다. **애플리케이션이나 다른 모듈에서 최상위 모듈 변수는 접근할 수 없다.**

REPL test 코드
```python
 var globalValue;
 
 exports.setGlobal = function(){
	 globalValue = val;
 };
 
 exports.returnGlobal = function(){
	 console.log(global);
	 return globalValue;
 }
```

globalValue라는 최상위 변수와 변수 값을 설정하고 반환하기 위한 함수를 가지고 있다. 값을 반환하는 함수 내에서는 console.log 메서드를 호출해서 global 개체를 출력하고 있다.   
애플리케이션에서 변수를 설정했을 때와 마찬가지로 global 개체의 출력 결과에 globalValue가 있을 것이라고 예상할 수 있지만, 결과는 그렇지 않다.
REPL 세션을 시작한 후 require를 호출하여 새로운 모듈을 포함시킨다:   
`var mod1 = require('./mod1.js');`    

값을 설정한 다음, 다시 조회해보자:   
```
mod1.setGlobal(34);
var val = mod1.returnGlobal();
```
전역으로 정의된 값을 반환하기 전에 console.log 메서드가 global 개체를 출력한다. 가져온 모듈에 대한 참조를 가지고 있는 새로운 변수는 하단에서 볼 수 있지만, val은 변수가 설정되지 않았기 때문에 undefined이다. 뿐만 아니라, 출력된 내용에 모듈 자체의 최상위 globalValue에 대한 참조는 전혀 포함되어있지 않다:
```
mod1: { setGlobal:[function], returnGlobal:[Function]},
_: undefined,
val:undefined
```
명령을 다시 실행해보면, 애플리케이션 변수인 val은 값이 할당되지만 globalValue는 여전히 보이지 않는다:
```
mod1 : {setGlobal:[Function], returnGlobal: [Function] },
_:undefined,
val: 34 }
```

**모듈 데이터에 접근하려면 모듈에서 제공하는 수단을 통해서만 가능하다.** 이는 JavaScript 개발자 입장에서 라이브러리 내의 전역 변수 때문에 발생하는 예기치 못한 데이터 충돌 문제를 더 이상 겪지 않아도 된다는 것을 의미한다.