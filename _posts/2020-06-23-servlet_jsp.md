---



title:  "Servlet&JSP"



layout: post



---


## [오늘 할 내용]
1. WAS(Web Application Server)를 이용해서 웹프로젝트를 생성할 수 있다.
2. WAS의 원리를 이해할 수 있다.
3. Servlet의 상속구조와 인터페이스를 활용해서 http 프로토콜을 통해 값을 	Client -> Server 구조로 전달 할 수 있다.

***

### java 로 클래스를 만들자. com.test.MyTest.class 만들어서 web 브라우저로 출력하고 싶다.
1. WAS를 설치(내가만든 클래스를 http 프로토콜로 url을 맵핑 시켜줌) -> 톰캣 설치
2. com.test.MyTest.class를 만든다.(http://localhost:8787/Day49/test)
	- WebContent/WEB-INF/web.xml : 웹페이지의 정보를 설정하고 저장하는 곳

    ~~~ java
    package com.test;

    import java.io.IOException;
    import java.io.PrintWriter;

    import javax.servlet.ServletException;
    import javax.servlet.http.HttpServlet;
    import javax.servlet.http.HttpServletRequest;
    import javax.servlet.http.HttpServletResponse;

    public class MyTest extends HttpServlet {

        @Override
        protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
            PrintWriter out = resp.getWriter();
            out.print("<html><body><h1>응답이야 </h1></body></html>");
            out.close();
        }
    }
    ~~~

    - 'MyTest' -> '/test'로 만들어주는 작업을 해야함(web.xml에서 함)

    ~~~ xml
    <?xml version="1.0" encoding="UTF-8"?>
    <web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xmlns="http://xmlns.jcp.org/xml/ns/javaee"
        xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_3_1.xsd"
        id="WebApp_ID" version="3.1">
        <display-name>Day49</display-name>
        <welcome-file-list>
            <welcome-file>index.jsp</welcome-file>
        </welcome-file-list>

        <!-- 1. 선언된 서블릿 클래스를 등록한다. -->
        <servlet>
            <servlet-name>myServlet</servlet-name>
            <servlet-class>com.test.MyTest</servlet-class>
        </servlet>
        <!-- 2. 등록된 서블릿 클래스를 url 맵핑한다. -->
        <servlet-mapping>
            <servlet-name>myServlet</servlet-name>
            <url-pattern>/test</url-pattern>
        </servlet-mapping>
    </web-app>
    ~~~

3. http: 프로토콜을 통해서 연동할 수 있는 클래스를 찾아서 상속 구조를 만든다.
4. HttpServlet 클래스를 상속 받는다.
	- Provides an abstract class to be subclassed to create an HTTP servlet suitable for a Web site.

***

### 컨테이너에 있는 클래스 로딩 순서
1. Day49가 WAS[Servlet/jsp].. Servlet/jsp가 만든 클래스를 브라우저에 올려준다.
2. /Day49/WebContent/META-INF/context.xml[Web application project 설정파일] 파일을 로딩
3. /Day49/WebContent/WEB-INF/web.xml[웹 페이지 설정파일] 로딩
4. http:// 프로토콜을 통해 페이지 요청 응답할 준비를 한다.(servlet)
5. 원형
	- Servlet = java 코드 안에 html 코드 들어있음
	- Jsp : html 코드 안에 java 코드 들어있음

***

![image](https://user-images.githubusercontent.com/52989294/85386909-22f24c00-b57f-11ea-8dd9-b29b235d4ea9.png)

***

~~~ jsp
<%@ page language="java" contentType="text/html; charset=EUC-KR"
	pageEncoding="EUC-KR"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="EUC-KR">
<title>Insert title here</title>
</head>
<body style="font-size: 1.5em;">
	<div style="margin: 100px 100px 100px 100px">
		Day49 WebProject<br>
		<a href="/Day49/test"> MyTest 이동 </a> <br>
		<a href="/Day49/utest"> UTest 이동 </a> <br>
		<a href="/Day49/InputText.html"> Text 화면으로 이동</a> <br>
		<a href="/Day49/test02?no=3&nono=2"> UTest02 이동 </a> <br>
	</div>

</body>
</html>
~~~

***

~~~ html
<!DOCTYPE html >
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=EUC-KR">
<title>text 상자</title>
</head>
<body>
	<form action="/Day49/utest" method="get">
		<label>이름 :</label> <input type="text" size="12" name="username" value="dominica"><br>
		<label>암호 :</label> <input type="password" size="12" name="pw">
		<p>
			<textarea name="thetext" rows="5" cols="60">
       	한줄 이상의 문자열을 출력합니다.
        	</textarea>
		</p>
		<button value = "전송"></button><br>
		<input type ="submit" value="전송[submit]"><br>
		<input type ="button" value="전송[button]"> <br>
		<input type ="reset" value="reset"> <br>
	</form>

</body>
</html>
~~~

***

~~~ java
// MyTest.java
package com.test;

import java.io.IOException;
import java.io.PrintWriter;

import javax.servlet.ServletException;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

public class MyTest extends HttpServlet {
	@Override
	protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
		PrintWriter out = resp.getWriter();
		out.print("<html><body><h1>응답이야 </h1></body></html>");
		out.close();
	}
}
~~~

***

~~~ java
// UTest.java
package com.test;

import java.io.IOException;
import java.io.PrintWriter;

import javax.servlet.ServletException;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

public class UTest extends HttpServlet {

	@Override
	protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
		String name = req.getParameter("username");
		String pw = req.getParameter("pw");
		String res = req.getParameter("thetext");

		resp.getWriter().append("<body> name : " + name + "<br>").append("pw : " + pw + "<br>").append(" thetext : " + res);

		PrintWriter out = resp.getWriter();
		out.println("<hr> input name = " + name + "<br>");
		out.println(" pw = " + pw + "<br>");
		out.println(" thetext = " + res + "<br>");

		out.print("<hr>");
		String str = String.format("%10s %10s %10s \n", name, pw, res);
		out.print(str + "</body>");
		out.close();
	}
}
~~~

***

~~~ java
// UTest02.java
package com.test;

import java.io.IOException;
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

public class UTest02 extends HttpServlet {

	protected void doGet(HttpServletRequest request, HttpServletResponse response)
			throws ServletException, IOException {
		System.out.println(request.getContextPath());
		System.out.println(request.getServletPath());
		System.out.println(request.getServletContext());
		System.out.println(request.getQueryString());

		System.out.println("no : " + request.getParameter("no"));
		System.out.println("nono : " + request.getParameter("nono"));
	}
}

~~~

***

~~~ xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns="http://xmlns.jcp.org/xml/ns/javaee"
	xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_3_1.xsd"
	id="WebApp_ID" version="3.1">
	<display-name>Day49</display-name>
	<welcome-file-list>
		<welcome-file>index.jsp</welcome-file>
	</welcome-file-list>

	<!-- 1. 선언된 서블릿 클래스를 등록한다. -->
	<servlet>
		<servlet-name>myServlet</servlet-name>
		<servlet-class>com.test.MyTest</servlet-class>
	</servlet>

	<!-- 2. 등록된 서블릿 클래스를 url 맵핑한다. -->
	<servlet-mapping>
		<servlet-name>myServlet</servlet-name>
		<url-pattern>/test</url-pattern>
	</servlet-mapping>

	<!-- 1. 선언된 서블릿 클래스를 등록한다. -->
	<servlet>
		<servlet-name>UServlet</servlet-name>
		<servlet-class>com.test.UTest</servlet-class>
	</servlet>
	<!-- 2. 등록된 서블릿 클래스를 url 맵핑한다. -->
	<servlet-mapping>
		<servlet-name>UServlet</servlet-name>
		<url-pattern>/utest</url-pattern>
	</servlet-mapping>

	<!-- 1. 선언된 서블릿 클래스를 등록한다. -->
	<servlet>
		<servlet-name>Test02</servlet-name>
		<servlet-class>com.test.UTest02</servlet-class>
	</servlet>
	<!-- 2. 등록된 서블릿 클래스를 url 맵핑한다. -->
	<servlet-mapping>
		<servlet-name>Test02</servlet-name>
		<url-pattern>/test02</url-pattern>
	</servlet-mapping>
</web-app>
~~~