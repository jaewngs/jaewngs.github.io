---



title:  "JQuery"



layout: post



---


## [오늘 할 내용]
1. Ajax, JQuery를 이용해서 비동기 통신을 할 수 있다.
2. <https://jquery.com>

***

### 비동기 통신
- 서버 페이지(정적페이지 제외한 동적페이지(Servlet,JSP))는 하나의 클래스 단위로 웹 서버가 멀티 스레딩을 연동해서 다중 통신을 사용할 수 있게 한다.
- 동기화 통신을 프로세스를 가진 WAS가 관리한다.
- 서버페이지에서 또 다른 서버페이지 및 프로세스를 제외한 프로세스 간의 통신을 할 때 사용되는 것이 비동기 통신[Ajax]이다.
- 신호를 보내면 신호를 받아야 프로세스가 진행되면 동기화(한 세트만 진행) : Request, Response
- 신호를 보내고 신호가 오는 동안 또 다른 신호를 보내는 통신은 비동기화

***

### Ajax [ 비동기 통신 ]
- JQuery를 이용해서 비동기 통신을 할 수 있다.
- JQuery = [ CSS3 + HTML + JavaScript ] + Ajax = HTML5 + Ajax

### ex)
- JSP -> Controller [ session, request ] -> JSP
- JSP -> Servlet -> JSP
- JSP -> JS(정적 페이지에 액션을 가지고 있음)
- JS -> JSP(동적 페이지 (서버를 가지고 있음))

***

### sec01/jquery.html
- $(document).ready(function( ) { } );
- $(function( ){  명령  } );



### sec01/
















