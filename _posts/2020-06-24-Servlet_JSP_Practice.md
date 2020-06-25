---



title:  "Servlet&JSP 회원가입/로그인/DB연동 "



layout: post



---


## [오늘 할 내용]
1. Servlet을 이용해서 페이지 제어를 할 수 있다.
2. Servlet에서 제공하는 인터페이스를 활용 할 수 있다.
3. 정적 페이지의 페이지 값을 동적으로 할당 받은 후 jsp 페이지로 전송할 수 있다.

***

### Servlet / JSP에서 꼭 알아야 할 것
- http 프로토콜 / 네트워크 구조 / server와 Web Application Server 구분
- WAS의 환경설정 server.xml, context.xml[전역DB, jsp 연동 디렉토리 등을 지정], web.xml[마인타입, 필터, 웹 보안설정 등]
- Servlet 상속 구조 / http 프로토콜과 함께 페이지 요청시 호출되는 메소드 / 페이지 전환 / 요청과 응답시 발생되는 객체의 메소드
- JSP 상속 구조 / http 프로토콜과 함께 페이지 요청시 호출되는 메소드 / 페이지 전환 / 요청과 응답시 발생되는 객체의 메소드 / 내장객체 / 빈즈
- 세션 / 쿠키 / 히든
- DB 연동(Web + JDBC) / MVC 패턴
- Web 파일처리(업로드, 다운로드, 웹 디렉토리 구조) / gui / 그래프 연동
- 태그 만드는 방법[jstl, el ... ]
- 필터와 환경설정
- Ajax / JQuery / XML
- 개인 사이트[회원가입, 방명록, 게시판[답변], 파일 업로드, 지도]

***

### 만들어보자

![image](https://user-images.githubusercontent.com/52989294/85701614-895ea200-b718-11ea-873e-688f1c9f9077.png)


### JDBC 연결

~~~ java
package common;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.sql.Statement;

// Connection & Close & Commit & rollback  4개만 user가 제어할 수 있게 만들어둠
public class JDBCTemplate {
	// 연결한 상태로 리턴해주겠다.
	public static Connection getConnection() {
		try {
			Class.forName("oracle.jdbc.OracleDriver");
		} catch (ClassNotFoundException e) {
			e.printStackTrace();
		}

		String url = "jdbc:oracle:thin:@localhost:1521:XE";
		String id = "big5";
		String pwd = "admin1234";
		Connection con = null;

		try {
			con = DriverManager.getConnection(url, id, pwd);
			con.setAutoCommit(false);
		} catch (SQLException e) {
			e.printStackTrace();
		}
		return con;
	}

	// 연결 객체를 받아서(연결한 상태를) close()하겠다.
	public static void Close(Connection con) {
		try {
			if (!con.isClosed() && con != null) {
				con.close();
			}
		} catch (SQLException e) {
			e.printStackTrace();
		}
	}

	public static void Close(Statement stmt) {
		try {
			if (stmt != null) {
				stmt.close();
			}
		} catch (SQLException e) {
			e.printStackTrace();
		}
	}

	public static void Close(ResultSet rs) {
		try {
			if (rs != null) {
				rs.close();
			}
		} catch (SQLException e) {
			e.printStackTrace();
		}
	}

	public static void Commit(Connection con) {
		try {
			if (con != null && !con.isClosed()) {
				con.commit();
			}
		} catch (SQLException e) {
			e.printStackTrace();
		}
	}

	public static void Rollback(Connection con) {
		try {
			if (con != null && !con.isClosed()) {
				con.rollback();
			}
		} catch (SQLException e) {
			e.printStackTrace();
		}
	}

}

~~~

***

### VO 만들기

~~~ java
package com.vo;

public class Login_vo {
	private String id;
	private String pw;
	private String name;
	private String email;
	private String addr;

	public Login_vo() {
		super();
		// TODO Auto-generated constructor stub
	}

	public Login_vo(String id, String pw, String name, String email, String addr) {
		super();
		this.id = id;
		this.pw = pw;
		this.name = name;
		this.email = email;
		this.addr = addr;
	}

	public Login_vo(String id, String pw) {
		super();
		this.id = id;
		this.pw = pw;
	}

	public String getId() {
		return id;
	}

	public void setId(String id) {
		this.id = id;
	}

	public String getPw() {
		return pw;
	}

	public void setPw(String pw) {
		this.pw = pw;
	}

	public String getName() {
		return name;
	}

	public void setName(String name) {
		this.name = name;
	}

	public String getEmail() {
		return email;
	}

	public void setEmail(String email) {
		this.email = email;
	}

	public String getAddr() {
		return addr;
	}

	public void setAddr(String addr) {
		this.addr = addr;
	}
}
~~~

***

### SQL & DAO 만들기
~~~ java
package com.dao;

public interface LoginDaoImpl {
	String minsert = "insert into mylogin values(?,?,?,?,?)";
	String mdelete = "delete from mylogin where id = ? or id is null";
	String mupdate = "update mylogin set name = ?, email = ?, addr = ? where id = ?";

	String mlist = "select * from mylogin";
	String mfind = "select * from mylogin where id = ?";
	String mlogin = "select * from myLogin where id = ? and pw = ?";
}
~~~

~~~ java
package com.dao;

import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.Statement;
import java.util.ArrayList;
import java.util.List;

import com.vo.*;
import static common.JDBCTemplate.*;

public class LoginDAO implements LoginDaoImpl {
	private Connection conn;

	public LoginDAO(Connection conn) {
		this.conn = conn;
	}

	public Login_vo getLogin(Login_vo login) {
		// id pw name email addr
		PreparedStatement pstm = null;
		ResultSet rs = null;
		Login_vo login_res = null;
		try {
			pstm = conn.prepareStatement(mlogin);
			pstm.setString(1, login.getId());
			pstm.setString(2, login.getPw());

			rs = pstm.executeQuery();

			while (rs.next()) {
				login_res = new Login_vo(rs.getString(1), rs.getString(2));
			}
		} catch (Exception e) {
			System.out.println(e);
		}

		return login_res;
	}

	// 전체 출력
	public List<Login_vo> getMyLoginAll() {
		Statement stmt = null;
		ResultSet rs = null;
		Login_vo my_login = null;
		List<Login_vo> all = new ArrayList<Login_vo>();
		try {
			stmt = conn.createStatement();
			rs = stmt.executeQuery(mlist);

			while (rs.next()) {
				my_login = new Login_vo(rs.getString(1), rs.getString(2), rs.getString(3), rs.getString(4),
						rs.getString(5));
				all.add(my_login);
			}
		} catch (Exception e) {
			System.out.println(e);
		}
		return all;
	}

	public boolean getMyLoginInsert(Login_vo login) {
		PreparedStatement pstm = null;
		boolean fw = false;

		try {
			pstm = conn.prepareStatement(minsert);
			pstm.setString(1, login.getId());
			pstm.setString(2, login.getPw());
			pstm.setString(3, login.getName());
			pstm.setString(4, login.getEmail());
			pstm.setString(5, login.getAddr());

			int rs = pstm.executeUpdate();
			if (rs > 0) {
				fw = true;
			} else {
				fw = false;
			}

		} catch (Exception e) {
			Rollback(conn);
			System.out.println(e);
		} finally {
			Commit(conn);
		}
		return fw;
	}

	public boolean getMyLoginUpdate(Login_vo login) {
		PreparedStatement pstm = null;
		boolean fw = false;

		try {
			pstm = conn.prepareStatement(mupdate);
			pstm.setString(1, login.getName());
			pstm.setString(2, login.getEmail());
			pstm.setString(3, login.getAddr());
			pstm.setString(4, login.getId());

			int rs = pstm.executeUpdate();
			if (rs > 0) {
				fw = true;
			} else {
				fw = false;
			}

		} catch (Exception e) {
			Rollback(conn);
			System.out.println(e);
		} finally {
			Commit(conn);
		}
		return fw;
	}

	public boolean getMyLoginDelete(Login_vo login) {
		PreparedStatement pstm = null;
		boolean fw = false;

		try {
			pstm = conn.prepareStatement(mdelete);
			pstm.setString(1, login.getId());

			int rs = pstm.executeUpdate();
			if (rs > 0) {
				fw = true;
			} else {
				fw = false;
			}

		} catch (Exception e) {
			Rollback(conn);
			System.out.println(e);
		} finally {
			Commit(conn);
		}
		return fw;
	}

	public Login_vo getMyLoginFind(Login_vo login) {
		PreparedStatement pstm = null;
		ResultSet rs = null;
		Login_vo login_res = null;
		try {
			pstm = conn.prepareStatement(mfind);
			pstm.setString(1, login.getId());

			rs = pstm.executeQuery();

			while (rs.next()) {
				login_res = new Login_vo(rs.getString(1), rs.getString(2));
				login_res.setAddr(rs.getString("addr"));
				login_res.setEmail(rs.getString("email"));
				login_res.setName(rs.getString("name"));
			}
		} catch (Exception e) {
			System.out.println(e);
		}

		return login_res;
	}
}
~~~

***

### BIZ 만들기

~~~ java
package com.biz;

import java.sql.Connection;
import com.vo.*;
import com.dao.*;
import static common.JDBCTemplate.*;
import java.util.*;

public class LoginBIZ {
	// id, pw 검증
	public Login_vo getLogin(Login_vo login) {
		Connection con = getConnection();
		Login_vo res = new LoginDAO(con).getLogin(login);
		Close(con);
		return res;
	}

	// 전체 출력
	public List<Login_vo> getMyLoginAll() {
		Connection con = getConnection();
		List<Login_vo> res = new LoginDAO(con).getMyLoginAll();
		Close(con);
		return res;
	}

	// 회원가입
	public boolean getMyLoginInsert(Login_vo login) {
		Connection con = getConnection();
		boolean res = new LoginDAO(con).getMyLoginInsert(login);
		Close(con);
		return res;
	}

	// 회원 수정
	public boolean getMyLoginUpdate(Login_vo login) {
		Connection con = getConnection();
		boolean res = new LoginDAO(con).getMyLoginUpdate(login);
		Close(con);
		return res;
	}

	// 회원 삭제
	public boolean getMyLoginDelete(Login_vo login) {
		Connection con = getConnection();
		boolean res = new LoginDAO(con).getMyLoginDelete(login);
		Close(con);
		return res;
	}

	// 회원 찾기
	public Login_vo getMyLoginFind(Login_vo login) {
		Connection con = getConnection();
		Login_vo res = new LoginDAO(con).getMyLoginFind(login);
		Close(con);
		return res;
	}
}
~~~

***

### Service Test
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

		String str = String.format("%10s <br> %10s <br> %10s <br> %10s <br> %10s <br> %10s", 
				req.getContextPath(), req.getRemoteHost(),
				req.getRemotePort(), req.getLocalAddr(),
				req.getLocalPort(),req.getRequestURI());

		PrintWriter out = resp.getWriter();
		out.print("<body> <font size = 5>" + str + "</font></body>");
		out.close();
	}

	@Override
	protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
		doGet(req, resp); // 내부 호출
		// <form action = "test01" method = "post"></form>로 들어오면 doPost() 실행

	}

}
~~~

~~~ java
package com.test;

import java.io.IOException;

import javax.servlet.ServletException;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import com.vo.*;
import com.biz.LoginBIZ;

public class Login extends HttpServlet {

	@Override
	protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
		String id = req.getParameter("id");
		String pw = req.getParameter("pw");

		// 입력 받은 id, pw를 biz를 통해 전달하면 biz는 DAO를 통해 입력된 데이터에 맞는
		// 테이블의 row를 담아 리턴한다.
		Login_vo input_login = new com.vo.Login_vo(id, pw);
		Login_vo res_login = new LoginBIZ().getLogin(input_login);

		if (id.equals(res_login.getId())) {
			// 로그인 성공 페이지로 이동
			resp.sendRedirect(req.getContextPath() + "/login/success.html");
		}

		else {
			// 로그인 실패 페이지로 이동
			resp.sendRedirect(req.getContextPath() + "/login/fail.html");
		}

	}

	@Override
	protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
		doGet(req, resp);
	}

}
~~~

***

### Controller

~~~ java
package com.controller;

import java.io.IOException;
import java.util.List;

import javax.servlet.RequestDispatcher;
import javax.servlet.ServletException;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

import com.biz.LoginBIZ;
import com.vo.Login_vo;

public class MyController extends HttpServlet {

	@Override
	protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
		String str = req.getRequestURI();
		if (str.endsWith("minsert")) {
			doMinsert(req, resp);
		} else if (str.endsWith("mdelete")) {
			doMdelete(req, resp);
		} else if (str.endsWith("mupdate")) {
			doMupdate(req, resp);
		} else if (str.endsWith("mlist")) {
			doMlist(req, resp);
		} else if (str.endsWith("mfind")) {
			doMfind(req, resp);
		} else {
			System.out.println(str + " : " + str.endsWith(str));
		}
	}

	private void doMfind(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
		String id = req.getParameter("id");
		Login_vo login = new Login_vo();
		login.setId(id);

		Login_vo res = new LoginBIZ().getMyLoginFind(login);

		RequestDispatcher rd = req.getRequestDispatcher("/member/memberFind.jsp");
		req.setAttribute("mylogin", res); // mall은 all이라는 객체를 담아서 forward할 때 넘긴다.

		rd.forward(req, resp); // 주소줄에 요청받은 제어권을 memberAll.jsp에 넘긴다.

		System.out.println("find");
	}

	private void doMlist(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
		System.out.println("list");
		List<Login_vo> all = new LoginBIZ().getMyLoginAll();
		RequestDispatcher rd = req.getRequestDispatcher("/member/memberAll.jsp");
		req.setAttribute("mall", all); // mall은 all이라는 객체를 담아서 forward할 때 넘긴다.

		rd.forward(req, resp); // 주소줄에 요청받은 제어권을 memberAll.jsp에 넘긴다.
	}

	private void doMupdate(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
		// post 요청시 한글 깨짐 방지는 반드시 메소드로 명시해준다.
		req.setCharacterEncoding("euc-kr");
		Login_vo mylogin = new Login_vo();
		mylogin.setId(req.getParameter("id"));
		mylogin.setName(req.getParameter("name"));
		mylogin.setEmail(req.getParameter("email"));
		mylogin.setAddr(req.getParameter("addr"));

		boolean fw = new LoginBIZ().getMyLoginUpdate(mylogin);

		if (fw) {
			resp.sendRedirect(req.getContextPath() + "/mlist");
		} else {
			// 에러페이지 이동 혹은 재입력 페이지로 이동 시킨다.
			// resp.sendRedirect(req.getContextPath() + "/member/loginError.html");
		}
		System.out.println("update");
	}

	private void doMdelete(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
		String id = req.getParameter("id");
		Login_vo mylogin = new Login_vo();
		mylogin.setId(id);

		boolean fw = new LoginBIZ().getMyLoginDelete(mylogin);

		if (fw) {
			resp.sendRedirect("/Day50/mlist");
		} else {
			System.out.println("에러에러");
		}
		System.out.println("delete");
	}

	private void doMinsert(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
		// 회원가입 입력페이지에서 받은 데이터를 biz로 넘겨 DB에 저장한다.
		Login_vo mylogin = new Login_vo();
		mylogin.setId(req.getParameter("id"));
		mylogin.setPw(req.getParameter("pw"));
		mylogin.setName(req.getParameter("name"));
		mylogin.setEmail(req.getParameter("email"));
		mylogin.setAddr(req.getParameter("addr"));

		boolean fw = new LoginBIZ().getMyLoginInsert(mylogin);

		if (fw) {
			resp.sendRedirect(req.getContextPath() + "/member/loginOk.html");
		} else {
			// 에러페이지 이동 혹은 재입력 페이지로 이동 시킨다.
			resp.sendRedirect(req.getContextPath() + "/member/loginError.html");
		}
		System.out.println("insert");
	}

	@Override
	protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
		doGet(req, resp);
	}
}
~~~

***

### WebContent/login

~~~ html
<!-- input_login.html -->
<!DOCTYPE html>
<html>
<head>
<meta charset="EUC-KR">
<title>login 화면</title>
<style type="text/css">
#mydiv {
	margin: 100px 100px 100px 100px;
	font-size: 1.5em;
}
</style>
</head>
<body>
	<div id="mydiv">
		<form action="/Day50/login" method="post">
			ID : <input type="text" name="id"><br> 
			PW : <input type="password" name="pw"><br> 
			<input type="submit" value="확인"> 
			<input type="reset" value="취소">
		</form>
	</div>
</body>
</html>
~~~

~~~ html
<!-- success.html -->
<!DOCTYPE html>
<html>
<head>
<meta charset="EUC-KR">
<title>Insert title here</title>
</head>
<body>
	<h1>로그인 성공</h1>
</body>
</html>
~~~

~~~ html
<!-- fail.html -->
<!DOCTYPE html>
<html>
<head>
<meta charset="EUC-KR">
<title>Insert title here</title>
</head>
<body>
	<h1>로그인 실패야</h1>
	<a href="/Day50/login/input_login.html">다시 입력하도록</a>
</body>
</html>
~~~

***

### WebContent/member html 파일

~~~ html
<!-- insert.html -->
<!DOCTYPE html>
<html>
<head>
<meta charset="EUC-KR">
<title>회원가입</title>
<style type="text/css">
#mydiv {
	margin: 100px 100px 100px 100px;
	font-size: 1.5em;
}
</style>
</head>
<body>
	<div id="mydiv">
		<form action="/Day50/minsert" method="post">
			ID : <input type="text" name="id"><br>
			PW : <input type="password" name="pw"><br>
			NAME : <input type="text" name="name"><br>
			EMAIL : <input type="text" name="email"><br>
			ADDR : <input type="text" name="addr"><br>
			<input type="submit" value="확인">
			<input type="reset" value="취소">
		</form>
	</div>
</body>
</html>
~~~

~~~ html
<!-- loginOk.html -->
<!DOCTYPE html>
<html>
<head>
<meta charset="EUC-KR">
<title>Insert title here</title>
</head>
<body>
<div style = "font-size : 1.5em;">
	<h1> 회원가입 성공했어 ^.^ </h1>
	<a href = "/Day50/mlist">목록으로</a>
	</div>
</body>
</html>
~~~

~~~ html
<!-- loginError.html -->
<!DOCTYPE html>
<html>
<head>
<meta charset="EUC-KR">
<title>Insert title here</title>
</head>
<body>
<div style = "font-size : 1.5em;">
	<h1> 회원가입 실패했어 ^.^ </h1>
	<a href = "/Day50/mlist">목록으로</a>
	</div>
</body>
</html>
~~~

***

### WebContent/member jsp 파일
- memberAll.jsp
    ~~~ jsp
    <%@ page language="java" contentType="text/html; charset=EUC-KR"
        pageEncoding="EUC-KR"%>
    <%@ page import="java.util.*, com.vo.Login_vo"%>
    <!DOCTYPE html>
    <html>
    <head>
    <meta charset="EUC-KR">
    <title>전체 목록 출력</title>
    </head>
    <body>
        <%
            List<Login_vo> all = (List<Login_vo>) request.getAttribute("mall");
        // object형으로 받아와서 명시적 캐스팅(List<Login_vo>) 해줌
        %>

        <div style="margin: 100px 100px 100px 100px; font-size: 1.5em;">

            <table border="1">
                <tr>
                    <th>id</th>
                    <th>pw</th>
                    <th>name</th>
                    <th>email</th>
                    <th>addr</th>
                    <th>수정</th>
                    <th>삭제</th>
                </tr>

                <%
                    for (Login_vo res : all) {
                %>
                <tr>
                    <td><%= res.getId() %>   </td>
                    <td><%= res.getPw() %>   </td>
                    <td><%= res.getName() %>   </td>
                    <td><%= res.getEmail() %>   </td>
                    <td><%= res.getAddr() %>   </td>
                    <td><a href = "mfind?id=<%= res.getId() %>">수정</a></td>
                    <td><a href = "mdelete?id=<%= res.getId() %>">삭제</a></td>
                    <%
                        }
                    %>

            </table>

            <a href='<%=application.getContextPath()%>'> 홈으로 </a><br>

        </div>
    </body>
    </html>
    ~~~

- memeberFind.jsp

    ~~~ jsp
    <%@ page language="java" contentType="text/html; charset=EUC-KR"
        pageEncoding="EUC-KR"%>
    <%@ page import="com.vo.*"%>
    <!DOCTYPE html>
    <html>
    <head>
    <meta charset="EUC-KR">
    <title>회원 수정 화면</title>
    <style type="text/css">
    #mydiv {
        margin: 100px 100px 100px 100px;
        font-size: 1.5em;
    }
    </style>
    </head>
    <body>
    <%
    Login_vo res = (Login_vo)request.getAttribute("mylogin");
    %>
        <div id="mydiv">
            <form action="/Day50/mupdate" method="post">
                ID : <input type="text" name="id" readonly="readonly" value = <%= res.getId() %>><br>
                NAME : <input type="text" name="name" value = <%= res.getName() %>><br>
                EMAIL : <input type="text" name="email" value = <%= res.getEmail() %>><br>
                ADDR : <input type="text" name="addr" value = <%= res.getAddr() %>><br>
                <input type="submit" value="수정">
                <input type="reset" value="취소">

            </form>
        </div>

    </body>
    </html>
    ~~~

***

### WebContent/WEB-INF
- web.xml

~~~ xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns="http://xmlns.jcp.org/xml/ns/javaee"
	xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_3_1.xsd"
	id="WebApp_ID" version="3.1">
	<display-name>Day50</display-name>
	<welcome-file-list>
		<welcome-file>index.jsp</welcome-file>
	</welcome-file-list>

	<!-- 1.com.test.MyTest 클래스를 /test01로 맵핑 -->
	<servlet>
		<servlet-name>Test</servlet-name>
		<servlet-class>com.test.MyTest</servlet-class>
	</servlet>

	<servlet-mapping>
		<servlet-name>Test</servlet-name>
		<url-pattern>/test01</url-pattern>
	</servlet-mapping>

	<!-- 2.com.test.Login 클래스를 /login로 맵핑 -->
	<servlet>
		<servlet-name>Login</servlet-name>
		<servlet-class>com.test.Login</servlet-class>
	</servlet>

	<servlet-mapping>
		<servlet-name>Login</servlet-name>
		<url-pattern>/login</url-pattern>
	</servlet-mapping>

	<!-- 3.com.controller.MyController 클래스를 /minsert로 맵핑 -->
	<servlet>
		<servlet-name>MyController</servlet-name>
		<servlet-class>com.controller.MyController</servlet-class>
	</servlet>

	<servlet-mapping>
		<servlet-name>MyController</servlet-name>
		<url-pattern>/minsert</url-pattern>
		<url-pattern>/mlist</url-pattern>
		<url-pattern>/mdelete</url-pattern>
		<url-pattern>/mupdate</url-pattern>
		<url-pattern>/mfind</url-pattern>
	</servlet-mapping>


</web-app>
~~~

***

### index.jsp

~~~ jsp
<%@ page language="java" contentType="text/html; charset=EUC-KR"
	pageEncoding="EUC-KR"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="EUC-KR">
<title>Insert title here</title>
</head>
<body>

	<!-- 	servlet에서 사용하는 인터페이스를 jsp 페이지에서는 객체를 생성하지 않고 사용한다
	이를 내장 객체라고 한다.
	Context = application = 현재 프로젝트의 값
	HttpServletRequest = request = 요청할 때의 값
	HttpServletResponse = response  = 응답할 때의 값
 -->
	<!-- out.println() -->
	<%=request.getContextPath()%>
	<%=application.getContextPath()%>
	<%=application.getRealPath(".")%>
	<%
		// 자바 코드 작성 부분
	String path = application.getContextPath();
	%>

	<div style="margin: 100px 100px 100px 100px; font-size: 1.5em;">
		Servlet Test<br> <br>
		<a href='<%=path%>/test01'> 0. 현재 로컬 컨텍스트 패스 </a><br>
		<a href='<%=path%>/member/insert.html'> 1. 회원가입 </a><br>
		<a href='<%=path%>/login/input_login.html'> 2. 로그인 </a><br>
		<a href='<%=path%>/mlist'> 3. 목록으로 </a><br>

	</div>

</body>
</html>
~~~





























