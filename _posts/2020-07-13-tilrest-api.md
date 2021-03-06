---
title: "[TIL]Rest api"
layout: single
author_profile: true
read_time: true
comments: true
share: true
related: true
categories:
- Rest API
- TIL
tags:
- Rest API
- TIL
---

# API 서버 
웹 API는 다른 웹 서비스의 기능을 사용하거나 자원을 가져올 수 있는 창구라고 할 수있다. 흔히 API를 열었다 또는 만들었다고 표현하는데, 이는 다른 프로그램에서 현재 기능을 사용할 수 있게 허용하였음을 뜻한다. 다른 사람에게 정보를 제공하고 싶은 부분만 API를 열어놓고, 제공하고 싶지 않은 부분은 API를 만들지 않는 것이다. 또한, API를 열어 놓았다 하더라도 모든 사람이 정보를 가져갈 수 있는 것이 아니라 인증된 사람만 일정 횟수 내에서 가져가게 제한을 둘 수도 있다.   
서버에 API를 올려서 URL을 통해 접근할 수 있게 만든 것을 **웹 API 서버** 라고 한다.

# Rest api

**api** : 소프트웨어가 다른 소프트웨어로 부터 지정된 형식으로 요청, 명령을 받을 수 있는 수단으로, 기능을 사용할 수 있게 해주는 접점을 의미한다.     
				
예) 가까이는 키보드와 마우스도 api 이고, 자판기의 버튼 같은 역할을 하는 것이 api이다.   

* Rest는 HTTP 기반으로 필요한 자원에 접근하는 방식을 정해놓은 네트워크 아키텍쳐 스타일.
		 (자원에는 저장된 데이터는 물론, 이미지/동영상/문서와 같은 파일, 서비스(이메일 전송, 푸쉬 메시지 등) 등을 모두 포함)   
* 아키텍쳐 스타일 : 제약조건의 집합   
* 요청 주소만으로 용도를 알 수 있어야한다.   

# Rest의 제약 조건
제약 조건들을 준수하는 웹서비스를 ' **RESTful** 하다' 라고 한다.
* **클라이언트/서버** : 클라이언트와 서버 각각의 역할이 구분되어야 한다.    서버는 API를 제공하고, API 요청 시 비지니스 로직 처리와 데이터 저장을 책임진다.    
클라이언트는 사용자 인증, 상태(세션,로그인 정보)관리와 서버 리소스 요청을 책임지는 구조로 역할을 구분한다.(상호 의존성을 줄임)   
* **무상태(Stateless)** : Rest 서버는 작업을 위한 상태정보(세션,쿠키)를 관리하지 않아야 한다. 시스템 영향없이 관리 및 업데이트가 가능해야한다.   
* **캐쉬(Cacheable)** : HTTP 웹 표준으로 HTTP가 가진 캐싱 기능이 적용된다.
* **계층화(Layered System)** : 서버를 다중 계층으로 구성할 수 있다. 비지니스 로직을 수행하는 API 서버와 그 앞단에 사용자 인증, 암호화, 로드밸런싱 등의 계층을 추가해 구조상의 유연성을 제공한다.   
* **인터페이스 일관성(Uniform Interface)** : 아키텍쳐를 단순화하고 분리해 각 부분을 독립적으로 발전시킬 수 있다.   
		
	<img width="650" alt="스크린샷 2020-07-14 오전 12 09 17" src="https://user-images.githubusercontent.com/42554237/87331673-53714880-c575-11ea-8a61-74095a084ebb.png">


	**Rest api** : Rest 기반으로 서비스 API를 구현한 것을 Rest api라고 한다.   
	최근 OpenAPI(누구나 사용할 수 있도록 공개된 api: 구글 맵, 공공 데이터 등). 마이크로 서비스(하나의 큰 애플리케이션을 여러개의 작은 애플리케이션으로 쪼개어 변경과 조합이 가능 하도록 만든 아키텍쳐) 등을 제공하는 서비스 대부분은 Rest api를 제공한다.   
	
	Rest는 HTTP 표준을 기반으로 구현하므로, HTTP를 지원하는 프로그램 언어로 클라이언트, 서버를 구현할 수 있다.   
	즉, Rest api를 제작하면 델파이 클라이언트 뿐 아니라, 자바, C#, 웹 등을 이용해 클라이언트를 제작할 수 있고, 그 반대 방법도 가능하다.      
	   
	서버에 Rest api로 요청을 보낼 때는 http를 따른다.    
# 	Rest 주요 구성요소
* **리소스** : 접근할 대상, URI를  통해 식별    
* **메소드**: 리소스에 대한 행위, 표준 HTTP 메소드에 따라 자원에 접근(생성, 조회, 수정, 삭제)    
* **메시지** : HTTP 헤더와 바디에 포함된 메시지는 메시지를 처리하기 위한 충분한 정보를 포함    
	
	=> 즉, Rest는 어떤 자원(리소스)에 어떤 행위(메소드)를 어떻게(메시지)할 지 HTTP 기반으로 정해놓은 아키텍쳐.   
	
## 	리소스
리소스는 URI를 통해 정의한다.   

<img width="864" alt="스크린샷 2020-07-14 오전 1 28 39" src="https://user-images.githubusercontent.com/42554237/87331706-64ba5500-c575-11ea-96ae-ce038de986e3.png">
	

## 메소드
Rest에서는 메소드를 통해 리소스에 대한 행위를 정의한다.   
>**메소드 : get, post, put, patch, delete**   
> 	**get** : 조회(Read)   
> 	**post**:추가(Create)   
> 	**put**, **patch** : put은 정보를 통째로 변경할때, patch는 특정 정보만 변경할때 사용(Update)   
> 	**delete**: 삭제   
	   
## 메시지
Rest에서 자원에 대한 정보는 HTTP 바디와 HTTP 헤더, 응답 상태코드를 활용한다.   

**HTTP 바디**   
HTTP 바디에 포함된 데이터를 통해 자원에 대한 정보를 전달한다.   
데이터 포맷으로는 최근 JSON을 많이 사용하며, XML과 사용자 정의 포맷 등을 정해서 사용할 수도 있다.   

조회(get) 요청 시 서버는 조건에 맞는 정보를 HTTP 바디에 담아 클라이언트에 응답한다.   
생성(post),수정(put,patch) 요청 시 클라이언트는 자원에 대한 정보를 HTTP 바디에 담아 서버에 요청한다.   

**HTTP 헤더**   
HTTP 헤더에는 HTTP 바디의 컨텐츠 종류를 명시할 수 있다.   
헤더에 정의된 컨텐츠 타입에 따라 데이터를 분석하도록 구현해야 한다.   
요청 HTTP 헤더는 "Accept" 항목으로, 응답 HTTP 헤더는 "Content-type"으로 컨텐츠 타입을 설명한다.   
예)   
* application/json   
* application/xml   
* text/plain   
* image/jpeg   
* image/png   

**HTTP 응답 상태 코드**   
리소스 요청에 대한 응답은 응답 상태코드로 설명할 수 있다.   

* 	200 - 정상 수행   
* 	201 - 리소스 생성 요청 성공(post로 생성 요청시에 한함)   
* 	400 번대 - 정상 수행 x   


**self-descriptive message**   
	: 메세지는 스스로를 설명해야한다.   
	
**HATEOAS**   
	: 애플리케이션의 상태는 Hyperlink를 이용해 전이되어야 한다.   
		
# 왜 Uniform Interface ?   
		
**독립적 진화**   
서버와 클라이언트가 각각 독립적으로 진화한다.   
서버의 기능이 변경되어도 클라이언트를 업데이트할 필요가 없다.   
REST를 만들게 된 계기: "**How do i improve HTTP without breaking the Web ?**"   
		   
**웹**
* 	웹 페이지를 변경했다고 웹 브라우저를업데이트할 필요는 없다.   
* 	웹 브라우저를 업데이트했다고 웹 페이지를 변경할 필요는 없다.   
* 	HTTP 명세가 변경되어도 웹은 잘 동작한다.   
* 	HTML 명세가 변경되어도 웹은 잘 동작한다.   
		   
# REST가 웹의 독립적 진화에 도움을 주었나 ?   
* HTTP에 지속적으로 영향을 줌   
* HOST 헤더 추가   
* 길이 제한을 다루는 방법 명시(414 URI Too Long 등)   
* URI에서 리소스의 정의가 추상적으로 변경 됨: "식별하고자 하는 무언가"   
* 기타 HTTP와 URI에 많은 영향을 줌.   
* HTTP/1.1 명세 최신판에서 REST에 대한 언급이 들어감   
* Reminder:ROY T.Fielding이 HTTP와 URI 명세의 저자 중 한명이다.   

# 그런데 Self-descriptive와 HATEOAS가 독립적 진화에 어떻게 도움이 될까?   

**Self-descriptive (확장 가능한 커뮤니케이션)**   
	: 서버나 클라이언트가 변경되더라도 오고가는 메시지는 언제나 self-descriptive하므로 언제나 해석이 가능하다.   

**방법1. Media type**      
* 	미디어타입을 하나 정의한다.   
* 	미디어 타입 문서를 작성한다. 이 문서에 "id"가 뭐고 "title"이 뭔지를 의미한다.   
* 	IANA에 미디어 타입을 등록한다. 이 때 만든 문서를 미디어 타입의 명세로 등록한다.   
* 	이제 이 메시지를 보는 사람은 명세를 찾아갈 수 있으므로 이 메시지의 의미를 온전히 해석할 수 있다.   

**방법2. Profile**   
	: Link에 < url> ; rel="profile"   


**HATEOAS (애플리케이션 상태전이의 late binding)**   
어디서 어디로 전이가 가능한지 미리 결정되지 않는다.    
어떤 상태로 전이가 완료되고 나서야 그 다음 전이도리 수 있는 상태가 결정된다.      
=> 링크는 동적으로 변경될 수 있다.    

**방법1. data로**   
data에 다양한 방법으로 하이퍼링크를 표현한다.   

**방법2. HTTP 헤더로**   
Link, Location 등의 헤더로 링크를 표현한다.   
   
**결국 HATEOAS는 data와 헤더 모두 활용하면 좋다.**   

**Hyperlink는 반드시 uri 여야하는가?**  하이퍼링크라는 것만 표현하면 된다.   
**Media type의 (IANA) 등록은 필수인가?**  상관없다.
