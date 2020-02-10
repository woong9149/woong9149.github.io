---
title: "[Nodejs]생활코딩"
---

# App - 글생성 UI 만들기

form태그는 사용자가 입력한 정보를, submit을 통해 서버로 전송하는 html의 기능

> from 태그의 method 
> - get 방식
* > 이름/ 값 쌍으로 양식 데이터를 URL에 추가
* >URL의 길이는 약 3000자로 제한
* >민감한 데이터에는 사용 x
* >사용자가 결과를 즐겨찾기에 추가하려는 양식 제출에 유용
* > GET은 google의 쿼리 문자열과 같은 비보안 데이터에 적합함.
>
>- post 방식
* > HTTP 요청 본문에 양식 데이터를 추가함.(데이터는 URL에 표시되지 않음)
* >크기 제한이 없음
* > POST가 포함 된 양식 제출은 북마크 할 수 없음

(w3schools link : 
[https://www.w3schools.com/tags/ref_httpmethods.asp]


# App - 파일생성과 리다이렉션
- 리다이렉션(redirection) : 사용자의 요청 처리 후 다른페이지로 사용자를 보내는 것 

301 - 이 주소는 다음 주소로 영원히 바꼈다는 뜻
302 - 일시적으로 바꼈다는 뜻