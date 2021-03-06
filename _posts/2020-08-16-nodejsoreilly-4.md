---
title: "[Nodejs]O'REILLY 정리 - 4"
layout: single
author_profile: true
read_time: true
comments: true
share: true
related: true
categories:
- oreilly
- nodejs
- TIL
tags:
- oreilly
- nodejs
- TIL
---

# Node의 이점

Node에서 모든 함수 호출이 비동기인 것은 아니다. 어떤 개체는 동일한 함수를 동기 및 비동기 버전 둘 다 제공한다. 하지만 **Node는 가능한 한 비동기 코딩을 사용할 때 가장 잘 동작한다.**
Node 이벤트 루프와 콜백 함수에는 두 가지 주요 이점이 있다.   

먼저,**단일 쓰레드를 실행하면 오버헤드가 적어 애플리케이션의 확장성을 쉽게 얻을 수 있다.** 예제 1-3의 Node 애플리케이션과 유사한 PHP 애플리케이션을 만들어보면, 사용자가 보는 페이지는 동일하겠지만 시스템에서는 상당한 차이를 느낄 수 있다. PHP 애플리케이션을 Apache에서 기본값인 prefork MPM으로 실행하면 애플리케이션이 요청될 때마다 별도의 자식 프로세스 내에서 처리해야 한다. 상당히 성능이 좋은 시스템이 아닌 이상, 병렬적으로 실행 가능한 자식 프로세스는 몇 백 개 정도에 불과할 것이다. 그 이상의 요청이 들어올 경우에는 클라이언트가 응답을 기다릴 수 밖에 없다.   

Node의 두 번째 이점은 **멀티 쓰레드로 개발하지 않고도 리소스 사용량을 최소화할 수 있다는 것이다.** 달리 말하면, 쓰레드 세이프 애플리케이션을 만들 필요가 없다는 것이다. 이전에 쓰레드 세이프 애플리케이션을 개발해본 적이 있다면, 아마도 이 얘기가 정말 반갑게 느껴질 것이다.   

하지만 브라우저에서 실행되는 단일 유저용 JavaScript 애플리케이션을 개발하는 것이 아닌, 비동기 애플리케이션으로 작업할 때는 비동기 함수를 호출했을 때 다른 함수를 호출하기 전에 종료된다고 가정해서는 안 된다는 것을 유념해야 한다. 첫 번째 함수 내부에서 두 번째 함술을 부르지 않는 한, 이러한 호출 순서가 보장되지 않기 때문이다. 추가로, var 키워드를 빼먹을 때처럼 **Node에서 전역 변수는 매우 위험**하다.
하지만 이러한 내용들은 리소스를 적게 요구하고 쓰레드에 대한 걱정을 하지 않아도 되는 Node의 장점들을 생각하면 충분히 감당할 수 있는 문제들이다.

> Node를 좋아할만한 궁극적인 이유는 IE6에 대해 걱정을 할 필요 없이 JavaScript 코딩을 할 수 있다는 것이다.