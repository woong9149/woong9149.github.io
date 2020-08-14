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