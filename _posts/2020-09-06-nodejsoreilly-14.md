---
title: "[Nodejs]O'REILLY 정리 - 14"
layout: single
author_profile: true
read_time: true
comments: true
share: true
related: true
categories:
- o'reilly
- Node.js
- TIL
tags:
- o'reilly
- Node.js
- TIL
---

# Node 모듈 시스템
Node의 기본 구현은 최대한 간결함을 유지하고 있다. Node 개발자들은 사용 가능한 모든 구성 요소들을 Node내에 포함시키는 대신, Node의 모듈을 통해 추가 기능들을 제공하고 있다.   
Node의 모듈 시스템은 상호 운용이 가능하도록 모듈을 생성하는 방법인 **CommonJS 모듈 시스템**을 본 따 만들어졌다. 모듈 시스템의 핵심은 자신의 모듈이 다른 모듈들과 잘 동작하도록 보장하기 위해 개발자들이 준수해야 하는 계약이다.   

CommonJS 모듈 시스템 요구사항 중에서 Node에서 구현된 것은 다음과 같다 :   
* 모듈 식별자를 받아서 노출된 API를 반환하는 require 함수에 대한 지원 포함
* 모듈 명은 캐릭터 문자열이며, 경로 식별을 위해 /를 포함할 수 있음
* 모듈은 모듈 외부로 노출될 항목을 명시적으로 내보내기 해야 함
* 변수는 모듈에 대해 private임

## require를 사용한 모듈 로딩과 기본 경로
Node는 간결한 모듈 로딩 시스템을 지원하는데, 파일과 모듈은 1:!로 대응된다.   

Node 애플리케이션에 모듈을 포함시키려면 require문을 사용하여 모듈에 대한 식별자 문자열을 전달한다:   

> var http = require('http');   

모듈의 전체 개체가 아닌 특정 개체만을 포함시킬 수도 있다 :   

> var spawn = require('child_process').spawn;   

모듈 식별자(HTTP 모듈의 경우 http)를 부여하여 Node가 본래 가지고 있는 코어 모듈이나 node_modules 폴더에 있는 모듈을 로드할 수 있다. 코어 모듈에 속하지도 않고 node_modules 폴더에도 포함되지 않은 모듈들은 경로를 나타내기 위해 /를 포함해야 한다. 예를 들어, 다음 require문에서는 Node 애플리케이션과 동일한 디렉터리에서 mymodule.js라는 모듈을 찾으려고 시도한다 :   

> require('./mymodule');   

혹은 전체 경로를 사용해도 된다 :   

> require('/home/myname/myapp/mymodule.js');   

모듈 파일은 **.js**, **.node**, **.json** 파일 확장자 중 하나를 가지게 된다. .node 확장자는 파일이 JavaScript 텍스트 파일이 아니라 컴파일된 바이너리라고 가정한다.   

**Node 코어 모듈은 외부 모듈보다 더 높은 우선순위를 가진다**. 만약 http 라는 사용자 정의 모듈을 로드하려고 시도하면, Node는 코어 HTTP 모듈을 로드한다. 다른 모듈 식별자를 사용하거나, 전체 경로를 제공해야 한다.   

경로가 없이 Node 식별자를 지정할 경우 해당 모듈은 코어 모듈이 아니므로 Node에서는 먼저 애플리케이션 하위에 node_mopdules 폴더를 찾아보고, 이 폴더에서 해당 모듈을 검색한다. 모듈을 찾지 못하면, 부모 하위 디렉터리에서 node_modules 폴더를 계속 찾아보게 된다.   

모듈 이름이 mymodule이고 애플리케이션이 다음과 같은 경로를 가진 하위 디렉터리에 위치해있다고 가정해보자 :    

> /home/myname/myprojects/myapp   

Nodes는 해당 모듈을 다음과 같은 순서로 검색한다 :   

* /home/myname/myprojects/myapp/node_modules/mymodule.js   
* /home/myname/myprojects/node_modules/mymodule.js   
* /home/myname/node_modules/mymodule.js   
* /home/node_modules/mymodule.js   
* /node_modules/mymodule.js   

Node는 require문을 호출하는 파일의 위치에 따라 검색을 최적화 한다. 예를 들어, require문을 가진 파일이 node_modules 폴더의 하위 디렉터리 내에 있는 모듈 자신인 경우 Node는 필요한 모듈을 최상위 node_modules 폴더에서 검색하기 시작한다.   

require에는 두 가지 변형된 형태가 존재하는데, **require.resolve**와 **require.cache**가 있다.  **require.resolve** 메서드는 주어진 모듈에 대한 탐색을 수행하긴 하지만, 모듈을 로드하는 대신 탐색된 파일 이름만을 반환한다. **require.cache** 개체는 로딩된 모든 모듈의 캐시된 버전을 가지고 있다. 동일한 컨텍스트에서 모듈을 다시 로드하려고 시도하면 캐시로부터 로드된다. 강제로 새로 로드하고 싶다면 항목을 캐시로부터 삭제한다.   

항목의 경로가 다음과 같다면,   

> var circle = require('/absolutepath/circle.js');    

다음과 같이 삭제한다 :   

> delete require.cache('./circle.js');   

이 코드는 다음 번에 require가 호출될 때 강제로 모듈을 다시 로드하게 한다.

## 외부 모듈과 Node 패키지 관리자
Node와 연관된 풍부한 기능들 대부분은 서드파티 모듈을 통해서 제공된다. 서드파티 모듈에는 라우터 모듈, 관계형 혹은 문서 기반 데이터베이스 시스템과 연동하는 모듈, 템플릿 모듈, 테스트 모듈, 심지어 지불 게이트웨어 모듈도 존재한다.   Node 모듈을 설치하는 가장 간단한 방법은 NPM(Node Package Manager)을 사용하는 것이다.    

npm 명령어에 대한 개요를  보려면 다음과 같이 입력한다 :   

> $ npm help npm   

모듈은 전역 혹은 로컬로 설치될 수 있다. 프로젝트에서 시스템을 공유하는 모든 사람들이 이 모듈에 접근할 필요가 없다면 로컬 설치가 가장 좋은 접근방법이다. 기본값인 로컬 설치는 모듈을 현재 위치의 node_modules 디렉터리에 설치한다 :   

> $ npm help 모듈이름   

설치가 되고나면 로컬 디렉터리의 node_modules 디렉터리에서 해당 모듈을 찾을 수 있다. 종속 모듈들도 모듈의 node_modules 디렉터리에 설치된다. 패키지를 전역으로 설치하고 싶다면 -g 또는 - -global 옵션을 사용한다.   
 
> npm -g install 모듈이름

파일 시스템 상의 폴더에 있는 모듈이나 로컬 혹은 URL을 통해 가져오는 타르볼(tarball)을 설치할 수도 있다 :    

> npm install http://somecompany.com/모듈이름.tgz   

패키지가 버전을 가지고 있는 경우 특정 버전을 설치할 수 있다 :   

> npm install 모듈이름@0.1 (0.1은 버전)   

사용하지 않는 모듈을 제거 할 수 있다 :   

> npm uninstall 모듈이름   

다음 명령어는 npm으로 하여금 새로운 모듈이 있는지 확인하고, 만약 있으면 업데이트를 수행하게 한다 :   

> npm update   

또는 단일 모듈만을 업데이트할 수도 있다 :   

> npm update 모듈이름    

최신 버전이 아닌 패키지가 있는지를 확인만하고 싶을 경우에는 다음을 사용한다 :   

> npm outdated (모듈이름)   

list, ls, la 또는 ll 로 설치된 패키지와 종속 관계를 나열할 수 있다 :   

>  npm list   

-d 플래그를 사용하여 모든 종속 관계를 직접 설치할 수도 있다. 예를 들어, 디렉터리 내의 모듈을 대상으로 하려면 다음을 입력한다 :   

> npm install -d   

npm 레지스트리에 아지 업로드되지 않은 버전의 모듈을 설치하고자 할 경우에는 Git repository에서 직접 설치하면 된다 :   

> npm install repository주소

## 사용자 정의 모듈 만들기
클라이언트 JavaScript에서와 마찬가지로 재사용 가능한 JavaScript를 라이브러리로 분리할 수 있다. 차이점은 JavaScript를 Node에서 사용하기 위한 모듈로 변환하기 위해 몇 가지 추가적인 단계가 필요하다는 것이다.   

문자열과 문자열 배열을 받아서  첫 번째 문자열을 배열 내의 각 문자열에 연결하는 concatArray라는 JavaScript 라이브러리 함수를 가지고 있다고 해보자 :   

```python
function concatArray(str, array) {
	return array.map(function(element){
		return str + ' '+ element;
	});
}
```