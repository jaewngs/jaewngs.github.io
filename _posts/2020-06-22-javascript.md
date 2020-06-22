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
	- reverse( ) : 배열의 순서를 역순으로 리턴
	- sort( ) : 배열 값을 정렬
	- slice( ) : 배열의 일부를 선택하여 부분적인 새로운 배열을 생성
	- concat( ) : 두 개의 배열을 하나의 배열로 만듦

~~~ javascript
// 배열명 = new Array(요소 ... | 요소의 크기);
var av = new Array(10,20,30,40,"abc");
~~~

### 함수
- 프로그램 내에서 특정 작업 수행을 위해 독립적으로 만들어진 하나의 단위
- <**head**>와 <**/head**> 태그 사이 정의, 'function'으로 시작
- 함수의 시작과 끝을 구분하는 기호 : 중괄호({ })
- 복귀형(return type), 매개 변수(parameter)에 대한 형 명시 생략
- return 없어도 됨

### 내장 함수
- eval(String) : String을 자바스크립트 코드로 실행(수치 연산 가능)
- isFinite(number) : number의 무한값의 여부
- isNan(number) : Number의 NaN인지 여부
- parseInt(Stirng, 진수) : String을 정수로 변환
- parseFloat(String) : String을 유리수로 변환
- escape( ) : 인코딩으로 변환
- unescape( ) : 디코딩으로 변환
- encodeURI(uri) : 최소한의 문자만 인코딩
- decodeURI(encodedURI) : 최소한의 문자만 디코딩
- Slice(인덱스1, 인덱스2) : [인덱스1]에서 [인덱스2]사이의 문자열을 문장 중에서 리턴
- Split(구분문자, 개수) : 문자열에서 구분문자를 이용하여 개수 만큼 문장을 나눔

***

### 객체 개념
- 추상화, 상속, 다형성
- 객체 : 기본적인 형태와 정보를 가진 단위
- 객체 = property + message
- 인스턴스 객체 : new를 이용한 생성한 객체
- 내장 객체, 사용자 정의 객체

### 사용자 객체
- 사용자가 원하는 고유의 개체를 정의하여 생성
- 객체 생성자 기능 함수를 정의, 속성과 메소드를 정의
- this 키워드로 현재 객체를 참조
- new 연산자를 이용한 인스턴스 생성
- 객체 이름으로 속성과 메소드를 호출
- [선언]
~~~ javascript
function 함수명( 매개변수1, 매개변수2, ...)
{
	this.이름1 = 매개변수1;
    this.이름2 = 매개변수2;
}
~~~

- [객체 생성]
~~~ javascript
객체이름 = new 함수명(매개변수1의 값, 매개변수2의 값, ...)
~~~

### 객체 제어문
- for_in : 하나 이상의 나열형 값을 가진 객체의 배열형식을 표시할 때 사용
- with : 객체에 포함된 속성이나 메소드를 여러 번 사용할 때 with 문으로 객체를 한 번 선언한 후 with 문 안에서 객체를 생략하고 속성이나 메소드명만 가지고 사용
- typeof : 변수의 데이터형을 알아내기 위해 사용

***

### Javascript window 객체
- Classes : CSS 클래스들의 정보를 가짐
- defaultsStats : 상태바에 초기 문자열을 설정
- Frames : Windows 객체 안의 프레임들의 배열 정보를 가짐
- Opener : Open( ) 메소드를 이용하여 연 문서를 말함
- Parent : 상위의 Window 객체
- Self : 현재 활성중인 창의 자신 객체
- Status : 브라우저의 상태바에 문자열을 출력하는 경우에 사용
- Tags : 문서 안에 정의된 모든 태그의 정보를 가지고 있음
- Top : 최상위의 window 객체를 가리킬 때 사용

### Javascript window 주요 메소드
- Back( ) : 이전 화면으로 이동
- Close( ) : Open( ) 메소드로 연 창을 닫음
- Find( ) : 문자열에서 특정 문자열을 찾음
- forward( ) : 다음 화면으로 이동
- moveBy( ) : 상대적인 좌표로 이동
- moveto( ) : 절대적인 좌표로 이동
- open( ) : 새로운 창을 열어줌
- print( ) : 화면에 있는 내용을 프린터로 출력

***

### Day47 sec07/index.html 예제
<https://dev.w3.org/html5/pf-summary/Overview#dom-window>
![image](https://user-images.githubusercontent.com/52989294/85253960-2d86e580-b49a-11ea-9ded-53ad5cdeaf69.png)

~~~ html
<!DOCTYPE html>
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=EUC-KR">
<title>Insert title here</title>
<script type="text/javascript">
	function win_open() {
		window.open("demo_sec07.html", "open_win1",
				"toolbar=yes,  status = yes, resizable = yes");
	}
</script>
</head>
<body>
	<br>
	<br>
	<br>
	<br>
	<br>
	<br>
	<br>
	<p align="center">
		<INPUT TYPE="button" VALUE="새로운 창 열기" onClick="win_open()"> </font><br>
	</p>
	<script type="text/javascript">
		document.write("<center><font color=#0000ff size=4 >브라우저 :"
				+ navigator.appName + "</br>");
		document.write("에이전트 :" + navigator.userAgent + "</br> ");
		document.write("버전 :" + navigator.appVersion + "</br>");
		document.write("코드 명 :" + navigator.appCodeName + "</br>");
		document.write("플랫폼 :" + navigator.platform + "</font></br>");
	</script>
</body>
</html>
~~~

***

### JS DOM
- 문서 객체 모델(Document Object Model)
- HTML, XML 문서에 대한 프로그래밍 인터페이스
- 객체 지향 모델로써 구조화된 문서를 표현하는 방식
- 플랫폼/언어 중립적으로 구조화된 문서를 표현하는 W3C의 공식 표준
- <http://www.w3.org/DOM/DOMTR>




















