---
title: "[MySQL] mysqldump를 이용한 db백업(feat.crontab)"
layout: single
author_profile: true
read_time: true
comments: true
share: true
related: true
categories:
- mysql
- 데이터베이스
- mysqldump
- db백업
- TIL
tags:
- 데이터베이스
- mysql
- mysqldump
- db백업
- TIL
---

# mysqldump
**특징**   
* 적은량의 데이터를 백업할 때 저장하다.   
* 특정 정보나 형식을 골라서 백업받을 수 있다.   
* DB설치 시 기본으로 포함되어있다.   

실제로 압축해서 백업한 파일의 크키가 1GB가 넘었을때, 9분이 넘는 시간이 소요되는 것을 확인했다...그리고 백업중일  때 전체 서비스의 속도가 현저하게 느려지거나 데이터베이스를 호출에 실패하는 경우도 있었다.  

MySQL(MariaDB)을 백업하는 방법에 있어서 **mysqldump** 명령어를 사용해 **수동으로 백업파일을 생성** 하는 것과    
**쉘스크립트**를 만들어 **crontab**을 이용해 **주기적으로 백업**을 수행하도록 하는  방법을 다룬다.   

# 수동으로 백업파일을 생성

mysqldump를 사용해서 db 백업파일을 만드는 방법은 백업하는 범위에 따라 3가지로 구분된다.
* 전체 데이터베이스를 백업하는 방법   
* 특정 데이터베이스를 백업하는 방법   
* 특정 데이터베이스의 특정 테이블을 백업하는 방법   
   
<br/>	 
**기본사용법**   
> mysqldump -u[아이디] -p[패스워드] > [저장파일명].sql    

<br/>
mysqldump에 사용하는 계정은 select, show view, trigger, lock tables, file, create, alter database 등   의 권한이 있어야 되기때문에 주로 **root** 계정으로 실행한다.   

<br/>
**전체 데이터베이스를 백업하는 방법**   
> mysqldump -u[아이디] -p[패스워드] -A > backup_full.sql 

<br/>
**특정 데이터베이스를 백업하는 방법**   
> mysqldump -u[아이디] -p[패스워드] --database=dbName > backup_dbName.sql  

<br/>
**특정 데이터베이스의 특정 테이블을 백업하는 방법**   
> mysqldump -u[아이디] -p[패스워드] dbName tbName > backup_dbName_tbName.sql  
 
백업파일의 확장자는 **.sql** , **.gz(압축파일)** 등이 가능하다.   
이외에도 특정테이블의 특정값, 특정 데이터베이스의 DDL 등 원하는 것만 백업하는 것도 가능하다.   


# 주기적으로 백업파일을 생성
데이터베이스의 안정성은 백업파일을 만드는 방법과 서버의 이중화, 클라우드 서버 인스턴스의 복제 등 여러각도로 구현할 수 있으며 mysqldump로 백업파일을 만들경우 , 언제 어떤 이슈로인해 데이버테이스에 망실이 일어날지 모르기때문에 주기적으로 백업파일을 만들어 놓는 것이 좋다. 그러기 위해서 **쉘스크립트**와 **crontab**을 이용한다.

mysql이 설치된 곳에서, 백업파일이 저장될 폴더를 생성한 후 쉘스크립트(.sh)를 생성한다.   

**dbbackup.sh**
```python
#!/bin/bash

DB_BACKUP="백업파일을 저장할 경로"

DB_USER="db아이디"

DB_PASSWD="db패스워드"

db="db이름"

table="table이름"



# Remove backups older than 1 days

find $DB_BACKUP -ctime +1 -exec rm -f {} \;


# 데이터베이스를 모두 백업할경우
mysqldump --user=$DB_USER --password=$DB_PASSWD --lock-all-tables --databases $db | gzip > "$DB_BACKUP/mysqldump-$db-$(date +%Y-%m-%d-%H).gz";
~                                                                                                                                              
```

crontab 설정을 하기전에, 쉘 스크립트가 정상작동을하는지 실행해서 테스트를 해봐야한다.
(쉘스크립트를 실행하는 명령어는 bash dbbackup.sh , . dbbackup.sh , dbbackup.sh 등...)