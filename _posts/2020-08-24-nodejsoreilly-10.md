---
layout: single
author_profile: true
read_time: true
comments: true
share: true
related: true
title: "[Nodejs]O'REILLY 정리 - 10"
---

# 자식 프로세스
운영체제에서 제공하는 기능들 대부분은 명령줄을 통해서만 접근할 수 있다. 이러한 기능들을 Node 애플리케이션에서 접근하기위해 자식 프로세스(child process)가 생겨났다.   
Node는 새로운 자식 프로세스 내에서 시스템 명령줄을 실행하고 입/출력을 수신 대기할 수 있게 해준다. 명령에 인수를 전달하고, 한 명령의 결과를 다른 명령으로 보내는 것도 가능하다.   

## chile_process.spawn
자식 프로세스를 생성할 때 사용할 수 있는 기법에는 4가지가 존재한다. 가장 흔히 사용되는 것은 **spawn 메서드**를 사용하는 것이다. 그러면 새로운 프로세스에 명령을 실행하고 인수를 넘겨주게 된다.    
예제
```python
var spawn = require('child_process').spawn,
    pwd = spawn('pwd');

pwd.stdout.on('data', function(data){
    console.log('stdout: ' + data);
})

pwd.stderr.on('data', function(data){
    console.log('stderr: ' + data);
})

pwd.on('exit', function(code){
    console.log('child process exited with code ' + code);
});
```
예제에서는 자식 프로세스를 생성하고 Unix의 **pwd 명령**을 호출해서 현재 디렉터리를 출력하게 된다. 명령에서는 아무런 인수도 받지 않는다.    
자식 프로세스의 stdout 및 stderr에서 잡게 되는 이벤트들에 유의한다. 오류가 발생하지 않으면 명령줄에 출력된 내용은 자식 프로세스의 stdout으로 전송되고, 프로세스의 data 이벤트가 발생된다.   

```
var spawn = require('child_process').spawn,
	pwd = spawn('pwd' , [-g']);
```
과 같이 명령에 잘못된 옵션을 전달하는 것처럼 오류가 하나라도 발생하면, 오류는 stderr로 전송되고 콘솔에 다음과 같은 오류를 출력한다 :   
```
stderr: pwd: invalid option -- 'g'
Try 'pwd --help' for more information.
child process exited with code 1
```
프로세스는 오류가 발생했다는 것을 알리는 코드 1로 종료된다. 종료 코드는 운영체제와 오류에 따라 달라진다. 아무런 오류도 발생되지 않으면, 자식 프로세스는 코드 0으로 종료된다.