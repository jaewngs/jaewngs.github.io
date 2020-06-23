---



title:  "Javascript"



layout: post



---


## [오늘 할 내용]
1. Javascript
2. Javascript Style Object
3. Javascript 폼객체

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

~~~ html
<!-- alert prompt -->
<!DOCTYPE html >
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=EUC-KR">
<title>alert</title>
<script type="text/javascript">
	var res = window.alert("페이지 로딩중");
	document.write("res = " + res  + "<br>");
</script>
</head>
<body>
<script type="text/javascript">
	var res = window.alert("페이지 로딩중222");
	document.write("res = " + res);
</script>
</body>
</html>
~~~

![image](https://user-images.githubusercontent.com/52989294/85276801-58cefc00-b4bd-11ea-861f-0a5fc5b26a9c.png)
![image](https://user-images.githubusercontent.com/52989294/85276869-6d12f900-b4bd-11ea-9514-8b95c0b2af99.png)

***

~~~ html
<!-- comfirm prompt -->
<!DOCTYPE html >
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=EUC-KR">
<title>alert</title>
<script type="text/javascript">
	var res = confirm("현재 페이지를 종료할 까요?");
	document.write(res); // 확인 누르면 res = true , 취소 누르면  res = false
</script>
</head>
<body>
</body>
</html>
~~~

![image](https://user-images.githubusercontent.com/52989294/85276934-83b95000-b4bd-11ea-8363-8b7e5286953f.png)

***

~~~ html
<!DOCTYPE html >
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=EUC-KR">
<title>demo_sec01</title>

</head>
<body>
	<h1>이름을 입력 받아 보기</h1>
	<script type="text/javascript">
		var name = prompt("input name", "dominica");  // 프롬프트 창 이름입력
		alert("Input name " + name);  // 경고창에 입력한 이름 출력
		document.write("입력하신 이름은  " + name + "입니다");
	</script>
</body>
</html>
~~~

![image](https://user-images.githubusercontent.com/52989294/85277127-dbf05200-b4bd-11ea-995d-c32f42fff9e4.png)

***

~~~ javascript
// js01.js
document.write("First script<br>"); // 문자열 안에 <br> 넣어도 줄바꿈 됨
document.write("aaa");
document.write("bbb"+ "<BR>");
document.write("ccc");

var a = "abcde";
b = "1234";
document.write( a + b + "<BR>");
document.write(100 + 100 + "<BR>");
~~~

***

~~~ html
<!DOCTYPE html >
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=EUC-KR">
<title>스크립트 인용</title>

<script type="text/javascript" src="../js/js01.js">

</script>
</head>
<body>
	<script type="text/javascript">
		document.write("<br><br>");
		document.write("여기부터 Js01.html<br>");
		document.write("화면에 표시하고자 하는 문자열을 입력합니다<br>");
		document.write(document.domain + "<br>");
		var str = " ";
		str += "document.title = " + document.title + "<BR>";
		str += "document.charset = " + document.charset + "<BR>";
		str += "document.defaultCharset = " + document.defaultCharset + "<BR>";
		str += "document.location = " + document.location + "<BR>";
		str += "document.vlinkColor = " + document.vlinkColor + "<BR>";
		str += "document.body.clientWidth = " + document.body.clientWidth
				+ "<BR>";
		document.write(str);
	</script>
</body>
</html>
~~~

***

~~~ html
<!DOCTYPE html >
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=EUC-KR">
<title>Prompt</title>
<script type="text/javascript">
	var su;
	su = window.prompt("숫자를 입력하세요", "100");
	document.write("입력하신 숫자는  : " + su + " 입니다");
</script>
</head>
<body>

</body>
</html>
~~~

***

~~~ javascript
// js02.js
document.write("1111");
~~~

~~~ html
<!DOCTYPE html>
<html>
<head>
<meta charset="EUC-KR">
<title>Insert title here</title>
<script type="text/javascript" src="../js/js02.js"></script>
</head>
<body>
	<script type="text/javascript" src="../js/js02.js"></script>

	<script type="text/javascript">
		document.write("2222");
	</script>
</body>
</html>
~~~

![image](https://user-images.githubusercontent.com/52989294/85277218-fde9d480-b4bd-11ea-9855-4f5aa62bf5dd.png)

***

###  데이터 형식
- 기본 형식 : String, Number, Boolean
- 참조 형식 : Object, Array
- 특정 형식 : null, undefined

~~~ html
<!DOCTYPE html >
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=EUC-KR">
<title>Js Data Type</title>
</head>
<body>
	<hr />
	<h1>자바스크립트 dataType</h1>
	<hr />
	<script type="text/javascript">
		var num1 = 64;
		num2 = 0100;
		num3 = 0x40;
		num4 = 3.2541, num5 = 2e3;
		name = "홍길동";

		document.write("num1의 값은 " + num1 + " 입니다 <br/>");
		document.write("num2의 값은 " + num2 + " 입니다 <br/>");
		document.write("num3의 값은 " + num3 + " 입니다 <br/>");
		document.write("num4은 " + num4 + "이고 num5는 " + num5 + "입니다 <br/>");
		document.write("name :" + name + "입니다<br/><br/>");

		num1 = 100;
		document.write("num1의 값은 " + num1 + " 입니다 <br/>");
		name = 200;
		document.write("name :" + name + "입니다<br/><br/>");

	</script>
</body>
</html>
~~~

![image](https://user-images.githubusercontent.com/52989294/85277333-3b4e6200-b4be-11ea-9d8b-24c6d7b9d9cb.png)

***

~~~ html
<!DOCTYPE html >
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=EUC-KR">
<title>demo_sec03</title>
<script type="text/javascript">
	var str = prompt("input su: 1~ 4", "");
	
	// 형 변환이 자유롭다.
	document.write("case 1: " + (str == 1) + "<br>");
	document.write("case 2: " + (str == '1') + "<br>");
	document.write("case 3: " + (str == "1") + "<br>");
	document.write("case 4: " + (str + 100) + "<br>");

	var hap = 0;

	document.write("<hr> <h1>if </h1></hr>");

	if (str == 1) {
		document.write("봄");
	} else if (str == 2) {
		document.write("여름");
	} else if (str == 3) {
		document.write("가을");
	} else {
		document.write("겨울");
	}

	document.write("<hr> <h1>switch </h1></hr>");

	switch (str) {
	case 1:
		document.write("일사분기");
		break;
	case '2':
		document.write("이사분기");
		break;
	case '3':
		document.write("삼사분기");
		break;
	case '4':
		document.write("사사 분기");
		break;
	default:
		document.write(str + "해당 사항 없음");
	}
	document.write("<hr> <h1>for </h1></hr>");
	for (i = 1; i <= str; i++) {

		hap += i; // hap=hap+i; 

		document.write("1~ " + str + "의  합은  :" + hap + "</br>");
	}

	document.write("<hr> <h1>while </h1></hr>");
	while (str > 0) {
		document.write("while 구문입니다<br> ");
		str--;
	}
	document.write("<hr> <h1>do~ while </h1></hr>");

	do {
		document.write("do~ while 구문입니다 ");

	} while (str > 0);
</script>

</head>
<body>
</body>
</html>
~~~

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

~~~ html
<!DOCTYPE html >
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=EUC-KR">
<title>arraytest</title>
</head>
<script type="text/javascript">
	ar = new Array("홍길동", 80, 90, 100, "가나다", new Array(150, 200, 3, 4));
	arr = new Array(4);
	arr[0] = "정길동";
	arr[1] = 100;
	arr[2] = 90;
	arr[3] = 20;
	document.write(ar[5][1]); // 2

	document.write("<h2> join() : " + ar.join() + "<br>"); // 결합(기본 ','로 연결)
	document.write("join(+) : " + ar.join("+") + "<br>"); // + 로 결합
	document.write("reverse()  : " + ar.reverse() + "<br>");
	document.write("sort():   " + ar.sort() + "<br>");
	//arr=new Array("정길동",100,90,20);
	document.write("contcat( arr) :  " + ar.concat(arr) + "<br>");
</script>
<body>

</body>
</html>
~~~

![image](https://user-images.githubusercontent.com/52989294/85277513-7fd9fd80-b4be-11ea-85fa-c151746dd923.png)
.
***

~~~ html
<!DOCTYPE html >
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=EUC-KR">
<title>funtest</title>
<script type="text/javascript">
	function callname(name) {
		document.write("안녕하세요! " + name + "<BR>");
		alert(name); // alert 창에 이름 뜸
	}
	callname("도미니까");
	callname("도미니꼬");
</script>
</head>
<body>
	<form>
		<!-- 확인 1 버튼을 눌렀을 때 -->
		<input type="button" value="확인1" onclick="callname('AAA');" />
		<!-- 확인 2 버튼을 눌렀을 때 -->
		<input type="button" value="확인2" onclick="callname('BBB')">
	</form>
</body>
</html>
~~~

***

~~~ html
<!DOCTYPE html >
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=EUC-KR">
<title>demo_sec04</title>

<script type="text/javascript">
	function Result() {

		myTbl = new Array("도미니까의 홈페이지", "도미니꼬 씨의 홈페이지", "폴리오 홈페이지",
				" 루리의 홈페이지", "루세의 홈페이지", "루오의 홈페이지 ");

		myTblColor = new Array("#DB7093", "#D8BFD8");
		for (i = 0; i < myTbl.length; i++) {
			document.write("<tr>");
			document.write("<td bgcolor='", myTblColor[i % 2], "'>");
			document.write(myTbl[i]);
			document.write("</td> ");
			document.write("</tr>");
		}
	}
</script>
</head>
<body>
	<table border="1">
		<script type="text/javascript">
			Result();
		</script>

	</table>
</body>
</html>
~~~

***

### 함수
- 프로그램 내에서 특정 작업 수행을 위해 독립적으로 만들어진 하나의 단위
- <**head**>와 <**/head**> 태그 사이 정의, 'function'으로 시작
- 함수의 시작과 끝을 구분하는 기호 : 중괄호({ })
- 복귀형(return type), 매개 변수(parameter)에 대한 형 명시 생략

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

~~~ html
<!DOCTYPE html >
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=EUC-KR">
<title>Date</title>
<script type="text/javascript">
	var day, week;
	var today = new Date();
	week = new Array("일", "월", "화", "수", "목", "금", "토");
	day = week[today.getDay()];
	document.write("오늘은 : " + today.getFullYear() + "년 "
			+ (today.getMonth() + 1) + "월 " + today.getDate() + "일 " + day
			+ "요일");
</script>
</head>
<body>

</body>
</html>
~~~

![image](https://user-images.githubusercontent.com/52989294/85278089-69807180-b4bf-11ea-8f09-077cd85db8b8.png)

***

~~~ html
<!DOCTYPE html >
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=EUC-KR">
<title>Insert title here</title>
<script type="text/javascript">
	var acnt = "4/0";
	var acnt1 = "4/0 + 1";
	var acnt2 = "1.5 + 1";
	// eval(String) 문자열 연산하기
	document.write(acnt + " = " + eval(acnt) + "입니다.<br>");
	document.write(acnt1 + " = " + eval(acnt1) + "입니다.<br>");
	document.write(acnt2 + " = " + eval(acnt2) + "입니다.<br>");

	document.write("한글: escape('대한민국') ====> " + escape('대한민국') + "<p>");

	document.write("unescape('%uB300%uD55C%uBBFC%uAD6D') ====> " +

	unescape('%uB300%uD55C%uBBFC%uAD6D') + "<p>");

	document.write("unescape('%21%23%24') ====> " + unescape('%21%23%24')
			+ "<p>");
</script>
</head>
<body>

</body>
</html>
~~~

![image](https://user-images.githubusercontent.com/52989294/85278729-46a28d00-b4c0-11ea-982c-9d8054731c7a.png)

***

~~~ html
<!DOCTYPE html >
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=EUC-KR">
<title>Insert title here</title>

<script type="text/javascript">
	document.write("<H2> [1] 최대값 :" + Math.max(10, 20) + "<BR>");
	document.write(" [2] 최소값 : " + Math.min(10, 20) + "<BR>");
	document.write(" [3] 올  림 : " + Math.ceil(7.8) + "<BR>");
	document.write(" [4] 내  림 : " + Math.floor(7.8) + "<BR>");
	document.write(" [5] 절대값 : " + Math.abs(-7) + "</H2>");
</script>
</head>
<body>

</body>
</html>
~~~

![image](https://user-images.githubusercontent.com/52989294/85278849-78b3ef00-b4c0-11ea-9274-595bf0ca8084.png)

***

~~~ html
<!DOCTYPE html >
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=EUC-KR">
<title>demo_sec05</title>
<script type="text/javascript">
	myDate = new Date(); // 오늘 날짜 데이터 검색
	myWeekTbl = new Array("일", "월", "화", "수", "목", "금", "토"); // 요일 테이블 정의
	myMonthTbl = new Array(31, 28, 31, 30, 31, 30, 31, 31, 30, 31, 30, 31); // 월 테이블 정의

	myYear = myDate.getFullYear(); // 윤년을 계산 
	if (((myYear % 4) == 0 && (myYear % 100) != 0) || (myYear % 400) == 0) {
		myMonthTbl[1] = 29;
	}

	myMonth = myDate.getMonth(); // 달을 취득 (10 월 ~ 11 월)
	myToday = myDate.getDate(); // 오늘의 '일'을 제거
	myDate.setDate(1); // 날짜를 '1 일 '로 바꾸고,
	myWeek = myDate.getDay(); // '1 일 '요일을 리턴해서 대입
	myTblLine = Math.ceil((myWeek + myMonthTbl[myMonth]) / 7); // 달력의 행
	
	myTable = new Array(7 * myTblLine); // 테이블 셀 몇 분 정의

	for (i = 0; i < 7 * myTblLine; i++)
		myTable[i] = ""; // myTable를  클리어 
	for (i = 0; i < myMonthTbl[myMonth]; i++)
		myTable[i + myWeek] = i + 1; // 날짜 포함


		// 레이아웃 출력 
	document.write("<table border='1'>"); // 테이블 작성 시작
	document.write("<tr> <td colspan='7' bgcolor='#7fffd4'>"); // 머리글 행 세트
	document.write("<strong>" + myYear + "년" + (myMonth + 1)
			+ "월 캘린더 </strong>");
	document.write("</ td> </ tr>");

	document.write("<tr>"); // 요일 표제 세트
	for (i = 0; i < 7; i++) { // 줄 (1 주) 루프
		document.write("<td align = 'center'");
		if (i == 0)
			document.write("bgcolor = '#fa8072'>"); // 일요일 셀 색
		else
			document.write("bgcolor = '#ffebcd'>"); // 월 ~ 토 셀 색
		document.write("<strong>" + myWeekTbl[i] + "</strong>");
		document.write("</td>");
	}
	document.write("</tr>");

	for (i = 0; i < myTblLine; i++) { // 테이블의 "행"루프
		document.write("<tr>"); // 행의 시작
		for (j = 0; j < 7; j++) { // 테이블 "열"의 루프
			document.write("<td align = 'center'"); // 열 (셀) 만들기
			myDat = myTable[j + (i * 7)]; // 써 넣을 내용 검색
			if (myDat == myToday)
				document.write("bgcolor = '#00ffff'>"); // 오늘의 셀 색
			else if (j == 0)
				document.write("bgcolor = '#ffb6c1'>"); // 일요일 셀 색
			else
				document.write("bgcolor = '#ffffe0'>"); // 평일 셀 색
			document.write("<strong>" + myDat + "</strong>"); // 날짜 세트
			document.write("</td>"); // 열 (셀)의 끝
		}
		document.write("</tr>"); // 행의 끝
	}
	document.write("</table>"); // 테이블 끝

</script>
</head>
<body>
</body>
</html>
~~~

![image](https://user-images.githubusercontent.com/52989294/85388848-91380e00-b581-11ea-802f-beddc4675218.png)

***

~~~html
<!DOCTYPE html >
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=EUC-KR">
<title>with</title>
<script type="text/javascript">
	score_array = new Array("홍길동", 80, 90, 100)

	with (score_array) {
		document.write("<h2>" + join() + "<br>");
		document.write(join("+") + "<br>");
		document.write(join("|") + "<br>");
	}
</script>
</head>
<body>

</body>
</html>
~~~

![image](https://user-images.githubusercontent.com/52989294/85389063-e70cb600-b581-11ea-88c1-096af29653ba.png)

***

~~~ html
<!DOCTYPE html >
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=EUC-KR">
<title>Score</title>
<script type="text/javascript">
	function sung(my_name, kor, eng, mat) {
		this.name = my_name;
		this.kor = kor;
		this.eng = eng;
		this.mat = mat;
		this.gettot = function() {
			tot = this.kor + eng + mat;
			return tot;
		}

		this.getavg = function() {
			avg = this.gettot() / 3.0;
			return avg;
		}
	}

	var jumsu = [];
	jumsu[0] = new sung("도미니까", 90, 80, 70);
	jumsu[1] = new sung("도미니꼬", 50, 60, 70);
	jumsu[2] = new sung("폴리오", 100, 90, 80);
	for (i = 0; i < jumsu.length; i++) {
		document.write(jumsu[i].name + "：" + jumsu[i].gettot() + " "
				+ jumsu[i].getavg() + "<br>");
	}

	for (i = 0; i < jumsu.length; i++) {
		with (jumsu[i]) {
			document.write(name + "：" + gettot() + " " + getavg() + "<br>");
		}
	}
</script>
</head>
<body>

</body>
</html>
~~~

![image](https://user-images.githubusercontent.com/52989294/85389214-18858180-b582-11ea-9035-59af1f0e0641.png)

***

~~~ html
<!DOCTYPE html >
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=EUC-KR">
<title>demo_sec06</title>
<script type="text/javascript">
	function sung(my_name, kor, eng, mat) {
		this.name = my_name;
		this.kor = kor;
		this.eng = eng;
		this.mat = mat;
		this.gettot = function() {
			tot = this.kor + eng + mat;
			return tot;
		}
		this.getavg = function() {

			avg = this.gettot() / 3;
			return avg
		}
	}

	var jumsu = [];
	jumsu[0] = new sung("도미니까", 90, 80, 70);
	jumsu[1] = new sung("도미니꼬", 50, 60, 70);
	jumsu[2] = new sung("폴리오", 100, 90, 80);

	document.write("  <h2>  Object Print _for</h2>");
	for (i = 0; i < jumsu.length; i++) {
		document.write(jumsu[i].name + "： 총점 (" + jumsu[i].gettot() + ") 평균 ( "
				+ jumsu[i].getavg() + "  )<br>");
	}

	document.write("<hr>");
	document.write("  <h2>  Object Print _for  in</h2>");

	for (sungres in jumsu) { // jumsu가 index 값을 리턴해줌
		document.write("no [" + [ sungres ] + "] :" + jumsu[sungres].name
				+ "： 총점 (" + jumsu[sungres].gettot() + ") 평균 ( "
				+ jumsu[sungres].getavg() + "  )<br>");
	}
</script>
</head>
<body>

</body>
</html>
~~~

![image](https://user-images.githubusercontent.com/52989294/85389327-3c48c780-b582-11ea-85fd-dc47a0f53519.png)

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
- document : DOM의 최고 상위 객체, html 문서
- element : html 문서에 사용되는 모든 태그, 요소
- attribute : 엘리먼트가 가진 속성
- node : 객체를 지칭, 요소, 속성, 텍스트 노드

### JS Node DOM.Properties
- nodeName : 노드의 이름을 리턴, 텍스트 노드의 경우 #text를 리턴
- nodeType : 노드의 유형을 정수로 리턴(1 : 요소, 2 : 속성, 3 : 텍스트, 8 : 주석)
- nodeValue : 노드의 값을 리턴. 요소는 값이 없으므로 null 리턴
- childNodes : 노드의 자식 노드들을 노드 리스트(배열)로 리턴
- firstChild : 노드의 첫번째 자식노드를 리턴
- lastChild : 노드의 마지막 자식노드를 리턴
- previousSibling : 노드의 이전 위치에 자리한 형제노드를 리턴
- nextSibiling : 노드의 다음 위치에 자리한 형제노드를 리턴
- parentNode : 노드의 부모노드(요소)를 리턴

### JS document 객체 메소드
- Createattribute( ) : 속성 노드를 생성
- createElement( ) : 엘리먼트를 생성
- appendChild( ) : 하위 엘리먼트를 추가
- execCommand( ) : 문서에서 사용 가능한 명령을 실행
- getElementsById( ) : id 속성이 일치하는 엘리먼트를 리턴
- getElementsByName( ) : name 속성이 일치하는 엘리먼트를 리턴
- getElementsByTagName( ) : 태그 네임이 일치하는 엘리먼트를 리턴

***

~~~ html
<!DOCTYPE html>
<html>
<head>
<meta charset="EUC-KR">
<title>my title</title>
<style type="text/css">
.myclass {
	background-color: pink;
	font-size: 1.5em;
}

.myp {
	border: 1px;
}

.myp02 {
	font-size: 2em;
}
</style>

</head>
<body>
	<div class="myclass">abcd</div>

	<p class="myp">가나다라</p>

	<p class="myp02">123</p>

	<script type="text/javascript">
		var res = document.getElementsByTagName("div");
		document.write("res : " + res + "<br/>");

		var res02 = document.getElementsByTagName("p");
		document.write("res02 : " + res02 + "<br/>");

		var res03 = document.getElementsByTagName("div");
		document.write("res03[0]  : " + res03[0] + "<br/>");

		var res04 = document.getElementsByTagName("div");
		document.write("res04[0].TEXT_NODE  :" + res04[0].TEXT_NODE + "<br/>");

		var res05 = document.getElementsByTagName("div");
		document.write("res05[0].getRootNode()  : " + res05[0].getRootNode()
				+ "<br/>");

		var res06 = document.getElementsByTagName("div");
		// getRootNode() --> 맨위(Root)에  <!DOCTYPE html>까지 올라감
		document.write("res06[0].getRootNode().nodeName : "
				+ res06[0].getRootNode().nodeName + "<br>"); // <!DOCTYPE html>의 nodeName 출력 ..#document
		document.write("res06[0].getRootNode().nodeType : "
				+ res06[0].getRootNode().nodeType + "<br>");
		document.write("res06[0].getRootNode().nodeValue  : "
				+ res06[0].getRootNode().nodeValue + "<br>");

		var res06_1 = document.getElementsByTagName("div");
		document.write("res06_1[0].nodeName  :  " + res06_1[0].nodeName
				+ "<br>"); //res06_1[0]의 nodeName 출력 .. DIV
		document.write("res06_1[0].nodeType  :  " + res06_1[0].nodeType
				+ "<br>");
		document.write("res06_1[0].nodeValue  :  " + res06_1[0].nodeValue
				+ "<br>");

		var res07 = document.getElementsByTagName("p");
		document.write("res07 : " + res07 + "<br>");
		document.write("res07[1].nodeName : " + res07[1].nodeName + "<br>");
		document.write("res07[1].nodeType : " + res07[1].nodeType + "<br>");
		document.write("res07[1].nodeValue : " + res07[1].nodeValue + "<br>");
		document.write("res07[1].hasChildNodes() : " + res07[1].hasChildNodes()
				+ "<br>");
		document.write("res07[1].nextSibling : " + res07[1].nextSibling
				+ "<br>");

		document.write("res07[1].nextSibling.nodeName : "
				+ res07[1].nextSibling.nodeName + "<br>");
	</script>

</body>
</html>
~~~

![image](https://user-images.githubusercontent.com/52989294/85390122-4fa86280-b583-11ea-9f16-37e41be1c5be.png)

***

~~~ html
<!DOCTYPE html>
<html>
<head>
<meta charset="EUC-KR">
<title>Insert title here</title>
</head>
<body>
	<p>첫번째 p 태그</p>
	<p>두번째 p 태그</p>
	<script type="text/javascript">
		// getElementsByTagName()은 태그의 이름을 직접 호출해서
		// 객체로 관리하고 인덱스로 참조한다.
		// 내용을 수정할 때는 태그 객체.innerText or innerHTML
		var x = document.getElementsByTagName("p");
		document.write(x[0].innerText + " : " + x[1].innerText + "<br>");
		document.write("내용 변경을 해보자 <br>");
		x[0].innerText = "<b>변경했다.</b>";
		x[1].innerHTML = "<b>변경했다 두번째 p 태그</b>";
		document.write(x[0].innerText + " : " + x[1].innerText + "<br>");

		for (var i = 0; i < x.length; i++) {
			document.write(x[i].innerHTML = "<font color = red> 전체변경 </font>");
		}
	</script>
</body>
</html>
~~~

![image](https://user-images.githubusercontent.com/52989294/85390256-841c1e80-b583-11ea-8a93-b061aa511b87.png)

***

~~~ html
<!DOCTYPE html>
<html>
<head>
<meta charset="EUC-KR">
<title>Insert title here</title>
</head>
<body>
	<div id="mydiv">
		<p>첫번째</p>
		<p>두번째</p>
	</div>

	<p>세번째</p>
	<script type="text/javascript">
		//getElementById() : 태그 안에 id로 지정된 이름을 가져옴
		var res = document.getElementById("mydiv").getElementsByTagName("p");
		document.write(res[0].innerText);

		res = document.getElementsByTagName("div")[0].getElementsByTagName("p");

		for(var i = 0; i < res.length ; i++){
			res[i].innerText = "내용 변경";
		}
	</script>
</body>
</html>
~~~

![image](https://user-images.githubusercontent.com/52989294/85390327-a4e47400-b583-11ea-9fbb-5f24a1ec49f0.png)

***

~~~ html
<!DOCTYPE html>
<html>
<head>
<meta charset="EUC-KR">
<title>Insert title here</title>
</head>
<body>
	<p class="p01">첫번째</p>
	<p class="p02">두번째</p>
	<p name="p03">세번째</p>
	<div></div>
	<script type="text/javascript">
		var x = document.getElementsByClassName("p01");
		var y = document.getElementsByClassName("p02");
		var z = document.getElementsByName("p03");

		for (var i = 0; i < x.length; i++) {
			console.log(x[i]);
		}

		for (var i = 0; i < x.length; i++) {
			console.log(y[i]);
		}

		document.write(z[0].innerText + 1 + "<br>");

		res = document.getElementsByTagName("div");

		res[0].innerText = z[0].innerText;

		document.write(res[0].innerText + "111");

		var res = z[0].innerText;
		document.getElementsByTagName("div")[0].innerHTML = "<font color=red size=7>"
				+ res + "</font>";
	</script>

</body>
</html>
~~~

![image](https://user-images.githubusercontent.com/52989294/85390406-c34a6f80-b583-11ea-8391-863c877068af.png)

***

~~~ html
<!DOCTYPE html>
<html>
<head>
<meta charset="EUC-KR">
<title>Insert title here</title>
</head>
<body>
	<div class="mydiv">mydiv</div>
	<p>첫번째</p>
	<p name="myp">두번째</p>
	<script type="text/javascript">
		//document.write(document.myp.name);

		var z = document.getElementsByName("myp");
		document.write(z[0].innerText + "zzz");

		//querySelector(), querySelectorAll()
		var x = document.querySelector(".mydiv");
		var y = document.querySelectorAll("p");
		x.innerHTML = "<b> 변경이야 </b>";

		for (var i = 0; i < y.length; i++) {
			y[i].innerHTML = "<b> p변경 </b>";
		}

		// ex) document.querySelectorAll("p span"); p 요소 안에 span 속성을 모두 리턴
		// document.querySelectorAll("div.myclass p");
		// myclass라는 class 이름의 div 태그를 찾아서 inner 모든 p 태그를 리턴
		// querySelectorAll("div > *"); div 안에 있는 모든 요소를 리턴
	</script>

</body>
</html>
~~~

![image](https://user-images.githubusercontent.com/52989294/85390518-e2e19800-b583-11ea-8ac0-5d6149f44b60.png)

***

~~~ html
<!DOCTYPE html >
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=EUC-KR">
<title>demo_sec08</title>

</head>

<body>

	<script type="text/javascript">
		var myDiv = document.createElement("div");
		var myPtag = document.createElement("p");
		var myAtag = document.createElement("a");
		myAtag.setAttribute("href", "#");//<div><p><a href="#"> 자바스크립트 </a><p</div>

		var txt1 = document.createTextNode("자바스크립트");

		myAtag.appendChild(txt1);
		myPtag.appendChild(myAtag);
		myDiv.appendChild(myPtag);

		document.body.appendChild(myDiv);

		var myUl = document.createElement("ul");
		var myLi1 = document.createElement("li");
		var myLi2 = document.createElement("li");
		var myLi3 = document.createElement("li");

		var theAtag1 = document.createElement("a");
		theAtag1.setAttribute("href", "http://www.nate.com");
		var theAtag2 = document.createElement("a");
		theAtag2.setAttribute("href", "http://www.daum.net");
		var theAtag3 = document.createElement("a");
		theAtag3.setAttribute("href", "http://www.naver.com");

		var txt2 = document.createTextNode("네이트");
		var txt3 = document.createTextNode("다음");
		var txt4 = document.createTextNode("네이버");

		theAtag1.appendChild(txt2);
		myLi1.appendChild(theAtag1);
		myUl.appendChild(myLi1);
		theAtag2.appendChild(txt3);
		myLi2.appendChild(theAtag2);
		myUl.appendChild(myLi2);
		theAtag3.appendChild(txt4);
		myLi3.appendChild(theAtag3);
		myUl.appendChild(myLi3);

		document.body.appendChild(myUl);

		document.write("Last update : ", document.lastModified);
	</script>
	<br>
	<br>
	<br>
	<br>
	<br>

	<IMG NAME="img1" SRC="mini.png" border=2 align=bottom alt="미니">

	<br>
	<br>
	<script type="text/javascript">
		document.write("이미지 주소 : " + document.img1.src + "<BR>")
		document.write("이미지 이름 : " + document.img1.name + "<BR>")
		document.write("이미지 가로길이 : " + document.img1.width + "<BR>")
		document.write("이미지 세로길이 : " + document.img1.height + "<BR>")
		document.write("이미지 가로여백 : " + document.img1.hspace + "<BR>")
		document.write("이미지 세로여백 : " + document.img1.vspace + "<BR>")
		document.write("이미지 태두리 굵기 : " + document.img1.border + "<BR>")
		document.write("이미지 정렬방법 : " + document.img1.align + "<BR>")
		document.write("이미지 설명 : " + document.img1.alt + "<BR>")
		document.write("이미지 전송 완료 상태 : " + document.img1.complete + "<BR>")
	</script>
</body>
</html>
~~~

![image](https://user-images.githubusercontent.com/52989294/85390624-0a386500-b584-11ea-9d23-12dbce0ac196.png)

***

~~~ html
<!-- 버튼 누르면 색상 변경 -->
<!DOCTYPE html>
<html>
<head>
<meta charset="EUC-KR">
<title>Insert title here</title>
<style type="text/css">
#my_div {
	margin: 100px,100px,100px,100px;
	background-color: yellow;
	font-size: 1.5em;
}
</style>
<script type="text/javascript">
	function myfun() {
		my_div.style.color = 'red';
	}
</script>

</head>
<body>
	<div id="my_div" style = "color: blue;">연습이라네</div>

	<form>
		<input type="button" value='스타일 변경' onclick="myfun();">
	</form>
</body>
</html>
~~~

***

## JavaScript Object
- 엘리먼트의 style 속성 객체
- css 속성을 지정할 수 있는 객체
- 나열해서 여러 개의 속성을 대입
- 인라인, 외부 스타일 적용

### JavaScript Object 적용
- 인라인 스타일
	- element.style.color = "red"

- 외부 스타일
	- document.styleSheets[0].rules[0].style.color = "red"

### Js Style Object _ position
- 객체의 위치 지정
- static : default, 흐름에 위치지정
- absolute : left **|** right **|** top **|** bottom
- relative : 엘리먼트 내부에 absolute에 기준

### Js Style Object _ float
- Js : styleFloat(cssFloat)
- none : default
- left : 왼쪽 끝부터 정렬
- right : 오른쪽 끝부터 정렬

***

## Js Form 객체
- <FORM> 태그는 독립적으로 사용하지 않는다.
- <FORM> 태그 안에는 여러 가지 입력 양식을 사용
- DOM(Document Object Model)로 접근하여 제어
- Form 객체를 액세스 하여 제어

### Form 객체 속성
- action : <FORM> 태그의 ACTION 속성과 동일 적용
- elements : <FORM> 태그 안에 있는 양식을 배열로 저장
- Encoding : <FORM> 태그의 ENCTYPE 속성과 동일하게 적용
- length : <FORM> 태그 안에 있는 양식의 수를 리턴
- name : <FORM> 태그의 NAME 속성과 동일하게 적용
- method : <FORM> 태그의 METHOD 속성과 동일하게 적용
- target : <FORM> 태그의 TARGET 속성과 동일하게 적용

### Form 객체의 메소드와 이벤트
- 메소드
	- blur( ) : 커서를 제거
	- reset( ) : 양식을 초기화
	- submit( ) : 입력된 양식 내용을 서버에 전송
- 이벤트
	- onReset : 리셋 버튼을 누를 때 발생
	- onSubmit : 서브밋 버튼을 누를 때 발생

### Js Form 객체 액세스
- 태그의 name 속성 : document.폼명.요소명
- forms/elements 배열
	- document.fm.name.value
	- document.forms[0].elements[0].value
	- document.forms['fm'].elements['name'].value
	- document.forms['fm']['name'].value

***

~~~ html
<!DOCTYPE html >
<html>
<head>
<title>회원가입 폼</title>
<script type="text/javascript">
	function result() {
		var dsp_str = "[1] name   :  " + document.myform.name + "\n"
		dsp_str += "[2] action  :  " + document.myform.action + "\n"
		dsp_str += "[3] target  :  " + document.myform.target + "\n"
		dsp_str += "[4] method  :  " + document.myform.method + "\n"
		dsp_str += "[5] encoding  :  " + document.myform.encoding + " \n"
		dsp_str += "[6] length  :  " + document.myform.length
		alert(dsp_str);
	}
	function formres(myform) {
		//value,name 확인
		var el_str = " ";

		for (var i = 0; i < myform.length; i++) {
			if (myform.elements[i].value == null || !myform.elements[i].checked) {
				el_str += myform.elements[i].name + "\n";
			}
		}
		myform.intro.value = el_str;
	}
	
	function formview(myform) {
		var el_str = " ";
		//  체크박스 
		var result = [];
		var f = myform.hobby;
		for (var i = 0; i < f.length; i++) {
			if (f[i].checked) {
				result.push(f[i].value);
			}
		}
		//라디오
		var result2 = [];
		var f2 = myform.s;
		for (var i = 0; i < f2.length; i++) {
			if (f2[i].checked) {
				result2.push(f2[i].value);
			}
		}

		el_str += "이름은 " + myform.name.value + "이고 " + "\n" + "전화번호는  "
				+ myform.tel.value + "-" + myform.tel2.value + "-"
				+ myform.tel3.value + ", \n 직업은"
				+ myform.job.options[myform.job.selectedIndex].value
				+ "이며 \n 성별은 " + result2.toString() + "이고 \n 취미는"
				+ result.toString() + "입니다 ";

		myform.intro.value = el_str;
	}
</script>

</head>
<body bgcolor="#EFDCDC">
	<p align="center">
		<font size="4" color="blue"><b>회원 정보 입력</b></font>
	</p>
	<hr>
	<form name="myform" method="post">
		<table width="600" border="1" cellspacing="5" align="center">
			<tr>
				<td>아이디</td>
				<td><input type="text" name="id" size="10"></td>
			</tr>
			<tr>
				<td>비밀번호</td>
				<td><input type="password" name="pw" size="10"></td>
			</tr>
			<tr>
				<td>이름</td>
				<td><input type="text" name="name" size="10"></td>
			</tr>
			<tr>
				<td>전화번호</td>
				<td><input type="text" name="tel" size="3" value="02">-
					<input type="text" name="tel2" size="4">- <input
					name="tel3" size="4"></td>
			</tr>
			<tr>
				<td>직업</td>
				<td><select size="1" name="job">
						<option selected>자산관리사</option>
						<option>프로그래머</option>
						<option>회사원</option>
						<option>기타</option>
				</select></td>
			</tr>
			<tr>
				<td>성별</td>
				<td><INPUT type="radio" name="s" value="Male"> 남자
					&nbsp; &nbsp; <INPUT type="radio" name="s" value="Female">
					여자</td>
			</tr>

			<tr>
				<td>취미</td>
				<td><input type="checkbox" name="hobby" value="travel ">
					여행 &nbsp; &nbsp; <input type="checkbox" name="hobby" value="ski">
					스키 &nbsp; &nbsp; <input type="checkbox" name="hobby"
					value="mountaineering"> 등산 &nbsp; &nbsp; <input
					type="checkbox" name="hobby" value="shopping"> 쇼핑 &nbsp;
					&nbsp;</td>
			</tr>
			<tr>
				<td>자기 소개</td>
				<td><textarea rows="10" cols="50" name="intro">
     자기소개를 간략하게  글로 쓰세요
    </textarea></td>
			<tr>
				<td colspan="2" align="center"><input type="button"
					value="폼의 정보" onclick="result()"> <input type="button"
					value="확인" onclick="formview(this.form)"> &nbsp; &nbsp; 
					
					<input type="button"
					value="확인2" onclick="formres(this.form)">
					
					<input
					type="reset" value="취소"></td>
			</tr>

		</table>
	</form>
</body>
</html>
~~~

![image](https://user-images.githubusercontent.com/52989294/85391030-9ea2c780-b584-11ea-9acd-6bf3403d0d1c.png)

***

## JS TABLE OBJECT

### Table Object 속성
- border : 테이블의 테두리 너비를 설정하거나 리턴
- caption : 테이블의 제목을 설정하거나 리턴
- cellPadding : 셀 테두리와 셀 내용 사이의 공간 크기를 설정하거나 리턴
- cellSpacing : 테이블 셀 사이의 공간 크기를 설정하거나 리턴
- frame : 테이블 테두리를 설정하거나 리턴
- rules : 테이블에 테두리를 설정하거나 리턴
- summary : 테이블의 설명을 설정하거나 리턴
- tFoot : 테이블 TFoot 개체를 리턴
- tHead : 테이블 THead 개체를 리턴
- width : 테이블의 너비를 설정하거나 리턴

### TableRow_메소드
- 열(셀)에 있는 객체에 접근하는 방법
	- mytable.rows[0].cells[0].childNodes.item(0).value
	- mytable.rows[0].cells[0].innerHTML

### TableCell 개체
- TableCell 개체는 HTML 테이블의 셀을 나타냄
- HTML 문서의 각 <**td**> 태그에 대해 tableCell 개체가 만들어짐

~~~ html
<!DOCTYPE html >
<html>
<head>
<title>demo_sec11</title>
<script type="text/javascript">
	function addRow() {
		var myRow = mytable.insertRow();
		myRow.onmouseover = function() {
			mytable.clickedRowIndex = this.rowIndex
		};
		var myCell1 = myRow.insertCell();
		var myCell2 = myRow.insertCell();
		var myCell3 = myRow.insertCell();
		var myCell4 = myRow.insertCell();
		myCell1.innerHTML = "<input type=text name=name>";
		myCell2.innerHTML = "<input type=text name=addr>";
		myCell3.innerHTML = "<input type=text name=tel>";
		myCell4.align = "center";
		myCell4.innerHTML = "<input type=button value=\"행삭제\" onClick=\"delRow()\">";
	}
	function delRow() {
		mytable.deleteRow(mytable.clickedRowIndex);
	}
</script>
</head>
<body bgcolor="#EFDCDC">
	<center>
		<form action="#">
			<table id="mytable" border="1" width="850" cellpadding="0"
				cellspacing="0">
				<tr>
					<th width="250">이름</th>
					<th width="250">주소</th>
					<th width="250">전화번호</th>
					<th width="100"><input type=button value="행추가"
						onClick="addRow()"></th>
				</tr>
				<tr onMouseOver="mytable.clickedRowIndex=this.rowIndex">
					<td><input type=text name=name></td>
					<td><input type=text name=addr></td>
					<td><input type=text name=tel></td>
					<td align="center"><input type=button name=mytable_delRow
						value="행삭제" onClick="delRow()"></td>
				</tr>
			</table>
		</form>
	</center>
</body>
</html>
~~~

![image](https://user-images.githubusercontent.com/52989294/85391158-c8f48500-b584-11ea-98f6-4e301a845700.png)

***

### 행,열 입력해서 표 만들기 + 리셋 버튼

~~~ html
<!-- 방법1 단,취소 후 재생성 불가 -->
<!DOCTYPE html>
<html>
<head>
<meta charset="EUC-KR">
<title>Insert title here</title>
<script type="text/javascript">
	function addTable(a1, a2) {
		var tag = "<table border='1'>";
		var b1 = parseInt(a1);
		var b2 = parseInt(a2);
		for (var i = 0; i < b1; i++) {
			tag += "<tr>";
			for (var j = 0; j < b2; j++) {
				tag += "<td>" + "1" + "</td>";
			}
			tag += "</tr>";
		}
		tag += "</table>";
		area.innerHTML = tag;
	}
	function delTable() {
		document.getElementById("row").value = '';
		document.getElementById("col").value = '';
		area.remove();
	}
</script>

</head>
<body>
	<div>
		행 입력 : <input type=text id=row> 열 입력 : <input type=text id=col>
		<input type=button value="테이블 생성" onClick="addTable(row.value, col.value)"> 
		<input type=button value="취소" onClick="delTable()">
	</div>

	<div id="area"></div>
</body>
</html>
~~~

***

~~~ html
<!-- 방법2 -->
<!DOCTYPE html>
<html>
<head>
<meta charset="EUC-KR">
<title>Insert title here</title>
<script type="text/javascript">
	function create(myform) {
		x = myform.myrow.value;
		y = myform.mycell.value;

		var my_div = document.getElementsByTagName("div");
		var mytable = document.createElement("table");

		mytable.setAttribute("border", "2");
		mytable.setAttribute("width", "50%");

		for (i = 0; i < x; i++) {
			row = mytable.insertRow();
			for (j = 0; j < y; j++) {
				cell = row.insertCell();
				cell.innerText = "1";
			}
		}
		document.body.appendChild(mytable);

	}
	function my_delete() {
		myform.myrow.value = '';
		myform.mycell.value = '';
		document.body.lastChild.remove();
		
	}
	
</script>

</head>
<body>
	<div>
		<form name="myform">
			행 입력 : <input type=text name="myrow"> 열 입력 : <input type=text
				name="mycell"> <input type=button value="확인"
				onClick="create(this.form);"> <input type=button value="취소"
				onClick="my_delete()">
		</form>
	</div>
	<div id="res"></div>
</body>
</html>
~~~

***

## Js Event Handler 정의
- 이벤트(event) : 사용자에 의한 특정 행위의 결과로 발생하는 사용자와 프로그램 사이의 상호작용 처리의 요인
- 이벤트 객체는 이벤트가 발생한 요소, 키보드 키의 상태, 마우스의 위치와 마우스 버튼의 상태 등과 같은 이벤트 상태를 나타냄
- 이벤트 핸들러(Event Handler) : 함수, 메소드 등으로 이벤트를 처리하는 기능을 말함

### 달력 만들기

~~~ html
<!DOCTYPE html >
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=EUC-KR">
<title>demo_sec12</title>
<script language="JavaScript">
	var Selected_Month;
	var Selected_Year;
	var Current_Date = new Date();
	var Current_Month = Current_Date.getMonth();

	var Days_in_Month = new Array(31, 28, 31, 30, 31, 30, 31, 31, 30, 31, 30,
			31);
	var Month_Label = new Array('January', 'February', 'March', 'April', 'May',
			'June', 'July', 'August', 'September', 'October', 'November',
			'December');

	var Current_Year = Current_Date.getYear();
	if (Current_Year < 1000)
		Current_Year += 1900

	var Today = Current_Date.getDate();

	function Header(Year, Month) {
		//윤년 계산 
		if (Month == 1) {
			Days_in_Month[1] = ((Year % 400 == 0) || ((Year % 4 == 0) && (Year % 100 != 0))) ? 29
					: 28;
		}
		var Header_String = Month_Label[Month] + ' ' + Year;
		return Header_String;
	}

	//레이아웃

	function Make_Calendar(Year, Month) {
		var First_Date = new Date(Year, Month, 1);
		var Heading = Header(Year, Month);
		var First_Day = First_Date.getDay() + 1;
		if (((Days_in_Month[Month] == 31) && (First_Day >= 6))
				|| ((Days_in_Month[Month] == 30) && (First_Day == 7))) {
			var Rows = 6;
		} else if ((Days_in_Month[Month] == 28) && (First_Day == 1)) {
			var Rows = 4;
		} else {
			var Rows = 5;
		}

		var HTML_String = '<table><tr><td valign="top"><table BORDER=4 CELLSPACING=1 cellpadding=2 FRAME="box" BGCOLOR="C0C0C0" BORDERCOLORLIGHT="808080">';

		HTML_String += '<tr><th colspan=7 BGCOLOR="FFFFFF" BORDERCOLOR="000000">'
				+ Heading + '</font></th></tr>';

		HTML_String += '<tr><th ALIGN="CENTER" BGCOLOR="FFFFFF" BORDERCOLOR="000000">Sun</th><th ALIGN="CENTER" BGCOLOR="FFFFFF" BORDERCOLOR="000000">Mon</th><th ALIGN="CENTER" BGCOLOR="FFFFFF" BORDERCOLOR="000000">Tue</th><th ALIGN="CENTER" BGCOLOR="FFFFFF" BORDERCOLOR="000000">Wed</th>';

		HTML_String += '<th ALIGN="CENTER" BGCOLOR="FFFFFF" BORDERCOLOR="000000">Thu</th><th ALIGN="CENTER" BGCOLOR="FFFFFF" BORDERCOLOR="000000">Fri</th><th ALIGN="CENTER" BGCOLOR="FFFFFF" BORDERCOLOR="000000">Sat</th></tr>';

		var Day_Counter = 1;
		var Loop_Counter = 1;
		for (var j = 1; j <= Rows; j++) {
			HTML_String += '<tr ALIGN="left" VALIGN="top">';
			for (var i = 1; i < 8; i++) {
				if ((Loop_Counter >= First_Day)
						&& (Day_Counter <= Days_in_Month[Month])) {
					if ((Day_Counter == Today) && (Year == Current_Year)
							&& (Month == Current_Month)) {
						HTML_String += '<td BGCOLOR="FFFFFF" BORDERCOLOR="000000"><strong><font color="red">'
								+ Day_Counter + '</font></strong></td>';
					} else {
						HTML_String += '<td BGCOLOR="FFFFFF" BORDERCOLOR="000000">'
								+ Day_Counter + '</td>';
					}
					Day_Counter++;
				} else {
					HTML_String += '<td BORDERCOLOR="C0C0C0"> </td>';
				}
				Loop_Counter++;
			}
			HTML_String += '</tr>';
		}
		HTML_String += '</table></td></tr></table>';
		cross_el = document.all.Calendar
		cross_el.innerHTML = HTML_String;
	}

	function Check_Nums() {
		if ((event.keyCode < 48) || (event.keyCode > 57)) {
			return false;
		}
	}

	function On_Year() {
		var Year = document.when.year.value;
		if (Year.length == 4) {
			Selected_Month = document.when.month.selectedIndex;
			Selected_Year = Year;
			Make_Calendar(Selected_Year, Selected_Month);
		}
	}

	function On_Month() {
		var Year = document.when.year.value;
		if (Year.length == 4) {
			Selected_Month = document.when.month.selectedIndex;
			Selected_Year = Year;
			Make_Calendar(Selected_Year, Selected_Month);
		}

	}

	function Defaults() {
		var Mid_Screen = Math.round(document.body.clientWidth / 2);
		document.when.month.selectedIndex = Current_Month;
		document.when.year.value = Current_Year;
		Selected_Month = Current_Month;
		Selected_Year = Current_Year;
		Make_Calendar(Current_Year, Current_Month);
	}

	function Skip(Direction) {
		if (Direction == '+') {
			if (Selected_Month == 11) {
				Selected_Month = 0;
				Selected_Year++;
			} else {
				Selected_Month++;
			}
		} else {
			if (Selected_Month == 0) {
				Selected_Month = 11;
				Selected_Year--;
			} else {
				Selected_Month--;
			}
		}
		Make_Calendar(Selected_Year, Selected_Month);
		document.when.month.selectedIndex = Selected_Month;
		document.when.year.value = Selected_Year;
	}
</script>
</head>
<body onLoad="Defaults()">
	<div id=NavBar style="position: relative; width: 286px; top: 5px;"
		align="left">
		<form name="when">
			<table>
				<tr>
					<td><input type="button" value="<-- Last" onClick="Skip('-')"></td>
					<td></td>
					<td><select name="month" onChange="On_Month()">

							<script type="text/javascript">
								for (j = 0; j < Month_Label.length; j++) {
									document.writeln('<option value=' + j + '>'
											+ Month_Label[j]);
								}
							</script></td>
					<td><input type="text" name="year" size=4 maxlength=4
						onKeyPress="return Check_Nums()" onKeyUp="On_Year()"></td>
					<td></td>
					<td><input type="button" value="Next -->" onClick="Skip('+')"></td>
				</tr>
			</table>
		</form>
	</div>
	<div id=Calendar style="position: relative; width: 238px; top: -2px;"
		align="left"></div>


</body>
</html>
~~~

![image](https://user-images.githubusercontent.com/52989294/85391649-6b146d00-b585-11ea-8291-598ca93f4a77.png)
