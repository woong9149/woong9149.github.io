---
title: "[Thymeleaf] 타임리프 기본 문법"
categories:
- TIL
- Thymeleaf
- 타임리프
- 템플릿엔진
tags:
- TIL
- Thymeleaf
- 타임리프
- 템플릿엔진
---

# 타임리프란 ?
컨트롤러가 전달하는 데이터를 이용해 동적으로 화면을 만들어주는 역할을 하는 뷰 템플릿 엔진이다.    
Spring boot에서 공식적으로 지원하는 뷰 템플릿이며, 
* 서버상에서 동작하지 않아도 HTML 파일의 내용을 바로 확인 가능
* 순수한 HTML 구조를 유지   

하는 것이 대표적인 특징이다.

<br/>
> **템플릿 엔진(Template Engine)이란?**    
> 템플릿 양식과 특정 데이터 모델에 따른 입력 자료를 합성하여 결과 문서를 출력하는 소프트웨어 또는 소프트웨어 컴포넌트를 말한다.   
> 템플릿 엔진은 view code(html)와 data logic code를 분리해주는 기능을 하며,  
> 서버사이드 템플릿 엔진 vs 클라이언트 사이드 템플릿 엔진, 레이아웃 템플릿 엔진 vs  텍스트 템플릿 엔진 등으로 구분할 수 있다.

<br/>
**타임리프 사용**   
	Spring boot(Gradle) - build.gradle

```java
implementation 'org.springframework.boot:spring-boot-starter-thymeleaf'
```

<br/>
# 타임리프 문법   
**타임리프 사용 선언**
```java
<html xmlns:th="http://www.thymeleaf.org">
```   

<br/>
**속성 변경1 - th:href**   
```java
th:href="@{/css/bootstrap.min.css}"
```  
- `href="value1"` 을 `th:href="value2"` 의 값으로 변경한다.
- 타임리프 뷰 템플릿을 거치게 되면 원래 값을 `th:xxx` 값으로 변경한다. 만약 값이 없다면 새로 생성한다.
- HTML을 그대로 볼 때는 `href` 속성이 사용되고, 뷰 템플릿을 거치면 `th:href` 의 값이 `href` 로 대체되면서 동적으로 변경할 수 있다.
- 대부분의 HTML 속성을 `th:xxx` 로 변경할 수 있다.

<br/>
**타임리프 핵심**   
- 핵심은 `th:xxx` 가 붙은 부분은 서버사이트에서 랜더링 되고, 기존 것을 대체한다. `th:xxx`이 없으면 기존 html의 `xxx `속성이 그대로 사용 된다.
- HTML을 파일로 직접 열었을 때, `th:xxx` 가 있어도 웹 브라우저는 `th:` 속성을 알지 못하므로 무시한다.
- 따라서 HTML을 파일 보기를 유지하면서 템플릿 기능도 할 수 있다.

<br/>
**URL 링크 표현식 - @{...}**   
```javascript
th:href="@{/css/bootstrap.min.css}"
```
- **@{...}**: 타임리프는 URL 링크를 사용하는 경우 @{...}를 사용한다. 이것을 URL 링크 표현식이라 한다.
- URL 링크 표현식을 사용하면 서블릿 컨텍스트를 자동으로 포함한다.

<br/>
**속성변경2- th:onclick**   
- html의 `onclick="location.href='addForm.html'"`가 `th:onclick="|location.href='@{/basic/items/add}'|"`로 대체

<br/>
**리터럴 대체 - |...|**   
- 타임리프에서 문자와 표현식은 분리되어 있기때문에 더해서 사용해야 한다.
	- ```javascript
		<span th:text="'Welcome to our application, ' + ${user.name} + '!'">
		```
-  다음과 같이 리터럴 대체 문법을 사용하면, 더하기 없이 편리하게 사용할 수 있다.
	-  ```javascript
		<span th:text="|Welcome to our application, ${user.name}!|">
		```

<br/>
**반복 출력 - th:each**   
- ```javascript
	<tr th:each="item: ${items}">
	```
- 반복은 `th:each` 를 사용한다. 이렇게 하면 모델에 포함된 items 컬렉션 데이터가 item 변수에 하나씩 포함되고, 반복문 안에서 item 변수를 사용할 수 있다.
- 컬렉션의 수 만큼 `<tr>..</tr>`이 하위 테그를 포함해서 생성된다.

<br/>
**변수 표현식 - ${...}**   
- ```javascript
	<td th:text="${item.price}">10000</td>
	```
- 모델에 포함된 값이나, 타임리프 변수로 선언한 값을 조회할 수 있다.
- 프로퍼티 접근법을 사용 한다.(`item.price()`)

<br/>
**내용 변경 - th:text**   
- ```javascript
	<td th:text="${item.price}">10000</td>
	```
- 내용의 값을 `th:text`의 값으로 변경한다.
- 여기서는 10000을 `${item.price}` 의 값으로 변경한다.

<br/>
**속성 변경 - th:action**   
- HTML form에서 `action` 값이 없으면 현재 URL에 데이터를 전송한다.
- 그래서 조회와 입력 URL을 똑같이 맞추고, HTTP 메서드로 두 기능을 구분하는 것이 편리하다.
	- 상품 등록 폼: GET `/basic/items/add`
	- 상품 등록 처리: POST `/basic/items/add`
- 이렇게 하면 하나의 URL로 등록 폼과 등록 처리를 깔끔하게 처리할 수 있다.

<br/>
**속성 변경 - th:object="${}"**  
- HTML form을 submit 할 때 데이터가 `th:object` 속성을 통해 object에 지정한 객체에 값을 담아 덤긴다.
- `th:field` 속성이 지정된 엘리먼트의 값을 넘긴다.

<br/>
**속성 변경 - th:field="\*{}"**  
- `th:object` 속성과 함께 `th:field`를 이용해서 HTML 태그에 멤버 변수를 매핑할 수 있다.
- `th:field`를 이용한 사용자 입력 필드는 id, name, value 속성 값이 자동으로 매핑된다.
- `th:object`와 `th:field`는 Controller에서 특정 클래스의 객체를 전달 받은 경우에만 사용 가능하다.


<br/>
**URL 링크 표현식2 - @{...}**   
- ```javascript
	th:href="@{/basic/items/{itemId}(itemId=${item.id})}"
	```
- URL 링크 표현식을 사용하면 경로를 템플릿처럼 편리하게 사용할 수 있다.
- 경로 변수 (`{itemId}`) 뿐만 아니라 쿼리 파라미터도 생성한다.
- 예) 
	-  ```javascript
	th:href="@{/basic/items/{itemId}(itemId=${item.id}, query='test')}"
	```
	- 생성 링크:` http://localhost:8080/basic/items1?query=test`

<br/>
**URL 링크 간단히**   
- ```javascript
	th:href="@{|/basic/items/{itemId}|}"
	```
- 리터럴 대체 문법을 활용해서 간단히 사용할 수도 있다.

<br/>
**조건문 - th:if, th:unless**   
- th:if는 if 문과 동일하고, th:unless는 else문과 같다.
- th:unless는 일반적인 언어의 else문과 다르게 th:if에 들어가는 조건과 동일한 조건을 지정해야 한다.

<br/>
> **참고**   
> 타임리프는 순수 HTML 파일을 웹 브라우저에서 열어도 내용을 확인할 수 있고, 서버를 통해 뷰 템플릿을 거치면 동적으로 변경된 결과도 확인할 수 있다.   
> JSP를 생각해보면, JSP 파일은 웹 브라우저에서 그냥 열면 JSP 소스코드와 HTML이 뒤죽박죽 되어서 정상적인 확인이 불가능 하다. 오직 서버를 통해서 
> JSP를 열어야 한다.   
> 이렇게 순수 HTML을 그대로 유지하면서 뷰 템플릿도 사용할 수 있는 타임리프의 특징을**네츄럴 템플릿**(natural templates)이라 한다.
