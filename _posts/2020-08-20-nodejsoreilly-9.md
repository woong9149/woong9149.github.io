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

# TCP 소켓과 서버
Node의 **Net 모듈**을 사용하면 기본적인 TCP 서버 및 클라이언트를 만들 수 있다. **TCP는 웹 서비스와 이메일 같은 대부분의 인터넷 애플리케이션의 근간**을 이루고 있다. TCP는 **클라이언트와 서비스 소켓 간에 신뢰성 있게 데이터를 전송하는 방법을 제공**한다.   
TCP 서버를 만드는 것은 HTTP 서버를 만드는 것과는 약간 다른데, 콜백 함수를 넘겨서 서버를 만들게 된다. TCP 서버는 requestListner를 넘기는 대신 들어오는 연결을 수신하는 소켓의 인스턴스를 TCP 콜백 함수의 유일한 인수로 받는다는 점에서 HTTP 서버와는 차이가 있다.   
예제3-2 **8124 포트로 클라이언트 통신을 수신하는 소켓을 가진 간단한 TCP 서버** 
```python
var net = require('net');

var server = net.createServer(function(conn){
    console.log('connected');

    conn.on('data', function(data){
        console.log(data + ' from ' + conn.remoteAddress + '' + conn.remotePort);
        conn.write('Repeating: '+data);
    });
    
    conn.on('close', function(){
        console.log('cliend closed connection');
    });

}) .listen(8124);

console.log('listening on port 8124');
```
예제 3-2는 TCP 서버를 생성하는 코드다. 서버 소켓이 생성되고 나면 데이터를 받을 때와 클라이언트가 연결을 닫을 때에 대한 두 개의 이벤트를 수신하게 된다.   

createServer에는 선택적 매개변수인 **allowHalfOpen**이 있다. 이 매개변수를 **true**로 설정하면 클라이언트로부터 FIN 패킷을 받더라도 소켓이 FIN을 보내지 않는다. 이렇게 하면 **소켓을 쓰기 상태(읽기는 아님)로 열어둔 채로 유지**하게 된다. 이후에 **소켓을 닫으려면 end 메서드를 명시적으로 호출**해야 한다. 기본적으로 allowHalfOpen은 false이다.   on 메서드를 통해 두 이벤트에 콜백 함수가 연결되는 방법에 유의해야 한다. Node에서 이벤트를 발생시키는 많은 개체들이 **on 메서드를 사용해서 함수를 이벤트 수신기로 등록하는 방법을 제공**한다. on 메서드는 첫 번째 매개변수로 이벤트 이름을, 두번째 매개변수로 함수 수신기를 받는다.