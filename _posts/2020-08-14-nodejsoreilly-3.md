---
title: "[Nodejs]O'REILLY 정리 - 3"
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

# 비동기 프로그램 흐름 살펴보기
서비스와 서비스를 테스트하기 위한 두 개의 새로운 애플리케이션을 만든다.   
예제 1-3은 서비스 애플리케이션, 예제 1-4는 테스트 애플리케이션 코드 이다.   

**예제 1-3) 일련의 숫자와 파일 내용을 출력하는 새로운 서비스**   
```python
var http = require('http');
var fs = require('fs');

//숫자를 쓴다
function writeNumbers(res){
	var counter = 0;
	
	//숫자를 증가해가면서 클라이언트에게 쓴다
	for(var i = 0; i<100; i++){
		counter++;
		res.write(counter.toString() + '\n');
	}
}

//서버 생성
http.createServer(function(req, res) {
	var query = require('url').parse(req.url).query;
	var app = require('querystring').parse(query).file + ".txt";
	
	//컨텐츠 헤더
	res.writeHead(200,{'Content-Type': 'text/plain'});
	
	//숫자를 쓴다
	writeNumbers(res);
	
	//파일을 열고 내용을 읽기 위한 타이머
	setTimeout(function(){
		console.log('opening ' + app);
		//파일 내용을 열고 읽음
		fs.readFile(app, 'utf8', function(err,data) {
			if(err)
				res.write('Could not find or open file for reading\n');
			else {
				res.write(data);
			}
			//출력 완료
			res.end();
		})
	},2000)
}).listen(8124);

console.log('Server running at 8124');
```

숫자를 출력하는 루프는 연산이 많은 프로세스를 수행하면서 프로세스가 종료될 때까지 차단되는 경우 발생하는 것과 유사하게 애플리케이션을 지연시키는 데 사용되었다. setTimeout 함수는 또다른 비동기 함수로, 다음 차례인 두 번째 비동기 함수 readFile을 호출한다. 이 애플리케이션은 동기와 비동기 프로세스를 둘 다 결합하고 있다.

main.txt라는 텍스트 파일을 만들고 Chrome 브라우저에서 해당 페이지를 file=main이라는 쿼리 문자열로 접근하면 다음과 같이 콘솔에 출력된다:
```
Server running at 8124/
opening main.txt
opening undefined.txt
```
처음 두 줄은 예상했던 것이다. 첫 번째 줄은 애플리케이션 끝부분에서 console.log를 실행한 결과이며, 두 번째 줄은 열고자 하는 파일을 출력한 것이다.
문제는 세 번째 줄의 undefined.txt 이다.   
**브라우저에서 보낸 웹 요청을 처리할 때, 브라우저가 요청을 한 번이 아니라 여러 번 보낼 수 있다는 것에 유의해야 한다.** 예를 들어, 브라우저가 favicon.ico를 찾는 두 번째 요청을 보낼 수도 있다. 이 때문에, 쿼리 문자열을 처리할 때 필요한 데이터가 제공되었는지를 확인하고 데이터가 없는 요청은 무시해야 한다.

> 쿼리 문자열을 통해 값을 받는다고 예상하는 경우, 여러 번 요청을 보내는 브라우저는 애플리케이션에 영향을 줄 수 있다. 애플리케이션을 적절하게 조정해야 하고, 여러 다른 브라우저들로 애플리케이션을 테스트해야 한다.

지금까지 수행한 것은 모두 브라우저에서 node 애플리케이션을 테스트한 것 인데, 이러한 방법으로는 사실 Node 애플리케이션의 비동기 특성에 맞게 충분한 부하를 주지 못한다.

예제 1-4는 HTTP 모듈을 사용해서 루프를 돌면서 예제 서버에 여러 번 요청을 보내는 테스트 코드다. 비동기로 요청하는 것은 아니지만, 테스트 프로그램을 실행하면서 브라우저를 통해서도 서비스에 접근해볼 것이다. 둘을 결합하면 애플리케이션을 비동기적으로 테스트하게 된다.

예제 1-4
```python
var http = require('http');

//원하는 url과 필요한 경로 및 옵션
var options = {
    host: 'localhost',
    port: 8124,
    path:'/?file=secondary',
    method:'GET'
};

var processPublicTimeline = function(response){
    //종료되었으면 데이터를 파일에 쓴다
    console.log('finished request');
};

for(var i = 0;i < 2000; i++){
    //요청을 보낸 후 종료하고 연결을 닫는다
    http.request(options, processPublicTimeline).end();
}
```
secondary.txt 라는 이름으로 두 번째 텍스트 파일을 만든다.  테스트 프로그램이 실행 된 상태에서 브라우저를 사용하여 애플리케이션에 접근한다. 애플리케이션에서 출력되는 콘솔 메시지를 보면, 브라우저를 통한 수동 요청과 테스트 프로그램의 자동화된 요청 양쪽 모두가 처리되는 것을 볼 수 있다.    
결과 페이지 :
* 1부터 100까지 숫자 출력
* 텍스트 파일(main.txt)의 내용이 출력