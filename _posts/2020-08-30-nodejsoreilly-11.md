---
title: "[Nodejs]O'REILLY 정리 - 11"
layout: single
author_profile: true
read_time: true
comments: true
share: true
related: true
categories:
- o'reilly
- nodejs
- TIL
tags:
- o'reilly
- nodejs
- TIL
---

# 도메인 해석과 URL 처리
DNS 모듈은 비동기 DNS 요청 기능을 가진 C라이브러리인 c-ares를 사용하여 DNS 해석을 제공한다. DNS 모듈은 Node의 다른 모듈들에서 사용되고, 도메인이나 IP 주소를 찾아내는 것이 필요한 애플리케이션들에게 유용하다.   

지정된 도메인의 IP 주소를 찾아내려면 dns.lookup 메서드를 사용하여 반환된 IP 주소를 출력한다:   
```python
var dns = require('dns');
dns.lookup('burningbird.net', function(err,ip){
	if(err) throw err;
	console.log(ip);
});
```
**dns.reverse** 메서드는 지정된 IP 주소에 대해 도메인명의 배열을 반환한다:   

```python
dns.reverse('173.255.206.103', function(err,domains){
	domainns.forEach(function(domain){
		console.log(domain);
	});
});
```

**dns.resolve** 메서드는 A, MX, NS 등과 같이 지정된 유형에 따라 레코드 유형의 배열을 반환한다. 다음 코드는 도메인명이 burningbird.net의 네임서버 도메인을 찾는다 :   
```python
dns.resolve('173.255.206.103', function(err,domains){
	domains.forEach(function(domain){
		console.log(domain);
	});
});
```
이 코드는 다음과 같은 결과를 반환한다 :    
```
ns1.linode.com
ns3.linode.com
ns5.linode.com
ns4.linode.com
```

**URL 모듈**은 URL을 파싱해서 모든 URL 구성요소를 가진 개체를 반환해준다. 다음 URL을 전달하면,   
```python
var url = require('url');
var urlObj = url.parse('http://examples.burningbird.net:8124/?file=main');
```
다음과 같은 JavaScript 개체가 반환된다:   
```
{ protocol: 'http:',
	slashes: true,
	host: 'examples.burningbird.net:8124',
	port: '8124',
	hostname:'examples.burningbird.net',
	href: 'http://examples.burningbird.net:8124/?file=main',
	search: '?file=main',
	query: 'file=main',
	pathname: '/',
	path: '/?file=main'
}
```
각 구성요소는 다음과 같이 별도로 접근할 수 있다 :   
`var qs = urlObj.query; //get the query string`   
**URL.format** 메서드를 호출하면 반대 동작을 수행한다 :   
`console.log(url.format(urlObj)); // 원래의 URL을 반환`   

**URL 모듈은 Query String 모듈과 함께 사용되는 경우가 많다**. Query String 모듈은 수신된 쿼리 문자열을 파싱하거나 쿼리 문자열로 사용하기 위한 문자열을 준비하기 위한 기능들을 제공하는 간단한 유틸리티 모듈이다.   

쿼리 문자열에서 키/값 쌍을 추출해내래면 **queryString.parse** 메서드를 사용한다 :   
`var vals = querystring.parse('file=main&file=secondary&type=html');`   
이 코드는 개별 쿼리 문자열 값에 쉽게 접근할 수 있게 해주는 JavaScript 개체를 반환한다 :   
`{ file: ['main', 'secondary'], type: 'html' }`   
쿼리 문자열 내에서 file이 두 번 나오므로, file의 두 값은 배열로 그룹화되고 각 값에 개별적으로 접근할 수 있다 :   
`console.log(vals.file[0]); // main을 반환`   

**querystring.stringify** 를 사용하여 개체를 쿼리 문자열로 변환할 수도 있다 :   
`var qryString = querystring.stringify(vals);`