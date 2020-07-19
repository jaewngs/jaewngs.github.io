---



title:  "Spring AOP"



layout: post



---


## [오늘 할 내용]
1. SpringAOP
2. sample01 : 자바 코드 작성을 위한 처리 흐름
3. sample02 : sample01을 AOP로 적용
	- AOP(Aspect Oriented Programming)란?
		- OOP의 문제를 개선하기 위해 적용된 것
		- Core concerns : 해당 시스템의 핵심 가치와 고유의 목적이 그대로 들어난 관심 영역
		- Crosscutting Concerns : 로깅, 보안, 트랜잭션 관리 등과 같이 여러 모듈간의 공통적으로 적용되는 공통 관심 영역

    - AOP의 장점 : 기존의 코드를 수정하지 않고도 필요한 공통 기능 모듈 적용 가능
    - AOP의 툴 4가지

		- 자바언어를 확장한 언어를 사용하는 툴 : AspectJ
			- 제록스 연구소 개발 -> Eclipse에 기증 -> 현재 IBM에서 지원 받아 개발중
			- Aspect, PoingCut, Advice를 만들 수 있다.

		- 기존의 자바 언어만으로 AOP 구현 가능 : AspectWerkZ
			- Eclipse 프로젝트(PointCutm, Advice)에서 XML로 정의할 수 있다.

		- 대표적인 인터셉터 체인 방식의 구현 AOP 방식 : SpringAOP, JBossAOP
			- 최근에 등장한 프레임워크, 어노테이션으로 정의할 수 있다.
			- SpringDI패턴에서 동작하는 인터페이스이다.
			- 기존 클래스에서 byte 코드를 수정하지 않고 JDK의 XML에서 지정한 bean의 객체를 통해 제어한다.

	- Advisor = AOP advice (action to take at a joinpoint) + advice(such as a pointcut);
	- PointCut = A pointcut is composed of a ClassFilter and a MethodMatcher.
	- AOP의 주요 구성요소[sec.11]
		1. joinpoint : 프로그램의 제어 흐름 중 한 시점을 의미
			- 메소드 호출시점, 메소드 호출결과가 리턴되는 시점, 예외 던져지는 시점
			- 각각의 joinpoint들을 전후로 Crosscutting Concerns의 기능이 AOP에 의해 자동으로 추가되어 동작되는 후보 지점이다.

		2. pointcut : AOP를 선별하는 것
			- value = "execution(public \* \*(..))" => pointcut를 정의한 것
			- AOP의 전체 코드에서 실행되어지는 메소드 유형을 정의한다.

            - 기본표현식
                - set*(..) : 메소드 명이 set으로 시작하고 0개 이상의 매개인자를 가진 메소드
                - *main(..) : 메소드 명이 main이고 return type이 any type(모든 타입)이고 0개 이상의 매개인자를 가진 메소드

            - 타입 매핑 형식 - java.io.*
                - org.com.test..\* : org.com.test 내의 서브 패키지에 속한 모든 하위요소
                - Number+ : Number 또는 Number 서브타입의 모든 타입
                - !(Number+) : Number 또는 Number 서브타입의 모든 타입이 아닌 것
                - int or Integer : ing형 또는 Integer형
                - org.com.test..* && !Serializable+ : org.com.test 패키지 또는 하위서브 패키지 내에 존재하면서 Serializable의 타입이 아닌 모든 요소

            - 접근 지정에 따른 형식
                - public static void main(..)
                - !private \*  \*  (..) : 리턴 타입이 모든 타입이고, 0개 이상의 매개인자를 가진 메소드 중 접근 제한자가 private가 아닌 메소드
                - \*main(..): 접근 지정자 명시하지 않으면 public 접근 제한자
                - \*main(\*, ..)  : 리턴 타입이 모든 타입이고 최소 1개의 모든 타입을 가진 메소드
                - \*main (\*, .., String,  \*) : 리턴타입이 모든 타입이고 최소 3개의 매개인자를 가지며 끝에서 두번째 반드시 String 타입이어야 한다.

            - 생성자 형식
                - new(..) : 0개 이상의 모든 타입을 가진 생성자
                - Account.new(..) : 0개 이상의 모든 타입을 가진 Account 클래스의 생성자
                - execution 또는 call : 특정 메소드나 생성자 실행 시점을 정의한다.
                    - execution( \*main(..))
                    - call( \*main(..))
                    - get(Collection+ ) : 멤버변수에 선언
                    - set(Collection+ ) : 멤버변수에 선언
                - Exception 처리 : handler(RuntimeException+)
                    - RuntimeException 하위에 있는 Exception을 모두 catch하는 시점
                - within 처리 : 특정 유형의 joinpoint를 정의하는 시점
                - withincode : 특정 메소드 또는 생성자 내에 정의된 코드를 정의하는 시점
                - this : 해당 joinPoint를 정의하는 시점
                - target : 대상 객체의 타입을 정의하는 시점
                    - ex) call (\* \* (..)) && target(MyTest)
                        - MyTest라는 클래스 내의 모든 메소드를 호출

		3. advice : 각 joinpoint에 삽입되어 동작할 수 있는 코드
		4. Weaving : Core concerns랑 Crosscutting Concerns를 엮어서 동작하도록 수행한다는 의미
			- target객체에 aspect를 적용해서 새로운 프록시 객체를 생성하는 절차
		5. aspect : 다수의 클래스에서 공통적으로 구현하는 것으로 모듈화를 말한다.
			- 어디서(pointcut)에서 무엇을 할지(Advice)를 합쳐 놓은 것
			- aspect = pointcut + advice
			- Advice는 각 joinpoint에 삽입되어서 동작할 수 있는 코드이다.
		6. 프록시 : 어드바이스를 target객체에 적용하면 생성되는 객체

4. sample03 : @AspectJ
	 - <https://www.eclipse.org/aspectj/doc/released/runtime-api/index.html>

5. sample04~09

6. sample10(springAOP가 제공하는 lib interface를 활용)
	- ThrowsAdvice : 예외가 발생했을 때 try{ ... } catch{  ...  } 를 따로 구현하지 않고org.springframework.aop.ThrowsAdvice 구현체에서 처리한다.
	- org.springframework.aop.framework.ProxyFactoryBean[AOP의 bean자체를 말함]
		- proxyInterfaces : 문자열 인터페이스 이름의 배열
		- interceptorNames : 적용할 Advisor나 인터셉터, 다른 어드바이스 이름의 문자열 배열
		- singleton : getObject() 메소드를 얼마나 많이 호출했는지 상관없이 팩토리가 하나의 객체를 반환해야 하는지를 결정함

***

## sample01
- Man.java

    ~~~ java
    package sample01;

    public class Man {
        public void classWork() {
            System.out.println("강의장 문을 연다 입실카드 체크");

            try {
                System.out.println("노트북을 켜고 Game을 시작한다.");
            } catch (Exception e) {
                System.out.println("공사 하는날");
            } finally {
                System.out.println("퇴실카드 체크");
            }
            System.out.println("강의장 문을 닫고 간다.");
        }
    }
    ~~~

- Woman.java

    ~~~ java
    package sample01;

    public class Woman {
        public void classWork() {
            System.out.println("강의장 문을 연다 입실카드 체크");

            try {
                System.out.println("노트북을 켜고 거울을 한번보고 공부를 시작한다.");
            } catch (Exception e) {
                System.out.println("공사 하는날");
            } finally {
                System.out.println("퇴실카드 체크");
            }
            System.out.println("강의장 문을 닫고 간다.");
        }
    }
    ~~~

- MTest.java

    ~~~ java
    package sample01;

    public class MTest {
        public static void main(String[] args) {
            Woman w = new Woman();
            Man m = new Man();
            System.out.println("***** 여학생 입실 *****");
            w.classWork();

            System.out.println("====================");
            System.out.println("***** 남학생 입실 *****");
            m.classWork();
        }
    }
    ~~~

***

## sample02
- Person.java

    ~~~ java
    package sample02;

    public interface Person {
        public void classWork();
    }
    ~~~

- Man.java

    ~~~ java
    package sample02;

    public class Man implements Person {
        @Override
        public void classWork() {
            System.out.println("노트북을 켜고 Game을 시작한다.");
        }
    }
    ~~~

- Woman.java

    ~~~ java
    package sample02;

    public class Woman implements Person {
        @Override
        public void classWork() {
            System.out.println("노트북을 켜고 거울을 한번보고 공부를 시작한다.");
        }
    }
    ~~~

- MyAspect.java

    ~~~ java
    package sample02;

    import org.aopalliance.intercept.MethodInterceptor;
    import org.aopalliance.intercept.MethodInvocation;

    public class MyAspect implements MethodInterceptor {
        @Override
        public Object invoke(MethodInvocation invo) throws Throwable {
            Object res = null;
            System.out.println("강의실 문을 연다 입실카드 체크");
            try {
                res = invo.proceed(); // M,W 핵심 코드 주는 곳 classWork();
            } catch (Exception e) {
                System.out.println("공사하는 날");
            } finally {
                System.out.println("퇴실 카드 체크");
            }
            System.out.println("강의실 문을 닫고 간다.");
            return res;
        }
    }
    ~~~

- beans.xml

    ~~~ xml
    <?xml version="1.0" encoding="UTF-8"?>
    <beans xmlns="http://www.springframework.org/schema/beans"
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xmlns:aop="http://www.springframework.org/schema/aop"
        xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
            http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop-4.3.xsd">

        <bean id="woman" class="sample02.Woman" />
        <bean id="man" class="sample02.Man" />

        <!-- Advice :횡단관심사항(공통사항) -->
        <bean id="myAdvice" class="sample02.MyAspect" />

        <bean
            class="org.springframework.aop.framework.autoproxy.DefaultAdvisorAutoProxyCreator" />

        <!-- Aspect = advice(무엇을 할지) + pointcut(어디에서) : 핵심관심사항 -->
        <bean id="myAdvisor"
            class="org.springframework.aop.aspectj.AspectJExpressionPointcutAdvisor">

            <!-- 공통사항, 메소드를 가로채기 한 다음 전체 실행 중에 proceed()로 선별한다. -->
            <property name="advice" ref="myAdvice" />
            <property name="expression" value="execution(public * *(..))" />
        </bean>
    </beans>
    ~~~

- MTest.java

    ~~~ java
    package sample02;

    import org.springframework.context.ApplicationContext;
    import org.springframework.context.support.ClassPathXmlApplicationContext;

    public class MTest {
        public static void main(String[] args) {
            ApplicationContext factory = new ClassPathXmlApplicationContext("sample02/beans.xml");

            Person my = (Person) factory.getBean("woman");
            my.classWork(); // 핵심코드실행
            ((ClassPathXmlApplicationContext) factory).close();
        }
    }
    ~~~

	![image](https://user-images.githubusercontent.com/52989294/87870090-e2e88280-c9df-11ea-936d-e91eeecd6075.png)

***

## sample03
- Person.java

    ~~~ java
    package sample03;

    public interface Person {
        public void classWork();
    }
    ~~~

- Man.java

    ~~~ java
    package sample03;

    public class Man implements Person {
        @Override
        public void classWork() {
            System.out.println("노트북을 켜고 Game을 시작한다.");
        }
    }
    ~~~

- Woman.java

    ~~~ java
    package sample03;

    public class Woman implements Person {

        @Override
        public void classWork() {
            System.out.println("노트북을 켜고 거울을 한번보고 공부를 시작한다.");
        }

    }
    ~~~

- MyAspect.java

    ~~~ java
    package sample03;

    import org.aspectj.lang.JoinPoint;
    import org.aspectj.lang.annotation.After;
    import org.aspectj.lang.annotation.AfterThrowing;
    import org.aspectj.lang.annotation.Aspect;
    import org.aspectj.lang.annotation.Before;

    @Aspect
    public class MyAspect {

        // sample03 패키지 밑에 있는 모든 파일의 이름을 정의한다.
        @Before("execution(public void sample03.*.* (..))")
        public void before(JoinPoint joinpoint) {
            System.out.println("강의실 문을 연다 입실카드 체크");
        }

        @AfterThrowing(pointcut = "execution(public void sample03.*.* (..))", throwing = "e")
        public void throwing(JoinPoint join, Throwable e) {
            System.out.println("공사하는 날");
            System.out.println(e.toString());
        }

        @After("execution(public void sample03.*.* (..))")
        public void after_finally(JoinPoint joinpoint) {
            System.out.println("퇴실카드 체크.");
            System.out.println(joinpoint.getSourceLocation());
            System.out.println(joinpoint.getTarget().getClass());
        }

        @After("execution(public void sample03.*.* (..))")
        public void after(JoinPoint joinpoint) {
            System.out.println("강의장 문을 닫고 나간다.");
        }
    }
    ~~~

- beans.xml

    ~~~ xml
    <?xml version="1.0" encoding="UTF-8"?>
    <beans xmlns="http://www.springframework.org/schema/beans"
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xmlns:aop="http://www.springframework.org/schema/aop"
        xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
            http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop-4.3.xsd">
        <aop:aspectj-autoproxy />
        <bean id="woman" class="sample03.Woman" />
        <bean id="man" class="sample03.Man" />
        <bean id="myAspect" class="sample03.MyAspect" />

    </beans>
    ~~~

- MTest.java

    ~~~ java
    package sample03;

    import org.springframework.context.ApplicationContext;
    import org.springframework.context.support.ClassPathXmlApplicationContext;

    public class MTest {
        public static void main(String[] args) {
            ApplicationContext factory = new ClassPathXmlApplicationContext("sample03/beans.xml");

            Person my = (Person) factory.getBean("woman");
            my.classWork(); // 핵심코드실행
            ((ClassPathXmlApplicationContext) factory).close();
        }
    }
    ~~~

***

## sample04
- Person.java

    ~~~ java
    package sample04;

    public interface Person {
        public void classWork();
    }
    ~~~

- Man.java

    ~~~ java
    package sample04;

    public class Man implements Person {
        @Override
        public void classWork() {
            System.out.println("노트북을 켜고 Game을 시작한다.");
        }
    }
    ~~~

- Woman.java

    ~~~ java
    package sample04;

    public class Woman implements Person {

        @Override
        public void classWork() {
            System.out.println("노트북을 켜고 거울을 한번보고 공부를 시작한다.");
        }
    }
    ~~~

- MyAspect.java

    ~~~ java
    package sample04;

    public class MyAspect {

        public void before() {
            System.out.println("강의실 문을 연다");
        }

        public void after() {
            System.out.println("강의장 문을 닫고 퇴근한다.");
        }

        public void throwing() {
            System.out.println("엔코아 공사하는 날");
        }

        public void after_finally() {
            System.out.println("퇴실카드 체크.");
        }
    }
    ~~~

- beans.xml

    ~~~ xml
    <?xml version="1.0" encoding="UTF-8"?>
    <beans xmlns="http://www.springframework.org/schema/beans"
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xmlns:aop="http://www.springframework.org/schema/aop"
        xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
            http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop-4.3.xsd">
        <aop:aspectj-autoproxy />
        <bean id="woman" class="sample04.Woman" />
        <bean id="man" class="sample04.Man" />
        <bean id="myAspect" class="sample04.MyAspect" />

        <aop:config>
            <aop:aspect ref="myAspect">
                <aop:before method="before"
                    pointcut="execution(* classWork())" />
                <aop:before method="after"
                    pointcut="execution(* classWork())" />
                <aop:before method="after_finally"
                    pointcut="execution(* classWork())" />
                <aop:after-throwing method="throwing"
                    pointcut="execution(public void sample04.*.*(..))" throwing="e" />
            </aop:aspect>
        </aop:config>
    </beans>
    ~~~

- MTest.java

    ~~~ java
    package sample04;

    import org.springframework.context.ApplicationContext;
    import org.springframework.context.support.ClassPathXmlApplicationContext;

    public class MTest {
        public static void main(String[] args) {
            ApplicationContext factory = new ClassPathXmlApplicationContext("sample04/beans.xml");

            Person my = (Person) factory.getBean("woman");
            try {
                my.classWork(); // 핵심코드실행
            } catch (Exception e) {
                e.printStackTrace();
            }
            ((ClassPathXmlApplicationContext) factory).close();
        }
    }
    ~~~

***

## sample05
- Person.java

    ~~~ java
    package sample05;

    public interface Person {
        public void classWork();
    }
    ~~~

- Man.java

    ~~~ java
    package sample05;

    public class Man implements Person {
        @Override
        public void classWork() {
            System.out.println("노트북을 켜고 Game을 시작한다.");
        }
    }
    ~~~

- Woman.java

    ~~~ java
    package sample05;

    public class Woman implements Person {

        @Override
        public void classWork() {
            System.out.println("노트북을 켜고 거울을 한번보고 공부를 시작한다.");
        }
    }
    ~~~

- MyAspect.java

    ~~~ java
    package sample05;

    import org.aspectj.lang.annotation.After;
    import org.aspectj.lang.annotation.Aspect;
    import org.aspectj.lang.annotation.Before;
    import org.aspectj.lang.annotation.Pointcut;

    @Aspect
    public class MyAspect {
        // 리턴 타입 X, 코드 있으면 X private 지정, 가상메소드에 Pointcut를 지정해서 주소 대입
        @Pointcut("execution(* classWork(..))")
        public void myClass() {
        }

        @Before("myClass()")
        public void before() {
            System.out.println("강의실 문을 연다");
        }

        @After("myClass()")
        public void after() {
            System.out.println("강의장 문을 닫고 퇴근한다.");
        }
    }
    ~~~

- beans.xml

    ~~~ xml
    <?xml version="1.0" encoding="UTF-8"?>
    <beans xmlns="http://www.springframework.org/schema/beans"
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xmlns:aop="http://www.springframework.org/schema/aop"
        xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
            http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop-4.3.xsd">
        <aop:aspectj-autoproxy />
        <bean id="woman" class="sample05.Woman" />
        <bean id="man" class="sample05.Man" />
        <bean id="myAspect" class="sample05.MyAspect" />

        <!-- myAspect에 어노테이션이 없을 때 방법 (주석 해제하고 MyAspect에 어노테이션 지우고 실행)-->
        <!--
        <aop:config>
            <aop:pointcut expression="execution(* classWork())"
                id="myClass" />
            <aop:aspect ref="myAspect">
                <aop:before method="before"
                    pointcut="execution(* classWork())" />
                <aop:before method="after"
                    pointcut="execution(* classWork())" />
            </aop:aspect>
        </aop:config>
        -->
    </beans>
    ~~~

- MTest.java

    ~~~ java
    package sample05;

    import org.springframework.context.ApplicationContext;
    import org.springframework.context.support.ClassPathXmlApplicationContext;

    // [순수 XML 기반의 설정 파일]
    // 개발 시점에 편집기에서 XML과 JAVA(POJO : Plain Old Java Object 방식)파일을
    // 오가면서 작성, 유지보수 시점에 XML파일만 보면 Bean들 사이의 관계를 쉽게 알 수 있다.

    public class MTest {
        public static void main(String[] args) {
            ApplicationContext factory = new ClassPathXmlApplicationContext("sample05/beans.xml");

            Person my = (Person) factory.getBean("woman");
            my.classWork(); // 핵심코드실행
            ((ClassPathXmlApplicationContext) factory).close();
        }
    }
    ~~~

***

## sample06
- Person.java

    ~~~ java
    package sample06;

    public interface Person {
        public void classWork();
    }
    ~~~

- Man.java

    ~~~ java
    package sample06;

    import org.springframework.stereotype.Component;

    @Component("man")
    public class Man implements Person {
        @Override
        public void classWork() {
            System.out.println("노트북을 켜고 Game을 시작한다.");
        }
    }
    ~~~

- Woman.java

    ~~~ java
    package sample06;

    import org.springframework.stereotype.Component;

    @Component
    public class Woman implements Person {
        @Override
        public void classWork() {
            System.out.println("노트북을 켜고 거울을 한번보고 공부를 시작한다.");
        }
    }
    ~~~

- MyAspect.java

    ~~~ java
    package sample06;

    import org.aspectj.lang.annotation.After;
    import org.aspectj.lang.annotation.Aspect;
    import org.aspectj.lang.annotation.Before;
    import org.aspectj.lang.annotation.Pointcut;
    import org.springframework.stereotype.Component;

    @Component
    @Aspect
    public class MyAspect {
        // 리턴 타입 X, 코드 있으면 X, private 지정, 가상메소드에 Pointcut를 지정해서 주소 대입
        @Pointcut("execution(* classWork(..))")
        public void myClass() {
        }

        @Before("myClass()")
        public void before() {
            System.out.println("강의실 문을 연다");
        }

        @After("myClass()")
        public void after() {
            System.out.println("강의장 문을 닫고 퇴근한다.");
        }
    }
    ~~~

- MTest.java

    ~~~ java
    package sample06;

    import org.springframework.context.ApplicationContext;
    import org.springframework.context.support.ClassPathXmlApplicationContext;

    // [Annotation 기반의 XML 설정 파일]
    // 일부 java 파일은 스프링프레임워크에 의존하며,
    // 개발 시점에 XML파일을 한번 설정한 후에 다시는 볼일이 없다.

    // 유지보수 시점에 XML파일만 봐서는 Bean들 사이의 관계를 알 수가 없다.
    // 모든 java파일을 열어서 확인해야 정확한 구조를 파악 할 수 있다.
    // (Annotation 확인)

    public class MTest {
        public static void main(String[] args) {
            ApplicationContext factory = new ClassPathXmlApplicationContext("sample06/beans.xml");

            Person my = (Person) factory.getBean("woman");
            my.classWork(); // 핵심코드실행
            ((ClassPathXmlApplicationContext) factory).close();
        }
    }
    ~~~

***

## sample07

- CoreEx.java

    ~~~ java
    package sample07;

    public class CoreEx {
        public void zeroMethod(int a, int b) {
            try {
                System.out.println(a + " / " + b + " = " + (a / b));
            } catch (Exception e) {
                throw new RuntimeException("0으로 나눌 수 없습니다.");
            }
        }
    }
    ~~~

- AdviceEx.java

    ~~~ java
    package sample07;

    import org.aspectj.lang.ProceedingJoinPoint;
    import org.aspectj.lang.annotation.After;
    import org.aspectj.lang.annotation.AfterReturning;
    import org.aspectj.lang.annotation.AfterThrowing;
    import org.aspectj.lang.annotation.Around;
    import org.aspectj.lang.annotation.Aspect;
    import org.aspectj.lang.annotation.Before;

    @Aspect
    public class AdviceEx {
        @Before("execution(* zeroMethod(..))")
        public void before() {
            System.out.println("Joinpoint 앞에서 실행되는 Advice");
        }

        @After("execution(* zeroMethod(..))")
        public void after() {
            System.out.println("Joinpoint 뒤에서 실행되는 Advice");
        }

        @AfterThrowing(pointcut = "execution(* zeroMethod(..))", throwing = "e")
        public void afterThrowing(Throwable e) {
            System.out.println("예외가 실행될 때 호출되는 Advice");
            System.out.println(e.getMessage());
        }

        @AfterReturning(pointcut = "execution(* zeroMethod(..))", returning = "ret")
        public void afterReturning(Object ret) {
            System.out.println("Joinpoint가 정상 종료 후 실행되는 Advice");
        }

        @Around("execution(* zeroMethod(..))")
        public Object around(ProceedingJoinPoint jp) throws Throwable {
            System.out.println("Joinpoint 전역에서 실행되는 Advice : " + jp.getTarget());
            return jp.proceed();// 실행할 메소드(zeroMethod())를 가져와서 구현
        }
    }
    ~~~

- beans.xml

    ~~~ xml
    <?xml version="1.0" encoding="UTF-8"?>
    <beans xmlns="http://www.springframework.org/schema/beans"
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xmlns:aop="http://www.springframework.org/schema/aop"
        xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
            http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop-4.3.xsd">

        <aop:aspectj-autoproxy />

        <!-- 타겟(pointcut) -->
        <bean name="core" class="sample07.CoreEx" />

        <!-- Advice -->
        <bean name="advice" class="sample07.AdviceEx" />
    </beans>
    ~~~

- MTest.java

    ~~~ java
    package sample07;

    import org.springframework.context.ApplicationContext;
    import org.springframework.context.support.ClassPathXmlApplicationContext;

    public class MTest {
        public static void main(String[] args) {
            ApplicationContext factory = new ClassPathXmlApplicationContext("sample07/beans.xml");

            CoreEx my = (CoreEx) factory.getBean("core");
            my.zeroMethod(10, 2); // 핵심코드실행
            ((ClassPathXmlApplicationContext) factory).close();
        }
    }
    ~~~

***

## sample08
- CalcMethod.java

    ~~~ java
    package sample08;

    public class CalcMethod {
        public void sum(int a, int b) {
            System.out.println(a + " + " + b + " = " + (a + b));
        }

        public void div(int a, int b) {
            try {
                System.out.println(a + " / " + b + " = " + (a / b));
            } catch (Exception e) {
                System.out.println("0으로 나눌 수 없습니다.");
            }
        }
    }
    ~~~

- CalcAdvice.java

    ~~~ java
    package sample08;

    import org.aspectj.lang.ProceedingJoinPoint;
    import org.aspectj.lang.annotation.After;
    import org.aspectj.lang.annotation.Around;
    import org.aspectj.lang.annotation.Aspect;
    import org.aspectj.lang.annotation.Before;
    import org.aspectj.lang.annotation.Pointcut;

    @Aspect
    public class CalcAdvice {
        // 방법0
        @Pointcut("execution(public * *(..))")
        public void myCalc() {

        }

        @Before("myCalc()")
        public void before() {
            System.out.println("연산을 시작합니다.");
        }

        @After("myCalc()")
        public void after() {
            System.out.println("연산을 종료합니다.");
        }

        /*
         * 방법1
         *
         * @Pointcut("execution(public * *(..))") public void myCalc() {
         *
         * }
         *
         * @Around("myCalc()") public void around(ProceedingJoinPoint pjp) throws
         * Throwable{ System.out.println(" *** 연산시작 ***"); pjp.proceed();
         * System.out.println(" *** 연산종료 ***"); }
         */

        /*
         * 방법2 //@Before("execution(public void sample08.*.* (..))")
         *
         * @Before("execution(public * *(..))") public void before() {
         * System.out.println("연산을 시작합니다."); }
         *
         * @After("execution(public void sample08.*.* (..))") public void after() {
         * System.out.println("연산을 종료합니다."); }
         */
    }
    ~~~

- beans.xml

    ~~~ xml
    <?xml version="1.0" encoding="UTF-8"?>
    <beans xmlns="http://www.springframework.org/schema/beans"
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xmlns:aop="http://www.springframework.org/schema/aop"
        xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
            http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop-4.3.xsd">

        <aop:aspectj-autoproxy />

        <!-- 타겟(pointcut) -->
        <bean name="calc" class="sample08.CalcMethod" />

        <!-- Advice -->
        <bean name="advice" class="sample08.CalcAdvice" />
    </beans>
    ~~~

- CalcMain.java

    ~~~ java
    package sample08;

    import org.springframework.context.ApplicationContext;
    import org.springframework.context.support.ClassPathXmlApplicationContext;

    public class CalcMain {
        public static void main(String[] args) {
            ApplicationContext factory = new ClassPathXmlApplicationContext("sample08/beans.xml");

            CalcMethod my = (CalcMethod) factory.getBean("calc");
            my.sum(5, 4);
            System.out.println();
            my.div(10, 0);
            ((ClassPathXmlApplicationContext) factory).close();
        }
    }
    ~~~

***

## sample09
- CalcMethod.java

    ~~~ java
    package sample09;

    public class CalcMethod {
        public void sum(int a, int b) {
            System.out.println(a + " + " + b + " = " + (a + b));
        }

        public void div(int a, int b) {
            try {
                System.out.println(a + " / " + b + " = " + (a / b));
            } catch (Exception e) {
                System.out.println("0으로 나눌 수 없습니다.");
            }
        }
    }
    ~~~

- CalcAdvice.java

    ~~~ java
    package sample09;

    public class CalcAdvice {
        public void before() {
            System.out.println("연산을 시작합니다.");
        }

        public void after() {
            System.out.println("연산을 종료합니다.");
        }
    }
    ~~~

- beans.xml

    ~~~ xml
    <?xml version="1.0" encoding="UTF-8"?>
    <beans xmlns="http://www.springframework.org/schema/beans"
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xmlns:aop="http://www.springframework.org/schema/aop"
        xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
            http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop-4.3.xsd">

        <aop:aspectj-autoproxy />
        <bean id="calc" class="sample09.CalcMethod" />
        <bean id="advice" class="sample09.CalcAdvice" />

        <aop:config>
            <aop:pointcut expression="execution(public * *(..))"
                id="mypoint" />
            <aop:aspect ref="advice">
                <aop:before method="before" pointcut-ref="mypoint" />
                <aop:before method="after" pointcut-ref="mypoint" />
            </aop:aspect>
        </aop:config>

    </beans>
    ~~~

- CalcMain.java

    ~~~ java
    package sample09;

    import org.springframework.context.ApplicationContext;
    import org.springframework.context.support.ClassPathXmlApplicationContext;

    public class CalcMain {
        public static void main(String[] args) {
            ApplicationContext factory = new ClassPathXmlApplicationContext("sample09/beans.xml");

            CalcMethod my = (CalcMethod) factory.getBean("calc");
            my.sum(5, 4);
            System.out.println();
            my.div(10, 0);
            ((ClassPathXmlApplicationContext) factory).close();
        }
    }
    ~~~

***

## sample10
- MyInterface.java

    ~~~ java
    package sample10;

    public interface MyInterface {
        void Prn();
    }
    ~~~

- MyImplClass.java

    ~~~ java
    package sample10;

    public class MyImplClass implements MyInterface {
        @Override
        public void Prn() {
            System.out.println("핵심 코드 시작");
            int res = 10 / 0;
            System.out.println("핵심 비즈니스 코드");
        }

        /* MTest에서 (MyInterface) factory.getBean("myclass"); 일때
        @Override
        public void Prn() {
            System.out.println("핵심 코드 시작");
            int res = 0;
            try {
                res = 10 / 0;
            } catch (Exception e) {
                System.out.println(res);
            }
            System.out.println("핵심 비즈니스 코드");
        }
        */
    }
    ~~~

- MyThrowsAdvice.java

    ~~~ java
    package sample10;

    import java.lang.reflect.Method;
    import org.springframework.aop.ThrowsAdvice;

    public class MyThrowsAdvice implements ThrowsAdvice {
        // org.springframework.aop의	Interface ThrowsAdvice에 4번째
        // public void afterThrowing(Method method, Object[] args, Object target, Exception ex)
        public void afterThrowing(Method method, Object[] args, Object target, RuntimeException ex) {
            System.out.println("::: 오류 발생 객체 : " + target.getClass().getName());
            System.out.println("::: 오류 발생 메소드 : " + method.getName());
            System.out.println("::: 오류 발생 메시지 : " + ex.getMessage());
            System.exit(0);
        }

        // MTest에서 (MyInterface) factory.getBean("myclass"); 일때
        public void afterThrowing(Exception ex) {
            System.out.println("=========>" + ex);
        }

        /* 위에 메소드랑 주석 바꿔서 실행해보기
        public void afterThrowing(ArithmeticException ex) {
            System.out.println("=========>" + ex);
            System.exit(0);
        }
        */
    }
    ~~~

- beans.xml

    ~~~ xml
    <?xml version="1.0" encoding="UTF-8"?>
    <beans xmlns="http://www.springframework.org/schema/beans"
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xmlns:aop="http://www.springframework.org/schema/aop"
        xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
            http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop-4.3.xsd">

        <bean id="myclass" class="sample10.MyImplClass" />
        <bean id="myexception" class="sample10.MyThrowsAdvice" />

        <bean id="myproxy"
            class="org.springframework.aop.framework.ProxyFactoryBean">
            <property name="proxyInterfaces" value="sample10.MyInterface" />
            <property name="interceptorNames">
                <list>
                    <value>myexception</value>
                </list>
            </property>
            <property name="target">
                <ref bean="myclass" />
            </property>
        </bean>
    </beans>
    ~~~

- MTest.java

    ~~~ java
    package sample10;

    import org.springframework.context.ApplicationContext;
    import org.springframework.context.support.AbstractApplicationContext;
    import org.springframework.context.support.ClassPathXmlApplicationContext;

    public class MTest {
        public static void main(String[] args) {
            ApplicationContext factory = new ClassPathXmlApplicationContext("sample10/beans.xml");

            // MyInterface my = (MyInterface) factory.getBean("myclass");
            MyInterface my = (MyInterface) factory.getBean("myproxy");
            my.Prn();
            ((AbstractApplicationContext) factory).close();
        }
    }
    ~~~

***

## sample11
- sample11.core
	- DayService.java

        ~~~ java
        package sample11.core;

        public interface DayService {
            public String getDay();

            public int getYear();

            public void errorTest();
        }
        ~~~

	- DayServiceImpl.java

    ~~~ java
    package sample11.core;

    import java.util.Calendar;
    import java.util.GregorianCalendar;

    public class DayServiceImpl implements DayService {
        GregorianCalendar cal;

        public DayServiceImpl() {
            cal = new GregorianCalendar();
        }

        @Override
        public String getDay() {
            int day = cal.get(Calendar.DAY_OF_WEEK);
            int month = cal.get(Calendar.MONTH) + 1;
            int date = cal.get(Calendar.DAY_OF_MONTH);
            return "오늘은 " + month + "월 " + date + "일 입니다.";
        }

        @Override
        public int getYear() {
            return cal.get(Calendar.YEAR);
        }

        @Override
        public void errorTest() {
            int result = 100 / 0;
            System.out.println(result);
        }
    }
    ~~~

- sample11.advice
	- ThrowLogAdvice.java

        ~~~ java
        package sample11.advice;

        import java.lang.reflect.Method;
        import org.springframework.aop.ThrowsAdvice;

        public class ThrowLogAdvice implements ThrowsAdvice {

            public void afterThrowing(Method method, Object[] args, Object target, RuntimeException ex) {
                System.out.println("::: 오류 발생 객체 : " + target.getClass().getName());
                System.out.println("::: 오류 발생 메소드 : " + method.getName());
                System.out.println("::: 오류 발생 메시지 : " + ex.getMessage());
                System.exit(0);
            }
        }
        ~~~

	- MethodBeforeLogAdvice.java

        ~~~ java
        package sample11.advice;

        import java.lang.reflect.Method;

        import org.springframework.aop.MethodBeforeAdvice;

        public class MethodBeforeLogAdvice implements MethodBeforeAdvice {
            // 로그인 작업 검증 AOP할 경우 WEB_SpringEL검증
            // 클라이언트의 정보를 파일에 저장하고 싶다.
            @Override
            public void before(Method method, Object[] args, Object target) throws Throwable {
                System.out.println("<<<< MethodBeforeLogAdvice - " + target
                        + "의" + method.getName() + "( ) 호출 전 >>>");
            }
        }
        ~~~

- sample11
	- beans.xml

        ~~~ xml
        <?xml version="1.0" encoding="UTF-8"?>
        <beans xmlns="http://www.springframework.org/schema/beans"
            xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
            xmlns:aop="http://www.springframework.org/schema/aop"
            xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
                http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop-4.3.xsd">

            <!-- target definition (핵심로직) -->
            <bean id="dayServiceTarget" class="sample11.core.DayServiceImpl" />

            <!-- Advice definition -->
            <bean id="beforeLogAdvice"
                class="sample11.advice.MethodBeforeLogAdvice" />
            <bean id="throwLogAdvice" class="sample11.advice.ThrowLogAdvice" />

            <!-- pointcut definition -->
            <bean id="dayPointcut"
                class="org.springframework.aop.support.JdkRegexpMethodPointcut">
                <property name="pattern" value=".*getDay.*" />
            </bean>

            <bean id="errorPointcut"
                class="org.springframework.aop.support.JdkRegexpMethodPointcut">
                <property name="pattern" value=".*error.*" />
            </bean>

            <!-- advisor(advice+pointcut의 결합) definition -->
            <bean id="dayAdvisor"
                class="org.springframework.aop.support.DefaultPointcutAdvisor">
                <property name="advice" ref="beforeLogAdvice" />
                <property name="pointcut" ref="dayPointcut" />
            </bean>

            <bean id="errorAdvisor"
                class="org.springframework.aop.support.DefaultPointcutAdvisor">
                <property name="advice" ref="throwLogAdvice" />
                <property name="pointcut" ref="errorPointcut" />
            </bean>

            <!-- proxy(대리자) definition -->
            <bean id="dayService"
                class="org.springframework.aop.framework.ProxyFactoryBean">

                <property name="target" ref="dayServiceTarget" />
                <property name="interceptorNames">
                    <list>
                        <value>dayAdvisor</value>
                    </list>
                </property>
            </bean>

            <bean id="errorTest"
                class="org.springframework.aop.framework.ProxyFactoryBean">
                <property name="target" ref="dayServiceTarget" />
                <property name="interceptorNames">
                    <list>
                        <value>errorAdvisor</value>
                    </list>
                </property>
            </bean>
        </beans>
        ~~~

- sample11.main
	- MainApi.java

        ~~~ java
        package sample11.main;

        import org.springframework.context.ApplicationContext;
        import org.springframework.context.support.ClassPathXmlApplicationContext;

        import sample11.core.DayService;

        public class MainApi {
            public static void main(String[] args) {
                String[] configs = { "sample11/beans.xml" };

                ApplicationContext context = new ClassPathXmlApplicationContext(configs);

                //DayService service1 = (DayService) context.getBean("errorTest");
                DayService service1 = (DayService) context.getBean("dayService");

                String day1 = service1.getDay();
                System.out.println("오늘은  " + day1 + " 입니다.");

                int year1 = service1.getYear();
                System.out.println("올해는 " + year1 + "년 입니다.");

                //service1.errorTest();

                ((ClassPathXmlApplicationContext) context).close();
            }
        }
        ~~~

***