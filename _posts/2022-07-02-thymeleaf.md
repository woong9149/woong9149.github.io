---
title: "[Thymeleaf] 타임리프 기본 문법"
---

# 타임리프란 ?
컨트롤러가 전달하는 데이터를 이용해 동적으로 화면을 만들어주는 역할을 하는 뷰 템플릿 엔진이다.    
Spring boot에서 공식적으로 지원하는 뷰 템플릿이며, 
* 서버상에서 동작하지 않아도 HTML 파일의 내용을 바로 확인 가능
* 순수한 HTML 구조를 유지   

하는 것이 대표적인 특징이다.

> **템플릿 엔진(Template Engine)이란?**    
> 템플릿 양식과 특정 데이터 모델에 따른 입력 자료를 합성하여 결과 문서를 출력하는 소프트웨어 또는 소프트웨어 컴포넌트를 말한다.   
> 템플릿 엔진은 view code(html)와 data logic code를 분리해주는 기능을 하며,  
> 서버사이드 템플릿 엔진 vs 클라이언트 사이드 템플릿 엔진, 레이아웃 템플릿 엔진 vs  텍스트 템플릿 엔진 등으로 구분할 수 있다.

**타임리프 사용**   
	Spring boot(Gradle) - build.gradle

```java
implementation 'org.springframework.boot:spring-boot-starter-thymeleaf'
```


# 타임리프 문법   
**타임리프 사용 선언**
```java
<html xmlns:th="http://www.thymeleaf.org">
```   

**속성 변경 - th:href**   
```java
th:href="@{/css/bootstrap.min.css}"
```  
- `href="value1"` 을 `th:href="value2"` 의 값으로 변경한다.
- 타임리프 뷰 템플릿을 거치게 되면 원래 값을 `th:xxx` 값으로 변경한다. 만약 값이 없다면 새로 생성한다.
- HTML을 그대로 볼 때는 `href` 속성이 사용되고, 뷰 템플릿을 거치면 `th:href` 의 값이 `href` 로 대체되면서 동적으로 변경할 수 있다.
- 대부분의 HTML 속성을 `th:xxx` 로 변경할 수 있다.

**타임리프 핵심**   
- 핵심은 `th:xxx` 가 붙은 부분은 서버사이트에서 랜더링 되고, 기존 것을 대체한다. `th:xxx`이 없으면 기존 html의 `xxx `속성이 그대로 사용 된다.
- HTML을 파일로 직접 열었을 때, `th:xxx` 가 있어도 웹 브라우저는 `th:` 속성을 알지 못하므로 무시한다.
- 따라서 HTML을 파일 보기를 유지하면서 템플릿 기능도 할 수 있다.
