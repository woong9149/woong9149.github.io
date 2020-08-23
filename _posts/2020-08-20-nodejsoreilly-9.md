---
title: "[Nodejs]O'REILLY 정리 - 9"
layout: single
author_profile: true
read_time: true
comments: true
share: true
related: true
categories:
- nodejs
- o'reilly
- TIL
tags:
- nodejs
- o'reilly
- TIL
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
> 특수한 개체인 EventEmitter로부터 상속받은 Node 개체들은 이번 장에서 논의되는 on 메서드 이벤트 처리를 노출하고 있다.   
   
**예제3-3** TCP 서버로 데이터를 전송하는 클라이언트 소켓   
```python
var net = require('net');

var client = new net.Socket();
client.setEncoding('utf8');

//서버로 연결
client.connect('8124','localhost',function(){
    console.log('connected to server');
    client.write('Who needs a browser to commnicate?');
});

//터미널로부터 입력을 준비
process.stdin.resume();

//데이터를 입력 받으면 서버로 전송
process.stdin.on('data', function(data){
    client.write(data);
});

//데이터를 수신하면 콘솔로 출력
client.on('data', function(data){
    console.log(data);
})

//서버가 닫히는경우
client.on('close',function(){
    console.log('connection is closed');
})
```


TCP 클라이언트는 서버와 마찬가지로 쉽게 생성할 수 있다.  클라이언트에서 **setEncoding** 메서드를 호출하면 수신되는 데이터의 인코딩을 변경한다.  데이터가 버퍼로 전송되지만 setEncoding을 사용해서 utf8 문자열로 읽을 수 있다. 소켓의 **write 메서드**는 데이터를 전송하는 데 사용한다. 또한 클라이언트는 데이터가 수신되는 경우인 data와 서버가 연결을 닫는 경우인 close라는 두 개의 이벤트에 수신기 함수를 연결하고있다.   
두 소켓 간에 전송되는 데이터는 터미널에서 입력되며, Enter를 누를 때 전송된다. 먼저 클라이언트 애플리케이션에서 입력한 문자열을 전송하면 TCP서버는 콘솔에 해당 문자열을 출력한다. 서버는 해당 메세지를 다시 클라이언트에게 전송하고, 이번에는 클라이언트가 콘솔에 메시지를 출력한다. 서버는 소켓의 remoteAddress 및 remotePort 속성을 사용해서 클라이언트의 IP 주소와 포트도 출력한다.   
클라이언트와 서버 간의 연결은 둘 중 하나를 강제 중료시키거나 Ctrl+C 를 사용할 때까지 유지된다. 계속 열려있는 소켓은 close 이벤트를 받아 콘솔에 출력한다. 관련 함수들이 모두 비동기 이므로, 서버는 하나 이상의 클라이언트로부터 하나 이상의 연결에 대해 서비스할 수 있다.   

**Tip**   
예제 3-2, 3-3 실행 시 클라이언트 혹은 서버를 종료시키면 close 이벤트 핸들러가 동자가지 않고 오류가 발생한다. close 이벤트나 error 이벤트 이후에 발생하기 때문에 다음과 같이 처리해야 close 이벤트 핸들러가 정상적으로 동작한다:   
`conn.on('error', function(){});`   

TCP는 HTTP 를 비롯하여 오늘날 우리가 인터넷에서 사용하는 대부분의 기능들을 위한 하부 전송 메커니즘이다.

# HTTP
네트워크에서 TCP는 전송 계층이고, HTTP는 애플리케이션 계층이다. Node에 포함된 모듈들을 살펴보면, HTTP 서버를 생성할 때 TCP 기반의 net.Server로 부터 기능을 상속 받는다는 것을 알게 될 것이다.   

HTTP 서버에서 requestListener는 소켓이고, http.ServerRequest 개체는 읽기 가능한 스트림이며, http.ServerResponse는 쓰기 가능한 스트림이다. HTTP는 **청크 분할 전송 인코딩(chunked transfer encoding)을 지원**하기 때문에 좀 더 복잡하다. 청크 분할 전송 인코딩은 **전체 처리가 완료되기 전까지는 정확한 응답 크기를 알 수 없는 상황에서 데이터를 전송할 수 있게 해준다**. 대신, 크기가 0인 청크가 질의가 끝났다는 것을 알리기 위해 전송된다. 이 인코딩 유형은 받아야 할 데이터들이 남아 있더라도 데이터를 쓰는 작업을 시작할 수 있으므로, 대량의 데이터베이스쿼리 결과를 HTML 테이블로 출력하는 것과 같은 요청을 처리하는데 유용하다.   

**예제 3-4** Unix 소켓에 바인딩된 HTTP 서버
```python
//서버와 콜백 함수를 생성
var http = require('http');
var fs = require('fs');

http.createServer(function(req,res){
    var query = require('url').parse(req.url).query;
    console.log(query);
    file = require('querystring').parse(query).file;

    //컨텐츠 헤더
    res.writeHead(200, {'Content-Type' : 'text/plain'});

    //전역으로 증가시키면서 클라이언트에 쓴다
    for(var i = 0; i < 100; i++){
        res.write(i + '\n');
    }
    //파일 컨텐츠를 열어서 읽음
    var data = fs.readFileSync(file, 'utf8');
    res.write(data);
    res.end();
}).listen('/tmp/nmode-server-sock');
```
클라이언트는 Node.js 사이트의 http.request 개체에 대한Node 코어 문서에서 제공되는 코드 예제를 기반으로 한다. `http.reqiuest `개체는 기본적으로 풀링된 소켓을 지원하는 `http.globalAgent`를 사용한다. 이 풀의 크기는 기본적으로 5개의 소켓이지만, agent.maxSockets 값을 변경해서 조정 가능하다.   
클라이언트는 서버에서 반환되는 청크 분할 데이터를 받아서 콘솔에 출력한다. 또한 예제 3-5에서 볼 수 있듯이 서버가 응답하도록 write를 두 번 호출한다.   

**예제3-5** Unix 소켓에 연결하여 받은 데이터를 출력
```python
var http = require('http');

var options = {
    method: 'GET',
    socketPath : '/tmp/node-server-sock',
    path: "/?file=main.txt"
};

var req = http.request(options,function(res){
    console.log('STATUS: ' + res.statusCode);
    console.log('HEADERS: ' + JSON.stringify(res.headers));
    res.setEncoding('utf8');
    res.on('data', function(chunk){
        console.log('chunk o\' data : ' + chunk);
    });
})

req.on('error', function(e){
    console.log('problem with request: ' + e.message);
});

//요청 본문에 데이터를 쓴다
req.write('data\n');
req.write('data\n');
req.end();
```
`http.request `개체와 함께 비동기 파일 읽기 함수를 사용하지 않은 이유는 **비동기 함수를 호출하면 연결이 이미 닫힌 상태가 되어버려 아무런 파일 내용도 반환되지 않기 때문**이다. Apache나 다른 웹 서버들에서 흔히 기대하는 동작들이 Node HTTP 서버에서는 내장되어 있지 않다는 점에 유의해야 한다. 예를 들어, 웹 사이트를 암호로 보호할 경우 Apache에서는 사용자 이름과 암호를 물어보는 창이 나타나지만 Node HTTP 서버에서는 나타나지 않는다. 이 기능이 필요하다면, 이를 위한 코드를 작성해야 한다.

# UDP/데이터그램 소켓
TCP는 두 끝점이 통신을 하는 동안 계속 연결이 유지되어있어야 한다. UDP는 비연결형(connectionless) 프로토콜인데, 두 끝점 간에 연결이 보장되지 않는다는 것을 의미한다. 이러한 이유로 TCP에 비해 신뢰성과 견고함이 떠러진다. 반면에, 일반적으로 UDP는 TCP보다 빨라서 실시간 용도 또는 **VoIP**(Voice over Internet Protocol)처럼 TCP로 연결할 경우 신호 품질이 저하될 수 있는 기술들에서 더 많이 사용 된다.   

Node 코어는 두 소켓 유형을 모두 지원한다. UDP 모듈 식별자는 dgram이다 :   
`require('dgram');`   
UDP 소켓을 만들기 위해서는 **createSocket** 메서드를 사용하면서 소켓 유형(**udp4** 또는 **udp6**)을 넘겨준다. 이벤트를 수신하기 위한 콜백 함수도 전달할 수 있다. TCP로 송신된 메시지와는 달리, **UDP를 사용하여 송신된 메시지는 반드시 문자열이 아닌 버퍼로 송신되어야 한다.**   

**예제 3-6** 터미널에 입력된 메시지를 전송하는 데이터그램 클라이언트 
```python
var dgram = require('dgram');

var client = dgram.createSocket("udp4");

//터미널로부터 입력을 준비
process.stdin.resume();

process.stdin.on('data', function(data){
    console.log(data.toString('utf8'));
    client.send(data, 0, data.length, 8124,"examples.burningbird.net", function(err,bytes){
            if(err)
                console.log('error: ' + err);
            else
                console.log('successful');    
    })
})
```
*"examples.burningbird.net" 에는 자신이 UDP 서버를 구동하는 host 주소로 변경해야 함. 예를 들어, 클라이언트 서버가 같은 host면 localhost를 사용.*    

예제3-6은 UDP 클라이언트 예제 코드다. 여기서 **process.stdin**을 통해 데이터를 입력 받아 UDP 소켓을 통해 송신한다. UDP 소켓은 버퍼만을 받으며, process.stdin 데이터가 버퍼이므로 문자열 인코딩을 설정할 필요가 없다는 점에 유의한다. 하지만 입력 내용을 되풀이하기 위해 **console.log** 메서드를 호출하려면 버퍼의 **toString** 메서드를 사용해서 버퍼를 문자열로 변환해야 한다.   

예제 3-7처럼 UDP 서버는 클라이언트보다 훨씬 더 간단하다. 서버 애플리케이션에서는 소켓을 생성해서 특정 포트(8124)에 바인딩시키고 message 이벤트를 수신 대기하면 된다. 메시지가 도착하면 애플리케이션에서는 메시지와 송신자의 IP 주소 및 포트를 **console.log**로 출력한다. 버퍼에서 문자열로 자동  변환되므로, 메시지 출력 시 인코딩이 필요 없다.   
소켓을 포트에 반드시 바인딩해야 하는 것은 아니지만, 바인딩을 하지 않을 경우에는 모든 포트에 대해 수신 대기를 시도한다.   

**예제3-7** 8124 포트에 바인딩되어 이벤트를 수신 대기하는 UDP 소켓 서버
```python
var dgram = require('dgram');

var server = dgram.createSocket("udp4");

server.on("message", function(msg,rinfo){
    console.log("Message: " + msg + " from " + rinfo.address + ":" + rinfo.port);
});

server.bind(8124);
```
메시지를 송신/수신한 후에 클라이언트나 서버에서 close 메서드를 호출하지 않았다. 클라이언트와 서버간에는 아무런 연결이 유지되지 않는 상태이지만, 소켓은 여전히 메시지를 송수신할 수 있다.

# 스트림, 파이프, Readline
지금까지의 소켓 간의 통신 스트림은 하부에 있는 stream 추상 인터페이스를 구현한 것이다. 스트림은 읽기, 쓰기 혹은 둘 다 가능하며, 모든 스트림은  **EventEmitter의 인스턴스**다.   
중요한 것은 **process.stdin** 및 **process.stdout**을 비롯한 모든 통신 스트림이 stream 추상 인터페이스를 구현한 것이라는 점이다. 이 하부 인터페이스 덕분에 Node의 모든 스트림에서 사용 가능한 기본 기능들이 존재한다:   
* setEncoding으로 스트림 데이터의 인코딩을 변경할 수 있다.
* 스트림이 읽기, 쓰기 혹은 둘 다 가능한지를 확인할 수 있다.
* 데이터 수신이나 연결 닫힘과 같은 스트림 이벤트를 잡아서 각각에 콜백 함수를 연결할 수 있다.
* 스트림을 일시 중지하거나 재개할 수 있다.
* 읽기 가능 스트림으로부터 쓰기 가능 스트림으로 데이터를 보낼 수 있다(pipe 가능).

마지막 기능은 지금까지 다뤄본 적이 없는 것이다. pipe 기능을 확인하기 위한 간단한 방법은 REPL 세션을 열어서 다음과 같이 입력하는 것이다:   
```
process.stdin.resume();
process.stdin.pipe(process.stdout);
```
그러면 이 시점부터 입력하는 모든 것들이 다시 되풀이 된다.       
연속 데이터를 위해 출력 스트림을 열어둔 상태로 유지하고 싶으면, 출력 스트림에 { end:false } 옵션을 전달한다 :   
`process.stdin.pipe(process.stdout, { end : false });`     
읽기 가능한 스트림의 경우에는 readline이라는 개체를 추가로 사용할 수 있다. Readline 모듈은 다음과 같이 포함시킨다:      
`var readline = require('readline');`   

Readline 모듈은 줄 단위로 스트림을 읽을 수 있게 해준다. 하지만 이 모듈을 포함시키고 나면 인터페이스와 stdin 스트림을 닫기 전까지 Node 프로그램이 종료되지 않는다는 것에 주의해야 한다.   

**예제3-8** Readline을 사용하여 간단한 명령어 기반 사용자 인터페이스 만들기   
```python
var readline = require('readline');

//새 인터페이스 생성
var interface = readline.createInterface(process.stdin,process.stdout, null);

//질문을 물어봄
interface.question(">>what is the meaning of life? ", function(answer){
    console.log("About the meaning of life, you said " + answer);
    interface.setPrompt(">>");
    interface.prompt();
})

//인터페이스를 닫기 위한 함수
function closeInterface(){
    console.log('Leaving interface...');
    process.exit();
}

// .leave 수신 대기
interface.on('line', function(cmd){
    if(cmd.trim() == '.leave'){
        closeInterface();
        return;
    }else{
        console.log("repeating command: " + cmd);
    }
    interface.setPrompt(">>");
    interface.prompt();
});

interface.on('close', function(){
    closeInterface();
})
```
애플리케이션은 실행되자마자 질문을 물어본 다음, 답을 출력한다. 또한 명령어(\n 으로 끝나는 줄)를 수신 대기한다. **.leave** 명령이 들어오면 애플리케이션을 종료하고, 그 외에는 명령어와 프롬프트를 반복한다. **Ctrl + C**나 **Ctrl + D** 키를 눌러 애플리케이션을 강제 종료시켜도 된다.