---
title:  "JDBC Connection & Join사용(Entity 조건 병합) & 분산 함수"
layout: post
---

# DAY 28

## [오늘 할 내용]
1. JDBC Connection을 살펴보고 각 흐름을 숙지 할 수 있다.
2. Join을 사용하여 Entity의 조건 병합에 대해서 살펴 보고 활용 할 수 있다.
3. 분산 함수를 살펴보고 활용 할 수 있다.


***
## Database 연결하기
![image](https://user-images.githubusercontent.com/52989294/84564678-63dbab00-ad9e-11ea-8281-a440829a0343.png)
-- 더블클릭 또는 마우스 우클릭으로 Connect 하기

![image](https://user-images.githubusercontent.com/52989294/84564717-b5843580-ad9e-11ea-879f-1a6c986a5a81.png)
-- 마우스 우클릭 > new > SQL File 생성

![image](https://user-images.githubusercontent.com/52989294/84564747-0a27b080-ad9f-11ea-96b7-f3ab28865cad.png)
-- SQL 파일 생성

~~~ sql
-- day28.sql 파일에 SQL문 연습하기

--사원테이블에서 사원의 이름과 봉급을 출력해 보자.
SELECT ENAME, SAL
FROM EMP;

--부서테이블의 모든 정보를 출력해보자.
SELECT * FROM DEPT;

--사원테이블에서 사원 번호, 사원 이름, 부서번호만 추출해서 MY_Emp 테이블을 작성하자
CREATE TABLE My_Emp
AS
SELECT EMPNO, ENAME, DEPTNO
FROM EMP;

SELECT * FROM My_Emp;

--부서 테이블의 모든 내용을 MY_DEPT 테이블로 작성하자
CREATE TABLE MY_DEPT
AS
SELECT * FROM DEPT;

SELECT * FROM MY_DEPT;

--원하는 이름을 입력받아 레코드를 출력하자
-- my_emp에서 MARTIN의 정보를 출력하자.
SELECT *
FROM MY_EMP
WHERE ENAME = 'MARTIN';

--사원 번호와 사원 이름을 입력받아 레코드를 출력하자
SELECT *
FROM My_Emp
WHERE ENAME = 'FORD' and EMPNO = 7902;

DROP TABLE X,S,M;

CREATE TABLE M
(M1 CHAR(6), M2 VARCHAR(10));

CREATE TABLE S
(S1 CHAR(6), S2 VARCHAR(10));

CREATE TABLE X
(X1 CHAR(6), X2 VARCHAR(10));

INSERT INTO M VALUES('A', '1');
INSERT INTO M VALUES('B', '1');
INSERT INTO M VALUES('C', '3');
INSERT INTO M VALUES(NULL, '3');

INSERT INTO S VALUES('A', 'X');
INSERT INTO S VALUES('B', 'Y');
INSERT INTO S VALUES(NULL, 'Z');

INSERT INTO X VALUES('A', 'DATA');

SELECT M1
FROM M;
~~~

***

## DB 연결하기
1. Class.forName( ) ⇒ 드라이버 연결
2. Connection conn = DriverManager.getConnection()  ⇒ DB 연결
3. Statement stmt = conn.createStatement( );  ⇒ SQL 명령 준비
4. 쿼리를 실행해서 결과를 리턴[ boolean, int [ ] , ResultSet, int ]
	- boolean stmt.execute(String sql)  ⇒ 지정된 쿼리를 실행 유무를 리턴
	- int [ ]  stmt.executeBatch( )  ⇒ 추가된 쿼리들을 일괄[insert, delete,update]로 실행한 결과를 리턴(일괄 처리)
	- ResultSet stmt.executeQuery(String sql)  ⇒ 지정된 쿼리 중 select 쿼리를 테이블의 데이터로 저장해서 리턴(가상 메모리 테이블)
	- int stmt.executeUpdate(String sql)  ⇒ [insert, delete, update] 실행 결과
5. SELECT 쿼리를 ResultSet의 getDataType형 메소드로 결과를 컬럼형으로 리턴 받는다.


## 패키지 생성 순서
- com.vo ⇒⇒⇒ com.dao ⇒⇒⇒ com.biz ⇒⇒⇒ com.view
(Day27에서 common 패키지 가져오기)

***

## JDBC  + MVC
- Model : 연산,  DB 처리 등을 가지고 있는 비즈니스 로직을 말함 (biz + DAO)
- View : 화면 설계단(GUI, 회원가입 화면, 게시판 화면 ... 보여지는 화면)
- Controller : view에서 입력 받는 값들을 조건에 따라 모델로 전달 한 후 결과를 다시 view로 리턴하는	 기능(모델, 뷰 사이에 있음), 데이터 검증 후 로직을 제어하는 역할

## My_Emp 테이블을 사용한 프로젝트(MVC)
- Model : com.biz.MyEmpBIZ, com.dao.MyEmpDAO
	- 계산이 있으면 계산하고 계산 후 DAO를 호출
	- 계산이 없으면 바로 DAO 호출
- View + Controller : MTest.main()
- VO : MVC 간의 데이터를 전달할 객체 : com.vo.MyEmpVO
- Entity : com.entity.MyEmpEntity

~~~ java
package com.vo;

// vo (value Object) : mvc 패턴에 값을 전달하는 역할 : beans
public class MyEmpVO {
//EMPNO, ENAME, DEPTNO
	private int empno;
	private String name;
	private int deptno;

	public MyEmpVO() {
		super();
	}

	public MyEmpVO(int empno, String name, int deptno) {
		super();
		this.empno = empno;
		this.name = name;
		this.deptno = deptno;
	}

	public int getEmpno() {
		return empno;
	}
	public void setEmpno(int empno) {
		this.empno = empno;
	}

	public String getName() {
		return name;
	}
	public void setName(String name) {
		this.name = name;
	}

	public int getDeptno() {
		return deptno;
	}
	public void setDeptno(int deptno) {
		this.deptno = deptno;
	}

	@Override
	public String toString() {
		return String.format("MyEmpVO [empno=%s, name=%s, deptno=%s]", 
				empno, name, deptno);
	}
}
~~~

***

~~~ java
// com.dao.MyEmpSql.java(인터페이스)
package com.dao;

public interface MyEmpSql {
	String myemp_all = "SELECT * FROM My_Emp";

	// ? 에 매개변수를 사용하겠다.
	String myemp_vo = "SELECT * FROM MY_EMP WHERE ENAME = ?";

	String myemp_vo2 = "SELECT * FROM MY_EMP WHERE ENAME = ? AND EMPNO = ?";
}
~~~

***

~~~ java
package com.dao;

import java.sql.*;
import java.util.*; //  List<MyEmpVo>  all;
import com.vo.*;
import static common.JDBCTemplate.*;
// CRUD 메서드만 존재 한다.
// view -> controller 호출 -> model[biz] connection & close -> dao
// dao -> biz -> controller -> view로 다시 리턴해줌
public class MyEmpDAO implements MyEmpSql {
	private Connection conn;

	public MyEmpDAO(Connection conn) {
		this.conn = conn;
	}

	public List<MyEmpVO> getAll_MyEmp() {
		// select 결과를 한줄씩 vo에 담아서 List 객체에 추가한 후 리턴
		MyEmpVO vo = null;
		List<MyEmpVO> all = new ArrayList<>();

		// 명령을 수행하는 객체생성
		Statement stmt = null;

		// 결과, select 실행 후 데이터를 리턴 받을 객체
		ResultSet rs = null;

		try {
			stmt = conn.createStatement(); // 명령실행 준비
			rs = stmt.executeQuery(myemp_all); // select 문을 실행 후 결과를 리턴
			while (rs.next()) {
				// 결과를 한줄씩 읽어서 vo에 담는다.
				vo = new MyEmpVO(rs.getInt(1), rs.getString(2), rs.getInt(3));

				// List 객체에 추가한다.
				all.add(vo);
			}

		} catch (Exception e) {
			System.out.println(e);
		} finally{
			Close(rs);
			Close(stmt);
		}
		return all;
	} // end method

	public MyEmpVO getAll_VO(String name) {
		PreparedStatement pstm = null;
		ResultSet rs = null;
		MyEmpVO vo = null;

		try {
			pstm = conn.prepareStatement(myemp_vo);
			pstm.setString(1, name);
			rs = pstm.executeQuery();
			while (rs.next()) {
				vo = new MyEmpVO(rs.getInt(1), rs.getString(2), rs.getInt(3));
			}
		} catch (Exception e) {
			System.out.println(e.toString());
		}finally{
			Close(rs);
			Close(pstm);
		}
		return vo;
	}

	public MyEmpVO getAll_VO002(MyEmpVO input_vo) {
		PreparedStatement pstm = null;
		ResultSet rs = null;
		MyEmpVO vo = null;

		try {
			pstm = conn.prepareStatement(myemp_vo2);
			pstm.setString(1, input_vo.getName());
			pstm.setInt(2, input_vo.getEmpno());

			rs = pstm.executeQuery();
			while (rs.next()) {
				vo = new MyEmpVO(rs.getInt(1), rs.getString(2), rs.getInt(3));
			}
		} catch (Exception e) {
			System.out.println(e.toString());
		}
		finally{
			Close(rs);
			Close(pstm);
		}
		return vo;
	}
}
~~~

***

~~~ java
package com.biz;

import java.sql.*;
import java.util.*;
import com.vo.*;
import com.dao.*;
import static common.JDBCTemplate.*;

// biz : 1. 계산 결과를 controller에게 리턴
//		 2. 계산 결과를 DAO로 연결해서 수행 결과를 controller로 리턴
//		 3. 계산 없으면 DAO를 호출한다. connection & close를 호출하는 곳
public class MyEmpBIZ {

	public List<MyEmpVO> getAll_MyEmp() {
		Connection conn = getConnection();
		// 계산이 있다면 계산 한 후 dao에게 전달 후 결과를 리턴
		List<MyEmpVO> all = new MyEmpDAO(conn).getAll_MyEmp();
		Close(conn);

		return all;
	}

	public MyEmpVO getAll_VO(String name) {
		Connection conn = getConnection();
		// controller에게 받은 name을 dao에게 전달해서 정보를 리턴받는다.
		MyEmpVO res = new MyEmpDAO(conn).getAll_VO(name);
		Close(conn);
		return res;
	}

	public MyEmpVO getAll_VO002(MyEmpVO input_vo) {
		Connection conn = getConnection();
		// controller에게 받은 name, empno을 dao에게 전달해서 정보를 리턴받는다.
		MyEmpVO res = new MyEmpDAO(conn).getAll_VO002(input_vo);
		Close(conn);
		return res;
	}
}

~~~

***

~~~ java
package com.view;

import java.util.List;
import com.vo.*;
import com.biz.*;

// View + Controller <-> biz <-> dao <-> JDBCTemplate <-> DB
public class MTest {
	public static void main(String[] args) {
		// 전체 출력을 한다.
		List<MyEmpVO> all = new MyEmpBIZ().getAll_MyEmp();
		for (MyEmpVO res : all) {
			System.out.println(res);
		}
	}
}
~~~

***

~~~ java
package com.view;

import java.sql.*;
import java.util.Scanner;

import static common.JDBCTemplate.*;
//SELECT * FROM MY_EMP WHERE ENAME = 'MARTIN';

public class MTest01 {
	public static void main(String[] args) throws Exception {
		Scanner sc = new Scanner(System.in);
		String name = sc.next(); // 이름1을 입력
		String name1 = sc.next();// 이름2을 입력

		String sql = "SELECT * FROM MY_EMP WHERE ENAME = ? or ENAME = ?";
		Connection con = getConnection();
		PreparedStatement Pstmt = con.prepareStatement(sql);
		Pstmt.setString(1, name);
		Pstmt.setString(2, name1);

		ResultSet rs = Pstmt.executeQuery();
		while (rs.next()) {
			System.out.println(rs.getInt(1) + "," + rs.getString(2) + "," + rs.getInt(3));
		}
		Close(con);
	}
}
~~~

***

~~~ java
package com.view;

import java.util.Scanner;
import com.biz.MyEmpBIZ;
import com.vo.MyEmpVO;

//SELECT * FROM MY_EMP WHERE ENAME = 'MARTIN';
// MVC 패턴으로 바꾸기
public class MTest02 {
	public static void main(String[] args) throws Exception {
		Scanner sc = new Scanner(System.in);
		System.out.print("이름 입력 : ");
		String name = sc.next(); // 이름1을 입력

		// 입력한 이름에 대한 정보를 리턴 받겠다.
		MyEmpVO res = new MyEmpBIZ().getAll_VO(name);
		System.out.println(res);
	}
}
~~~

***

~~~ java
package com.view;

import java.util.Scanner;
import com.biz.MyEmpBIZ;
import com.vo.MyEmpVO;

//SELECT * FROM MY_EMP WHERE ENAME = 'MARTIN';
// MVC 패턴 name, empno 받아서 출력하기
public class MTest03 {
	public static void main(String[] args) throws Exception {
		Scanner sc = new Scanner(System.in);
		System.out.println("input name : ");
		String name = sc.next(); // 이름1을 입력
		System.out.println("input empno : ");
		int empno = sc.nextInt();

		MyEmpVO input_vo = new MyEmpVO();
		input_vo.setName(name);
		input_vo.setEmpno(empno);

		// 입력한 이름,번호에 대한 정보를 리턴 받겠다.
		MyEmpVO res = new MyEmpBIZ().getAll_VO002(input_vo);
		System.out.println(res);
	}
}
~~~

***

~~~ java
package com.view;

import java.awt.*;
import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;
import java.awt.event.WindowAdapter;
import java.awt.event.WindowEvent;
import java.util.List;

import com.biz.MyEmpBIZ;
import com.vo.MyEmpVO;

public class MTest04 extends Frame implements ActionListener {

	Button bt;
	TextArea ta;

	public MTest04() {
		super("MyMVC 테스트");
		bt = new Button("확인");
		ta = new TextArea(20, 50);
	}

	public void go() {
		setLayout(new GridLayout(2, 1));
		bt.addActionListener(this);
		addWindowListener(new WindowAdapter() {
			// '창닫기' 클릭해서 창 닫기
			@Override
			public void windowClosing(WindowEvent e) {
				System.exit(0);
			}
		});

		add(bt);
		add(ta);
		setSize(500, 500);
		setVisible(true);

	}

	public static void main(String[] args) {
		MTest04 mt = new MTest04();
		mt.go();
	}

	@Override
	public void actionPerformed(ActionEvent e) {
		List<MyEmpVO> all = new MyEmpBIZ().getAll_MyEmp();
		StringBuffer sb = new StringBuffer();
		for (MyEmpVO res : all) {
			sb.append(res.toString() + "\n");
		}
		ta.setText(sb.toString());
	}
}
~~~

***

## Join이란?

- 데이터베이스에서 여러 테이블의 데이터가 필요한 경우 Join을 사용
- Join은 관계형 데이터베이스에서 가장 기본적이고 가장 중요한 기능
- 어떤 테이블을 기준으로 다른 테이블에 있는 Row를 찾아오는 것
- 관계형 데이터베이스에서는 서로 독립적인 데이터들 간의 Join을 이용하여 필요 시 원하는 다양한 정보를 참조한다.
- 해당 열에 존재하는 공통 값, 일반적으로는 기본 키 및 외래 키 열을 Join 조건으로 사용하여 한 테이블의 행을 다른 테이블의 행에 Join 할 수 있다.
- 테이블 간의 관계에 따른 분류
	- INNER JOIN
	- CROSS JOIN
	- OUTER JOIN
	- SELF JOIN
	- NONEQUI JOIN

### < INNER JOIN >
- 각 테이블의 조인 컬럼(공통 컬럼)을 비교하여 조인조건을 만족하는 레코드만 선택하는 조인
- 테이블 M, S에서 M1 = S1 조건을 만족하는 레코드만 가져오는 JOIN문을 작성해보자.
- NULL, 같지 않은 FALSE 값 가지고 오지 않음
(SELECT * FROM M JOIN S ON M1=S1;) … 그냥 JOIN으로 쓰면 INNER JOIN해줌

~~~ sql
-- Ansi JOIN
SELECT * FROM M INNER JOIN S ON M1 = S1;

-- SQL SERVER JOIN
SELECT * FROM M, S WHERE M1 = S1;
~~~

Q1. INNER JOIN을 이용해서 사원의 이름과 그 사원이 속해 있는 부서이름을 출력
~~~ sql
SELECT ENAME, DNAME
FROM EMP, DEPT
WHERE EMP.DEPTNO = DEPT.DEPTNO;

--Ansi join
SELECT ENAME, DNAME
FROM EMP JOIN DEPT
USING(DEPTNO);
~~~

Q2. 사원의 이름과 그 사원이 속해있는 부서이름과 부서번호를 출력
~~~ sql
SELECT ENAME, DNAME, EMP.DEPTNO
FROM EMP, DEPT
WHERE EMP.DEPTNO = DEPT.DEPTNO;

--Ansi join
SELECT ENAME, DNAME, DEPTNO
FROM EMP JOIN DEPT
USING(DEPTNO);
~~~

### < CROSS JOIN >
- 각 테이블의 모든 로우에 대해서 가능한 모든 조합을 가지는 쿼리 결과를 만들어 내는 조인

~~~ sql
--Ansi JOIN
SELECT *
FROM M CROSS JOIN S

-- SQL SERVER JOIN
SELECT *
FROM M,S
~~~

### < OUTER JOIN >
- 두 테이블간에 주, 종관계를 두고 주 테이블의 모든 레코드와 종 테이블에서 조인 조건을 만족하는 레코드만 가져올 때 사용
- 주 테이블의 위치에 따라서 LEFT OUTER JOIN, RIGHT OUTER JOIN, 그리고 두 개의 결과를 합한 FULL OUTER JOIN으로 구분

~~~ sql
--Ansi JOIN
SELECT *
FROM M CROSS JOIN S

-- SQL SERVER JOIN
SELECT *
FROM M,S
~~~

- LEFT OUTER JOIN
	- M 테이블을 주 테이블로 놓고, S 테이블을 종 테이블로 하여 조인을 걸어서 M1 = S1 조건을 만족하는 레코드를 가져오는 LEFT OUTER JOIN을 작성
	- 주 테이블은 다 나오고 종 테이블은 맞는 것만 출력됨

	~~~ sql
    --Ansi JOIN
	SELECT *
	FROM M LEFT OUTER JOIN S
	ON M1 = S1;

	--SQL SERVER JOIN
	SELECT *
	FROM M,S
	WHERE M1 = S1(+);
	~~~

- RIGHT OUTER JOIN
	-S 테이블을 주 테이블로 놓고 M 테이블을 종 테이블로한 RIGHT OUTER JOIN을 작성

    ~~~ sql
    --Ansi JOIN
	SELECT *
	FROM M RIGHT OUTER JOIN S
	ON M1 = S1;

	--SQL SERVER JOIN
	SELECT *
	FROM M,S
	WHERE M1(+)= S1;
    ~~~

- FULL OUTER JOIN
	- 두 테이블을 LEFT OUTER JOIN, RIGHT OUTER JOIN을 한 결과를 합한 것과 동일한 결과

    ~~~ sql
	--Ansi JOIN
	SELECT *
	FROM M FULL OUTER JOIN S
	ON M1 = S1;

	-- LEFT, RIGHT, OUTER JOIN 을 사용한 쿼리를 UNION으로 합했을 경우
	SELECT *
	FROM M,S
	WHERE M1(+)= S1
    UNION
	SELECT *
	FROM M,S
	WHERE M1 = S1(+);
	~~~

- 3개 이상의 테이블에서 조인 걸기
	~~~ sql
	--Ansi JOIN
	SELECT *
	FROM M INNER JOIN S
	ON M1 = S1
		INNER JOIN X
		ON S1 = X1;

	-- SQL SERVER JOIN
	SELECT *
	FROM M,S,X
	WHERE M1 = S1 AND S1 = X1;
	~~~

- SELF JOIN
	- 하나의 테이블 내에서 서로 다른 컬럼 간의 참조 관계가 있을때 걸리는 JOIN
	사원번호, 이름 출력
	~~~ sql
    --SQL SERVER JOIN
	SELECT 사원.EMPNO, 사원.ENAME, 관리자.EMPNO, 관리자.ENAME
	FROM EMP 사원, EMP 관리자
	WHERE 사원.MGR = 관리자.EMPNO(+);

	--Ansi JOIN
	SELECT E.EMPNO, E.ENAME, D.EMPNO, D.ENAME
	FROM EMP E LEFT OUTER JOIN EMP D
	FRON E.MGR = D.EMPNO;
    ~~~

- NON EQUI JOIN
	- Q1. 각 사원의 이름과 월급, 그리고 그 사원의 급여등급을 출력하라.
	~~~ sql
    SELECT E.ENAME, E.SAL, S.GRADE
	FROM EMP E, SALGRADE S
	WHERE E.SAL BETWEEN S.LOSAL AND S.HISAL;

	--Ansi JOIN
	SELECT ENAME, SAL, GRADE
	FROM EMP JOIN SALGRADE ON ( SAL BETWEEN LOSAL AND HISAL);
	~~~

	- Q2. 각 사원의 이름, 월급, 급여등급, 그가 속한 부서이름을 출력하라.
	~~~ sql
    SELECT E.ENAME, E.SAL, D.DNAME, S.GRADE
	FROM EMP E, DEPT D, SALGRADE S
	WHERE E.DEPTNO = D.DEPTNO AND E.SAL BETWEEN S.LOSAL AND S.HISAL;

	--Ansi JOIN
	SELECT ENAME, SAL, DNAME, GRADE
	FROM EMP JOIN DEPT USING(DEPTNO)
	JOIN SALGRADE ON (SAL BETWEEN LOSAL AND HISAL);
    ~~~

***

## 연습문제

1. 사원들의 이름, 부서번호, 부서이름 출력
	~~~ sql
	-- ORACLE
	SELECT E.ENAME, E.DEPTNO, D.DNAME
	FROM EMP E, DEPT D
	WHERE E.DEPTNO = D.DEPTNO;

	-- ANSI
	SELECT ENAME, DEPTNO, DNAME
	FROM EMP INNER JOIN DEPT USING(DEPTNO);
	~~~

2. DALLAS에서 근무하는 사원의 이름, 직위, 부서번호, 부서이름 출력
	~~~ sql
    -- ORACLE
	SELECT ENAME, JOB, D.DEPTNO, DNAME
	FROM EMP E, DEPT D
	WHERE E.DEPTNO = D.DEPTNO
	AND D.LOC = 'DALLAS';

	-- ANSI
	SELECT ENAME, JOB, DEPTNO, DNAME
	FROM EMP INNER JOIN DEPT USING(DEPTNO)
	WHERE LOC = 'DALLAS';
    ~~~

3. 이름에 ‘A’ 가 들어가는 사원들의 이름과 부서이름을 출력
	~~~ sql
    -- ORACLE
	SELECT ENAME, DNAME
	FROM EMP E, DEPT D
	WHERE E.DEPTNO = D.DEPTNO
	AND ENAME LIKE '%A%';

	-- ANSI
	SELECT ENAME, DNAME
    FROM EMP INNER JOIN DEPT USING(DEPTNO)
	WHERE ENAME LIKE '%A%';
~~~

4. 사원이름과 그 사원이 속한 부서의 부서명, 월급을 출력하는데 월급이 3000이상인 사원을 출력
	~~~ sql
	-- ORACLE
	SELECT ENAME, DNAME, SAL
    FROM EMP E, DEPT D
	WHERE E.DEPTNO = D.DEPTNO
	AND SAL >= 3000;

	-- ANSI
	SELECT ENAME, DNAME, SAL
	FROM EMP INNER JOIN DEPT USING(DEPTNO)
	WHERE SAL>= 3000;
	~~~

5. 직위가 ‘SALESMAN’ 인 사원들의 직위와 그 사원이름, 그 사원이 속한 부서 이름을 출력
	~~~ sql
    -- ORACLE
	SELECT JOB, ENAME, DNAME
	FROM EMP E, DEPT D
	WHERE E.DEPTNO =D.DEPTNO
	AND JOB = 'SALESMAN';

	-- ANSI
	SELECT JOB, ENAME, DNAME
	FROM EMP INNER JOIN DEPT USING(DEPTNO)
	WHERE JOB = 'SALESMAN';

    --- or
	SELECT JOB, ENAME, DNAME
	FROM EMP JOIN DEPT USING(DEPTNO)
	WHERE JOB = 'SALESMAN';
	~~~

6. 커미션이 책정된 사원들의 사원번호, 이름, 연봉, 연봉+커미션, 급여등급을 출력하되, 각각의 컬럼명을 ‘ 사원번호’, ‘사원이름’, ‘연봉’, ‘실급여’, ‘급여등급’ 으로 하여 출력하라.
	~~~ sql
    -- ORACLE
	SELECT EMPNO AS 사원번호, ENAME AS 사원이름, SAL*12 AS 연봉, (SAL+COMM)*12 AS 실급여, GRADE AS 급여등급
	FROM EMP, SALGRADE
	WHERE COMM IS NOT NULL
	AND SAL BETWEEN LOSAL AND HISAL;

	-- ANSI
	SELECT EMPNO AS 사원번호, ENAME AS 사원이름, SAL*12 AS 연봉, (SAL+COMM)*12 AS 실급여, GRADE AS 급여등급
	FROM EMP JOIN SALGRADE
	ON (SAL BETWEEN LOSAL AND HISAL)
	WHERE COMM IS NOT NULL;
    ~~~

7. 부서번호가 10번인 사원들의 부서번호, 부서이름, 사원이름, 월급, 급여등급을 출력하라
	~~~ sql
    -- ORACLE
	SELECT E.DEPTNO, DNAME, ENAME, SAL, GRADE
	FROM EMP E, DEPT D, SALGRADE S
    WHERE E.DEPTNO = D.DEPTNO
    AND SAL BETWEEN LOSAL AND HISAL
	AND E.DEPTNO = 10;

	-- ANSI
	SELECT DEPTNO, DNAME, ENAME, SAL, GRADE
	FROM EMP JOIN DEPT USING(DEPTNO)
	JOIN SALGRADE ON (SAL BETWEEN LOSAL AND HISAL)
	WHERE DEPTNO =10;
    ~~~

8. 부서번호 10번, 20번인 사원들의 부서번호, 부서이름, 사원이름, 월급, 급여등급을 출력,
출력된 결과물을 부서번호가 낮은 순으로, 월급이 높은 순으로 정렬
	~~~ sql
    -- ORACLE
	SELECT E.DEPTNO, DNAME, ENAME, SAL, GRADE
	FROM EMP E, DEPT D, SALGRADE S
	WHERE E.DEPTNO = D.DEPTNO
	AND SAL BETWEEN LOSAL AND HISAL
	AND E.DEPTNO IN(10,20)
	ORDER BY E.DEPTNO,SAL DESC;

	-- ANSI
	SELECT DEPTNO, DNAME, ENAME, SAL, GRADE
	FROM EMP JOIN DEPT USING(DEPTNO)
	JOIN SALGRADE ON (SAL BETWEEN LOSAL AND HISAL)
	WHERE DEPTNO IN(10,20)
	ORDER BY DEPTNO,SAL DESC;
	~~~

9. 사원번호와 사원이름, 그리고 그 사원을 관리하는 관리자의 사원번호와 사원이름을 출력하되 각각의 컬럼명을 ‘사원번호’, ‘사원이름’, ‘관리자번호’, ‘관리자이름’으로 하여 출력하라.
	~~~ sql
    -- ORACLE
    SELECT E1.EMPNO AS 사원번호, E1.ENAME AS 사원이름, E1.MGR AS 관리자번호, E2.ENAME AS 관리자이름
	FROM EMP E1, EMP E2
	WHERE E1.MGR = E2.EMPNO(+);

	-- ANSI
	SELECT E1.EMPNO AS 사원번호, E1.ENAME AS 사원이름, E1.MGR AS 관리자번호, E2.ENAME AS 관리자이름
	FROM EMP E1 LEFT OUTER JOIN EMP E2
	ON E1.MGR = E2.EMPNO;
    ~~~

10. 자신의 관리자보다 먼저 입사한 모든 사원의 이름 및 입사일을 해당 관리자의 이름 및 입사일과 함께 표시하고 열 이름을 각각 -- EMPLOYEE, EMP_HIREDATE, MANAGER, MGR_HIREDATE로 저장
    ~~~ sql
    -- ORACLE
	SELECT E1.ENAME  AS EMPLOYEE, E1.HIREDATE AS EMP_HIREDATE, E2.ENAME AS MANAGER, E2.HIREDATE AS MGR_HIREDATE
	FROM EMP E1, EMP E2
	WHERE E1.MGR = E2.EMPNO(+)
	AND E1.HIREDATE<E2.HIREDATE;

	-- ANSI
	SELECT E1.ENAME  AS EMPLOYEE, E1.HIREDATE AS EMP_HIREDATE, E2.ENAME AS MANAGER, E2.HIREDATE AS MGR_HIREDATE
	FROM EMP E1 LEFT OUTER JOIN EMP E2
	ON E1.MGR = E2.EMPNO
	WHERE E1.HIREDATE < E2.HIREDATE;
    ~~~

11. 해당 부서의 모든 사원에 대한 부서 이름, 위치, 사원 수 평균 급여를 -- 표시하는 정의를 작성한다. 열 이름을 각각 DNAME, LOC, NUMBER OF PEOPLE, SALARY로 한다.
	~~~ sql
    -- ORACLE
	SELECT D.DNAME AS DNAME, D.LOC AS LOC, COUNT(*) AS "NUMBER OF PEOPLE", 			AVG(E.SAL) AS SALARY
	FROM EMP E, DEPT D
	WHERE E.DEPTNO = D.DEPTNO GROUP BY D.DNAME, D.LOC;

	-- ANSI
	SELECT DNAME, LOC, COUNT(*) AS "NUMBER OF PEOPLE", AVG(SAL) AS SALARY
	FROM EMP JOIN DEPT USING(DEPTNO)
	GROUP BY DNAME, LOC;
    ~~~