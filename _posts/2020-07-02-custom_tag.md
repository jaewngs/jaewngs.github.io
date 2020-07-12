---



title:  "커스텀 태그 & Servlet 필터링"



layout: post



---


## [오늘 할 내용]
1. 커스텀 태그를 작성할 수 있다.
2. Servlet의 필터링을 할 수 있다.
3. ServletContext, ServletConfig

***

## 커스텀 태그
- ver 1.2
	- " javax.servlet.jsp.tagext.* " 중 상속을 받아 메소드를 재정의한다.
	- 태그가 생성되면 /WebContent/WEB-INF/tld 폴더를 만든 다음 생성된 태그를 호출해서 사용한다.
- ver 2.0
	- SimpleTagSupport 상속 받아 메소드 정의
	- 추가로 bean객체에 값을 담아 속성을 만들어 저장하거나 전역변수를 만들어 저장한 것을 .tld에 정의한 다음. jsp에서 태그로 호출한다.
	- /WEB-INF/tags/*.tag로 태그를 @ 로 속성과 변수를 선언한 다음, 원하는 값은 <c:set>태그로 저장을 하고 .jsp로 바로 호출한다.

- 사용자가 임의로 만들어 쓸 수 있는 태그
- 자주 사용되는 기능을 태그로 만들어 사용할 수 있음
- 태그의 이름과 속성, 접두어 등은 사용자가 임의로 정할 수 있음

***

### HelloWorldTag.tld
-  TLD 파일에서 속성 내용 추가

    ~~~ tld
    <?xml version="1.0" encoding="ISO-8859-1" ?>
    <!DOCTYPE taglib
            PUBLIC "-//Sun Microsystems, Inc.//DTD JSP Tag Library 1.2//EN"
            "http://java.sun.com/j2ee/dtd/web-jsptaglibrary_1_2.dtd">

    <!-- a tag library descriptor -->

    <taglib>
        <tlib-version>1.0</tlib-version>
        <jsp-version>1.2</jsp-version>
        <short-name>hello</short-name>

        <tag>
            <name>HelloWorld</name>
            <tag-class>com.test.HelloWorldTag</tag-class>
        </tag>
    </taglib>
    ~~~

### HelloWorldTag.java
- extends TagSupport 추가

    ~~~ java
    package com.test;

    import javax.servlet.jsp.JspException;
    import javax.servlet.jsp.JspWriter;
    import javax.servlet.jsp.tagext.TagSupport;

    public class HelloWorldTag extends TagSupport {

        @Override
        public int doStartTag() throws JspException {
            try {
                JspWriter out = pageContext.getOut();
                out.print("나만의 태그 <br>");
                out.print("성공 했다.<br>");
                out.close();
            } catch (Exception e) {
                System.out.println(e);
            }
            return SKIP_BODY;
        }
    }
    ~~~

### hello.jsp
- jsp 파일에 <%@ taglib uri=" " , prefix=" "%> 추가

    ~~~ jsp
    <%@ page language="java" contentType="text/html; charset=EUC-KR"
        pageEncoding="EUC-KR"%>
    <%@ taglib uri="/WEB-INF/tld/HelloWorldTag.tld" prefix="hw"%>
    <!DOCTYPE html>
    <html>
    <head>
    <meta charset="EUC-KR">
    <title>Insert title here</title>
    </head>
    <body>
        <i> <hw:HelloWorld /></i>
    </body>
    </html>
    ~~~

	![image](https://user-images.githubusercontent.com/52989294/87240575-8fad8780-c455-11ea-9b57-e2e9cfc9d837.png)

***

### MyDateTag.tld
-  TLD 파일에서 속성 내용 추가

    ~~~ tld
    <?xml version="1.0" encoding="ISO-8859-1" ?>
    <!DOCTYPE taglib
            PUBLIC "-//Sun Microsystems, Inc.//DTD JSP Tag Library 1.2//EN"
            "http://java.sun.com/j2ee/dtd/web-jsptaglibrary_1_2.dtd">

    <!-- a tag library descriptor -->

    <taglib>
        <tlib-version>1.0</tlib-version>
        <jsp-version>1.2</jsp-version>
        <short-name>date</short-name>

        <tag>
            <name>DateTag</name>
            <tag-class>com.test.MyDateTag</tag-class>
        </tag>
    </taglib>
    ~~~

### MyDateTag.java
- extends TagSupport 추가

    ~~~ java
    package com.test;

    import java.text.SimpleDateFormat;
    import java.util.Date;

    import javax.servlet.jsp.JspException;
    import javax.servlet.jsp.JspWriter;
    import javax.servlet.jsp.tagext.TagSupport;

    public class MyDateTag extends TagSupport {
        @Override
        public int doStartTag() throws JspException {
            SimpleDateFormat format1 = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
            Date time = new Date();
            String time1 = format1.format(time);

            try {
                JspWriter out = pageContext.getOut();
                out.print(time1);
                // out.close();
            } catch (Exception e) {
                System.out.println(e);
            }
            return SKIP_BODY;
        }
    }
    ~~~

### mydate.jsp
- 현재 시간을 찍어보자

    ~~~ jsp
    <%@ page language="java" contentType="text/html; charset=EUC-KR"
        pageEncoding="EUC-KR"%>
       <%@ taglib uri = "/WEB-INF/tld/MyDateTag.tld"  prefix = "my"%>
    <!DOCTYPE html>
    <html>
    <head>
    <meta charset="EUC-KR">
    <title>Insert title here</title>
    </head>
    <body style = "font-size : 1.5em">
    <i> <my:DateTag/> </i>
    </body>
    </html>
    ~~~

	![image](https://user-images.githubusercontent.com/52989294/87240682-ba4c1000-c456-11ea-8b4c-26c5022df87c.png)

***























