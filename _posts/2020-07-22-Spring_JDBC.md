---



title:  "Spring JDBC"



layout: post



---


## [오늘 할 내용]
1. OSGi : Java 모듈의 동적 추가 및 실행을 관리하기 위한 기반 시스템
	- OSGi Alliance는 1999년에 "Open Service Gateway Initiative" 명칭으로 설립
	- 게이트웨이 장치에서 실행되는 서비스 프로그램의 실행 기반을 목적으로 함
	- java + DB [JDBC] = SimpleDriverDataSource 사용
	- NOTE: Within special class loading environments such as OSGi, this class is effectively superseded by SimpleDriverDataSource due to general class loading issues with the JDBC DriverManager that be resolved through direct Driver usage (which is exactly what SimpleDriverDataSource does).
	- java -> Spring + DB[JDBC] = DriverManagerDataSource를 사용(org.springframework.jdbc.*)[sec 19.2.1]

2. SpringJDBC _ test01/test02
	1. SpringBeans.xml
		- org.springframework.jdbc.datasource.DriverManagerDataSource를 이용해서 DB드라이버를 연결한다.
	2. JdbcDaoSupport의 setDataSource()로 DB를 연결한다.
	3. JdbcTemplate
		- getJdbcTemplate() 를 연결한 상태에서 쿼리 객체를 리턴하는 메소드를 호출한다.
	4. 리턴받은 JdbcTemplate의 객체를 통해 update() 등의 메소드로 퀴리를 실행한다.
	5. JdbcTemplate.update() / update/insert/delete
	6. JdbcTemplate.query() / select / update / insert / delete의 쿼리를 받아 실행
	7. JdbcTemplate.queryforObject() / select 형의 Object
	8. JdbcTemplate.queryforInt() / query의 return Type이 int형

3. test03(ORM_[sec 20. Object Relational Mapping(ORM) Data Access])
	- <https://mybatis.org/mybatis-3/>
	- jdbc.properties
	- 환경설정.XML : config.xml
		- DB 연결코드
		- SQL구문.XML : SQLMapXML.xml
	- Springbeans.xml
	- case1 : ( jdbc.properties , Springbeans.xml ) + (환경설정.xml(config.xml), sql구문.xml(SQLMapXML.xml))
	- case2 : jdbc.properties + 환경설정.xml(config.xml), sql구문.xml(SQLMapXML.xml) + Springbeans.xml
	- case3 : 환경설정.xml(config.xml), sql구문.xml(SQLMapXML.xml) + Springbeans.xml

	- test03(mybatis)
		- goods-mapping.xml[sql]
		- mybatis-config.xml[환경설정]
		- beans.xml
		- jdbc.properties


***




***

## pom.xml

## sql/a.sql

## src/test01
- applicationContext.xml
- FirstJdbcDao.java
- FirstJdbcDaoImple.java





















