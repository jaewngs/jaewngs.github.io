---



title:  "Spring AOP"



layout: post



---


## [오늘 할 내용]
1. SpringAOP
2. sample01 : 자바 코드 작성을 위한 처리 흐름
3. sample02 : sample01을 AOP로 적용
	- AOP(Aspect Oriented Programming)란? OOP의 문제를 개선하기 위해 적용된 것
		- Core concerns : 해당 시스템의 핵심 가치와 고유의 목적이 그대로 들어난 관심 영역
		- Crosscutting Concerns : 로깅, 보안, 트랜잭션 관리 등과 같이 여러 모듈간의 공통적으로 적용되는 공통 관심 영역

    - AOP의 장점 : 기존의 코드를 수정하지 않고도 필요한 공통 기능 모듈 적용 가능
    - AOP의 툴 4가지
    	- 자바언어를 확장한 언어를 사용하는 툴 : AspectJ
			- 제록스 연구소 개발 -> Eclipse에 기증 -> 현재 IBM에서 지원 받아 개발중
			- Aspect, PoingCut, Advice를 만들 수 있다.
    	- 기존의 자바 언어만으로 AOP 구현 가능 : AspectWerkZ
    		- Eclipse 프로젝트(PointCut, Advice)에서 XML로 정의할 수 있다.
    	- 대표적인 인터셉터 체인 방식의 구현 AOP 방식 : SpringAOP, JBossAOP
    		- 최근에 등장한 프레임워크, 어노테이션으로 정의할 수 있다.
    		- SpringDI패턴에서 동작하는 인터페이스이다.
    		- 기존 클래스에서 byte 코드를 수정하지 않고 JDK의 XML에서 지정한 bean의 객체를 통해 제어한다.
	- Advisor = AOP advice (action to take at a joinpoint) + advice(such as a pointcut);
	- PointCut = A pointcut is composed of a ClassFilter and a MethodMatcher.
	- AOP의 주요 구성요소[sec.11]
		1. joinpoint : 프로그램의 제어 프름 중 한 시점을 의미
			- 메소드 호출시점, 메소드 호출결과가 리턴되는 시점, 예외 던져지는 시점
			- 각각의 joinpoint들을 전후로 Crosscutting Concerns의 기능이 AOP에 의해 자동으로 추가되어 동작되는 후보 지점이다.

		2. pointcut : AOP를 선별하는 것
			- value = "execution(public * *(..))" => pointcut를 정의한 것
			- AOP의 전체 코드에서 실행되어지는 메소드 유형을 정의한다.

            - 기본표현식
                - set*(..) : 메소드 명이 set으로 시작하고 0개 이상의 매개인자를 가진 메소드
                - *main(..) : 메소드 명이 main이고 return type이 any type(모든 타입)이고 0개 이상의 매개인자를 가진 메소드

            - 타입 매핑 형식 - java.io.*
                - org.com.test..* : org.com.test 내의 서브 패키지에 속한 모든 하위요소
                - Number+ : Number 또는 Number 서브타입의 모든 타입
                - !(Number+) : Number 또는 Number 서브타입의 모든 타입이 아닌 것
                - int **||** Integer : ing형 또는 Integer형
                - org.com.test..* && !Serializable+ : org.com.test 패키지 또는 하위서브 패키지 내에 존재하면서 Serializable의 타입이 아닌 모든 요소

            - 접근 지정에 따른 형식
                - public static void main(..)
                - !private ** * ** ** * ** (..) : 리턴 타입이 모든 타입이고, 0개 이상의 매개인자를 가진 메소드 중 접근 제한자가 private가 아닌 메소드
                - ** *main(..) ** : 접근 지정자 명시하지 않으면 public 접근 제한자
                - ** *main **(** * **, ..)  : 리턴 타입이 모든 타입이고 최소 1개의 모든 타입을 가진 메소드
                - ** *main ** (** * **, .., String, ** * **) : 리턴타입이 모든 타입이고 최소 3개의 매개인자를 가지며 끝에서 두번째 반드시 String 타입이어야 한다.

            - 생성자 형식
                - new(..) : 0개 이상의 모든 타입을 가진 생성자
                - Account.new(..) : 0개 이상의 모든 타입을 가진 Account 클래스의 생성자
                - execution 또는 call : 특정 메소드나 생성자 실행 시점을 정의한다.
                    - execution( * main(..))
                    - call( * main(..))
                    - get(Collection+ ) : 멤버변수에 선언
                    - set(Collection+ ) : 멤버변수에 선언
                - Exception 처리 : handler(RuntimeException+)
                    - RuntimeException 하위에 있는 Exception을 모두 catch하는 시점
                - within 처리 : 특정 유형의 joinpoint를 정의하는 시점
                - withincode : 특정 메소드 또는 생성자 내에 정의된 코드를 정의하는 시점
                - this : 해당 joinPoint를 정의하는 시점
                - target : 대상 객체의 타입을 정의하는 시점
                    - ex) call (** * ** ** * ** (..)) && target(MyTest)
                        - MyTest라는 클래스 내의 모든 메소드를 호출

		3. advice : 각 joinpoint에 삽입되어 동작할 수 있는 코드
		4. Weaving : Core concerns랑 Crosscutting Concerns를 엮어서 동작하도록 수행한다는 의미
			- target객체에 aspect를 적용해서 새로운 프록시 개체를 생성하는 절차
		5. aspect : 다수의 클래스에서 공통적으로 구현하는 것으로 모듈화를 말한다.
			- 어디서(pointcut)에서 무엇을 할지(Advice)를 합쳐 놓은 것
			- aspect = pointcut + advice
			- Advice는 각 joinpoint에 삽입되어서 동작할 수 있는 코드이다.
		6. 프록시 : 어드바이스를 target객체에 적용하면 생성되는 객체

***

## sample01
- Man.java
- Woman.java
- MTest.java

***

## sample02
- Person.java
- Man.java
- Woman.java
- MyAspect.java
- beans.xml
- MTest.java


























