---



title:  "Servlet&JSP"



layout: post



---


## [오늘 할 내용]
1. was를 이용해서 웹프로젝트를 생성할 수 있다.
2. was의 원리를 이해할 수 있다.
3. servlet의 상속구조와 인터페이스를 활용해서 http 프로토콜을 통해 값을 	Client -> Server 구조로 전달 할 수 있다.

***

### java 로 클래스를 만들자. com.test.MyTest.class 만들어서 web 브라우저로 출력하고 싶다.
1. WAS를 설치(내가만든 클래스를 http 프로토콜로 url을 맵핑 시켜줌) -> 톰캣 설치
2. com.test.MyTest.class를 만든다.(http://localhost:8787/Day49/test)
	- WebContent/WEB-INF/web.xml : 웹페이지의 정보를 설정하고 저장하는 곳
	- 'MyTest' -> '/test'로 만들어주는 작업을 해야함(web.xml에서 함)
3. http: 프로토콜을 통해서 연동할 수 있는 클래스를 찾아서 상속 구조를 만든다.
4. HttpServlet 클래스를 상속 받는다.
	: Provides an abstract class to be subclassed to create an HTTP servlet suitable for a Web site.


***
### 컨테이너에 있는 클래스 로딩 순서
1. Day49가 WAS[Servlet/jsp].. Servlet/jsp가 만든 클래스를 브라우저에 올려준다.
2. /Day49/WebContent/META-INF/context.xml[전체적인 프로젝트 설정파일] 파일을 로딩
3. /Day49/WebContent/WEB-INF/web.xml[웹 페이지 설정파일] 로딩
4. 























