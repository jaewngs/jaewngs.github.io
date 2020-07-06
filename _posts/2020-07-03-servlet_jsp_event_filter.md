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
			- 보안인증 코드 등 페이지 요청 보안(QR코드)
		- Data compression Filters
		- Encryption Filters : 툴을 이용해서 필터를 기반으로 연동하는 프로그램을 호출
		- Tokenizing Filters : 페이지 요청 get/post 방식으로 폼 데이터가 전송되거나 URL에 데이터가 Param값을 가지고 전송될 때 사용하는 필터(ex. http://localhost:8787/Day57/Test?no=3&name=%234%)
		- Filters that trigger resource access events
		- XSL/T filters
		- Mime-type chain Filter
	- ex) 단일 필터 작성
		- Request -> filter가 가로채기 -> Response 발생
		- jsp 실행 -> filter 발생 -> Web 화면 결과

3.ServletContext, ServletConfig, HttpSession, Request의 객체에 발생되는 이벤트를 활용할 수 있다.

### WebApplication
- WAS(Tomcat 5.0) [ /: http   / ftp : ] api, DB
    - java (JDBCTemplate) ... Tomcat documentation 10)
    - JNDI ... Tomcat documentation 9)
    - JDBC Connection Pool org.apache.tomcat.jdbc.pool ... Tomcat documentation 32)

- /lib, /cfg, /bin, /webapps, /work, /log
- server.xml -> context.xml -> Web.xml ==> 환경설정 파일

***

package com.test; SimpleFilter.java

web.xml 수정 <!-- 첫번째 필터 선언 -->

web-INF/test/a.jsp
b.jsp 생성후 a.jsp를 실행하면서 콘솔을 확인하자

***


web.xml 수정 <!-- 두번째 필터 선언 -->

package com.test; ChainedFilter.java

***


web.xml 수정 <!-- 세번째 필터 선언 -->(첫번째 두번째 필터 주석)

com.test; MyFilter.java

encoding_input.html

encoding_res.jsp

***
web.xml 수정 <!-- 네번째 필터 선언 -->(첫번째 두번째  세번째 필터 주석)

com.test01.MyListener.java

a.log 파일 생성됨 (notice폴더에)

***

web.xml 수정

***











