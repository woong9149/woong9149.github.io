---
title: "[MySQL] 임시테이블 생성 (feat.WITH절)"
layout: single
author_profile: true
read_time: true
comments: true
share: true
related: true
categories:
- mysql
- database
- 임시 테이블
- with
- recursive
tags:
- mysql
- database
- 임시 테이블
- with
- recursive
---

with - 임시 테이블 생성
with recursive - 임시테이블을 서브쿼리안에서 사용

with절은 동일한 SQL이 반복되어 사용될 때 성능을 높이기 위해 사용된다.(장시간 걸리는 쿼리의 결과를 임시테이블에 저장해놓고 저장해놓은 데이터를 엑세스 하기때문에 성능이 향상됨)
임시 테이블을 생성해서 필요한 데이터를 저장해서 사용한다.

문법
WITH temp AS (서브쿼리)
임시 테이블을 이용해 사용할 쿼리

동시에 여러개의 with절이 실행되어 임시테이블이 견딜 수 있는 수준이 넘어가면 모두 느려짐.