---
title: "[Nodejs-express] 생활코딩"
layout: single
author_profile: true
read_time: true
comments: true
share: true
related: true
categories:
- Nodejs
- JavaScript
- Express
- 생활코딩
- TIL
tags:
- Nodejs
- JavaScript
- Express
- 생활코딩
- TIL
---

# Express 설치  


**npm을 이용한 express 설치** 

> npm install express —save

**기본사용법** 
```

const express = require(‘express’ )
const app = express()

app.get(‘/‘,(requires) => res.send(‘Helle World!’))

app.listen(3000, () => console.log(‘Example app listening on port 3000!’))
```


Express 가 제공하는 라우팅 방식은 처리하는 부분(create,update,delete 마다)에 req,res를 각각 구현하기때문에 코드가 훨씬 보기쉽다

# Clean URL로 Route를 구현
Simentic url, Pretty URL 이라고도 함.  
질의어 없이 경로만 가진 간단한 구조의 URL. 깔끔하지 않은 QueryString에 비해 기억하기 쉽고, 입력하기 쉽다는게 장점이다. 따라서 사용성과 접근성이 높아진다.  (검색엔진 에 최적화 하기위해 사용한다고 하는데 검색엔진 최적화에대한 지식이 1도없어서 어떻게 관련이 있는건지는 잘 모르겠다.)

sementic URL로 전달된 값은 request 객체의 params에 담긴다.

`/:pageId` 형식으로 앞에 :(콜론)을 붙여서 넘기면,  
`request.params.pageId` 로 접근할 수 있다.

# Express redirection 기능
**문법**  
> response.redirect(‘/경로’);

Nodejs와 express 의 연동은 미들웨어를 사용하는 것이 핵심이다.   이는 express의 정의를 보면 알 수 있는데, Express는 자체적은 최소한의 기능을 갖춘 라우팅 및 미들웨어 웹 프레임워크이며, Express 애플리케이션은 기본적으로 일련의 미들웨어 함수 호출이다.
미들웨어 함수는 요청 객체(request), 응답 객체(response), 그리고 그 다음의 미들웨어 함수에 대한 액세스 권한을 갖는 함수인 next()가 있다.
  
미들웨어 함수는 
* 모든 코드를 실행
* 요청 및 응답 객체에 대한 변경을 실행
* 요청-응답 주기를 종료
* stack 내의 그 다음 미들웨어 함수를 호출
  
	하는 태스크를 수행할 수 있다.
	
	  
* 	[애플리케이션 레벨 미들웨어](http://https://expressjs.com/ko/guide/using-middleware.html#middleware.application)
* 	[라우터 레벨 미들웨어](http:/https://expressjs.com/ko/guide/using-middleware.html#middleware.router/)
* 	[오류 처리 미들웨어](http://https://expressjs.com/ko/guide/using-middleware.html#middleware.error-handling)
* 	[기본 제공 미들웨어](http://https://expressjs.com/ko/guide/using-middleware.html#middleware.built-in)
* 	[써드파티 미들웨어](http://https://expressjs.com/ko/guide/using-middleware.html#middleware.third-party)

등의 유형이 있는데 차후 공부할 내용.  

**사용한 미들웨어**  

> body-parser
웹 브라우저에서 서버로 요청한 데이터 본체를 body라고함.  
body를 설명하는 데이터를 header.  

사용자의 요청이 있을때마다
`app.use(bodyParser.urlencoded({extended: false}));`
가 실행 됨.  
  
내부적으로 사용자가 전송한 post 데이터의 body를 분석해서 필요한 형태로 가공해 줌  
  
# 데이터 압축하기

```app.use(compression()); //compression()은 미들웨어를 리턴해서 app.use에 장착```
웹서버가 웹브라우저에 응답할때 데이터의 용량이 크면 데이터를 압축을 할 수 있다.    
압축을하고 푸는것이 네트워크 전송 비용보다 싸고 빠르다.    
Response headers에  Content-Encoding을 보면 gzip으로 되있음.  
  
크롬 - 개발자도구 -네트워크 탭
에서 데이터 용량을 볼 수 있다.(Size)
  
**Cmd +shift+R** => 캐시가 삭제 됨( 강제 re-load)
  
# 미들웨어의 실행 순서

Application-level middleware가 제일 중요하다.  
Req,res를 받아서 변형할 수 있다.  
next를 호출받아 다음 미들웨어를 실행할지 안할지를 결정할 수 있다.( 해당 미들웨어의 이전 미들웨어가 결정한다.)  
경로를 줘서 특정 경로에서만 미들웨어를 사용할 수 있다.
인자로 함수를 연속적으로 줌으로써 미들웨어 여러개를 붙일 수 있다.  
**next()** 는 다음 인자를 가리킴.  
**next()** 안에 인자를 주면 순서를 제어할 수 있다.  
**next(‘route’)** 는 다음 route의 middle ware를 실행시킨다.    
  
미들웨어를 잘 설계하면 애플리케이션이 실행되는 흐름을 쉽게 제어할 수 있다.  
  
# 정적인 파일의 서비스
`app.use(express.static('public'));`

# 에러처리방법

없는 페이지를 찾을 때,
```
app.use(function(req,res,next){
  res.status(404).send('Sorry cant find that !');
})
```

에러가 있는경우 next()에 인자를주면, 그 인자의 값이 있는경우 nodejs는 인자가 4개인 함수가 등록되어있는 미들웨어를 호출하도록 약속 되어있다.