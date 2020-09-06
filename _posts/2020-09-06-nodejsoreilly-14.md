---
title: "[Nodejs]O'REILLY 정리 - 14"
layout: single
author_profile: true
read_time: true
comments: true
share: true
related: true
---

# Node 모듈 시스템
Node의 기본 구현은 최대한 간결함을 유지하고 있다. Node 개발자들은 사용 가능한 모든 구성 요소들을 Node내에 포함시키는 대신, Node의 모듈을 통해 추가 기능들을 제공하고 있다.   
Node의 모듈 시스템은 상호 운용이 가능하도록 모듈을 생성하는 방법인 **CommonJS 모듈 시스템**을 본 따 만들어졌다. 모듈 시스템의 핵심은 자신의 모듈이 다른 모듈들과 잘 동작하도록 보장하기 위해 개발자들이 준수해야 하는 계약이다.   

CommonJS 모듈 시스템 요구사항 중에서 Node에서 구현된 것은 다음과 같다 :   
* 모듈 식별자를 받아서 노출된 API를 반환하는 require 함수에 대한 지원 포함
* 모듈 명은 캐릭터 문자열이며, 경로 식별을 위해 /를 포함할 수 있음
* 모듈은 모듈 외부로 노출될 항목을 명시적으로 내보내기 해야 함
* 변수는 모듈에 대해 private임