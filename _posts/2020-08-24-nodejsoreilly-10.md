---
title: "[Nodejs]O'REILLY 정리 - 10"
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

예제 3-9 "test" 라는 검색어로 하위 디렉터리에서 파일을 찾기 위해 자식 프로세스를 사용

```python
var spawn = require('child_process').spawn,
    find = spawn('find',['.','-ls']),
    grep = spawn('grep',['test']);

    grep.stdout.setEncoding('utf8');

    //찾은 결과를 grep으로 전달
    find.stdout.on('data', function(data){
        grep.stdin.write(data);
    })

    //grep을 실행해서 결과를 출력
    grep.stdout.on('data', function(data){
        console.log(data);
    })

    //양쪽(find,grep)에 대한 오류 처리
    find.stderr.on('data', function(data){
        console.log('grep stderr: ' + data);
    });
    grep.stderr.on('data', function(data){
        console.log('grep stderr: '+ data);
    });

    //양쪽에 대한 종료 처리
    find.on('close', function(code){
        if(code !== 0){
            console.log('find process exited with code ' + code);
        }

        //grep 프로세스도 종료
        grep.stdin.end();
    })

    grep.on('exit', function(code){
        if(code !== 0){
            console.log('grep process exited with code ' + code);
        }

        find.stdin.end();
    })
```

**예제 3-9**는 로컬 디렉터리로부터 시작해서 모든 하위 디렉터리에서 이름 내에 특정한 단어(예:test)를 가지고 있는 파일들을 검색하는 것(`find . -ls | grep test)`을 자식 프로세스로 구현하고 있다.   
find를 수행하는 첫 번째 명령은 2개의 인수를 받는 반면, 두 번째 명령은 인수를 한 개(사용자가 입력하여 stdin에서 전달된 단어)만 받는다. 그리고 데이터가 버퍼로 출력 되는것을 막기위해 stdout 인코딩은 **setEncoding**을 통해 변경한다.    
애플리케이션을 실행하면, 현재 디렉터리 및 하위 디렉터리에서 파일 명에 test를 포함하고 있는 모든 파일들의 목록을 얻게 된다.

## chile_process.exec와 child_process.execFile
자식 프로세스를 만들어 내는 것 외에, child_process.exec와 child_process.execFile을 사용하여 쉘에서 명령을 실행해서 결과를 버퍼로 넣을 수도 있다. child_process.exec와 child_process.execFile의 유일한 차이는 execFile은 명령을 실행하는 대신, 파일에서 애플리케이션을 실행하는 것이다.   

두 메서드의 첫 번째 매개변수는 명령 혹은 파일 및 그 위치이고, 두 번째 매개변수는 명령에 대한 옵션, 세 번째는 콜백 함수다.   
콜백 함수는 3개의 매개변수 error, stdout, stderr 을 받는다. 아무런 오류가 발생하지 않으면, 데이터가 stdout의 버퍼로 보내진다.

만약 실행 파일의 내용이 다음과 같으면,   
```
#!/usr/local/bin/node
console.log(global);
```
 다음 애플리케이션은 버퍼에 있는 결과를 출력한다:   
```python
 var execfile = require('child_process').execfile,
	 child;
	 
	 child = execfile('./app.js', function(error, stdout, stderr){
		 if(error == null){
			 console.log('stdout: '+stdout);
		 }
	 });
```