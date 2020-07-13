---



title:  "Spring Intro"



layout: post



---


## [오늘 할 내용]
1. 이클립스에 Spring 환경 구축하기
2. SpringDI
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
		addr = new Address("111", "111", "111");
	}

	public ListAddress(Address addr) {
		super();
		this.addr = addr;
	}

	public Address getAddr() {
		return addr;  // addr.toString();
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

- 





















