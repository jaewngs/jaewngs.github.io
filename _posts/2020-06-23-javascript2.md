---



title:  "Javascript2"



layout: post



---


## [오늘 할 내용]
1. Javascript Style Object
2. Javascript 폼객체
***

sec09 demo

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
- absolute : left | right | top | bottom
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


































