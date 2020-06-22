---



title:  "Javascript"



layout: post



---


## [오늘 할 내용]
1. 자바스크립트
2. 

***

### 클라이언트에서 서버에 페이지 요청[<**a**>, <**link**>, <**form action**>]
- 폼 데이터 전송시[submit] get, post 방식이 발생(데이터 사이즈, 보안)
- get
	- 페이지 요청시에 쿼리스트링을 통해서 값 전달
	- 주소창에 132자까지만 들어갈 수 있음
	- ex) https://search.naver.com/search.naver?sm=top_hty&fbm=0&ie=utf8&query=daum
	-  name = sm, value = top_hyt
	-  name = fbm value = 0
- post
	- 폼의 데이터를 이진화로 비공개형식으로 전체 전달

***

## JavaScript
- 인터프리터 방식, 객체 기반의 스크립트 프로그래밍 언어
- 응용프로그램의 내장 객체 접근 할 수 있는 기능
- 장점 : 이식성, 생산성, 접근성
- 단점 : 한정된 객체와 메소드, 코드 노출

### JavaScript 적용방법
- 인라인 스크립트 적용 : <head>, <body> 태그 적용
	- <head> 안에 선언되면 전역
	- <body> 안에 선언되면 지역
- 외부 스크립트 적용

~~~ javascript
<SCRIPT Type = "text/javascript">
자바스크립트 코드
</SCRIPT>
~~~