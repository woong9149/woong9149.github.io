---
layout: single
author_profile: true
read_time: true
comments: true
share: true
related: true
title: "[TIL]Rest api"
---

# Rest api

검색 키워드 ==>’그런 rest api로 괜찮은가’

api : 소프트웨어가 다른 소프트웨어로 부터 지정된 형식으로 요청, 명령을 받을 수 있는 수단 

Rest api : rest는 형식. 
		요청 주소만으로 용도를 알 수 있어야한다.
Rest :  분산 하이퍼미디어 시스템(예: 웹) 을 위한 아키텍쳐 스타일
	아키텍쳐 스타일 : 제약조건의 집합

		
서버에 Rest api로 요청을 보낼 때는 http를 따른다.
메소드 : get, post put, patch, delete
	get : 조회
	post: create 추가
	put, patch : put은 정보를 통째로 변경할때, patch는 특정 정보만 변경할때 사용
	delete