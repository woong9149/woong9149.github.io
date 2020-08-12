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

## RDS 생성방법
**1. AWS 로그인 후 콘솔로 접속해서 RDS를 검색한다.**   
<img width="1280" alt="스크린샷 2020-08-11 오후 5 53 46" src="https://user-images.githubusercontent.com/42554237/89910163-dea03580-dc2a-11ea-86c0-bb2045ff323b.png">

**2. '데이터베이스 성생하기' 를 클릭 한다.**   
<img width="1254" alt="스크린샷 2020-08-11 오후 11 15 53" src="https://user-images.githubusercontent.com/42554237/89910166-df38cc00-dc2a-11ea-89cf-81965d961e2f.png">

**3. 데이터베이트 엔진과 사양,설정들을 선택한다.**   
<img width="1254" alt="스크린샷 2020-08-11 오후 11 16 37" src="https://user-images.githubusercontent.com/42554237/89910169-dfd16280-dc2a-11ea-8444-5d124824c2e0.png">

'설정'에 있는 항목들은 이후 애플리케이션과 연동하거나 DB Tool과 연동할 때 필요한 정보들이다.   
<img width="1254" alt="스크린샷 2020-08-11 오후 11 17 00" src="https://user-images.githubusercontent.com/42554237/89910172-dfd16280-dc2a-11ea-87cc-8cee51eccbfe.png">
   
<img width="1254" alt="스크린샷 2020-08-11 오후 11 17 45" src="https://user-images.githubusercontent.com/42554237/89911198-19ef3400-dc2c-11ea-95e8-b44197da7198.png">

**4. 설정 완료 후 '데이터베이스 생성하기' 버튼을 클릭한다.**   
<img width="773" alt="스크린샷 2020-08-11 오후 11 19 35" src="https://user-images.githubusercontent.com/42554237/89911209-1e1b5180-dc2c-11ea-9aae-b5a850f3aecf.png">

**5.데이터 베이스가 생성 되는 동안, MySQL에서 한글을 사용하기 위한 파라미터 설정을 해준다.**   
<img width="773" alt="스크린샷 2020-08-11 오후 11 20 19" src="https://user-images.githubusercontent.com/42554237/89911214-1f4c7e80-dc2c-11ea-9159-4895ed0c0002.png">

**6.파라미터 그룹을 생성하고, 'char' 와 'collation'을 검색해 인코딩 설정을 할 수 있는 항목은 모두 'utf8'로 설정 해준다.(collation은 utf8_general_ci)**   
<img width="773" alt="스크린샷 2020-08-11 오후 11 21 06" src="https://user-images.githubusercontent.com/42554237/89911216-1fe51500-dc2c-11ea-807c-231b52ca64e7.png">

**7. 설정을 저장한 후 DB 인스턴스로 가서, 생성해놓은 DB 식별자를 클릭한다.**   
<img width="644" alt="스크린샷 2020-08-11 오후 11 58 21" src="https://user-images.githubusercontent.com/42554237/89913703-fda0c680-dc2e-11ea-95cc-e16ace44a02c.png">

**8. '수정'으로 들어가 'DB 파라미터 그룹'을 생성해 놓은 파라미터 그룹으로 설정한다.**   
<img width="644" alt="스크린샷 2020-08-11 오후 11 59 15" src="https://user-images.githubusercontent.com/42554237/89913715-01cce400-dc2f-11ea-90ac-4d02d32a5403.png">
   
<img width="644" alt="스크린샷 2020-08-12 오전 12 00 28" src="https://user-images.githubusercontent.com/42554237/89913721-02657a80-dc2f-11ea-9811-028164daa8b8.png">
   
<img width="644" alt="스크린샷 2020-08-12 오전 12 00 35" src="https://user-images.githubusercontent.com/42554237/89913722-02fe1100-dc2f-11ea-8034-dca7b1608c3e.png">

## DataGrip 과 연동
**1. data source를 MySql로 선택 한다.**   
**2. host에는 DB의 접속 URL를 넣는다.**   
<img width="803" alt="스크린샷 2020-08-12 오전 12 06 45" src="https://user-images.githubusercontent.com/42554237/89915045-d3e89f00-dc30-11ea-9f24-17ca26ae7610.png">

* DB 접속 URL은 AWS 콘솔에 있는 엔드포인트를 입력 한다.   
* user와 password 는 DB를 생성할 때 설정 값으로 입력했던 값들을 입력 한다.   
<img width="637" alt="스크린샷 2020-08-12 오전 12 07 19" src="https://user-images.githubusercontent.com/42554237/89915063-d814bc80-dc30-11ea-9ec4-ee354708416f.png">

**3. Test Connection 을 누른 후 아래와 같이 나오면 연결 성공 !**     
<img width="560" alt="스크린샷 2020-08-12 오전 12 14 11" src="https://user-images.githubusercontent.com/42554237/89915061-d77c2600-dc30-11ea-9caa-8d37a2ed9a19.png">


## RDS vs EC2
EC2에 DB를 설치해서 사용하는 것과 RDS를 사용하는 것이 어떻게 다른 것인지 생각해 볼 필요가 있다.   
**RDS는** DB의 설정, 운영, 백업 등의 기능이 자동으로 가능한 DB 전용 서버이다.   
반면에 **EC2에** 직접 DB를 설치해서 사용하는 것은 DB의 설정, 운영, 백업과 복구 등을 직접 해야 하는 수고스러움이 있다.   
EC2가 가격이 비교적 저렴하고, 사용자의 요구에 맞게 커스터마이징을 하기에는 EC2가 더 적합할 수 있겠다. 하지만 편리하게 DB를 관리하고 스케일링까지 생각한다면 RDS를 고려해보는 것도 괜찮을 것 같다.