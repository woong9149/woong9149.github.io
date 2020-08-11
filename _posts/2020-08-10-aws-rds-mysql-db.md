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

## RDS vs EC2
EC2에 DB를 설치해서 사용하는 것과 RDS를 사용하는 것이 어떻게 다른 것인지 생각해 볼 필요가 있다.   
**RDS는** DB의 설정, 운영, 백업 등의 기능이 자동으로 가능한 DB 전용 서버이다.   
반면에 **EC2에** 직접 DB를 설치해서 사용하는 것은 DB의 설정, 운영, 백업과 복구 등을 직접 해야 하는 수고스러움이 있다.   
EC2가 가격이 비교적 저렴하고, 사용자의 요구에 맞게 커스터마이징을 하기에는 EC2가 더 적합할 수 있겠다. 하지만 편리하게 DB를 관리하고 스케일링까지 생각한다면 RDS를 고려해보는 것도 괜찮을 것 같다.