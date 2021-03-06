---



title:  "Spring File Upload"



layout: post



---


## [오늘 할 내용]

1.  0727_TestMVC05[Project명] http://localhost:8787/0727_TestMVC05 => Context Root
	- src
		- controller패키지
			- HelloController.java
	- WebContent
		- META-INF
		- WEB-INF
			- web.xml[Servlet 설정]
			- mvc-servlet.xml[서블릿이름-servlet.xml]
			- view폴더 생성
				- hello.jsp
			- view02폴더 생성
				- a.jsp
				- b.jsp
				- c.jsp
				- d.jsp
		- index.html
	- pom.xml

2. PUT
	- 서버에게 resource의 업데이트 하거나 resource가 없다면 새로운 resource를 생성해 달라고 요청한다.
	- 모든 필드를 호출 후 수정한다. 회원정보 수정 등 [전체 메모리 로드 후 수정]
3. PATCH
	- 서버에게 resource의 업데이트를 요청한다.
	- 부분 데이터를 업데이트 할 때 사용
4. DELETE : 서버에게 resource의 삭제를 요청할 때 사용
5. GET : 폼의 데이터를 쿼리 스트링과 함께 서버에 요청한다.
6. POST : 폼의 데이터를 쿼리 스트링 없이 바이너리로 변환하여 서버에 요청한다.

7. @ResponseBody (여기부터 0727_TestFileUp)
	- HTTP요청의 본문 body 부분이 그대로 전달된다.
	- @ResponseBody가 메소드 레벨에 부여되면 메소드가 리턴하는 오브젝트 뷰를 통해 결과를 만들어 내는 모델로 사용하는 대신, 메세지 컨버터를 통해 바로 HTTP 응답의 메시지 본문으로 변환된다.
	- 즉, 요청한 형태에 맞춰서 메시지 변환기를 통해 결과값을 반환한다.
	- [Spring 2.0 ~ ] ContentNegotiatingViewResolver와는 동작 방식이 좀 다르다.
	- ContentNegotiatingViewResolver는 등로고디어 있는 ViewResolver 중에서 Controller 메소드의 리턴값을 통해 등록된 ViewResolver 중에서 적합한 형태로 처리해서 반환한다.
	- @ResponseBody 는 @RequestBody가 선택한 형식으로 결과값을 변환하여 반환한다.
	- ex) 컨트롤러 메소드에 @ResponseBody가 쓰여지면 메소드에서 View를 통하지 않고 HTTP의 Response Body에 직접 작성된다. 객체가 특정 형태로 변함(문자열, json, xml, 파일 등)


***

## xml

- pom.xml

    ~~~ xml
    <project xmlns="http://maven.apache.org/POM/4.0.0"
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
        <modelVersion>4.0.0</modelVersion>
        <groupId>0727_TestMVC05</groupId>
        <artifactId>0727_TestMVC05</artifactId>
        <version>0.0.1-SNAPSHOT</version>
        <packaging>war</packaging>
        <build>
            <sourceDirectory>src</sourceDirectory>
            <plugins>
                <plugin>
                    <artifactId>maven-compiler-plugin</artifactId>
                    <version>3.8.0</version>
                    <configuration>
                        <source>1.8</source>
                        <target>1.8</target>
                    </configuration>
                </plugin>
                <plugin>
                    <artifactId>maven-war-plugin</artifactId>
                    <version>3.2.3</version>
                    <configuration>
                        <warSourceDirectory>WebContent</warSourceDirectory>
                    </configuration>
                </plugin>
            </plugins>
        </build>
        <dependencies>
            <!-- https://mvnrepository.com/artifact/org.springframework/spring-context -->
            <dependency>
                <groupId>org.springframework</groupId>
                <artifactId>spring-context</artifactId>
                <version>${spring.version}</version>
            </dependency>

            <!-- https://mvnrepository.com/artifact/org.springframework/spring-core -->
            <dependency>
                <groupId>org.springframework</groupId>
                <artifactId>spring-core</artifactId>
                <version>${spring.version}</version>
            </dependency>

            <!-- https://mvnrepository.com/artifact/org.springframework/spring-beans -->
            <dependency>
                <groupId>org.springframework</groupId>
                <artifactId>spring-beans</artifactId>
                <version>${spring.version}</version>
            </dependency>

            <dependency>
                <groupId>org.springframework</groupId>
                <artifactId>spring-jdbc</artifactId>
                <version>${spring.version}</version>
            </dependency>

            <dependency>
                <groupId>org.springframework</groupId>
                <artifactId>spring-aop</artifactId>
                <version>${spring.version}</version>
            </dependency>

            <dependency>
                <groupId>org.springframework</groupId>
                <artifactId>spring-web</artifactId>
                <version>${spring.version}</version>
            </dependency>

            <dependency>
                <groupId>org.springframework</groupId>
                <artifactId>spring-webmvc</artifactId>
                <version>${spring.version}</version>
            </dependency>
            <!-- https://mvnrepository.com/artifact/org.aspectj/aspectjweaver -->
            <dependency>
                <groupId>org.aspectj</groupId>
                <artifactId>aspectjweaver</artifactId>
                <version>1.8.1</version>
            </dependency>

            <!-- https://mvnrepository.com/artifact/org.aspectj/aspectjrt -->
            <dependency>
                <groupId>org.aspectj</groupId>
                <artifactId>aspectjrt</artifactId>
                <version>1.8.1</version>
            </dependency>

            <dependency>
                <groupId>org.mybatis</groupId>
                <artifactId>mybatis</artifactId>
                <version>3.4.5</version>
            </dependency>
        </dependencies>
        <properties>
            <spring.version>4.3.27.RELEASE</spring.version>
        </properties>
    </project>
    ~~~

***

## WebContent
- index.html

    ~~~ html
    <!DOCTYPE html>
    <html>
    <head>
    <meta charset="EUC-KR">
    <title>Insert title here</title>
    </head>
    <body style = "font-size : 1.5em; margin:100px 100px 100px 100px;">
    <h1>index.html</h1>

    <a href = "/0727_TestMVC05/MyTest/hello01.sp"> a 페이지 이동1</a><br><br>
    <a href = "/0727_TestMVC05/MyTest/hello02.sp"> b 페이지 이동2</a><br><br>
    <a href = "/0727_TestMVC05/MyTest/hello03.sp?id=3"> c 페이지 이동3</a><br><br>
    <a href = "/0727_TestMVC05/MyTest/hello04.sp?id=3&name=abc"> d 페이지 이동4</a><br><br>
    <a href = "/0727_TestMVC05/MyTest/hello05.sp/42"> e 페이지 이동5</a><br><br>
    <hr>
    <a href = "/0727_TestMVC05/MyTest/something"> 1. 가상 페이지 이동</a><br><br>
    <a href = "/0727_TestMVC05/MyTest/something02"> 2. 가상 페이지</a><br><br>
    <a href = "/0727_TestMVC05/MyTest/something03"> 3. 가상 페이지</a><br><br>
    </body>
    </html>
    ~~~

***

## WebContent/WEB-INF
- web.xml

    ~~~ xml
    <?xml version="1.0" encoding="UTF-8"?>
    <web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://java.sun.com/xml/ns/javaee" xsi:schemaLocation="http://java.sun.com/xml/ns/javaee http://java.sun.com/xml/ns/javaee/web-app_3_0.xsd" id="WebApp_ID" version="3.0">
      <!-- WebApplicationInitializer -->
      <!-- ContextLoader -->
      <display-name>0727_TestMVC05</display-name>
      <welcome-file-list>
        <welcome-file>index.html</welcome-file>
      </welcome-file-list>

      <!-- web페이지의 설정파일 -->
      <servlet>
      <servlet-name>mvc</servlet-name>
      <servlet-class>
      org.springframework.web.servlet.DispatcherServlet
      </servlet-class>
      <load-on-startup>1</load-on-startup>
      </servlet>

      <servlet-mapping>
      <servlet-name>mvc</servlet-name>
      <!-- <url-pattern>*.sp</url-pattern> -->
		<url-pattern>/MyTest/*</url-pattern>
      </servlet-mapping>
    </web-app>
    ~~~

- mvc-servlet.xml

    ~~~ xml
    <?xml version="1.0" encoding="UTF-8"?>
    <beans xmlns="http://www.springframework.org/schema/beans"
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xmlns:context="http://www.springframework.org/schema/context"
        xmlns:mvc="http://www.springframework.org/schema/mvc"
        xmlns:c="http://www.springframework.org/schema/c"
        xsi:schemaLocation="http://www.springframework.org/schema/mvc http://www.springframework.org/schema/mvc/spring-mvc-4.3.xsd
            http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
            http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-4.3.xsd">
        <!-- 이 페이지 자체가 Servlet -->
        <!-- 서블릿 페이지 설정 -->
        <mvc:annotation-driven />
        <mvc:annotation-driven
            enable-matrix-variables="true" />
        <context:component-scan
            base-package="controller" />
        <context:component-scan base-package="BIZ" />
        <bean id="myUserService" class="BIZ.MyUserService">

            <constructor-arg>
                <set>
                    <bean class="VO.MyUser" c:id="1" c:name="one" />
                    <bean class="VO.MyUser" c:id="2" c:name="two" />
                    <bean class="VO.MyUser" c:id="3" c:name="three" />
                </set>
            </constructor-arg>
        </bean>
    </beans>
    ~~~

## WebContent/WEB-INF/view02
- a.jsp

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
    <h1>a.jsp임</h1>
    </body>
    </html>
    ~~~

- b.jsp

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
    <h1>b.jsp임</h1>
    </body>
    </html>
    ~~~

- c.jsp

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
        <h1>c.jsp임</h1>
         id : ${myid}
    </body>
    </html>
    ~~~

- d.jsp

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
    <h1>d.jsp임</h1>
    id : ${myid}
    <br>
    name : ${myname}
    </body>
    </html>
    ~~~

- e.jsp

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
    <h1>e.jsp임</h1>
    ${myno}
    </body>
    </html>
    ~~~

***

## src/VO
- MyUser.java

    ~~~ java
    package VO;

    import java.util.Objects;

    public class MyUser {
        private String id;
        private String name;

        public MyUser(String id, String name) {
            super();
            this.id = id;
            this.name = name;
        }

        public String getId() {
            return id;
        }

        public void setId(String id) {
            this.id = id;
        }

        public String getName() {
            return name;
        }

        public void setName(String name) {
            this.name = name;
        }

        @Override
        public int hashCode() {
            // this의 해쉬코드 값
            return Objects.hash(id, name);
        }

        @Override
        public boolean equals(Object obj) {
            if (this == obj)
                return true;
            if (obj == null)
                return false;
            if (getClass() != obj.getClass())
                return false;
            MyUser other = (MyUser) obj;
            if (name == null) {
                if (other.name != null)
                    return false;
            } else if (!name.equals(other.name))
                return false;
            return true;
        }

        @Override
        public String toString() {
            return "MyUser [id=" + id + ", name=" + name + "]";
        }
    }
    ~~~

## src/BIZ
- IService.java

    ~~~ java
    package BIZ;

    import java.util.Set;

    import VO.MyUser;

    public interface IService {
        boolean delete(String name);

        Set<MyUser> all();
    }
    ~~~

- MyUserService.java

    ~~~ java
    package BIZ;

    import java.util.HashSet;
    import java.util.Set;
    import org.springframework.stereotype.Service;

    import VO.MyUser;

    @Service
    public class MyUserService implements IService {

        Set<MyUser> myuser = new HashSet<>();

        public MyUserService(Set<MyUser> myuser) {
            super();
            this.myuser = myuser;
        }

        @Override
        public boolean delete(String name) {
            boolean isRemoved = ((MyUser) myuser).getName().equals(name);
            // 삭제 코드 추가하기

            return isRemoved;
        }

        @Override
        public Set<MyUser> all() {
            return this.myuser;
        }
    }
    ~~~

***

## src/controller
- HelloController.java

    ~~~ java
    package controller;

    import java.io.UnsupportedEncodingException;

    import org.apache.ibatis.annotations.Param;
    import org.springframework.beans.factory.annotation.Autowired;
    import org.springframework.http.HttpEntity;
    import org.springframework.http.HttpHeaders;
    import org.springframework.http.HttpStatus;
    import org.springframework.http.ResponseEntity;
    import org.springframework.stereotype.Controller;
    import org.springframework.ui.Model;
    import org.springframework.web.bind.annotation.GetMapping;
    import org.springframework.web.bind.annotation.MatrixVariable;
    import org.springframework.web.bind.annotation.PathVariable;
    import org.springframework.web.bind.annotation.PostMapping;
    import org.springframework.web.bind.annotation.RequestMapping;
    import org.springframework.web.bind.annotation.RequestMethod;
    import org.springframework.web.bind.annotation.ResponseBody;
    import org.springframework.web.servlet.ModelAndView;

    import BIZ.MyUserService;

    @Controller
    //@RequestMapping("/MyTest")
    // web.xml에 <url-pattern>/MyTest/*</url-pattern>해줬으니까 주석처리
    public class HelloController {

        @Autowired
        MyUserService myUserService;

        @RequestMapping(path = "/hello01.sp", method = RequestMethod.GET)
        public String hello01() {
            return "/WEB-INF/view02/a.jsp";
        }

        @GetMapping(value = "/hello02.sp")
        public String hello02() {
            return "/WEB-INF/view02/b.jsp";
        }

        @GetMapping(value = "/hello03.sp")
        public String hello03(@Param("id") String id, Model model) {
            model.addAttribute("myid", id);
            return "/WEB-INF/view02/c.jsp";
        }

        @RequestMapping(path = "/hello04.sp", method = RequestMethod.GET)
        public String hello04(@Param("id") String id, @Param("name") String name, Model model) {
            model.addAttribute("myid", id);
            model.addAttribute("myname", name);
            return "/WEB-INF/view02/d.jsp";
        }

        /*
         * @GetMapping("/hello05.sp/{myno}") public String hello05(@PathVariable("myno")
         * String myno, Model model) { model.addAttribute("myno", myno); return
         * "/WEB-INF/view02/e.jsp"; }
         */

        /*
         * @GetMapping("/hello05.sp/{myno}") public String
         * hello05(@MatrixVariable(required = false, defaultValue= "1") String myno,
         * Model model) { model.addAttribute("myno", myno); return
         * "/WEB-INF/view02/e.jsp"; }
         */

        @GetMapping("/hello05.sp/{id}")
        public String hello05(@PathVariable("id") String id,
                @MatrixVariable(required = false, defaultValue = "1") String myno, Model model) {
            model.addAttribute("myno", id);
            return "/WEB-INF/view02/e.jsp";
        }

        @GetMapping("/something")
        @ResponseBody
        public String helloWorld() {
            return "Hello World " + "<br><a href='/0727_TestMVC05'>HOME</a>";
        }

        // 위에꺼랑 똑같은 기능임
        // 위 내용처럼 하는게 훨씬 간편
        // 응답페이지를 만들어줌
        @RequestMapping("/something02")
        public ResponseEntity<String> handle(HttpEntity<byte[]> requestEntity) throws UnsupportedEncodingException {
            String requestHeader = requestEntity.getHeaders().getFirst("MyRequestHeader");
            byte[] requestBody = requestEntity.getBody();

            // do something with request header and body

            HttpHeaders responseHeaders = new HttpHeaders();
            responseHeaders.set("MyResponseHeader", "MyValue");
            return new ResponseEntity<String>("Hello World <br><a href='/0727_TestMVC05'>Home</a>", responseHeaders,
                    HttpStatus.CREATED);
        }

        @GetMapping("/something03")
        @ResponseBody
        public String all() {
            return myUserService.all().toString() + "<br><br><a href='/0727_TestMVC05'>home</a>";
        }
    }
    ~~~

***

#0727_TestFileUp

## xml
- pom.xml

    ~~~ xml
    <project xmlns="http://maven.apache.org/POM/4.0.0"
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
        <modelVersion>4.0.0</modelVersion>
        <groupId>0727_TestFileUp</groupId>
        <artifactId>0727_TestFileUp</artifactId>
        <version>0.0.1-SNAPSHOT</version>
        <packaging>war</packaging>
        <build>
            <sourceDirectory>src</sourceDirectory>
            <plugins>
                <plugin>
                    <artifactId>maven-compiler-plugin</artifactId>
                    <version>3.8.0</version>
                    <configuration>
                        <source>1.8</source>
                        <target>1.8</target>
                    </configuration>
                </plugin>
                <plugin>
                    <artifactId>maven-war-plugin</artifactId>
                    <version>3.2.3</version>
                    <configuration>
                        <warSourceDirectory>WebContent</warSourceDirectory>
                    </configuration>
                </plugin>
            </plugins>
        </build>
        <dependencies>
            <!-- https://mvnrepository.com/artifact/org.springframework/spring-context -->
            <dependency>
                <groupId>org.springframework</groupId>
                <artifactId>spring-context</artifactId>
                <version>${spring.version}</version>
            </dependency>

            <!-- https://mvnrepository.com/artifact/org.springframework/spring-core -->
            <dependency>
                <groupId>org.springframework</groupId>
                <artifactId>spring-core</artifactId>
                <version>${spring.version}</version>
            </dependency>

            <!-- https://mvnrepository.com/artifact/org.springframework/spring-beans -->
            <dependency>
                <groupId>org.springframework</groupId>
                <artifactId>spring-beans</artifactId>
                <version>${spring.version}</version>
            </dependency>

            <dependency>
                <groupId>org.springframework</groupId>
                <artifactId>spring-jdbc</artifactId>
                <version>${spring.version}</version>
            </dependency>

            <dependency>
                <groupId>org.springframework</groupId>
                <artifactId>spring-aop</artifactId>
                <version>${spring.version}</version>
            </dependency>

            <dependency>
                <groupId>org.springframework</groupId>
                <artifactId>spring-web</artifactId>
                <version>${spring.version}</version>
            </dependency>

            <dependency>
                <groupId>org.springframework</groupId>
                <artifactId>spring-webmvc</artifactId>
                <version>${spring.version}</version>
            </dependency>
            <!-- https://mvnrepository.com/artifact/org.aspectj/aspectjweaver -->
            <dependency>
                <groupId>org.aspectj</groupId>
                <artifactId>aspectjweaver</artifactId>
                <version>1.8.1</version>
            </dependency>

            <!-- https://mvnrepository.com/artifact/org.aspectj/aspectjrt -->
            <dependency>
                <groupId>org.aspectj</groupId>
                <artifactId>aspectjrt</artifactId>
                <version>1.8.1</version>
            </dependency>

            <dependency>
                <groupId>org.mybatis</groupId>
                <artifactId>mybatis</artifactId>
                <version>3.4.5</version>
            </dependency>

            <!-- 파일 추가 위해서 dependency 추가 -->
            <dependency>
                <groupId>commons-fileupload</groupId>
                <artifactId>commons-fileupload</artifactId>
                <version>1.3.1</version>
            </dependency>
            <dependency>
                <groupId>commons-io</groupId>
                <artifactId>commons-io</artifactId>
                <version>2.4</version>
            </dependency>
            <dependency>
                <groupId>commons-logging</groupId>
                <artifactId>commons-logging</artifactId>
                <version>1.2</version>
            </dependency>
            <dependency>
                <groupId>javax.servlet</groupId>
                <artifactId>jstl</artifactId>
                <version>1.2</version>
            </dependency>
        </dependencies>
        <properties>
            <spring.version>4.3.27.RELEASE</spring.version>
        </properties>
    </project>
    ~~~

## WebContent/WEB-INF

- web.xml

    ~~~ xml
    <?xml version="1.0" encoding="UTF-8"?>
    <web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xmlns="http://java.sun.com/xml/ns/javaee"
        xsi:schemaLocation="http://java.sun.com/xml/ns/javaee http://java.sun.com/xml/ns/javaee/web-app_3_0.xsd"
        id="WebApp_ID" version="3.0">
        <display-name>0727_TestFileUp</display-name>
        <welcome-file-list>
            <welcome-file>index.jsp</welcome-file>
        </welcome-file-list>

        <filter>
            <filter-name>encodingFilter</filter-name>
            <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
            <init-param>
                <param-name>encoding</param-name>
                <param-value>UTF-8</param-value>
            </init-param>

        </filter>
        <filter-mapping>
            <filter-name>encodingFilter</filter-name>
            <url-pattern>/*</url-pattern>
        </filter-mapping>

        <!-- web페이지의 설정파일 -->
        <servlet>
            <servlet-name>fileup</servlet-name>
            <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
            <load-on-startup>1</load-on-startup>
        </servlet>

        <servlet-mapping>
            <servlet-name>fileup</servlet-name>
            <url-pattern>/</url-pattern>
        </servlet-mapping>

    </web-app>
    ~~~

- fileup-servlet.xml

    ~~~ xml
    <?xml version="1.0" encoding="UTF-8"?>
    <beans xmlns="http://www.springframework.org/schema/beans"
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xmlns:context="http://www.springframework.org/schema/context"
        xmlns:p="http://www.springframework.org/schema/p"
        xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
            http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-4.3.xsd">

        <context:annotation-config />
        <context:component-scan
            base-package="controller" />
        <context:component-scan base-package="validate" />
        <bean id="viewResolver"
            class="org.springframework.web.servlet.view.UrlBasedViewResolver">
            <property name="viewClass"
                value="org.springframework.web.servlet.view.JstlView" />
            <property name="prefix" value="/WEB-INF/view/" />
            <property name="suffix" value=".jsp" />
            <property name="contentType" value="text/html; charset=UTF-8" />
        </bean>

        <bean id="multipartResolver"
            class="org.springframework.web.multipart.commons.CommonsMultipartResolver">
            <property name="maxUploadSize" value="5242880" />
            <property name="defaultEncoding" value="UTF-8" />
        </bean>
    </beans>
    ~~~

***

## src/validate
- UploadFile.java

    ~~~ java
    package validate;

    import org.springframework.stereotype.Service;
    import org.springframework.web.multipart.MultipartFile;

    @Service("uploadFile")
    public class UploadFile {
        private String filename;
        private String desc;
        private MultipartFile file;

        public UploadFile() {
            super();
        }

        public UploadFile(String filename, String desc, MultipartFile file) {
            super();
            this.filename = filename;
            this.desc = desc;
            this.file = file;
        }

        public String getFilename() {
            return filename;
        }

        public void setFilename(String filename) {
            this.filename = filename;
        }

        public String getDesc() {
            return desc;
        }

        public void setDesc(String desc) {
            this.desc = desc;
        }

        public MultipartFile getFile() {
            return file;
        }

        public void setFile(MultipartFile file) {
            this.file = file;
        }
    }
    ~~~

- FileValidator.java

    ~~~ java
    package validate;

    import org.springframework.stereotype.Service;
    import org.springframework.validation.Errors;
    import org.springframework.validation.Validator;

    // spring에서 제공해주는 유효성 검사 클래스를 implements
    @Service("fileValidator")
    public class FileValidator implements Validator {

        @Override
        public boolean supports(Class<?> clazz) {
            // 유효성 대상 클래스
            return false;
        }

        @Override
        public void validate(Object uploadFile, Errors errors) {
            UploadFile file = (UploadFile) uploadFile;
            System.out.println(file.getDesc() + " 1");
            System.out.println(file.getFilename() + " 2");
            System.out.println(file.getFile().getName() + " 3");

            if (file != null) {
                if (file.getFile().getSize() == 0) {
                    errors.rejectValue("file", "uploadForm", "야 파일 내용이 없잖아!");
                }

                if (file.getDesc().isEmpty()) {
                    errors.rejectValue("desc", "uploadForm", "야 설명이 없잖아!");
                }
            }
        }
    }
    ~~~

***

## src/controller

- UploadController.java

    ~~~ java
    package controller;

    import java.io.File;
    import java.io.FileOutputStream;
    import java.io.IOException;
    import java.io.InputStream;
    import java.io.OutputStream;

    import javax.servlet.http.HttpServlet;

    import org.springframework.beans.factory.annotation.Autowired;
    import org.springframework.stereotype.Controller;
    import org.springframework.validation.BindingResult;
    import org.springframework.web.bind.annotation.ModelAttribute;
    import org.springframework.web.bind.annotation.RequestMapping;
    import org.springframework.web.bind.annotation.RequestMethod;
    import org.springframework.web.multipart.MultipartFile;
    import org.springframework.web.servlet.ModelAndView;

    import validate.FileValidator;
    import validate.UploadFile;

    @Controller
    public class UploadController extends HttpServlet {

        @Autowired
        private FileValidator fileValidator; // 유효성 검사

        // ----------------------------------------
        @RequestMapping(value = "/form", method = RequestMethod.GET)
        public String getUploadForm(@ModelAttribute UploadFile uploadFile) {
            return "upload/uploadForm";
        }

        @RequestMapping(value = "/upload", method = RequestMethod.POST)
        public ModelAndView fileUpload(@ModelAttribute("uploadFile") UploadFile uploadFile, BindingResult result) throws IOException {
            // 1. 유효성 검사
            fileValidator.validate(uploadFile, result);
            if (result.hasErrors()) {
                return new ModelAndView("upload/uploadForm");
            }

            // 2. 업로드 VO의 객체 중 MultipartFile로 getFile()을 리턴 받는다.
            MultipartFile file = uploadFile.getFile();

            // 3. 리턴 받은 MultipartFile의 객체의 원본 파일 이름을 리턴 받는다.
            String filename = file.getOriginalFilename();

            // 4. uploadFile.jsp 에서 사용할 객체를 생성한 후 VO에 입력한다.
            UploadFile fileobj = new UploadFile();
            fileobj.setFilename(filename);
            fileobj.setDesc(uploadFile.getDesc());

            // 5. 저장장소를 지정하고 File의 객체를 통해 읽어서 저장한다.
            InputStream inputStream = null;
            OutputStream outputStream = null;

            try {
                inputStream = file.getInputStream();
                String path = "C:\\springwork\\0727_TestMVC05\\WebContent\\storage";
                File newFile = new File(path + "/" + filename);
                if (!newFile.exists()) {
                    newFile.createNewFile();
                }
                outputStream = new FileOutputStream(newFile);
                int read = 0;
                byte[] b = new byte[(int) file.getSize()];
                while ((read = inputStream.read(b)) != -1) {
                    outputStream.write(b, 0, read);
                }

            } catch (IOException e) {
                e.printStackTrace();
            }finally {
                outputStream.close();
                inputStream.close();
            }
            return new ModelAndView("upload/uploadFile", "fileobj1", fileobj);
        } // upload end

    }
	~~~

***

## WebContent

- index.jsp

    ~~~ jsp
    <%@ page language="java" contentType="text/html; charset=EUC-KR"
        pageEncoding="EUC-KR"%>
    <%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core"%>
    <!DOCTYPE html>
    <html>
    <head>
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
    <title>메인화면</title>
    </head>
    <body>
        <c:redirect url="form" />
    </body>
    </html>
    ~~~

***

## WebContent/WEB-INF/view/upload

- uploadForm.jsp

    ~~~ jsp
    <%@ page language="java" contentType="text/html; charset=EUC-KR"
        pageEncoding="EUC-KR"%>

    <%@ taglib prefix="form" uri="http://www.springframework.org/tags/form"%>

    <!DOCTYPE html>
    <html>
    <head>
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
    <title>Insert title here</title>
    </head>
    <body>
        <form:form method="post" enctype="multipart/form-data"
            modelAttribute="uploadFile" action="upload">
            <h3>*** 업로드할 파일 선택 ***</h3>
            <br>
        파일 : <form:input type="file" path="file" name="file" />
            <br>
            <br>
            <form:input type="text" path="filename" name="filename" />

            <p style="color: red; font-weight: bold">
                <form:errors path="file" />
            </p>
            <br>

        설명 : <br>
            <form:textarea name="desc" path="desc" cols="40" rows="10" />
            <br>
            <br>

            <p style="color: red; font-weight: bold">
                <form:errors path="desc" />
            </p>
            <br>

            <input type="submit" value="전송">
        </form:form>
    </body>
    </html>
    ~~~

- uploadFile.jsp

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
        <h2>업로드 된 파일 정보</h2>
        파일명 : ${fileobj1.filename}
        <br> 설 명 : ${fileobj1.desc}
        <br>
    </body>
    </html>
    ~~~