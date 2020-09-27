---
title: "[Nodejs]O'REILLY 정리 - 17"
categories:
- ''
- Nodejs
- o'reilly
- TIL
tags:
- Nodejs
- o'reilly
- TIL
---

# 트래픽 라우팅, 파일 서비스, 미들웨어
웹 페이지에서 링크를 클릭하면, 무엇인가가 일어나기를 기대하게 된다. 통상적으로는 웹 페이지가 로드되어야 한다. 하지만 웹 리소스가 로드되기 이전에 실제로는 많은 일들이 일어나는데, 그 중에는 우리가 제어할 수 없는 것(예: 패킷 라우팅)도 있고, 링크의 컨텐츠를 기반으로 어떻게 응답해야 하는지를 알고 있는 소프트웨어를 설치한 우리에게 달려있는 것도 있다.   
리소스를 사용자에게 올바르고 신속하게 전달하기 위해 필요한 매우 기본적인 라우팅 및 미들웨어 기능을 제공할 목적으로 Node 개발자가 사용 가능한 기술들을 알아야 한다.

## 간단한 정적 파일 서버 만들기
간단한 라우터나 정적 파일을 제공하기 위해 필요한 모든 기능들은 Node에 직접 내장되어있다.  간단한 정적 파일 서버를 만들기 위해 필요한 것을 생각해보면, 다음과 같은 일련의 단계를 내놓게 된다 :   
> 1. HTTP 서버를 생성하여 요청을 대기한다.   
> 2. 요청이 도달하면, 요청 URL을 파싱하여 파일의 위치를 결정한다.   
> 3. 파일이 존재하는지 확인한다.   
> 4.  파일이 존재하지 않으면, 적절하게 응답한다.
> 5.  파일이 존재하면, 파일을 읽기 위해 연다.
> 6.  응답 헤더를 준비한다.
> 7.  응답에 파일 내용을 쓴다.   
> 8.  다음 요청을 대기한다.   

HTTP 서버를 생성하기 파일을 읽기 위해서는 HTTP 모듈과 파일 시스템 모듈이 필요하다. Path 모듈도 있으면 편리할 텐데, 파일을 읽기 위해 열려고 시도하기 전에 파일이 존재하는지를 확인하기 위한 방법을 제공하기 때문이다. 뿐만 아니라, 기반 디렉터리에 해당하는 전역 변수를 정의하거나 사전에 정의된 \_dirname을 사용하기를 원할 것이다.   

위 내용에 따라, 애플리케이션의 맨 위쪽에는 다음과 같은 코드가 존재한다 :   
```python
var http = require('http'),
	path = require('path'),
	fs = require('fs'),
	base = '/home/examples/public_html';
```
애플리케이션은 HTTP 요청 개체의 url 속성에 접근하여 요청된 문서를 가져올 수 있다. 요청에 대한 응답을 재확인 하기위해, 요청된 파일의 경로명을 console.log로 출력할 것이다. 서버가 최초 시작될 때에도 console.log 메시지를 출력한다 :   
```python
http.createServer(function (req,res){
	pathname = base + req.url;
	console.log(pathname);
}).listen(8124);

console.log('Server running at 8124/');
```

애플리케이션은 파일을 읽기 위해 열고 HTTP 응답에 쓰는 것을 시도하기 전에 파일이 존재하는지를 먼저 확인해야 한다. **path.exists** 함수가 이 시점에서 올바른 선택이 된다. 파일이 존재하지 않으면, 그 결과에 대해 간략한 메시지를 쓰고 상태 코드를 `404 : document not found`로 설정한다 :   

```python
path.exists(pathname, function(exists){
	if (exists) {
		//요청 처리를 위한 코드를 삽입
	} else {
		res.writeHead(404);
		res.write('Bad request 404\n');
		res.end();
}
```
이전 예제에서는 파일 내용을 읽기 위해 fs.readFile을 사용 했다. 하지만 fs.readFile의 문제는 파일을 사용하기 위해 파일 전체를 메모리로 읽어야 한다는 것이다.   
웹 상에서 제공되는 문서는 상당히 크기가 클 수 있다. 뿐만 아니라, 특정 시점에 해당 문서에 대해 수많은 요청이 발생할 수 있다 fs.readFile로는 확장성에 대응하기 어렵다.   
> path.exists 메서드는  Node 0.8에서 사용이 금지되었으므로, 그 대신에 fs.exists를 사용하여야 한다.   

> [Node공식문서](https://nodejs.org/api/fs.html#fs_fs_exists_path_callback/)에 따르면 fs.exists도 금지되었기 때문에, fs.stat()나 fs.access()를 사용 해야 한다.

fs.readFile을 사용하는 대신, 애플리케이션은**fs.createReadStream** 메서드를 기본 설정으로 사용하여 읽기 스트림을 생성한다. 그 다음에는 파일 내용을 HTTP 응답 개체에 그대로 **pipe**시킬뿐이다. 스트림이 종료될 때 종료 신호를 전송하므로, 읽기 스트림에 대해 end 메서드 호출을 사용하지 않아도 된다 :   
```python
res.setHeader('Content-Type' , 'text/html');

//200 상태 - 발견됨, 오류 없음
res.statusCode = 200;

//읽기 가능 스트림을 생성하고 pipe시킴
var file = fs.createReadStream(pathname);
file.on("open", function(){
	file.pipe(res);
});
file.on("error", function(err){
	console.log(err);
});
```
읽기 스트림은 open과 error 라는 두 개의 흥미로운 이벤트를 가지고 있다. open 이벤트는 스트림이 준비되었을 때 전송되며, error 이벤트는 문제가 발생한 경우에 전송된다. 애플리케이션은 open 이벤트의 콜백 함수 내에서 pipe 메서드를 호출한다.

**예제 6-1** 간단한 정적 파일 웹 서버   
```python
var http = require('http'),
  fs = require('fs'),
  path - require('path'),
  base = '/home/examples/public_html';//windows에서는 절대경로를 지정하거나 .을 사용하여 실행중인 루트 디렉터리 하위의 상태경로를 지정해야 한다.

  http.createServer(function(req,res){
    pathname = base + req.url;
    console.log(pathname);

    path.exists(pathname, function(exists){
      if(!exists){
        res.writehead(404);
        res.write('Bad request 404\n');
        res.end();
      } else{
        res.setHeader('Content-Type', 'text/html');

        //200상태 - 발견됨, 오류 없음
        res.statusCode = 200;

        //읽기 가능 스트림을 생성하고 pipe 시킴
        var file = fs.createWriteStream(pathname);
        file.on("open",function(){
          file.pipe(res);
        });
        file.on("error",function(err){
          console.log(err);
        });
      }
    });
  }).listen(8124);

  console.log("Server running at 8124/");
```
