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

### 적용방법
- 인라인 스크립트 적용 : <head>, <body> 태그 적용
	- <**head**> 안에 선언되면 전역
	- <**body**> 안에 선언되면 지역
- 외부 스크립트 적용

~~~ javascript
<SCRIPT Type = "text/javascript">
자바스크립트 코드
</SCRIPT>
~~~

~~~ javascript
<head>
<title> </title>
<script type = "text/javascript" src = "js01.js">
</script>
</head>
~~~

### 문장 규칙
- 코드 끝에 세미콜론 지정
- 코드 중간에 공백, 개행, 탭키 포함 가능
- 영문자 대소문자 구분
- // : 한 행 주석
- /* ~ */ : 복수 행 주석

### 변수
- 프로그래밍에서 데이터를 담을 수 있는 메모리 할당 영역
- 변수 선언시 var 키워드 사용
- 전역 변수, 지역 변수로 구분

~~~ javascript
var name  = "홍길동";
var a = 100;
~~~

### 식별자 규칙
- 영문자 혹은 밑줄(_)로 시작, 숫자로는 시작할 수 없다.
- 대소문자, 숫자, 밑줄(_)만 사용가능
- 자바스크립트의 예약어는 사용 불가

###  데이터 형식
- 기본 형식 : String, Number, Boolean
- 참조 형식 : Object, Array
- 특정 형식 : null, undefined

***

### 배열 함수
- 하나 이상의 상수를 나열해서 하나의 이름으로 등록해서 사용하는 참조형으로 인덱스는 0부터 시작, length 속성
- 메소드
	- join(연산자) : 배열을 하나의 문자열로 만든다.
	- reverse() : 배열의 순서를 역순으로 리턴
	- sort() : 배열 값을 정렬
	- slice() : 배열의 일부를 선택하여 부분적인 새로운 배열을 생성
	- concat() : 두 개의 배열을 하나의 배열로 만듦

~~~ javascript
// 배열명 = new Array(요소 ... | 요소의 크기);
var av = new Array(10,20,30,40,"abc");
~~~

### 함수
- 프로그램 내에서 특정 작업 수행을 위해 독립적으로 만들어진 하나의 단위
- <head>와 </head> 태그 사이 정의, 'function'으로 시작
- 함수의 시작과 끝을 구분하는 기호 : 중괄호({ })
- 복귀형(return type), 매개 변수(parameter)에 대한 형 명시 생략
- return 없어도 됨





























