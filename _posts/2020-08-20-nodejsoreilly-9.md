---
title: "[Nodejs]O'REILLY 정리 - 9"
layout: single
author_profile: true
read_time: true
comments: true
share: true
related: true
---

# 서버, 스트림, 소켓
Node 코어 API 중 상당수는 특정 유형의 통신을 수신하는 서비스를 만드는 것과 관련되어 있다. HTTP 웹서버를 만들기 위해 **HTTP 모듈**을 사용하고, 다른 메서드로는 **TCP 서버**, **TLS**(Transport Layer Security)**서버**, **UDP**(User Datagram Protocol)**/데이터그램 소켓**을 만들 수 있다.    

**소켓(socket)** 은 통신에서의 끝점(endpoint)이며, **네트워크 소켓**은 네트워크 상의 두 대의 다른 컴퓨터에서 실행되는 애플리케이션 간의 통신에서의 끝점이다. 소켓 간에 흘러가는 데이터는 **스트림(stream)** 이라고 한다. 스트림 내의 데이터는 버퍼 내의 바이너리 데이터나 유니코드로 된 문자열로 전송될 수 있다. 두 데이터 유형 모두 **패킷(packet)** 으로 전송되는데, 패킷은 특정한 크기 조각으로 분할된 데이터의 일부를 말한다. 특별한 유형의 패킷으로 **완료 패킷(FIN, finish packet)** 이 있는데, 전송이 종료되었다는 것을 알리기 위해 소켓이 전송하는 것이다. 통신이 관리되는 방식과 스트림의 신뢰도는 생성되는 소켓의 종류에 달려있다.