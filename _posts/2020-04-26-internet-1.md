---
title: "[Internet] 생활코딩-internet 1"
layout: single
author_profile: true
read_time: true
comments: true
share: true
related: true
categories:
- 생활코딩
- 인터넷
- TIL
- 도메인
tags:
- 생활코딩
- 인터넷
- TIL
- 도메인
---

# IP란
인터넷에 연결되어 있는 장치(컴퓨터,스마트폰,태플릿, 서버 등등)들은 **각각의 장치를 식별할 수 있는 주소**를 가지고있는데 이를 **ip**라고한다.   
예) 115.68.24.88, 192.168,0.1   

# 도메인(domain)이란
ip는 사람이 이해하고 기억하기 어렵기 때문에 이를 위해서**각 ip에 이름을 부여**할 수 있게 했는데, 이를 **도메인**이라고 한다.   
* opentutorials.org -> 115.68.24.88   
* naver.com -> 220.95.233.172   
* daum.net -> 114.108.157.19   
<br/>
<img width="798" alt="스크린샷 2020-04-26 오후 7 22 51" src="https://user-images.githubusercontent.com/42554237/80304828-92ec8c00-87f3-11ea-9ff4-cadb644df1c8.png">

내컴퓨터가 opentutorials.org에 접속한적이 없다면 **->** **네임서버**에 opentutorials.org의 ip를 요청해서 받음 **->** 해당 ip로 접속해서 요청과 응답을 받음

# 도메인의 구성요소
도메인은 **컴퓨터의 이름**과 **최상위 도메인**으로 구성되어 있다.   
예)   
**opentutorials.org**   
opentutorial : 컴퓨터의 이름   
org: 최상위 도메인(org는 비영리단체를 나타냄)   
   
**daum.co.kr**   
daum : 컴퓨터의 이름   
co: 국가 형태의 최상위 도메인을 의미   
kr: 대한민국의 NIC에서 관리하는 도메인을 의미   
   
# URL의 이해
**URL은 도메인 + 경로**   
예 ) http://opentutorials.org/user/module/121/298.png 의 경우   

* 도메인 : opentutorials.org   
* url : http://opentutorials.org/user/module/121/298.png