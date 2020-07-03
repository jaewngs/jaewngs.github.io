---



title:  "커스텀 태그 & Servlet 필터링"



layout: post



---


## [오늘 할 내용]
1. 커스텀 태그를 작성할 수 있다.
2. Servlet의 필터링을 할 수 있다.
3. ServletContext, ServletConfig

***

## 커스텀 태그
- ver 1.2
	- " javax.servlet.jsp.tagext.* " 중 상속을 받아 메소드를 재정의한다.
	- 태그가 생성되면 /WebContent/WEB-INF/tld 폴더를 만든 다음 생성된 태그를 호출해서 사용한다.
- ver 2.0
	- SimpleTagSupport 상속 받아 메소드 정의
	- 추가로 bean객체에 값을 담아 속성을 만들어 저장하거나 전역변수를 만들어 저장한 것을 .tld에 정의한 다음. jsp에서 태그로 호출한다.
	- /WEB-INF/tags/*.tag로 태그를 @ 로 속성과 변수를 선언한 다음, 원하는 값은 <c:set>태그로 저장을 하고 .jsp로 바로 호출한다.




























