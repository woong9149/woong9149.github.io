---
layout: single
author_profile: true
read_time: true
comments: true
share: true
related: true
title: "[Nodejs]O'REILLY 정리 - 12"
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