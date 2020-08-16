---
title: "[Nodejs]O'REILLY 정리 - 1"
layout: single
author_profile: true
read_time: true
comments: true
share: true
related: true
categories:
- nodejs
- oreilly
- TIL
tags:
- nodejs
- oreilly
- TIL
---

# Hello, World ! 띄우기
### 예제 1-1
```python
// http 모듈을 로드
var http = require('http');

// http 서버를 생성
http.createServer(function (req, res) {	

  // content header
  res.writeHead(200, {'content-type': 'text/plain'});

  // 메세지를 쓰고 통신이 완료되었다는 신호를 보냄
  res.end("Hello, World!\n");
}).listen(8124);

console.log('Server running on 8124/');
```

### 예제 1-1 분석

`var http = require('http');`   
Node 기능 대부분은 **모듈**이라고 불리는 외부 애플리케이션 및 라이브러리를 통해 제공된다.   
위의 코드는 HTTP 모듈을 로드해서 로컬 변수에 할당한다. HTTP 모듈은 기본 HTTP 기능을 제공하여 애플리케이션이 네트워크에 접근할 수 있게 해준다.   

`http.createServer(function (req, res) { ...`   
이 코드에서 **createServer**를 사용하여 새로운 서버가 생성되며, 함수 호출 매개변수로 익명함수가 전달 된다. 이 익명 함수는 **requestListener** 함수로 서버 요청(**http.ServerRequest**)과 서버 응답(**http.ServerResponse**)이라는 두개의 매개변수를 가진다.   
익명 함수 내에는 `res.writeHead(200, { 'content-Type' : 'text/plain'});` 라는 코드가 있다.   
**http.ServerResponse** 개체는 **writeHead**라는 메서드를 가지는데, **응답 상태**(200)와 **응답 content-type**을 제공하는 **응답 헤더**를 전송하고 있다.   
Headers 개체 내에 content-length나 connection과 같은 다른 응답 헤더 정보를 포함시킬 수 있다.
`
	{ 'content-length' : '123',
	   'content-type' : 'text/plain',
   	'connection' : 'keep-alive',
	  'accept' : '*/*' }
`
writeHead의 두 번째 선택적 매개변수는 reasonPhrase로, 상태 코드에 대한 텍스트 설명을 제공한다.   
<br/>
헤더 생성 코드 다음은 "Hello, World!" 메시지를 쓰는 명령이다.   
`res.end("Hello, World!\n");`   
**http.ServerResponse.end 메서드**는 모든 헤더와 응답 본문이 전송되었고 통신이 종료되었다는 신호를 보낸다.   
이 메서드는 **모든 http.ServerResponse 개체에서 사용되어야 한다.**   
**end** 메서드는 두 개의 매개변수를 가진다 :   
* 문자열이나 버퍼가 될 수 있는 데이터 청크   
* 데이터 청크가 문자열일 경우 두 번째 매개변수에 인코딩을 지정   
두 매개변수는 선택적이며, 두 번째 매개변수는 문자열 인코딩이 기본값인 utf8이 아닐 때만 필요하다.   

end 함수 내에 텍스트를 전달하는 대신 write 메서드를 사용하고,   
`res.write("Hello, World!\n");`   
다음과 같이 호출해도 된다.   
`res.end( );`   

익명 함수 및 createServer 함수는 둘 다 다음 코드에서 종결된다:   
`}).listen(8124)`   
**http.Server.listen** 메서드는 createServer 메서드가 끝난 후에 연결되어 해당 포트(이 경우 8124)로 들어오는 연결들을 대기한다.   
선택적 매개변수는 hostname과 콜백 함수다.   hostname이 제공되지 않으면,    
서버는 http://oreilly.com 또는 http://examples.burningbird.net 과 같은 웹 주소로 연결을 허용한다.


**Tip**   
Linux 시스텐에서 애플리케이션을 **백그라운드로 실행**하고 싶으면,   
`node app.js &`   
종료할 때는 kill을 사용하여 수동으로 해당 프로세스를 강제 종료 시켜야 한다.   

process ID를 찾기 위한 명령어   
`ps -ef | grep node`   

(**&** 로 실행하게 되면 shell 이 종료되거나 사용자 로그아웃을 하게 되면 프로세스가 중단(hangup) 된다.   
**nohup** 명령어를 함께 사용하면 백그라운드에서 작업함과 동시에 터미널 세션을 끊거나 컴퓨터를 종료해도 프로세스가 사라지지 않는다.)