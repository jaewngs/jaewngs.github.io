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

4. test04
	- @과 query로 매퍼를 작성한다.
	- <https://mybatis.org/mybatis-3/ko/java-api.html>

***

## pom.xml
- mybatis dependency 추가

    ~~~ xml
    <project xmlns="http://maven.apache.org/POM/4.0.0"
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
        <modelVersion>4.0.0</modelVersion>
        <groupId>0722_SpringJDBC</groupId>
        <artifactId>0722_SpringJDBC</artifactId>
        <version>0.0.1-SNAPSHOT</version>
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

## sql/a.sql
- goodsinfo 테이블 생성

    ~~~ sql
    drop table goodsinfo purge;

    create table goodsinfo(
    code varchar(10),
    name varchar(10),
    price number,
    maker varchar2(20));

    insert into goodsinfo(code, name, price, maker) values ('p0001', '자바', 25000, '공갈닷컴');
    insert into goodsinfo(code, name, price, maker) values ('p0002', 'JSP', 30000, '엔코아');

    insert into goodsinfo(code, name, price, maker) values ('p0003', '자바5', 15000, '인포');
    insert into goodsinfo(code, name, price, maker) values ('p0004', '오라클', 55000, '생능출판');

    insert into goodsinfo(code, name, price, maker) values ('p0005', 'Ajax', 35000, '엔코아');
    insert into goodsinfo(code, name, price, maker) values ('p0006', 'JSP', 45000, '멀캠');

    select * from goodsinfo;
    ~~~

## src/test01
- applicationContext.xml

    ~~~ xml
    <?xml version="1.0" encoding="UTF-8"?>
    <beans xmlns="http://www.springframework.org/schema/beans"
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xmlns:c="http://www.springframework.org/schema/c"
        xmlns:p="http://www.springframework.org/schema/p"
        xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

        <!-- 데이터 소스 설정: DriverManagerDataSource클래스 이용 -->
        <bean id="dataSource" class="org.springframework.jdbc.datasource.DriverManagerDataSource">

            <property name="driverClassName" value="oracle.jdbc.driver.OracleDriver"></property>
            <property name="url" value="jdbc:oracle:thin:@localhost:1521:XE"></property>
            <property name="username" value="big5"></property>
            <property name="password" value="admin1234"></property>
        </bean>
        <bean id="test" class="test01.FirstJdbcDaoImple">
            <property name="dataSource" ref="dataSource"/>
        </bean>
    </beans>
    ~~~

- FirstJdbcDao.java

    ~~~ java
    package test01;

    public interface FirstJdbcDao{
        public int insert(String code, String name, int price, String maker);
        public void execute(String sql); //select
        public int delete(String name); // delete
    }
    ~~~

- FirstJdbcDaoImple.java

    ~~~ java
    package test01;

    import java.sql.ResultSet;
    import java.sql.SQLException;

    import org.springframework.jdbc.core.RowMapper;
    import org.springframework.jdbc.core.support.JdbcDaoSupport;

    public class FirstJdbcDaoImple extends JdbcDaoSupport implements FirstJdbcDao {
        //DAOSPT는 sql 데이터소스를 지정해서 core가 가진 데이터를 기반으로 데이터소스를 리턴해준다.

        @Override
        public int insert(String code, String name,
                int price, String maker) {
            return super.getJdbcTemplate().update(
                "insert into goodsinfo(code,name,price,maker) values(?,?,?,?)"
                    , new Object[] {code,name,price,maker});
            //beans 연동으로 쓰면 이 연동을 잊기 쉬움. 배열로 하나의 매개인자로 치고 들어가서 뿌리는 셈
        }

        @Override
        public void execute(String sql) {
            System.out.println("교재코드 교재명 가격 출판사");
            System.out.println("============================");
            super.getJdbcTemplate().query(sql, new GoodsRowMapper<Object>());
        }//void end

        //중첩클래스(inner class : 클래스 안에서 다른 클래스를 만듬), 사용자 정의 클래스
        public class GoodsRowMapper<T> implements RowMapper<T>{
            @Override
            public T mapRow(ResultSet rs, int rowNum) throws SQLException{
                String code=rs.getString("CODE");
                String name=rs.getString("NAME");
                int price=rs.getInt("PRICE");
                String maker=rs.getString("MAKER");
                System.out.printf("%s  %s  %d  %s %d\n",
                        code, name, price, maker, rs.getRow());
                return null;
                //select가 5개면 얘도 5바퀴 자동. next가 자동으로 집행된다.
            }//end mapRow
        }//

        @Override
        public int delete(String name) {
            return super.getJdbcTemplate().
                update("delete from goodsinfo where name=?",
                    new Object[]{name});
        }
        //getConnection같은 뻘짓 안해도 된다.
        //connection open 그딴거 안쓴다. 그러니 close도 없다.
    }
    ~~~

- JdbcExample1.java

    ~~~ java
    package test01;

    import org.springframework.context.ApplicationContext;
    import org.springframework.context.support.ClassPathXmlApplicationContext;

    public class JdbcExample1 {
       public static void main(String[] args) {
          ApplicationContext factory
          = new ClassPathXmlApplicationContext("test01/applicationContext.xml");

          FirstJdbcDao dao = (FirstJdbcDao)factory.getBean("test");
          int count = dao.insert("p0007", "스프링", 45000, "엔코아");

          System.out.println(count + "행의 데이터가 입력되었습니다.");

          ((ClassPathXmlApplicationContext)factory).close();
       }
    }
    ~~~

- JdbcExample2.java

    ~~~ java
    package test01;

    import org.springframework.context.ApplicationContext;
    import org.springframework.context.support.ClassPathXmlApplicationContext;

    public class JdbcExample2 {
        public static void main(String[] args) {
            ApplicationContext factory = new ClassPathXmlApplicationContext("test01/applicationContext.xml");

            FirstJdbcDao dao = (FirstJdbcDao) factory.getBean("test");
            dao.execute("select * from goodsinfo");

            ((ClassPathXmlApplicationContext) factory).close();
        }
    }
    ~~~

- JdbcExample3.java

    ~~~ java
    package test01;

    import org.springframework.context.ApplicationContext;
    import org.springframework.context.support.ClassPathXmlApplicationContext;

    //추가하기(insert)
    public class JdbcExample3 {
        public static void main(String[] args) {
            ApplicationContext factory
            =new ClassPathXmlApplicationContext("test01/applicationContext.xml");

            FirstJdbcDao dao = (FirstJdbcDao)factory.getBean("test");
            int count = dao.delete(args[0]);//이름으로 삭제
            System.out.println(args[0]+"교재가"+count+"개 삭제되었습니다");

            ((ClassPathXmlApplicationContext)factory).close();
        }
    }
    ~~~

***

## src/test02
- jdbc.properties

    ~~~ properties
    jdbc.driverClassName = oracle.jdbc.driver.OracleDriver
    jdbc.url = jdbc:oracle:thin:@127.0.0.1:1521:xe
    jdbc.username = big5
    jdbc.password = admin1234
    ~~~

- applicationContext.xml

    ~~~ xml
    <?xml version="1.0" encoding="UTF-8"?>
    <beans xmlns="http://www.springframework.org/schema/beans"
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xmlns:c="http://www.springframework.org/schema/c"
        xmlns:p="http://www.springframework.org/schema/p"
        xmlns:context="http://www.springframework.org/schema/context"
        xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
            http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-4.3.xsd">

        <!-- test01보다 test02가 더 많이 쓴다. 이게 정석이다. 오라클/mysql일 수도 있기에 -->
        <context:property-placeholder
            location="classpath:test02/jdbc.properties" />

        <!-- 데이터소스설정 : DriverManagerDatasource클래스 이용 -->
        <bean id="dataSource"
            class="org.springframework.jdbc.datasource.DriverManagerDataSource"
            c:url="${jdbc.url}" c:username="${jdbc.username}"
            c:password="${jdbc.password}">
            <property name="driverClassName"
                value="${jdbc.driverClassName}" />
        </bean>

        <bean id="test" class="test02.FirstJdbcDaoImple">
            <property name="dataSource" ref="dataSource"></property>
        </bean>
    </beans>
    ~~~

- GoodsEntity.java

    ~~~ java
    package test02;

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

- FirstJdbcDao.java

    ~~~ java
    package test02;

    import java.util.List;
    import java.util.Map;

    public interface FirstJdbcDao {
        public List<GoodsEntity> listGoods();
        public List<Map<String, Object>> listGoods02();
        public GoodsEntity findGoods(String code);
        public Map<String, Object> findGoods02(String code);
        public int updateGoods(String name, int price, String maker, String code);
    }
    ~~~

- FirstJdbcDaoImple.java

    ~~~ java
    package test02;

    import java.sql.ResultSet;
    import java.sql.SQLException;
    import java.util.List;
    import java.util.Map;

    import org.springframework.jdbc.core.RowMapper;
    import org.springframework.jdbc.core.support.JdbcDaoSupport;

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
    }
    ~~~

- JdbcExample4.java

    ~~~ java
    package test02;

    import java.util.List;
    import java.util.Map;

    import org.springframework.context.ApplicationContext;
    import org.springframework.context.support.ClassPathXmlApplicationContext;

    //전체출력
    public class JdbcExample4 {
        public static void main(String[] args) {
            ApplicationContext factory = new ClassPathXmlApplicationContext("test02/applicationContext.xml");
            FirstJdbcDao dao = (FirstJdbcDao) factory.getBean("test");
            List<GoodsEntity> list = dao.listGoods();

            for (GoodsEntity entity : list) {
                System.out.print("코드 : " + entity.getCode() + "\t");
                System.out.print("책이름 : " + entity.getName() + "\t");
                System.out.print("가격 : " + entity.getPrice() + "\t");
                System.out.print("출판사 : " + entity.getMaker() + "\n");
            }
            System.out.println("==========================");

            List<Map<String, Object>> all = dao.listGoods02();
            for (Map<String, Object> res : all) {// sec 19.2.5
                System.out.println(res);
            }
            // 빅데이터 할 때는 이게 수월하다. DAOIMPL에서 select 문장해서
            // list-map으로 하는 것
            ((ClassPathXmlApplicationContext) factory).close();
        }
    }
    ~~~

- JdbcExample5.java

    ~~~ java
    package test02;

    import java.util.Map;

    import org.springframework.context.ApplicationContext;
    import org.springframework.context.support.ClassPathXmlApplicationContext;

    //전체출력
    public class JdbcExample5 {
        public static void main(String[] args) {
            ApplicationContext factory
            = new ClassPathXmlApplicationContext("test02/applicationContext.xml");
            FirstJdbcDao dao=(FirstJdbcDao)factory.getBean("test");
            /*GoodsEntity entity = dao.findGoods(args[0]);
            System.out.println(entity.getCode()+"   "
                              +entity.getName()+"   "
                              +entity.getPrice()+"   "
                              +entity.getMaker());*/

            System.out.println("==================");
            Map<String, Object> res2 = dao.findGoods02("p0003");
            System.out.println(res2);
            ((ClassPathXmlApplicationContext)factory).close();
        }
    }
    ~~~

- JdbcExample6.java

    ~~~ java
    package test02;

    import org.springframework.context.ApplicationContext;
    import org.springframework.context.support.ClassPathXmlApplicationContext;

    //전체출력
    public class JdbcExample6 {
        public static void main(String[] args) {
            ApplicationContext factory = new ClassPathXmlApplicationContext("test02/applicationContext.xml");
            FirstJdbcDao dao = (FirstJdbcDao) factory.getBean("test");
            int n = dao.updateGoods("jQuery", 35000, "인포믹스", "p0001");
            if (n > 0) {
                System.out.println("상품 " + n + "개를 수정하였습니다.");
            }
            ((ClassPathXmlApplicationContext) factory).close();
        }
    }
    ~~~

***

## src/test03
- jdbc.properties

    ~~~ properties
    driver = oracle.jdbc.driver.OracleDriver
    url = jdbc:oracle:thin:@127.0.0.1:1521:xe
    username = big5
    password = admin1234
    ~~~

- mybatis-config.xml

    ~~~ xml
    <?xml version="1.0" encoding="UTF-8" ?>
    <!DOCTYPE configuration
      PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
      "http://mybatis.org/dtd/mybatis-3-config.dtd">
    <configuration>
        <!-- 데이터베이스 접속 -->
        <properties resource="test03/jdbc.properties" />
        <environments default="development">
            <environment id="development">
                <transactionManager type="JDBC" /> <!-- JDBC : 커밋과 롤백 처리 수동 / MANAGED :자동커밋 -->
                <dataSource type="POOLED"> <!-- UNPOOLED : 매번 커넥션을 열고 닫음, POOLED : 한번만 열고 닫음, JNDI -->
                    <property name="driver" value="${driver}" />
                    <property name="url" value="${url}" />
                    <property name="username" value="${username}" />
                    <property name="password" value="${password}" />
                </dataSource>
            </environment>
        </environments>
        <!-- SQL 쿼리문 -->
        <mappers>
            <mapper resource="test03/goods-mapping.xml" />
        </mappers>
    </configuration>
    ~~~

- GoodsEntity.java

    ~~~ java
    package test03;

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

- beans.xml

    ~~~ xml
    <?xml version="1.0" encoding="UTF-8"?>
    <beans xmlns="http://www.springframework.org/schema/beans"
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xmlns:p="http://www.springframework.org/schema/p"
        xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

        <bean id="entity1" class="test03.GoodsEntity" p:code="p0009"
            p:name="Ajax" p:price="15000" p:maker="구민" />

        <bean id="entity2" class="test03.GoodsEntity" p:code="p0001"
            p:name="제이" p:price="38000" p:maker="인포" />
    </beans>
    ~~~

- goods-mapping.xml

    ~~~ xml
    <?xml version="1.0" encoding="UTF-8" ?>
    <!DOCTYPE mapper
      PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
      "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
    <mapper namespace="mybatis.goodsMapper">

        <!-- MTest.java -->
        <select id="listGoods" resultType="test03.GoodsEntity">
            SELECT * FROM GOODSINFO
        </select>

        <!-- MTest01.java -->
        <insert id="insertGoods" parameterType="test03.GoodsEntity">
            INSERT INTO
            GOODSINFO(CODE, NAME, PRICE, MAKER)
            VALUES(#{code}, #{name}, #{price},
            #{maker})
        </insert>

        <!-- MTest02.java -->
        <select id="selectGoodsByCPM" parameterType="hashmap"
            resultType="test03.GoodsEntity">
            SELECT * FROM GOODSINFO WHERE CODE LIKE #{code} AND PRICE >
            #{price} AND MAKER = #{maker}
        </select>

        <!-- MTest03.java -->
        <update id="updateGoods" parameterType="test03.GoodsEntity">
            UPDATE GOODSINFO SET
            NAME=#{name},
            PRICE=#{price},
            MAKER=#{maker}
            WHERE CODE=#{code}
        </update>

        <delete id="deleteGoods" parameterType="java.lang.String">
            DELETE FROM GOODSINFO
            WHERE NAME = #{name}
        </delete>

        <select id="findsGoods" parameterType="java.lang.String"
            resultType="test03.GoodsEntity">
            SELECT * FROM GOODSINFO WHERE CODE=#{code}
        </select>
    </mapper>
    ~~~

- SqlMapClientFactory.java

    ~~~ java
    package test03;

    import java.io.IOException;
    import java.io.Reader;

    import org.apache.ibatis.io.Resources;
    import org.apache.ibatis.session.SqlSessionFactory;
    import org.apache.ibatis.session.SqlSessionFactoryBuilder;

    // 매퍼를 포함한 환경설정 파일을 로드하는 코드 작성한 후 객체를 리턴
    public class SqlMapClientFactory {
        private static SqlSessionFactory factory = null;
        static {
            try {
                String resource = "test03/mybatis-config.xml";
                Reader reader = Resources.getResourceAsReader(resource);
                factory = new SqlSessionFactoryBuilder().build(reader);
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
        // ------------------------------------------------

        public static SqlSessionFactory getSqlMapClientInstance() {

            return factory;
        }
    }
    ~~~

***

- MTest.java

    ~~~ java
    package test03;

    import java.util.List;

    import org.apache.ibatis.session.SqlSession;

    public class MTest {
        public static void main(String[] args) {
            SqlSession session = SqlMapClientFactory.getSqlMapClientInstance().openSession();

            List<GoodsEntity> goodsList = session.selectList("mybatis.goodsMapper.listGoods");
            // 네임스페이스명.id명

            System.out.println("  상품코드   \t 상품명 \t\t 가격 \t\t 제조사");
            System.out.println("============================================================");
            for (GoodsEntity entity : goodsList) {
                System.out.print("코드 : " + entity.getCode() + "\t");
                System.out.print("책이름 : " + entity.getName() + "\t");
                System.out.print("가격 : " + entity.getPrice() + "\t");
                System.out.print("출판사 : " + entity.getMaker() + "\n");
            }
            session.close();
        }
    }
    ~~~

- MTest01.java

    ~~~ java
    package test03;

    import org.apache.ibatis.session.SqlSession;
    import org.springframework.context.ApplicationContext;
    import org.springframework.context.support.ClassPathXmlApplicationContext;

    public class MTest01 {
        public static void main(String[] args) {
            ApplicationContext factory = new ClassPathXmlApplicationContext("test03/beans.xml");

            SqlSession session = SqlMapClientFactory.getSqlMapClientInstance().openSession();

            GoodsEntity entity = factory.getBean("entity1", GoodsEntity.class);

            int count = session.insert("mybatis.goodsMapper.insertGoods", entity);
            if (count > 0) {
                session.commit();
                System.out.println(count + " 행의 데이터가 입력되었습니다.");
            } else {
                session.rollback();
            }

            session.close();
            ((ClassPathXmlApplicationContext) factory).close();
        }
    }
    ~~~

- MTest02.java

    ~~~ java
    package test03;

    import java.util.HashMap;
    import java.util.List;
    import java.util.Map;

    import org.apache.ibatis.session.SqlSession;

    // 복수 조건으로 검색
    // [문제] 코드에 "p0" 가 포함되어 있으며,
    // 금액이 25,000원을 초과하고, 출판사가 "엔코아"인 상품을 검색하시오

    public class MTest02 {
        public static void main(String[] args) {
            Map<String, Object> map = new HashMap<>();
            map.put("code", "%" + "p0" + "%");
            map.put("price", 25000);
            map.put("maker", "엔코아");

            SqlSession session = SqlMapClientFactory.getSqlMapClientInstance().openSession();

            List<GoodsEntity> goodsList = session.selectList("mybatis.goodsMapper.selectGoodsByCPM", map);

            System.out.println("  상품코드   \t 상품명 \t\t 가격 \t\t 제조사");
            System.out.println("============================================================");
            for (GoodsEntity entity : goodsList) {
                System.out.print("코드 : " + entity.getCode() + "\t");
                System.out.print("책이름 : " + entity.getName() + "\t");
                System.out.print("가격 : " + entity.getPrice() + "\t");
                System.out.print("출판사 : " + entity.getMaker() + "\n");
            }
            session.close();
        }
    }
    ~~~

- MTest03.java

    ~~~ java
    package test03;

    import org.apache.ibatis.session.SqlSession;
    import org.springframework.context.ApplicationContext;
    import org.springframework.context.support.ClassPathXmlApplicationContext;

    // [문제] p0001를 제이, 38000, 인포믹으로 변경하시오
    // [결과] 상품 1개를 수정하였습니다.

    public class MTest03 {
        public static void main(String[] args) {
            ApplicationContext factory = new ClassPathXmlApplicationContext("test03/beans.xml");

            GoodsEntity entity = factory.getBean("entity2", GoodsEntity.class);

            SqlSession session = SqlMapClientFactory.getSqlMapClientInstance().openSession();

            int count = session.update("mybatis.goodsMapper.updateGoods", entity);

            if (count > 0) {
                session.commit();
                System.out.println("상품 " + count + " 개를 수정하였습니다.");
            } else {
                session.rollback();
            }
            session.close();
        }
    }
    ~~~

***

## src/test04
- jdbc.properties

    ~~~ properties
    driver = oracle.jdbc.driver.OracleDriver
    url = jdbc:oracle:thin:@127.0.0.1:1521:xe
    username = big5
    password = admin1234
    ~~~

- mybatis-config.xml

    ~~~ xml
    <?xml version="1.0" encoding="UTF-8" ?>
    <!DOCTYPE configuration
      PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
      "http://mybatis.org/dtd/mybatis-3-config.dtd">
    <configuration>
        <!-- 데이터베이스 접속 -->
        <properties resource="test04/jdbc.properties" />
        <environments default="development">
            <environment id="development">
                <transactionManager type="JDBC" /> <!-- JDBC : 커밋과 롤백 처리 수동 / MANAGED :자동커밋 -->
                <dataSource type="POOLED"> <!-- UNPOOLED : 매번 커넥션을 열고 닫음, POOLED : 한번만 열고 닫음, JNDI -->
                    <property name="driver" value="${driver}" />
                    <property name="url" value="${url}" />
                    <property name="username" value="${username}" />
                    <property name="password" value="${password}" />
                </dataSource>
            </environment>
        </environments>
        <!-- SQL 쿼리문 -->
        <mappers>
            <mapper class="test04.GoodsMapper" />
        </mappers>
    </configuration>
    ~~~

- GoodsEntity.java

    ~~~ java
    package test04;

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

- GoodsMapper.java

    ~~~ java
    package test04;

    import java.util.List;

    import org.apache.ibatis.annotations.*;
    import org.apache.ibatis.jdbc.SQL;

    public interface GoodsMapper {
        @Select("SELECT * FROM GOODSINFO WHERE CODE = #{code}")
        GoodsEntity selectGoods(String code);

        @Select("SELECT * FROM GOODSINFO")
        List<GoodsEntity> listGoods();

        @Update("UPDATE GOODSINFO SET NAME=#{name},PRICE=#{price},MAKER=#{maker} WHERE CODE=#{code}")
        int updateGoods(GoodsEntity goods);

        @Delete("DELETE FROM GOODSINFO WHERE NAME = #{name}")
        int deleteGoods(String name);

        @Insert("INSERT INTO GOODSINFO(CODE, NAME, PRICE, MAKER) " + "VALUES(#{code}, #{name}, #{price}, #{maker})")
        int insertGoods(GoodsEntity goods);

        @SelectProvider(type = GoodsSelect.class, method = "select")
        List<GoodsEntity> listGoods02();

        class GoodsSelect {
            public static String select() {
                return new SQL() {
                    {
                        SELECT("*");
                        FROM("GOODSINFO");
                        ORDER_BY("name"); // 책이름으로 정렬
                    }
                }.toString();
            }
        }
    }
    ~~~

- SqlMapClientFactory.java

    ~~~ java
    package test04;

    import java.io.IOException;
    import java.io.Reader;
    import java.util.List;

    import org.apache.ibatis.io.Resources;
    import org.apache.ibatis.session.SqlSession;
    import org.apache.ibatis.session.SqlSessionFactory;
    import org.apache.ibatis.session.SqlSessionFactoryBuilder;

    // 매퍼를 포함한 환경설정 파일을 로드하는 코드 작성한 후 객체를 리턴
    // DAO CRUD 메소드 구현
    public class SqlMapClientFactory {
        private static SqlSessionFactory factory = null;
        static {
            try {
                String resource = "test04/mybatis-config.xml";
                Reader reader = Resources.getResourceAsReader(resource);
                factory = new SqlSessionFactoryBuilder().build(reader);
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
        // ------------------------------------------------

        public GoodsEntity FindGoods(String code) {
            try (SqlSession session = factory.openSession()) {
                GoodsMapper mapper = session.getMapper(GoodsMapper.class);
                return mapper.selectGoods(code);
            }
        }

        public List<GoodsEntity> SelectAll() {
            try (SqlSession session = factory.openSession()) {
                GoodsMapper mapper = session.getMapper(GoodsMapper.class);
                return mapper.listGoods();
            }
        }

        // providerAnnotation 으로 selectAll 해보기
        public List<GoodsEntity> SelectAll02() {
            try (SqlSession session = factory.openSession()) {
                GoodsMapper mapper = session.getMapper(GoodsMapper.class);
                return mapper.listGoods02();
            }
        }

        public int UpdateGoods(GoodsEntity entity) {
            try (SqlSession session = factory.openSession()) {
                GoodsMapper mapper = session.getMapper(GoodsMapper.class);
                int res = mapper.updateGoods(entity);
                if (res > 0) {
                    session.commit();
                    System.out.println("update 완료");
                } else {
                    session.rollback();
                    System.out.println("update 실패");
                }
                return res;
            }
        }

        public int DeleteGoods(String name) {
            try (SqlSession session = factory.openSession()) {
                GoodsMapper mapper = session.getMapper(GoodsMapper.class);
                int res = mapper.deleteGoods(name);
                if (res > 0) {
                    session.commit();
                    System.out.println("delete 완료");
                } else {
                    session.rollback();
                    System.out.println("delete 실패");
                }
                return res;
            }
        }

        public int InsertGoods(GoodsEntity entity) {
            try (SqlSession session = factory.openSession()) {
                GoodsMapper mapper = session.getMapper(GoodsMapper.class);
                int res = mapper.insertGoods(entity);
                if (res > 0) {
                    session.commit();
                    System.out.println("insert 완료");
                } else {
                    session.rollback();
                    System.out.println("insert 실패");
                }
                return res;
            }
        }
    }
    ~~~

***

- MTest.java

    ~~~ java
    package test04;

    // code가 "p0001"인 상품 출력
    public class MTest {
        public static void main(String[] args) {
            GoodsEntity entity = new SqlMapClientFactory().FindGoods("p0001");

            System.out.println("  상품코드   \t 상품명 \t\t 가격 \t\t 제조사");
            System.out.println("============================================================");

            System.out.print("코드 : " + entity.getCode() + "\t");
            System.out.print("책이름 : " + entity.getName() + "\t");
            System.out.print("가격 : " + entity.getPrice() + "\t");
            System.out.print("출판사 : " + entity.getMaker() + "\n");
        }
    }
    ~~~

- MTest02.java

    ~~~ java
    package test04;

    import java.util.List;

    // select all
    public class MTest02 {
        public static void main(String[] args) {
            List<GoodsEntity> list = new SqlMapClientFactory().SelectAll();

            System.out.println("  상품코드   \t 상품명 \t\t 가격 \t\t 제조사");
            System.out.println("============================================================");
            for (GoodsEntity entity : list) {
                System.out.print("코드 : " + entity.getCode() + "\t");
                System.out.print("책이름 : " + entity.getName() + "\t");
                System.out.print("가격 : " + entity.getPrice() + "\t");
                System.out.print("출판사 : " + entity.getMaker() + "\n");
            }
        }
    }
    ~~~

- MTest02_provider_ver.java

    ~~~ java
    package test04;

    import java.util.List;

    // select all
    //SelectAll1() providerAnnotation ver
    public class MTest02_provider_ver {
        public static void main(String[] args) {
            List<GoodsEntity> list = new SqlMapClientFactory().SelectAll02();

            System.out.println("  상품코드   \t 상품명 \t\t 가격 \t\t 제조사");
            System.out.println("============================================================");
            for (GoodsEntity entity : list) {
                System.out.print("코드 : " + entity.getCode() + "\t");
                System.out.print("책이름 : " + entity.getName() + "\t");
                System.out.print("가격 : " + entity.getPrice() + "\t");
                System.out.print("출판사 : " + entity.getMaker() + "\n");
            }
        }
    }
    ~~~

- MTest03.java

    ~~~ java
    package test04;

    // update
    public class MTest03 {
        public static void main(String[] args) {
            GoodsEntity entity = new GoodsEntity();
            entity.setCode("p0007"); // code가 p0007인 상품을
            entity.setName("C++"); // 로 바꿔라
            entity.setPrice(18000); // 로 바꿔라
            entity.setMaker("LG"); // 로 바꿔라

            new SqlMapClientFactory().UpdateGoods(entity);
        }
    }
    ~~~

- MTest04.java

    ~~~ java
    package test04;

    // delete
    public class MTest04 {
        public static void main(String[] args) {
            // 상품이름이 "Spring"인 상품을 삭제하라
            new SqlMapClientFactory().DeleteGoods("Spring");
        }
    }
    ~~~

- MTest05.java

    ~~~ java
    package test04;

    // insert
    public class MTest05 {
        public static void main(String[] args) {
            GoodsEntity entity = new GoodsEntity();
            entity.setCode("p0010");
            entity.setName("Spring");
            entity.setPrice(15000);
            entity.setMaker("en");

            new SqlMapClientFactory().InsertGoods(entity);
        }
    }
    ~~~




















