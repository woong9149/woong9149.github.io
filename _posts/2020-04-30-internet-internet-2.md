---
title: "[Internet] 생활코딩-internet 2"
layout: single
author_profile: true
read_time: true
comments: true
share: true
related: true
categories:
- 네임서버
- 포워딩
- 생활코딩
- TIL
tags:
- 네임서버
- 포워딩
- 생활코딩
- TIL
---

# (도메인)포워딩
도메인으로 접근한 사용자를 다른 도메인으로 보내는 행위   
예) ooo2.org로 사용자가 접근하면 opentutorials.org로 보냄   

보통 호스트를 사용할 수 없을 때 사용한다.

**호스트 설정과 포워딩의 차이점**   
호스트 설정은 도메인과 IP를 연결시키는 것이지만, 포워딩은 다른 도메인으로 보내는 것이기 때문에 포워딩한 사용자의 URL이 변경된다.   

생활코딩에서는  명확하게 지칭하지는 않았지만, 포워딩중에서도 네임서버를 설정하는 **도메인 포워딩**만을 말한다.   

사용자가 opentutorials.org로 접속하려고 할때,    
* opentutorials.com
* opentutorials.co.kr
* opentutorial.org   

등 처럼, 주소가 헷갈려 잘 못 입력했을 때도 opentutorials.org로 접속할 수 있게 하기위한 목적으로 사용되는 것 같다. 결과적으로 opentutorials.org로 **접속할수 있는 통로가 넓어지는 셈**이다.   
   
> 포트포워딩과는 목적과 방식이 전혀 다르다   

# 네임서버
도메인에 해당하는 ip를 알려주는 서비스   

**네임서버의 역할과 동작**   
<img width="760" alt="스크린샷 2020-04-30 오후 10 11 07" src="https://user-images.githubusercontent.com/42554237/80714426-e3375700-8b2f-11ea-9cb5-9d7a48ae5941.png">
'내컴퓨터' 가 opentutorials.org로 접속하려고 할 때, 최초로 접속하는 경우에는 '내컴퓨터'와 연결되어 있는 네임서버(ISP에서 제공)에 opentutorials.org의 IP를 요청한다.    IP를 응답받으면 해당 IP에 해당하는 컴퓨터로 접속해 웹페이지나 파일 등 여러 요청을 보낸다.

> '인터넷 대란'의 교훈에서 알 수 있듯이,  네임서버는 **신뢰성**이 중요하다.