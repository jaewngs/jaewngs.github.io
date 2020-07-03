---



title:  "Servlet/JSP 이벤트 & Context Filter"



layout: post



---


## [오늘 할 내용]
1. Servlet/JSP의 이벤트를 구현할 수 있다.
	- 내장객체가 연동될 때 이벤트가 발생된다.
	- Filter를 사용하여 요청시에 중간 작업을 할 수 있다.
2. Context의 Filter를 이용해서 페이지 요청에 가로채기를 할 수 있다.
	- 설정파일에 선언하는 방법 : web.xml, 주석으로 선언하는 방법 @
	- 웹 보안을 처리하는 객체
		- Authentication Filters : 서버 -> 웹서버 -> 웹어플리케이션 -> 페이지 연동
		- Logging and Auditing Filters : 인증값이 Authentication Filters을 통해 페이지 권한을 받고 다른페이지 요청을 할 경우 유효성 검사
			- php -> html -> (전페이지의 인증값을 검증) -> jsp
		- Image conversion Filters
		- Data compression Filters
		- Encryption Filters
		- Tokenizing Filters
		- Filters that trigger resource access events
		- XSL/T filters
		- Mime-type chain Filter

3.ServletContext, ServletConfig, HttpSession, Request의 객체에 발생되는 이벤트를 활용할 수 있다.






















