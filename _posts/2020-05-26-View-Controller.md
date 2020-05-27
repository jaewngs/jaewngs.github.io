---
title:  "200526_View_Controller"
layout: post
---
#DAY 29

###[오늘 할 내용]

###  VO(Value Object) 클래스 생성
사용자가 입력한 값을 객체에 담을 자료형 [ 사번, 이름, 부서번호 ]
~~~ java
package com.vo;

// vo (value Object) : mvc 패턴에 값을 전달하는 역할 _ beans
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

### DAO 클래스
실행할 SQL문을 CRUD에 맞는 Method로 구성
~~~ java
package com.dao;

import java.sql.*;
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

	/// INSERT
	public int getInsertVO(MyEmpVO vo) {
		int res = 0;
		PreparedStatement pstm = null;

		try {
			pstm = conn.prepareStatement(myemp_insert); // 쿼리 호출
			// ? 매개인자에 값 대입
			pstm.setInt(1, vo.getEmpno());
			pstm.setString(2, vo.getName());
			pstm.setInt(3, vo.getDeptno());

			// 쿼리 실행
			res = pstm.executeUpdate();

			if (res > 0) {
				System.out.println("입력 성공했다");
				Commit(conn); // conn.commit();
			}
		} catch (Exception e) {
			System.out.println(e.toString());
			Rollback(conn);
		} finally {
			Close(pstm);
		}

		return res;
	}

	/// DELETE
	public int getDeleteEmpnoVO(MyEmpVO vo) {
		int res = 0; // 삭제 결과 저장할 변수
		PreparedStatement pstm = null; 
		// SQL문장에 ? 를 사용하고 있으니까
		// PreparedStatement 사용

		try {
			pstm = conn.prepareStatement(myemp_delete); // 쿼리 호출
			// ? 매개인자에 값 대입
			pstm.setInt(1, vo.getEmpno());

			// 삭제 실행 후 결과를 리턴
			// 쿼리 실행
			res = pstm.executeUpdate(); // insert, delete, update query

			if (res > 0) {
				System.out.println("삭제 성공했다");
				Commit(conn); // conn.commit();
			}
		} catch (Exception e) {
			System.out.println(e.toString());
			Rollback(conn);
		} finally {
			Close(pstm);
		}
		return res;
	} // end method

	public int getUpdateVO(MyEmpVO vo) {
		int res = 0; // 업데이트 결과 저장할 변수
		PreparedStatement pstm = null;

		try {
			pstm = conn.prepareStatement(myemp_update); // 쿼리 호출
			// ? 매개인자에 값 대입
			pstm.setString(1, vo.getName());
			pstm.setInt(2, vo.getDeptno());
			pstm.setInt(3, vo.getEmpno());
			// 삭제 실행 후 결과를 리턴
			// 쿼리 실행
			res = pstm.executeUpdate(); // insert, delete, update query

			if (res > 0) {
				System.out.println("업데이트 성공했다");
				Commit(conn); // conn.commit();
			} else {
				System.out.println("바꿀게 없음");
			}
		} catch (Exception e) {
			System.out.println(e.toString());
			Rollback(conn);
		} finally {
			Close(pstm);
		}
		return res;
	} // end method
} // end class
~~~

### DAO SQL
~~~ java
package com.dao;

/// INSERT, DELETE, UPDATE ///
public interface MyEmpSql {
	// Insert
	String myemp_insert = "INSERT INTO MY_EMP VALUES(?,?,?)";

	// EMPNO를 받아 삭제하자.
	String myemp_delete = "DELETE FROM MY_EMP WHERE EMPNO = ?";

	// EMPNO를 찾아서 ENAME과 DEPTNO를 수정하자.
	String myemp_update = "UPDATE MY_EMP SET ENAME = ?, DEPTNO = ? WHERE EMPNO = ?";
}
~~~

### BIZ 클래스
VO로 받은 검증된 데이터를 controller클래스로 받아서

1. 연산 후 처리  2. 연산한 결과를 DB[DAO] 로 리턴   3.  DB호출
View   <=====>   Controller   <=====>   Model(BIZ + DAO)

~~~java
package com.biz;

import java.sql.*;
import com.vo.*;
import com.dao.*;
import static common.JDBCTemplate.*;

// biz : 1. 계산 결과를 controller에게 리턴
//		 2. 계산 결과를 DAO로 연결해서 수행 결과를 controller로 리턴
//		 3. 계산 없으면 DAO를 호출한다. connection & close를 호출
public class MyEmpBIZ {
	public int getInsert(MyEmpVO vo) {
		Connection conn = getConnection();
		int res = new MyEmpDAO(conn).getInsertVO(vo);
		// close
		Close(conn);
		return res;
	}

	public int getDeleteEmpno(MyEmpVO vo) {
		Connection conn = getConnection();
		int res = new MyEmpDAO(conn).getDeleteEmpnoVO(vo);
		// close
		Close(conn);
		return res;
	}

	public int getUpdate(MyEmpVO vo) {
		Connection conn = getConnection();
		int res = new MyEmpDAO(conn).getUpdateVO(vo);
		// close
		Close(conn);
		return res;
	}
}
~~~

### JDBC_Connection
~~~java
package common;

import java.sql.*;

// Connection & Close & Commit & Rollback  
// 4개만 user가 제어할 수 있게 만들어둠
public class JDBCTemplate {
	// 연결한 상태로 리턴해주겠다.
	public static Connection getConnection() {
		try {
			Class.forName("oracle.jdbc.OracleDriver");
		} catch (ClassNotFoundException e) {
			e.printStackTrace();
		}

		String url = "jdbc:oracle:thin:@localhost:1521:XE";
		String id = "big5";
		String pwd = "admin1234";
		Connection con = null;

		try {
			con = DriverManager.getConnection(url, id, pwd);
			con.setAutoCommit(false);
		} catch (SQLException e) {
			e.printStackTrace();
		}
		return con;
	}

	// 연결 객체를 받아서(연결한 상태를) close()하겠다.
	public static void Close(Connection con) {
		try {
			if (!con.isClosed() && con != null) {
				con.close();
			}
		} catch (SQLException e) {
			e.printStackTrace();
		}
	}

	public static void Close(Statement stmt) {
		try {
			if (stmt != null) {
				stmt.close();
			}
		} catch (SQLException e) {
			e.printStackTrace();
		}
	}

	public static void Close(ResultSet rs) {
		try {
			if (rs != null) {
				rs.close();
			}
		} catch (SQLException e) {
			e.printStackTrace();
		}
	}

	public static void Commit(Connection con) {
		try {
			if (con != null && !con.isClosed()) {
				con.commit();
			}
		} catch (SQLException e) {
			e.printStackTrace();
		}
	}

	public static void Rollback(Connection con) {
		try {
			if (con != null && !con.isClosed()) {
				con.rollback();
			}
		} catch (SQLException e) {
			e.printStackTrace();
		}
	}
}

~~~

- - -


### EmpTest.java
model은 연산 및 데이터베이스에 데이터를 처리한 결과를 다시 controller한테 리턴하면 view를 선택해서 리턴한다.

~~~java
package com.view;

import java.util.Scanner;
import com.biz.MyEmpBIZ;
import com.vo.MyEmpVO;


public class EmpTest {
	public static void main(String[] args) {
		// 1. 데이터를 Scanner로 입력을 받자.
		Scanner sc = new Scanner(System.in);
		System.out.println("input empno : ");
		int empno = sc.nextInt();

		System.out.println("\n input ename : ");
		String name = sc.next();

		System.out.println("\n input deptno : ");
		int deptno = sc.nextInt();

		// 2. 입력받은 데이터를 VO에 저장한다.
		MyEmpVO vo = new MyEmpVO(empno, name, deptno);		
		// 3. view -> Controller에서  <-> Biz로 전송 <-> DAO 전송
		int res = new MyEmpBIZ().getInsert(vo);
		if( res > 0) {
			System.out.println("입력 성공 ㅎㅎㅎ");
		}
	}
}
~~~

### EmpTest01.java
~~~java
package com.view;

import java.util.Scanner;

import com.biz.MyEmpBIZ;
import com.vo.MyEmpVO;
//EMPNO를 받아 삭제하자.
public class EmpTest01 {
	public static void main(String[] args) {
		// 1. 삭제할 번호를 입력 받는다.
		int empno = 0;
		Scanner sc = new Scanner(System.in);
		System.out.println("input delno : ");
		empno = sc.nextInt();

		// 2. vo 객체에 저장한다.
		MyEmpVO vo = new MyEmpVO();
		vo.setEmpno(empno);

		// 3. vo -> biz에 보낸 후 결과를 리턴받는다.
		int res = new MyEmpBIZ().getDeleteEmpno(vo);
		// 4. 결과를 출력한다. '삭제 성공이야!';
		if (res > 0) {
			System.out.println("삭제 성공이야!");
		}
	}
}
~~~

### EmpTest02.java
~~~java
package com.view;

import java.util.Scanner;
import com.biz.MyEmpBIZ;
import com.vo.MyEmpVO;

//사원번호를 찾아서 이름과 부서번호를 수정하자.
//String myemp_update =
// "UPDATE MY_EMP SET ENAME = ?, DEPTNO = ? WHERE EMPNO = ?";

public class EmpTest02 {
	public static void main(String[] args) {
		// 1. 데이터를 Scanner로 입력을 받자.
		Scanner sc = new Scanner(System.in);

		System.out.println("find empno : ");
		int empno = sc.nextInt();

		System.out.println("update ename : ");
		String name = sc.next();

		System.out.println("update deptno : ");
		int deptno = sc.nextInt();

		// 2. 입력받은 데이터를 VO에 저장한다.
		MyEmpVO vo = new MyEmpVO();
		vo.setEmpno(empno);
		vo.setDeptno(deptno);
		vo.setName(name);

		// 3. view -> Controller에서 <-> Biz로 전송 <-> DAO 전송
		int res = new MyEmpBIZ().getUpdate(vo);
		if (res > 0) {
			System.out.println("업데이트 성공 ㅎㅎㅎ");
		}
	}
}
~~~













