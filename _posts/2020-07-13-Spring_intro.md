---



title:  "Spring Intro"



layout: post



---


## [오늘 할 내용]
1. 이클립스에 Spring 환경 구축하기
2. Spring이란?
3. SpringDI
	- sample1 : DI 개념. is-a 관계를 이용한 구도 학습
	- sample2 : has-a 관계를 이용하여 DI를 이용할 수 있음
	- sample3 : setter, constructor injection의 이해도를 구현한다.
	- sample4 : 추상클래스의 패턴과 싱글톤 패턴을 이해하고 구현한다.
		- 싱글톤 패턴(Singleton Pattern)
			- static 메소드와 private 생성자를 만들어서 단 하나의 인스턴스만 연동할 수 있도록 구현하는 패턴(java에서 카렌다 클래스, 그래픽 클래스 등이 이에 속함)
			~~~ java
            class Test{
            	private Test(){}
                public static Test getInc(){return new Test();}
            }
            ~~~
            - Test t1 = Test.getInc();  ... 싱글톤
        - 싱글톤 레지스토리
        	- 스프링에서 직접 싱글톤 형태의 오브젝트를 만들고 관리하는 기능을 제공하는 것을 말함
        	- 스프링 빈 오브젝트는 내부적으로 싱글톤 레지스트리를 만들어서 연동함

***

## 스프링 환경세팅(이클립스)
- Help > Eclipse Marketplace... 로 들어가기
- 아래 그림처럼 sts검색 후 두개 설치하기

	![image](https://user-images.githubusercontent.com/52989294/87260855-d4393180-c4ee-11ea-98cd-439f721112d9.png)

***

## 스프링 프로젝트 생성하기
- java 프로젝트로 생성하기
- 프로젝트 우클릭(그림참조)

	![image](https://user-images.githubusercontent.com/52989294/87260766-5b39da00-c4ee-11ea-9aa7-c1fde2c2698b.png)

***

### pom.xml 수정하기
- <https://mvnrepository.com/search?q=spring> 에서 <**dependency**> 복사해서 붙이기

    ~~~ xml
    <project xmlns="http://maven.apache.org/POM/4.0.0"
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
        <modelVersion>4.0.0</modelVersion>
        <groupId>SpringDI_0713</groupId>
        <artifactId>SpringDI_0713</artifactId>
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
                <version>4.3.27.RELEASE</version>
            </dependency>

            <!-- https://mvnrepository.com/artifact/org.springframework/spring-core -->
            <dependency>
                <groupId>org.springframework</groupId>
                <artifactId>spring-core</artifactId>
                <version>4.3.27.RELEASE</version>
            </dependency>

            <!-- https://mvnrepository.com/artifact/org.springframework/spring-beans -->
            <dependency>
                <groupId>org.springframework</groupId>
                <artifactId>spring-beans</artifactId>
                <version>4.3.27.RELEASE</version>
            </dependency>
        </dependencies>
    </project>
    ~~~

***

## Spring이란?
- 오픈 소스 프레임워크
	- 엔터프라이즈 어플리케이션 개발의 복잡성을 줄여주기 위한 목적
	- EJB 사용으로 수행되었던 모든 기능을 일반 POJO(Plain Old Java Object)를 사용해서 가능하게 함
		- 경량 컨테이너(light weight container)
- 주요 개념
	- 의존성 주입(lightweight dependency injection)
	- 관점 지향 컨테이너(aspect-oriented container)

***

### POJO란?
- Plain Old Java Object 약자
- Plain : component interface를 상속받지 않는 특징(특정 프레임워크에 종속되지 않는)
- Old : EJB 이전의 자바 클래스를 의미

***

### Spring 장점
- 경량 컨테이너 - 객체의 라이프 사이클 관리, J2EE 구현을 위한 다양한 API 제공
- DI(Dependency Injection) 지원
- AOP(Aspect Oriented Programming) 지원
- POJO(Plain Old Java Object) 지원
- JDBC를 위한 다양한 API 지원
- Transaction 처리를 위한 일괄된 방법 제공
- 다양한 API와의 연동 지원

***

### 스프링 프레임워크의 취지
- 기술 자체보다 좋은 설계가 중요함
- 인터페이스를 통한 느슨한 결합은 자바 빈의 훌륭한 모델임
	- 느슨한 결합이란?
		- 두 객체가 느슨하게 결합되어 있다는 것이란, 그 둘이 상호작용을 하긴 하지만, 서로에 대해 서로 잘 모른다는 뜻
		- 느슨하게 결합되는 디자인을 사용하면 변경사항이 생겨도 무난히 처리할 수 있는 유연한 객체지향 시스템을 구축 할 수 있다.
- 코드는 테스트하기 쉽고 편해야 함
- EJB의 복잡도에서 벗어나야 함

***

### 의존성 주입(Dependency Injection, DI)
- 의존 관계 주입(dependency injection)
	- 객체 간의 의존관계를 객체 자신이 아닌 외부의 조립기가 수행함
	- 제어의 역행(inversion of control, IoC)이라는 의미로 사용되었음
	- DI를 통해 시스템에 있는 각 객체를 조정하는 외부 개체가 객체들에게 생성시에 의존관계를 주어짐
		- 즉, 의존이 객체로 주입됨
		- 객체가 협업하는 객체의 참조를 어떻게 얻어낼 것인가라는 관점에서 책임성의 역행(inversion of responsibility)임
	- 느슨한 결합(loose coupling)이 주요 강점
		- 객체는 인터페이스에 의한 의존관계만을 알고 있으며, 이 의존관계는 구현 클래스에 대한 차이를 모르는채 서로 다른 구현으로 대체가 가능

***

### 스프링 프레임워크와 DI(Dependency Injection)
- 의존성이 높은 결합
	- 자신이 사용할 DAO 객체를 직접 new 라는 키워드를 사용하여 생성하여 사용할 경우 의존성이 높음
	- 프로젝트 스펙이 바뀌거나 다른 DAO를 참조해야 할 때 코드의 수정이 불가피함
	- ex)

    ~~~ java
    class UserServiceImpl{
        UserDaoJDBCImpl dao;
        public void register(User user){ }
        public User findUser(String name){
        dao = new UserDaoJDBCImpl ( );
        User r = dao.select(name);
        return r;
        }
    }
    ~~~

    ~~~ java
    class UserDaoJDBCImpl{
        public User select(String name){
            // 이름을 받아서 "select * from user where name = ?"
        }
    }
    ~~~

    ~~~ java
    User res = new UserServiceImpl().findUser("홍길동");
    ~~~

- 인터페이스를 사용함
	- 인터페이스를 사용하여 의존성은 조금 낮아졌으나 여전히 의존성이 높음

- Factory 패턴을 이용하여 의존성을 낮춤
	- DAO와 의존성은 낮아졌으나 Factory 클래스와의 의존성은 여전히 높음

- 조립기(Assembler)를 통해 의존성 주입
	- 객체 외부의 조립기가 각 객체의 의존관계를 설정해 줌
	- 이를 DI패턴이라 함

***

### 스프링 프레임워크의 DI 방법
- 특정 interface를 통한 의존성 설정
	- 거의 사용하지 X

- Setter를 통한 의존성 설정
	- 클래스
	- xml

- Constructor를 통한 의존성 설정

- IoC(Inversion of Control)란?
	- 제어 역행화는 말 그대로 제어가 일반적인 흐름을 따르지 않고, 반대로 흘러간다는 뜻으로 각각의 프로그램이 가지고 있던 구현 객체에 대한 정보가 이젠 프레임워크에서 관리되는 것임.
	- 현재는 DI라는 용어를 더 많이 사용함

- 의존성이란?
	- 보통 비즈니스 로직을 수행하기 위해 둘 이상의 클래스를 사용한다.
	- 전통적으로 각 객체는 협업할 객체의 참조를 취득해야 하는 책임이 있다.
	- 이것이 의존성이다. 객체 간의 결합도가 높으면 테스트하기 어려운 코드를 만들어 낸다.

***

### 스프링 프레임워크 컨테이너의 종류
- BeanFactory
	- org.springframework.beans.factory.BeanFactory
	- 기본적인 의존성 주입을 지원하는 가장 간단한 형태의 컨테이너
	- AOP를 지원하지 않음

- ApplicationContext
	- org.springframework.context.ApplicationContext
	- 프로퍼티 파일의 메시지 해석, 이벤트 발행들의 서비스 제공

- WebApplicationContext
	- org.springframework.web.context.WebApplicationContext
	- 웹 어플리케이션을 위한 ApplicationContext

- BeanFactory와 ApplicationContext의 차이
	- BeanFactory는 getBean( ) 메소드가 호출될 때까지 빈의 생성을 미룬다.
	- 즉, BeanFactory는 모든 빈을 LazyLoading 한다.
	- ApplicationContext는 시작할 때 모든 싱글턴 빈을 미리 로딩한다.
	- 따라서, 빈이 필요할 때 즉시 사용될 수 있도록 보장해준다.(여러 개의 객체를 만들 수 있게 설정 가능함)

- Lazy Loading이란?
	- 객체를 실제로 사용할 때 initialize와 loading하자는 것이다.
	- 그 객체를 실제로 사용할 때까지 최대한 initialize와 loading을 늦춰서 memory운용의 효율성을 꾀하는 방법임

***

### BeanFactory
- 가장 단순한 컨테이너
- 다양한 유형의 빈을 생성하고 분배하는 책임을 짐
- 클래스를 객체화 할 때 협업하는 객체 간의 연관관계를 생성함
- 스프링에서는 다양한 BeanFactory 구현 클래스가 있음
- 사진있음 내용 추가 할것!

***

### 


***
## src/sample1
- MessageBean.java

~~~ java
package sample1;

public interface MessageBean {
	public void sayHello();
}

~~~

- MessageBeanImpl.java

~~~ java
package sample1;

public class MessageBeanImpl implements MessageBean {
	private String fruit;
	private int cost;

	public MessageBeanImpl() {
		super();
		this.fruit = "strawberry";
		this.cost = 5000;
	}
	public MessageBeanImpl(String fruit, int cost) {
		super();
		this.fruit = fruit;
		this.cost = cost;
	}
	@Override
	public void sayHello() {
		System.out.println(fruit + "\t" + cost);
	}
}
~~~

- MTest.java

~~~ java
package sample1;

public class MTest {
	public static void main(String[] args) {
		// java 형식의 코드
		// 객체를 생성한 다음 생성된 객체의 주소로 메소드를 호출하는 방식
		// 1. 기본 생성자 객체 생성
		MessageBeanImpl mi = new MessageBeanImpl();
		mi.sayHello();

		// 2. 매개 인자 전달 생성자로 객체 생성
		MessageBeanImpl mi02 = new MessageBeanImpl("melon", 7000);
		mi02.sayHello();
	}
}
~~~

***

- 스프링은 xml에 먼저 선언해줘야함
- 아래 사진 참조(MyBean.xml 생성)

	![image](https://user-images.githubusercontent.com/52989294/87262273-46f8db80-c4f4-11ea-911d-c335d51a305c.png)

- beans, context 선언

	![image](https://user-images.githubusercontent.com/52989294/87262330-8b847700-c4f4-11ea-8b5b-bdf6e34e2b5b.png)

***

- MyBean.xml

~~~ xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns:context="http://www.springframework.org/schema/context"
	xsi:schemaLocation="http://www.springframework.org/schema/beans https://www.springframework.org/schema/beans/spring-beans-4.3.xsd
		http://www.springframework.org/schema/context https://www.springframework.org/schema/context/spring-context-4.3.xsd">

	<!-- 메인을 먼저 만들지 않아도 메모리에 먼저 올라감 -->
	<!-- 스프링은 빈즈를 메모리에 자동으로 올려줌 생성자가 private라도 상관없음 -->
	<bean id="mi" class="sample1.MessageBeanImpl"></bean>
	<bean id="mi02" class="sample1.MessageBeanImpl">
		<constructor-arg index="0" value="melon"></constructor-arg>
		<constructor-arg index="1" value="7000"></constructor-arg>
	</bean>
</beans>
~~~

- MTest02.java

~~~ java
package sample1;

import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class MTest02 {

	// spring 선언한 Bean을 호출한 다음 메소드 호출
	public static void main(String[] args) {

		// 1. 클래스를 작성
		// 2. MyBean.xml에 id로 bean을 등록
		// 3. ClassPathXmlApplicationContext로 2번을 찾아온다.

		ApplicationContext ac = new ClassPathXmlApplicationContext("sample1/MyBean.xml");

		//방법1) MessageBean bean = (MessageBean) ac.getBean("mi");
		// or
		// 방법2) getBean(String name, Class<T> requiredType)
		MessageBean bean = ac.getBean("mi",MessageBeanImpl.class);
		bean.sayHello();

		bean = (MessageBean) ac.getBean("mi02");
		bean.sayHello();
	}
}
~~~

***

## src/sample2
- Address.java

~~~ java
package sample2;

public class Address {
	private String name;
	private String addr;
	private String tel;

	public Address(String name, String addr, String tel) {
		super();
		this.name = name;
		this.addr = addr;
		this.tel = tel;
	}

	public Address() {
		super();
		// TODO Auto-generated constructor stub
	}

	public String getName() {
		return name;
	}

	public void setName(String name) {
		this.name = name;
	}

	public String getAddr() {
		return addr;
	}

	public void setAddr(String addr) {
		this.addr = addr;
	}

	public String getTel() {
		return tel;
	}

	public void setTel(String tel) {
		this.tel = tel;
	}

	@Override
	public String toString() {
		return "Address [name=" + name + ", addr=" + addr + ", tel=" + tel + "]";
	}
}
~~~

- ListAddress.java

~~~ java
package sample2;

// has a 관계 [생성자, setter]로 값 전달 및 변경
public class ListAddress {
	private Address addr;

	public ListAddress() {
		super();
		// addr = new Address("111", "111", "111"); 결합도 높다.
	}

	public ListAddress(Address addr) {
		super();
		this.addr = addr;
	}

	public Address getAddr() {
		return addr; // addr.toString();
	}

	public void setAddr(Address addr) {
		this.addr = addr;
	}
}
~~~

- applicationContext.xml

~~~ xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

	<!-- 1. 단일 클래스인 Address의 객체를 선언 -->
	<bean id="myaddr01" class="sample2.Address" />

	<!-- 2. 단일 클래스인 Address의 생성자로 값 전달 -->
	<bean id="myaddr02" class="sample2.Address">
		<constructor-arg index="0" value="홍길동" type="String" />
		<constructor-arg index="1" value="서울"
			type="java.lang.String" />
		<constructor-arg index="2" value="02-000" />
	</bean>

	<!-- 3. 단일 클래스인 Address의 setter로 값 전달 -->
	<bean id="myaddr03" class="sample2.Address">
		<property name="name" value="최길동" />
		<property name="addr" value="서초구" />
		<property name="tel" value="02-777-7777" />
	</bean>

	<!-- 4. ListAddress의 기본 생성자로 객체를 선언 -->
	<bean id="mylist01" class="sample2.ListAddress" />

	<!-- 5. ListAddress의 매개인자 생성자로 객체를 선언 -->
	<bean id="mylist02" class="sample2.ListAddress">
		<constructor-arg ref="myaddr02"></constructor-arg>
	</bean>

	<!-- 6. ListAddress의 setter로 Address 값을 전달 후 선언 -->
	<bean id="mylist03" class="sample2.ListAddress">
		<property name="addr" ref="myaddr03"></property>
	</bean>

	<!-- 7. ListAddress의 생성자로 Address 값을 내부 선언 후 전달 -->
	<bean id="mylist04" class="sample2.ListAddress">
		<constructor-arg>
			<bean id="myaddr04" class="sample2.Address">
				<property name="name" value="777" />
				<property name="addr" value="777" />
				<property name="tel" value="777" />
			</bean>
		</constructor-arg>
	</bean>
</beans>
~~~

- MTest.java

~~~ java
package sample2;

import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class MTest {
	public static void main(String[] args) {
		ApplicationContext ac = new ClassPathXmlApplicationContext("sample2/applicationContext.xml");

		Address myaddr01 = ac.getBean("myaddr01", Address.class);
		System.out.println(myaddr01);

		Address myaddr02 = ac.getBean("myaddr02", Address.class);
		System.out.println(myaddr02); // 홍길동, 서울, 02-000

		Address myaddr03 = ac.getBean("myaddr03", Address.class);
		System.out.println(myaddr03);

		ListAddress li = ac.getBean("mylist01", ListAddress.class);
		System.out.println(li.getAddr());

		ListAddress li02 = ac.getBean("mylist02", ListAddress.class);
		System.out.println(li02.getAddr());

		ListAddress li03 = ac.getBean("mylist03", ListAddress.class);
		System.out.println(li03.getAddr());

		ListAddress li04 = ac.getBean("mylist04", ListAddress.class);
		System.out.println(li04.getAddr());
	}
}
~~~

***

## src/sample3
- UserVo.java

- UserServiceImpl.java

- UserService.java

- applicationContext.xml

- MTest.java

## src/sample4
- AbstractTest.java

- Monday.java ~ Sunday.java

- app.xml

- TestApp.java
















