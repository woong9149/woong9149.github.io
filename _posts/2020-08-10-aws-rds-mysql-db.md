---
title: "[AWS] RDS 서비스를 이용하여 MySQL DB 구축하기"
layout: single
author_profile: true
read_time: true
comments: true
share: true
related: true
---

#  Amazon RDS(Amazon Relational Database Service)

* 아마존 관계형 데이터베이스 서비스로, 아마존 웹 서비스가 서비스하는 분산 관계형 데이터베이스이다.   
* 관계형 데이터베이스의 설정이 설계된 클라우드 내에서 동작하는 웹서비스이므로, DB소프트웨어를 패치하거나 백업, 복구에 관한 복잡한 프로세스를 자동으로 관리할 수 있다.
* 스토리지와 연산 자원들을 스케일링 하는 것은 하나의 API 호출로 수행할 수 있다.
<br/>
(개발, 테스트 목적으로 AWS를 이용할 때는 **AWS Cost Exploer**를 자주 들어가서 비용을 확인하는 것이 좋다)
<br/>

나는 운이 좋게도 **AWS EDUCATE** 계정으로 테스트를 했다.