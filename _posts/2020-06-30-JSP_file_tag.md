---



title:  "Web 파일 처리 & GUI, 그래프 연동 & 사용자 태그[jstl, el]"



layout: post



---


## [오늘 할 내용]
1. Web에서 파일처리를 구현 할 수 있다. (java . io . * ;)
	- byte[stream], char, Object
2. 동적 페이지, 정적 페이지에서 파일 업로드를 할 수 있다. (java.* / servlet.jar에서 제공하는 추가 라이브러리 사용)
	- cos.jar [Servlet/JSP] 에서 주로 사용 <http://www.servlets.com/cos/>
	- common.jar [Spring] 에서 주로 사용(WEB-INF/lib에 cos.jar)
	- - HttpServletResource
		- ServletOutputStream getOutputStream( ) : 음악, 이미지, 배너, 그래프
		- java.io.PrintWriter getWriter( ) : text 로딩
3. gui 연동 / 그래프 연동
	- javax.servlet.ServletContext
		- java.net.URL	getResource(java.lang.String path)
			: Returns a URL to the resource that is mapped to a specified path.
		- java.io.InputStream	getResourceAsStream(java.lang.String path)
			: Returns the resource located at the named path as an InputStream object.
		- java.util.Set<java.lang.String>	getResourcePathsgetResourcePathsgetResourcePaths(java.lang.String path)
			: Returns a directory-like listing of all the paths to resources within the web application whose longest sub-path matches the supplied path argument.

4. jstl, el를 이용해서 사용자 태그를 작성할 수 있다.

***

### Web에서 파일처리 하기
- 동적 페이지는 서블릿, jsp로 나눠지는데 jsp에서는 autoflush가 true임

    ~~~ jsp
    <!-- WebContent/test/FileDirlist.jsp -->
    <%@ page language="java" contentType="text/html; charset=EUC-KR"
        pageEncoding="EUC-KR"%>
    <%@ page import="java.io.*"%>
    <!DOCTYPE html>
    <html>
    <head>
    <meta charset="EUC-KR">
    <title>파일과 디렉토리 리스트</title>
    </head>
    <body style="font-size: 1.5em">
        <%!String path;%>
        <%
            path = application.getRealPath("/");
        String[] listing = new File(path).list();
        for (int i = 0; i < listing.length; i++) {
            out.println("list [" + i + "] : " + listing[i] + "<br>");
        }

        out.println("<hr> 웹어플리케이션 프로젝트명 = " + request.getContextPath() + "<br><hr>");
        out.println("서블릿 절대 경로 = " + request.getServletPath() + "<br><hr>");
        out.println("실제 작업 어플리케이션의 루트 경로  = " + application.getRealPath("/") + "<br><hr>");
        out.println("실제 작업 어플리케이션의 루트 경로  = " + application.getRealPath(".") + "<br><hr>");
        %>
    </body>
    </html>
    ~~~

	![image](https://user-images.githubusercontent.com/52989294/86073126-21181380-babe-11ea-9db1-2fb65392df0c.png)

***

### 파일 읽기
- /etc/ex.txt 파일을 가져와서 읽어보자.
    ~~~ jsp
    <!--  WebContent/test/FileReader.jsp -->
    <%@ page language="java" contentType="text/html; charset=EUC-KR"
        pageEncoding="EUC-KR"%>
    <%@ page import="java.io.*"%>
    <!DOCTYPE html>
    <html>
    <head>
    <meta charset="EUC-KR">
    <title>BufferedReader를 이용한 파일 읽기</title>
    </head>
    <body>
        <%
            String Path = "/etc/ex.txt";
        %>
        실제 경로 :
        <br>
        <%=application.getRealPath(Path)%><br><hr>
        <br>
        <%=Path%>의 내용은 아래와 같습니다.<br>	<hr>
        <%
            BufferedReader br = null;
            char[] buff = new char[512];
            int len = -1;

        try {
            br = new BufferedReader(new InputStreamReader(application.getResourceAsStream(Path)));
            while ((len = br.read(buff)) != -1) {
                out.println(new String(buff, 0, len));
            }
        } catch (IOException ex) {
            out.println("예외 발생 : " + ex.getMessage());
        } finally {
            if (br != null) {
                try {
            br.close();
                } catch (IOException ex) {

                }
            }
        }
        %>
    </body>
    </html>
    ~~~

***

### 이미지 처리하기
- image/aaa.png 파일을 가져와서 열어보자
    ~~~ html
    <!DOCTYPE html>
    <html>
    <head>
    <meta charset="EUC-KR">
    <title>Insert title here</title>
    </head>
    <body>
    <img src = "PIC.jsp">
    </body>
    </html>
    ~~~

    ~~~ jsp
    <%@ page language="java" contentType="image/jpg; charset=EUC-KR"
        pageEncoding="EUC-KR"%>
    <%@ page import="java.io.*, javax.servlet.ServletOutputStream"%>

        <%
        byte[] buffer = new byte[1024];
        ServletOutputStream outt = response.getOutputStream();
        BufferedInputStream in = null;

        try {
            String file = application.getRealPath("/image/aaa.png");
            in = new BufferedInputStream(new FileInputStream(file));
            int n = 0;

            while ((n = in.read(buffer, 0, 1024)) != -1) {
                outt.write(buffer, 0, n);
            }
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            outt.close();
            in.close();
        }
        %>

    <!DOCTYPE html>
    <html>
    <head>
    <meta charset="EUC-KR">
    <title>Insert title here</title>
    </head>
    <body>

    </body>
    </html>
    ~~~

### java.io.BufferedInputStream
![image](https://user-images.githubusercontent.com/52989294/86075208-7524f700-bac2-11ea-9c90-7b41f4134a8a.png)

***

### 파일 관리
- historyView.jsp
    ~~~ jsp
    <%@ page language="java" contentType="text/html; charset=EUC-KR"
        pageEncoding="EUC-KR"%>
    <!DOCTYPE html>
    <html>
    <head>
    <meta charset="EUC-KR">
    <title>입사 지원서</title>
    </head>
    <body>
    <h3> 다음 입사 지원서 양식에 따라 자료를 입력하세요 </h3>
    1. <a href = "mswordView.jsp"> 워드 양식</a><br><br>
    2. <a href = "excelView.jsp"> 엑셀 양식</a><br><br>
    3. <a href = "pdfView.jsp"> PDF 양식</a><br><br>
    </body>
    </html>
    ~~~

- PDF 로 뽑아보기
    ~~~ jsp
    <%@ page contentType="application/pdf; charset=EUC-KR" %>
    <!DOCTYPE html>
    <html>
    <head>
    <meta charset="EUC-KR">
    <title>이력서</title>
    </head>
    <body>
    <center><h2> 입사 지원서 </h2></center>
    <table border = "1" cellspacing = "0" cellpadding = "0" style="width:100%">
      <tr>
        <th>Firstname</th>
        <th>Lastname</th>
        <th>Age</th>
      </tr>
      <tr>
        <td>Jill</td>
        <td>Smith</td>
        <td>50</td>
      </tr>
      <tr>
        <td>Eve</td>
        <td>Jackson</td>
        <td>94</td>
      </tr>
    </table>
    </body>
    </html>
    ~~~

- EXCEL 로 뽑아보기
    ~~~ jsp
    <%@ page contentType="application/vnd.ms-excel; charset=KSC5601" %>
    <!DOCTYPE html>
    <html>
    <head>
    <meta charset="EUC-KR">
    <title>이력서</title>
    </head>
    <body>
    <%
    response.setHeader("Content-Disposition", "attachment;filenam=profile.xls");
    %>

    <center><h2> 입사 지원서 </h2></center>
    <table border = "1" cellspacing = "0" cellpadding = "0" style="width:100%">
      <tr>
        <th>Firstname</th>
        <th>Lastname</th>
        <th>Age</th>
      </tr>
      <tr>
        <td>Jill</td>
        <td>Smith</td>
        <td>50</td>
      </tr>
      <tr>
        <td>Eve</td>
        <td>Jackson</td>
        <td>94</td>
      </tr>
    </table>
    </body>
    </html>
    ~~~

- WORD 로 뽑아보기
    ~~~ jsp
    <%@ page contentType="application/msword; charset=EUC-KR" %>
    <!DOCTYPE html>
    <html>
    <head>
    <meta charset="EUC-KR">
    <title>이력서</title>
    </head>
    <body>
    <center><h2> 입사 지원서 </h2></center>
    <table border = "1" cellspacing = "0" cellpadding = "0" style="width:100%">
      <tr>
        <th>Firstname</th>
        <th>Lastname</th>
        <th>Age</th>
      </tr>
      <tr>
        <td>Jill</td>
        <td>Smith</td>
        <td>50</td>
      </tr>
      <tr>
        <td>Eve</td>
        <td>Jackson</td>
        <td>94</td>
      </tr>
    </table>
    </body>
    </html>
    ~~~

***

### 파일 업로드 해서 읽기

- FileUpTest.html
    ~~~ html
    <!DOCTYPE html>
    <html>
    <head>
    <meta charset="EUC-KR">
    <title>파일 업로드</title>
    </head>
    <body>
        <h1>파일 업로드</h1>
        <form method="post" action="FileUpRes.jsp"
            enctype="multipart/form-data">
            <p>
                메시지 : <input type="Text" name="message"><br>
                파일 : <input type="file" name="uploadfile" size=60><br>
            <hr>
            <input type="submit" value="보내기">
            </form>
    </body>
    </html>
    ~~~

- FileUpRes.jsp
    ~~~ jsp
    <%@ page language="java" contentType="text/html; charset=EUC-KR"
        pageEncoding="EUC-KR"%>
    <%@ page import="java.io.*"%>
    <!DOCTYPE html>
    <html>
    <head>
    <meta charset="EUC-KR">
    <title>파일 업로드 결과</title>
    </head>
    <body>
        <%
            response.setContentType("text/html; charset = EUC-KR");
        out.println("<html><body> <h3> 문서 올리기 성공 </h3>");
        out.println("업로드 파일의 타입 정보 " + request.getContentType() + "<br>");
        out.println("업로드 파일의 타입 길이 " + request.getContentLength() + "<br>");

        out.println("<hr><font color = blue> <br>");
        InputStreamReader isr = new InputStreamReader(request.getInputStream(), "EUC-KR");

        BufferedReader br = new BufferedReader(isr);
        for (String line; (line = br.readLine()) != null;) {
            out.println(line + " ");
        }

        out.println("</font> </body> </html>");
        out.close();
        %>
    </body>
    </html>
    ~~~

***

### 파일 업로드해서 저장하기
- C:\\webwork\\Day54\\WebContent\\upload 폴더에 저장해보자

    ~~~ html
    <!DOCTYPE html>
    <html>
    <head>
    <meta charset="EUC-KR">
    <title>파일 업로드</title>
    </head>
    <body>
        <h1>파일 업로드</h1>
        <form method="post" action="uploadRes.jsp"
            enctype="multipart/form-data">
            <p>
                파일 제목 : <input type="Text" name="subject"><br> 
                파일 업로드 : <input type="file" name="uploadfile" size=30><br>
            <hr>
            <input type="submit" value="보내기">
            </form>
    </body>
    </html>
    ~~~

    ~~~ jsp
    <%@page import="com.oreilly.servlet.multipart.DefaultFileRenamePolicy"%>
    <%@page import="com.oreilly.servlet.MultipartRequest"%>
    <%@ page language="java" contentType="text/html; charset=EUC-KR"
        pageEncoding="EUC-KR"%>
    <!DOCTYPE html>
    <html>
    <head>
    <meta charset="EUC-KR">
    <title>Insert title here</title>
    </head>
    <body>

        <%
            String path = "C:\\webwork\\Day54\\WebContent\\upload";
        int postSize = 3 * 1024 * 1024;
        try {
            MultipartRequest MR = new MultipartRequest(request, path, postSize, "euc-kr", new DefaultFileRenamePolicy());
            String file = MR.getFilesystemName("uploadfile");
            String file2 = MR.getOriginalFileName("uploadfile");
            out.println("제목 = " + MR.getParameter("subject") + "<br>");
            out.println("저장된 파일 이름 = " + file + "<br>");
            out.println("업로드된 파일이름 = " + file2 + "<br>");
        } catch (Exception e) {
            e.getStackTrace();
        }
        %>

    </body>
    </html>
    ~~~

***




































