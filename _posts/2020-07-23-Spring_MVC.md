---



title:  "Spring MVC"



layout: post



---


## [오늘 할 내용]
0. DynamicWebProject로 생성 후, Maven-Project로 configure
1. TestMVC[Project명] http://localhost:8787/TestMVC => Context Root
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

2. Model : 객체값을 ' 변수 = 값; ' 으로 전달하여 view의 객체 요청시까지 소멸하지 않고 값을 가진다.
	- 스프링 웹 프로젝트 설정 후 메모리 호출 순서
	- org.springframework.web.WebApplicationInitializer : web.xml에서 페이지 설정, 프로젝트 초기값, 이벤트, 필터, 서블릿을 설정
    - org.springframework.web.context.ContextLoader : 웹프로젝트가 실행되면 가장 먼저 메모리에 로드되어 프로젝트 초기값을 이벤트[ContextLoaderListener]를 이용해서 호출한다.
	- org.springframework.web.servlet
		- 주요 Interface & Class : View, ModelAndView, DispatcherServlet

3. TestMvc2
	- src
	- WebContent
		- WEB-INF
			- web.xml
			- applicationContext.xml
			- mvc02-servlet.xml
		- index.html
	- 설정파일은 "웹, 비즈니스 계층"으로 분리가 된다.
	- **<**listener**>**라는 태그를 이용해서 설정파일에 연동되는 bean을 등록하는 파일을 지정한다.
	- mvc2-servlet.xml과 applicationContext.xml파일 두개로 지정한다.
	- applicationContext에서 정의된 bean을 mvc2-servlet에서 사용할 것이기 때문에 순서를 applicationContext.xml 먼저 등록하고 mvc2-servlet.xml를 나중에 등록한다.
	- ContextLoaderListener 클래스는 기본으로 applicationContext.xml파일을 읽는다.
	- javax.servlet.ServletContext 인터페이스를 구현한 클래스 WAS가 로딩할 때 제일 먼저 ContextLoaderListener가 ContextLoader 클래스를 통해 로딩한다.

***

# 0723_TestMVC

- pom.xml

    ~~~ xml
    <project xmlns="http://maven.apache.org/POM/4.0.0"
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
        <modelVersion>4.0.0</modelVersion>
        <groupId>0723_TestMVC</groupId>
        <artifactId>0723_TestMVC</artifactId>
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

- WebContent/index.html

    ~~~ html
    <!DOCTYPE html>
    <html>
    <head>
    <meta charset="EUC-KR">
    <title>Insert title here</title>
    </head>
    <body style = "font-size : 1.5em; margin:100px 100px 100px 100px;">
    <h1>index.html</h1>
    <a href = "/0723_TestMVC/hello.sp">이동</a><br><br>
    <a href = "/0723_TestMVC/hello01.sp">이동1</a><br><br>
    <a href = "/0723_TestMVC/hello02.sp">이동2</a><br><br>
    <a href = "/0723_TestMVC/hello03.sp">이동3</a><br><br>
    <a href = "/0723_TestMVC/hello04.sp">이동4</a><br><br>

    </body>
    </html>
    ~~~

- WEB-INF/web.xml

    ~~~ xml
    <?xml version="1.0" encoding="UTF-8"?>
    <web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://java.sun.com/xml/ns/javaee" xsi:schemaLocation="http://java.sun.com/xml/ns/javaee http://java.sun.com/xml/ns/javaee/web-app_3_0.xsd" id="WebApp_ID" version="3.0">
      <!-- WebApplicationInitializer -->
      <!-- ContextLoader -->
      <display-name>0723_TestMVC</display-name>
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
      <url-pattern>*.sp</url-pattern>
      </servlet-mapping>
    </web-app>
    ~~~

- WEB-INF/view/hello.jsp

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
        <h1>hello.jsp : ${m_name}</h1>
    </body>
    </html>
    ~~~

- WEB-INF/mvc-servlet.xml

    ~~~ xml
    <?xml version="1.0" encoding="UTF-8"?>
    <beans xmlns="http://www.springframework.org/schema/beans"
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xmlns:context="http://www.springframework.org/schema/context"
        xmlns:mvc="http://www.springframework.org/schema/mvc"
        xsi:schemaLocation="http://www.springframework.org/schema/mvc http://www.springframework.org/schema/mvc/spring-mvc-4.3.xsd
            http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
            http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-4.3.xsd">
        <!-- 이 페이지 자체가 Servlet -->
        <!-- 서블릿 페이지 설정 -->
        <context:component-scan
            base-package="controller" />
        <bean
            class="org.springframework.web.servlet.view.InternalResourceViewResolver">
            <!--<property name="viewClass"
                value="org.springframework.web.servlet.view.JstlView" />-->
            <!-- a,b,c,d.jsp 이동하려면 아래 두개 주석하고 실행하기 -->
            <property name="prefix" value="/WEB-INF/view/" />
            <property name="suffix" value=".jsp" />
        </bean>
    </beans>
    ~~~

***

## WEB-INF/view02

- a.jsp / b.jsp / c.jsp / d.jsp

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

***

## src/controller

- HelloController.java

    ~~~ java
    package controller;

    import org.springframework.stereotype.Controller;
    import org.springframework.ui.Model;
    import org.springframework.web.bind.annotation.RequestMapping;
    import org.springframework.web.servlet.ModelAndView;

    @Controller
    public class HelloController {
        // 원시적인 방법
        /*@RequestMapping(value = "/hello.sp") // hello.sp요청되면 아래 내용 리턴
        public String hello(Model model) {
            model.addAttribute("m_name", "hello spring!");
            // m_name : attributeName
            // "hello spring!" : attributeValue
            return "/WEB-INF/view/hello.jsp";
        }
        */

        @RequestMapping(value = "/hello.sp") // hello.sp요청되면 아래 내용 리턴
        public ModelAndView hello() {
            ModelAndView mv = new ModelAndView();
            mv.setViewName("hello");
            mv.addObject("m_name", "hello spring!");
            return mv;
        }

        @RequestMapping(value = "/hello01.sp")
        public String hello01() {
            return "/WEB-INF/view02/a.jsp"; // 단순 페이지 이동만
        }

        @RequestMapping(value = "/hello02.sp")
        public String hello02() {
            return "/WEB-INF/view02/b.jsp"; // 단순 페이지 이동만
        }

        @RequestMapping(value = "/hello03.sp")
        public String hello03() {
            return "/WEB-INF/view02/c.jsp"; // 단순 페이지 이동만
        }

        @RequestMapping(value = "/hello04.sp")
        public String hello04() {
            return "/WEB-INF/view02/d.jsp";
        }
    }
    ~~~

## WEB-INF/view

- hello.jsp

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
        <h1>hello.jsp : ${m_name}</h1>
    </body>
    </html>
    ~~~

***

# 0723_TestMVC2

- pox.xml

    ~~~ xml
    <project xmlns="http://maven.apache.org/POM/4.0.0"
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
        <modelVersion>4.0.0</modelVersion>
        <groupId>0723_TestMVC2</groupId>
        <artifactId>0723_TestMVC2</artifactId>
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

## WEB-INF
- web.xml

    ~~~ xml
    <?xml version="1.0" encoding="UTF-8"?>
    <web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://java.sun.com/xml/ns/javaee" xsi:schemaLocation="http://java.sun.com/xml/ns/javaee http://java.sun.com/xml/ns/javaee/web-app_3_0.xsd" id="WebApp_ID" version="3.0">
      <display-name>0723_TestMVC2</display-name>
      <welcome-file-list>
        <welcome-file>index.html</welcome-file>
      </welcome-file-list>

      <!-- POST 방식 한글깨짐 방지 -->
      <filter>
        <filter-name>encodingFilter</filter-name>
        <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
        <init-param>
           <param-name>encoding</param-name>
           <param-value>UTF-8</param-value>
        </init-param>
        <init-param>
           <param-name>forceEncoding</param-name>
           <param-value>true</param-value>
        </init-param>
     </filter>
     <filter-mapping>
        <filter-name>encodingFilter</filter-name>
        <url-pattern>/*</url-pattern>
     </filter-mapping>

      <listener>
      <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
      </listener>

      <context-param>
      <param-name>contextConfigLocation</param-name>
      <param-value>/WEB-INF/applicationContext.xml</param-value>
      </context-param>

      <!-- web페이지의 설정파일 -->
      <servlet>
      <servlet-name>mvc2</servlet-name>
      <servlet-class>
      org.springframework.web.servlet.DispatcherServlet
      </servlet-class>
      </servlet>

      <servlet-mapping>
      <servlet-name>mvc2</servlet-name>
      <url-pattern>*.do</url-pattern>
      </servlet-mapping>
    </web-app>
    ~~~

- mvc2-servlet.xml

    ~~~ xml
    <?xml version="1.0" encoding="UTF-8"?>
    <beans xmlns="http://www.springframework.org/schema/beans"
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xmlns:context="http://www.springframework.org/schema/context"
        xmlns:mvc="http://www.springframework.org/schema/mvc"
        xsi:schemaLocation="http://www.springframework.org/schema/mvc http://www.springframework.org/schema/mvc/spring-mvc-4.3.xsd
            http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
            http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-4.3.xsd">

        <context:component-scan
            base-package="com.controller" />

    </beans>
    ~~~

- applicationContext.xml

    ~~~ xml
    <?xml version="1.0" encoding="UTF-8"?>
    <beans xmlns="http://www.springframework.org/schema/beans"
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xmlns:context="http://www.springframework.org/schema/context"
        xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
            http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-4.3.xsd">

        <context:component-scan base-package="com.model" />
        <context:component-scan base-package="com.vo" />

        <!-- 데이터 소스 설정: DriverManagerDataSource클래스 이용 -->
        <bean id="dataSource"
            class="org.springframework.jdbc.datasource.DriverManagerDataSource">

            <property name="driverClassName"
                value="oracle.jdbc.driver.OracleDriver"></property>
            <property name="url"
                value="jdbc:oracle:thin:@localhost:1521:XE"></property>
            <property name="username" value="big5"></property>
            <property name="password" value="admin1234"></property>
        </bean>

        <bean class="com.model.HelloDao" id="helloDao">
            <property name="dataSource" ref="dataSource" />
        </bean>

        <bean class="com.model.FirstJdbcDaoImple" id="firstJdbcDaoImple">
            <property name="dataSource" ref="dataSource" />
        </bean>
    </beans>
    ~~~

- view/result.jsp

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
    <h1>message : ${message}</h1>
    </body>
    </html>
    ~~~

***

- index.html

    ~~~ html
    <!DOCTYPE html>
    <html>
    <head>
    <meta charset="EUC-KR">
    <title>Insert title here</title>
    </head>
    <body>
        <h1>
            <a href="/0723_TestMVC2/hello.do"> 이동 !! </a>
        </h1>
        <h1>
            <a href="/0723_TestMVC2/goods_all.do">전체 출력</a>
        </h1>

    </body>
    </html>
    ~~~

***
## com.vo

- GoodsEntity.java

    ~~~ java
    package com.vo;

    public class GoodsEntity {
        private String code;
        private String name;
        private int price;
        private String maker;

        public GoodsEntity() {
            super();
        }

        public String getCode() {
            return code;
        }

        public void setCode(String code) {
            this.code = code;
        }

        public String getName() {
            return name;
        }

        public void setName(String name) {
            this.name = name;
        }

        public int getPrice() {
            return price;
        }

        public void setPrice(int price) {
            this.price = price;
        }

        public String getMaker() {
            return maker;
        }

        public void setMaker(String maker) {
            this.maker = maker;
        }

        @Override
        public String toString() {
            return "GoodsEntity [code=" + code + ", name=" + name + ", price=" + price + ", maker=" + maker + "]";
        }
    }
    ~~~


## com.model

- HelloDao.java

    ~~~ java
    package com.model;

    import org.springframework.jdbc.core.support.JdbcDaoSupport;
    import org.springframework.stereotype.Repository;

    @Repository
    public class HelloDao extends JdbcDaoSupport {
        public String getHello() {
            // datasource로 연동한 결과
            return "HelloDao호출이다.!!" + getJdbcTemplate().toString();
        }
    }
    ~~~

- HelloService.java

    ~~~ java
    package com.model;

    import org.springframework.beans.factory.annotation.Autowired;
    import org.springframework.stereotype.Service;

    @Service
    public class HelloService {
        @Autowired
        private HelloDao hellodao;

        public String getHello() {
            return hellodao.getHello();
        }
    }
    ~~~

- FirstJdbcDao.java

    ~~~ java
    package com.model;

    import java.util.List;
    import java.util.Map;

    import com.vo.GoodsEntity;

    public interface FirstJdbcDao {
        public List<GoodsEntity> listGoods();
        public List<Map<String, Object>> listGoods02();

        public GoodsEntity findGoods(String code);
        public Map<String, Object> findGoods02(String code);

        public int updateGoods(String name, int price, String maker, String code);
        public int insertGoods(GoodsEntity vo);
    }
    ~~~

- FirstJdbcDaoImple.java

    ~~~ java
    package com.model;

    import java.sql.ResultSet;
    import java.sql.SQLException;
    import java.util.List;
    import java.util.Map;

    import org.springframework.jdbc.core.RowMapper;
    import org.springframework.jdbc.core.support.JdbcDaoSupport;
    import org.springframework.stereotype.Repository;

    import com.vo.GoodsEntity;

    @Repository
    public class FirstJdbcDaoImple extends JdbcDaoSupport implements FirstJdbcDao {

        // case1 : getJdbcTemplate().query()
        @Override
        public List<GoodsEntity> listGoods() {
            RowMapper<GoodsEntity> rowMapper = new GoodsRowMapper();
            return super.getJdbcTemplate().query("SELECT * FROM GOODSINFO", rowMapper);
        }

        public class GoodsRowMapper implements RowMapper<GoodsEntity> {
            public GoodsEntity mapRow(ResultSet rs, int rowNum) throws SQLException {
                GoodsEntity entity = new GoodsEntity();
                entity.setCode(rs.getString("CODE"));
                entity.setName(rs.getString("NAME"));
                entity.setPrice(rs.getInt("PRICE"));
                entity.setMaker(rs.getString("MAKER"));
                return entity;
            }
        }

        // case2 : getJdbcTemplate().queryForList()
        @Override
        public List<Map<String, Object>> listGoods02() {
            RowMapper<GoodsEntity> rowMapper = new GoodsRowMapper();
            return super.getJdbcTemplate().queryForList("SELECT * FROM GOODSINFO");
        }

        // case 3:queryForMap(String sql) [Single Row query]
        @Override
        public Map<String, Object> findGoods02(String code) {
            String sql = "SELECT * FROM GOODSINFO WHERE CODE=?";
            return this.getJdbcTemplate().queryForMap(sql, new Object[] { code });
        }
        // queryforList, queryforMap은 key/value를 가지고 오는 놈. mongo에서 쉽게 쓴다.

        // ------------------------------------------------
        @Override
        public GoodsEntity findGoods(String code) {
            String sql = "SELECT * FROM GOODSINFO WHERE CODE=?";
            RowMapper<GoodsEntity> mapper = new RowMapper<GoodsEntity>() {
                @Override
                public GoodsEntity mapRow(ResultSet rs, int rowNum) throws SQLException {
                    GoodsEntity entity = new GoodsEntity();
                    entity.setCode(rs.getString("CODE"));
                    entity.setName(rs.getString("NAME"));
                    entity.setPrice(rs.getInt("PRICE"));
                    entity.setMaker(rs.getString("MAKER"));
                    return entity;
                }// 다 return 할 때 까지 next가 자동으로 돌려준다.
            };
            // resultset의 next가 내장되어있어서 횟수만큼 돌려준다.
            // mapRow가 6번 호출되어서 각 resultSet가 쌓인 걸 담아서 리턴한다.
            return super.getJdbcTemplate().queryForObject(sql, mapper, new Object[] { code });
        }

        // ------------------------------------------
        @Override
        public int updateGoods(String name, int price, String maker, String code) {
            return super.getJdbcTemplate().update("update goodsinfo set name=?, price=?," + " maker=? where code=?",
                    new Object[] { name, price, maker, code });
        }

        @Override
        public int insertGoods(GoodsEntity vo) {
            return super.getJdbcTemplate().update("INSERT INTO GOODSINFO(CODE,NAME,PRICE,MAKER) VALUES(?,?,?,?)",
                    new Object[] { vo.getCode(), vo.getName(), vo.getPrice(), vo.getMaker() });

        }
    }
    ~~~

- FirstService.java

    ~~~ java
    package com.model;

    import java.util.List;

    import org.springframework.beans.factory.annotation.Autowired;
    import org.springframework.stereotype.Service;

    import com.vo.GoodsEntity;

    @Service
    public class FirstService {

        @Autowired
        FirstJdbcDao firstJdbcDao;

        public List<GoodsEntity> listGoods(){
            return firstJdbcDao.listGoods();
        }

        public int insertGoods(GoodsEntity vo) {
            return firstJdbcDao.insertGoods(vo);
        }
    }
    ~~~

***

## com.controller

- HelloController.java

    ~~~ java
    package com.controller;

    import org.springframework.beans.factory.annotation.Autowired;
    import org.springframework.beans.factory.annotation.Qualifier;
    import org.springframework.stereotype.Controller;
    import org.springframework.ui.Model;
    import org.springframework.web.bind.annotation.ModelAttribute;
    import org.springframework.web.bind.annotation.RequestMapping;
    import org.springframework.web.bind.annotation.RequestMethod;

    import com.model.FirstService;
    import com.model.HelloService;
    import com.vo.GoodsEntity;

    @Controller
    public class HelloController {
        @Autowired // biz 호출
        HelloService hello_service;

        @Autowired
        @Qualifier(value = "firstService")
        FirstService first_service;

        @RequestMapping("/hello.do")
        public String hello(Model model) {
            model.addAttribute("message", hello_service.getHello());
            return "/WEB-INF/view/result.jsp";
        }

        @RequestMapping("/goods_all.do")
        public String Goods_all(Model model) {
            model.addAttribute("all", first_service.listGoods());
            return "/WEB-INF/view/goods_all.jsp";
        }

        @RequestMapping("/goods_insert.do")
        public String Goods_insert() {
            return "/WEB-INF/view/goods_insert.jsp";
        }

        @RequestMapping(method = RequestMethod.POST, value = "/goods_insert_res.do")
        //@RequestMapping("/goods_insert_res.do")
        public String Goods_insert_res(@ModelAttribute GoodsEntity vo) {
            int r = first_service.insertGoods(vo);
            return "goods_all.do"; // TestMVC2/goods_all.do
        }
    }
    ~~~

***

### insert만 구현되어있음, 나머지 CRUD도 해보자










