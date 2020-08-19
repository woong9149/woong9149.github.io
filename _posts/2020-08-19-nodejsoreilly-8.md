---
title: "[Nodejs]O'REILLY 정리 - 8"
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

# 타이머: setTimeout, clearTimeout, setInterval, clearInterval
클라이언트 JavaScript에서 타이머 함수는 전역 window 객체에 속해있다. 타이머 함수는 JavaScript에 속한 것이 아니지만, JavaScript 개발에서 매우 자주 사용되는 항목이기에 Node 개발자들이 Node 코어 API에 포함시켰다.   
Node에서 동작하는 타이머 함수는 브라우저에서 동작하는 것과 유사하다. 실제로 Node의 타이머 함수 동작은 Chrome에서의 동작과 동일한데, Node가 Chrome의 V8 JavaSript 엔진을 기반으로 하기 때문이다.   

Node의 **setTimeout** 함수는 첫 번째 매개변수로**콜백 함수**, 두 번째 매개변수로 **지연 시간**(밀리초 단위) 그리고 **선택적인 인수**들을 받는다 :   
```python
//파일을 열고 내용을 읽어 HTTP 응답 개체로 보내기 위한 타이머
function on_OpenAndReadFile(filename, res){
    console.log('opening ' + filename);
    //파일을 열고 내용을 읽는다
    fs.readFile(filename, 'utf8', function(err,data){
        if(err){
            res.write('Could not find or open file for reading \n');
        }else{
            res.write(data);
        }
        //응답 완료
        res.end();
    })
    setTimeout(on_OpenAndReadFile, 2000, filename, res);
}

```
이 코드에서는 약 2,000 밀리초가 지난 후 콜백 함수인 onOpenAndReadFile이 호출되어 파일을 열고 읽어서 HTTP 응답으로 보낸다.

> Node 문서에서 기술하였듯이, 콜백 함수가 정확하게 n 밀리초에 호출된다는 보장은 없다. 이는 브라우저에서 setTimeout을 사용하는 것과 차이가 없다. 환경에 대한 절대적인 제어권을 가지고 있지 않으므로, 여러가지 요인들로 타이머가 약간 지연될 수 있다.

**clearTimeout** 함수는 이전에 설정된 setTimeout을 지운다. 반복적인 타이머가 필요하다면, **setInterval**을 사용해서 매 n 밀리초 마다 함수를 호출한다(n 값은 이 함수의 두 번째 매개변수임). setInterval은 **clearInterval**로 지운다.