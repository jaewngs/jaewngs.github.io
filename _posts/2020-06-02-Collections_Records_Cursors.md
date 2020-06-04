---

title:  "PL/SQL Collections and Records & Cursors"

layout: post

---

# DAY 34

## [오늘 할 내용]
1. 프로시져 복습 및 연습문제
2. PL/SQL Collections and Records
3. Cursors
4. Query Result Set Processing
5. Cursor Variables
6. CURSOR Expressions

## PROCEDURE 복습

ex1) SMITH 급여 출력 PROCEDURE 생성하기
~~~ sql
CREATE OR REPLACE PROCEDURE SP_SALARY
IS
V_SAL EMP.ENAME%TYPE;
BEGIN
SELECT SAL INTO V_SAL
FROM EMP
WHERE ENAME ='SMITH';
DBMS_OUTPUT.PUT_LINE('SMITH의 급여'||V_SAL);
END;
/
 ~~~
 
~~~ sql
-- 실행
EXEC SP_SALARY
~~~

~~~ sql
-- 프로시저 생성 확인
SELECT NAME, TEXT FROM USER_SOURCE
WHERE NAME LIKE('%SP_SAL%');
~~~

~~~ sql
-- 프로시저 제거하기
DROP PROCEDURE SP_SALARY;
~~~

ex2) IN 매개변수 사용하기
~~~ sql
CREATE PROCEDURE SP_SAL_ENAME(
V_ENAME IN EMP.ENAME%TYPE)
IS
V_SAL EMP.SAL%TYPE;
BEGIN
SELECT SAL INTO V_SAL
FROM EMP
WHERE ENAME = V_ENAME;
DBMS_OUTPUT.PUT_LINE(V_ENAME||'의 급여'|| V_SALARY);
END;
/
~~~

~~~ sql
-- 실행
EXEC SP_SAL_ENAME('KING');
~~~

ex3) OUT 매개변수 사용하기
~~~ sql
CREATE PROCEDURE SP_SAL_ENAME2(
V_ENAME IN EMP.ENAME%TYPE,
V_SAL OUT EMP.SAL%TYPE)
IS
BEGIN
SELECT SAL INTO V_SAL
FROM EMP
WHERE ENAME = V_ENAME;
END;
/

~~~

~~~ sql
-- 실행
VAR V_SAL VARCHAR2(14);  -- 변수 생성
EXEC SP_SAL_ENAME2('KING',:V_SAL); -- 프로시져 결과 V_SAL에 저장

PRINT V_SAL;  -- 출력
~~~

ex4) FUNCTION문 사용하기
~~~ sql
CREATE OR REPLACE FUNCTION FN_SAL_ENAME(
V_ENAME IN EMP.ENAME%TYPE)
RETURN NUMBER
IS
V_SAL NUMBER(7,2);
BEGIN
SELECT SAL INTO V_SAL
FROM EMP
WHERE ENAME = V_ENAME;
RETURN V_SAL;
END;
/
~~~

~~~ sql
-- 실행
VAR V_SAL NUMBER;  -- 변수 생성
EXEC :V_SAL := FN_SAL_ENAME('KING');  -- 함수 리턴 값 V_SAL에 저장

PRINT V_SAL; -- 출력
~~~

~~~ sql
-- 함수 호출하기
SELECT ENAME, FN_SAL_ENAME('KING')
FROM EMP
WHERE ENAME = 'KING';
~~~

## PROCEDURE 연습 문제
Q1. 사원테이블의 사원번호와 이름을 출력하자. EX_VIEW01
~~~ sql
-- PROCEDURE
CREATE OR REPLACE PROCEDURE EX_VIEW01
IS
V_EMPNO EMP.EMPNO%TYPE;
V_ENAME EMP.ENAME%TYPE;

CURSOR RES IS -- 1. 커서 선언 : 하나 이상의 row를 담을 객체
SELECT EMPNO, ENAME FROM EMP;

BEGIN -- 실제 실행 코드
OPEN RES; -- 2. 커서 시작
LOOP -- 한줄 이상 대입 변수 출력 , loop 구문 사용
FETCH RES INTO V_EMPNO,V_ENAME; -- 3. 변수를 대입

EXIT WHEN RES%NOTFOUND;
DBMS_OUTPUT.PUT_LINE(V_EMPNO || '  '||V_ENAME);
END LOOP;
CLOSE RES;
END EX_VIEW01;
/
~~~

~~~ sql
EXEC EX_VIEW01 -- 실행
~~~

~~~ sql
-- FUNCTION 
CREATE OR REPLACE FUNCTION FN_VIEW01
RETURN SYS_REFCURSOR
IS
MYRES SYS_REFCURSOR;
BEGIN
OPEN MYRES FOR
SELECT EMPNO, ENAME FROM EMP;
RETURN MYRES;
END FN_VIEW01;
/
~~~

~~~ sql
VAR MYRES REFCURSOR;
EXEC :MYRES := FN_VIEW01();

PRINT MYRES;
~~~



Q2. 사원테이블에서 사원의 이름과 연봉을 구해서 리턴하자. EX_VIEW02
~~~ sql
-- PROCEDURE 방법1
CREATE OR REPLACE PROCEDURE EX_VIEW02
IS
V_ENAME EMP.ENAME%TYPE;
V_SAL EMP.SAL%TYPE;

CURSOR RES IS -- 1. 커서 선언 : 하나 이상의 row를 담을 객체
SELECT ENAME,SAL*12 FROM EMP;

BEGIN -- 실제 실행 코드
OPEN RES; -- 2. 커서 시작
LOOP -- 한줄 이상 대입 변수 출력 , loop 구문 사용
FETCH RES INTO V_ENAME, V_SAL; -- 3. 변수를 대입

EXIT WHEN RES%NOTFOUND;
DBMS_OUTPUT.PUT_LINE(V_ENAME || '  '||V_SAL);
END LOOP;
CLOSE RES;
END EX_VIEW02;
/
~~~

~~~ sql
EXEC EX_VIEW02
~~~

~~~ sql
-- PROCEDURE 방법2
CREATE OR REPLACE PROCEDURE EX_VIEW02(myres out SYS_REFCURSOR)
IS
BEGIN
OPEN myres FOR
SELECT ENAME, SAL * 12 FROM EMP;
END EX_VIEW02;
/
~~~

~~~ sql
var my refcursor;
exec ex_view02(:my)
print my
~~~

~~~ sql
-- FUNCTION 
CREATE OR REPLACE FUNCTION FN_VIEW02
RETURN SYS_REFCURSOR
IS
MYRES SYS_REFCURSOR;
BEGIN
OPEN MYRES FOR
SELECT ENAME, SAL * 12 FROM EMP;
RETURN MYRES;
END FN_VIEW02;
/
~~~

~~~ sql
VAR MYRES REFCURSOR;
EXEC :MYRES := FN_VIEW02();

PRINT MYRES;
~~~


Q3. 사원의 이름을 입력 받아 봉급과 커미션을 리턴하자. EX_VIEW03
~~~ sql
-- PROCEDURE 방법1
CREATE OR REPLACE PROCEDURE EX_VIEW03(V_ENAME IN EMP.ENAME%TYPE)
IS
V_SAL EMP.SAL%TYPE;
V_COMM EMP.COMM%TYPE;

CURSOR RES IS -- 1. 커서 선언 : 하나 이상의 row를 담을 객체
SELECT SAL, NVL(COMM,0) FROM EMP WHERE ENAME = V_ENAME;

BEGIN
OPEN RES; -- 2. 커서 시작
LOOP -- 한줄 이상 대입 변수 출력 , loop 구문 사용
FETCH RES INTO V_SAL, V_COMM; -- 3. 변수를 대입

EXIT WHEN RES%NOTFOUND;
DBMS_OUTPUT.PUT_LINE(V_ENAME ||'의 봉급은 '||V_SAL|| ' 커미션은 ' || V_COMM);
END LOOP;
CLOSE RES;
END EX_VIEW03;
/
~~~

~~~ sql
EXEC EX_VIEW03('KING') -- 실행
EXEC EX_VIEW03('MARTIN')  -- 실행
~~~


~~~ sql
-- PROCEDURE 방법2
CREATE OR REPLACE PROCEDURE EX_VIEW03(MYNAME IN EMP.ENAME%TYPE, MYRES OUT SYS_REFCURSOR)
IS
BEGIN
OPEN MYRES FOR
SELECT SAL, NVL(COMM,0) FROM EMP WHERE ENAME = MYNAME;
END EX_VIEW03;
/
~~~

~~~ sql
-- 실행
var myres refcursor;
EXEC EX_VIEW03('KING', :MYRES)

PRINT MYRES;
~~~

~~~ sql
-- FUNCTION 
CREATE OR REPLACE FUNCTION FN_VIEW03(MYNAME IN EMP.ENAME%TYPE)
RETURN SYS_REFCURSOR
IS
MYRES SYS_REFCURSOR;
BEGIN
OPEN MYRES FOR
SELECT SAL, NVL(COMM,0) FROM EMP WHERE ENAME = MYNAME;
RETURN MYRES;
END;
/
~~~

~~~ sql
VAR MYRES REFCURSOR;
EXEC :MYRES := FN_VIEW03('KING');

PRINT MYRES;
~~~

Q4. 사원의 부서번호와 부서이름, 직업을 리턴하자. EX_VIEW04
~~~ sql
-- PROCEDURE 방법1
CREATE OR REPLACE PROCEDURE EX_VIEW04
IS
V_EMPNO EMP.EMPNO%TYPE;
V_DEPTNO DEPT.DEPTNO%TYPE;
V_DNAME DEPT.DNAME%TYPE;
V_JOB EMP.JOB%TYPE;

CURSOR RES IS -- 1. 커서 선언 : 하나 이상의 row를 담을 객체
SELECT E.EMPNO, D.DEPTNO, D.DNAME, E.JOB 
FROM EMP E, DEPT D 
WHERE E.DEPTNO = D.DEPTNO;

BEGIN -- 실제 실행 코드
OPEN RES; -- 2. 커서 시작
LOOP -- 한줄 이상 대입 변수 출력 , loop 구문 사용
FETCH RES INTO V_EMPNO,V_DEPTNO,V_DNAME,V_JOB; -- 3. 변수를 대입

EXIT WHEN RES%NOTFOUND;
DBMS_OUTPUT.PUT_LINE(V_EMPNO||' '||V_DEPTNO|| ' '||V_DNAME|| ' '||V_JOB);
END LOOP;
CLOSE RES;
END EX_VIEW04;
/
~~~

~~~ sql
EXEC EX_VIEW04 -- 실행
~~~

~~~ sql
-- PROCEDURE 방법2
CREATE OR REPLACE PROCEDURE EX_VIEW04(MYRES OUT SYS_REFCURSOR)
IS
BEGIN
OPEN MYRES FOR
SELECT DEPTNO, DNAME, JOB FROM EMP JOIN DEPT USING(DEPTNO);
END EX_VIEW04;
/
~~~

~~~ sql
var M_RES refcursor;
EXEC EX_VIEW04(:M_RES)

PRINT M_RES
~~~

~~~ sql
-- FUNCTION 
CREATE OR REPLACE FUNCTION FN_VIEW04
RETURN SYS_REFCURSOR
IS
MYRES SYS_REFCURSOR;
BEGIN
OPEN MYRES FOR
SELECT DEPTNO, DNAME, JOB FROM EMP JOIN DEPT USING(DEPTNO);
RETURN MYRES;
END FN_VIEW04;
/
~~~

~~~ sql
VAR MYRES REFCURSOR;
EXEC :MYRES := FN_VIEW04();
EXEC :MYRES := FN_VIEW04; -- 둘다 가능

PRINT MYRES;
~~~


Q5. 사원의 번호를 입력 받아 부서 이름, 직업, 봉급을 리턴하자. EX_VIEW05
~~~ sql
-- PROCEDURE 방법1
CREATE OR REPLACE PROCEDURE EX_VIEW05(V_EMPNO IN EMP.EMPNO%TYPE)
IS
V_DNAME DEPT.DNAME%TYPE;
V_JOB EMP.JOB%TYPE;
V_SAL EMP.SAL%TYPE;

CURSOR RES IS -- 1. 커서 선언 : 하나 이상의 row를 담을 객체
SELECT D.DNAME, E.JOB, E.SAL 
FROM EMP E, DEPT D 
WHERE E.DEPTNO = D.DEPTNO AND E.EMPNO = V_EMPNO;

BEGIN
OPEN RES; -- 2. 커서 시작
LOOP -- 한줄 이상 대입 변수 출력 , loop 구문 사용
FETCH RES INTO V_DNAME, V_JOB, V_SAL; -- 3. 변수를 대입

EXIT WHEN RES%NOTFOUND;
DBMS_OUTPUT.PUT_LINE(V_EMPNO || ' ' || '부서 ' || V_DNAME || ' 직업 ' || V_JOB || '봉급은 ' || V_SAL);
END LOOP;
CLOSE RES;
END EX_VIEW05;
/

~~~ sql
EXEC EX_VIEW05(7902) -- 실행
~~~

~~~ sql
-- PROCEDURE 방법2
CREATE OR REPLACE PROCEDURE EX_VIEW05(MYNO IN EMP.EMPNO%TYPE, MYRES OUT SYS_REFCURSOR)
IS
BEGIN
OPEN MYRES FOR
SELECT DNAME, JOB, SAL FROM EMP JOIN DEPT USING(DEPTNO) WHERE EMPNO = MYNO;
END EX_VIEW05;
/
~~~

~~~ sql
var myres02 refcursor;
EXEC EX_VIEW05(7902, :myres02)

print myres02
~~~

~~~ sql
-- FUNCTION 
CREATE OR REPLACE FUNCTION FN_VIEW05(MYNO IN EMP.EMPNO%TYPE)
RETURN SYS_REFCURSOR
IS
MYRES SYS_REFCURSOR;
BEGIN
OPEN MYRES FOR
SELECT DNAME, JOB, SAL FROM EMP JOIN DEPT USING(DEPTNO) WHERE EMPNO = MYNO;
RETURN MYRES;
END;
/
~~~

~~~ sql
VAR MYRES REFCURSOR;
EXEC :MYRES := FN_VIEW05(7902);

PRINT MYRES;
~~~

## PL/SQL Collections and Records

### PACKAGE
<https://docs.oracle.com/cd/E11882_01/appdev.112/e25519/composites.htm#LNPLS99856>

### [5-33] Declaring Record Constant
~~~ sql
-- 패키지 생성
CREATE OR REPLACE PACKAGE My_Types AUTHID DEFINER IS
  TYPE My_Rec IS RECORD (a NUMBER, b NUMBER, name varchar2(10)); -- 레코드 
  FUNCTION Init_My_Rec RETURN My_Rec; -- FUNCTION 
END My_Types;
/

CREATE OR REPLACE PACKAGE BODY My_Types IS
  FUNCTION Init_My_Rec RETURN My_Rec IS
    Rec My_Rec;  -- My_Rec 타입의 Rec
  BEGIN
    Rec.a := 0;
    Rec.b := 1;
    Rec.name := 'aaaaa';
    RETURN Rec;
  END Init_My_Rec;
END My_Types;
/

DECLARE
  r CONSTANT My_Types.My_Rec := My_Types.Init_My_Rec();
BEGIN
  DBMS_OUTPUT.PUT_LINE('r.a = ' || r.a);
  DBMS_OUTPUT.PUT_LINE('r.b = ' || r.b);
  DBMS_OUTPUT.PUT_LINE('r.name = ' || r.name);
END;
/
~~~

![image](https://user-images.githubusercontent.com/52989294/83479959-fcd41180-a4d4-11ea-89ea-ec29b6104979.png)

***


## RECORD TYPE
### [5-34] RECORD Type Definition and Variable Declarations
~~~ sql
DECLARE
  TYPE DeptRecTyp IS RECORD (
    dept_id    NUMBER(4) NOT NULL := 10,
    dept_name  VARCHAR2(30) NOT NULL := 'Administration',
    mgr_id     NUMBER(6) := 200,
    loc_id     NUMBER(4)
  );
 
  dept_rec DeptRecTyp; -- DeptRecTyp 타입의 dept_rec 생성
  dept_rec_2 dept_rec%TYPE;  -- dept_rec 타입의 dept_rec_2 생성
  
BEGIN
  DBMS_OUTPUT.PUT_LINE('dept_rec:');
  DBMS_OUTPUT.PUT_LINE('---------');
  DBMS_OUTPUT.PUT_LINE('dept_id:   ' || dept_rec.dept_id);
  DBMS_OUTPUT.PUT_LINE('dept_name: ' || dept_rec.dept_name);
  DBMS_OUTPUT.PUT_LINE('mgr_id:    ' || dept_rec.mgr_id);
  DBMS_OUTPUT.PUT_LINE('loc_id:    ' || dept_rec.loc_id);
 
  DBMS_OUTPUT.PUT_LINE('-----------');
  DBMS_OUTPUT.PUT_LINE('dept_rec_2:');
  DBMS_OUTPUT.PUT_LINE('-----------');
  DBMS_OUTPUT.PUT_LINE('dept_id:   ' || dept_rec_2.dept_id);
  DBMS_OUTPUT.PUT_LINE('dept_name: ' || dept_rec_2.dept_name);
  DBMS_OUTPUT.PUT_LINE('mgr_id:    ' || dept_rec_2.mgr_id);
  DBMS_OUTPUT.PUT_LINE('loc_id:    ' || dept_rec_2.loc_id);
END;
/
~~~

![image](https://user-images.githubusercontent.com/52989294/83480163-97cceb80-a4d5-11ea-8903-3b9c81710345.png)

### [5-35] RECORD Type with RECORD Field (Nested Record)
~~~ sql
DECLARE
  TYPE name_rec IS RECORD (
  	FIRST EMP.ENAME%TYPE,
    LAST EMP.ENAME%TYPE
  );
 
  TYPE contact IS RECORD (
  	NAME NAME_REC,   -- nested record
    EMP_NO EMP.EMPNO%TYPE
   );
   
  FRIEND CONTACT;  -- contact 타입의 friend 생성
  
BEGIN
  FRIEND.NAME.FIRST := 'JOHN';
  FRIEND.NAME.LAST := 'SMITH';
  FRIEND.EMP_NO := 1234;
  
  DBMS_OUTPUT.PUT_LINE (
    FRIEND.NAME.FIRST  || ' ' ||
    FRIEND.NAME.LAST   || ', ' ||
    FRIEND.EMP_NO
  );
END;
/
~~~ 

![image](https://user-images.githubusercontent.com/52989294/83480326-f4300b00-a4d5-11ea-8ac0-9d267a065f38.png)

### [5-36] RECORD Type with Varray Field
~~~ sql
DECLARE
  TYPE FULL_NAME IS VARRAY(2) OF VARCHAR2(20);
 
  TYPE CONTACT IS RECORD (
    NAME FULL_NAME := FULL_NAME('John', 'Smith'),  -- varray field
    EMP_NO EMP.EMPNO%TYPE
  );
 
  FRIEND CONTACT;
BEGIN
  FRIEND.EMP_NO := 1234;
  
  DBMS_OUTPUT.PUT_LINE (
    FRIEND.NAME(1) || ' ' ||  FRIEND.NAME(2) || ', ' || FRIEND.EMP_NO 
  );
END;
/
~~~ 

![image](https://user-images.githubusercontent.com/52989294/83480493-820bf600-a4d6-11ea-9c20-5c8b7683554a.png)


### [5-37] Identically Defined Package and Local RECORD Types
~~~ sql
CREATE OR REPLACE PACKAGE pkg AS
  TYPE rec_type IS RECORD (       -- package RECORD type
    f1 INTEGER,
    f2 VARCHAR2(4)
  );
  PROCEDURE print_rec_type (rec rec_type);
END pkg;
/

CREATE OR REPLACE PACKAGE BODY pkg AS
  PROCEDURE print_rec_type (rec rec_type) IS
  BEGIN
    DBMS_OUTPUT.PUT_LINE(rec.f1);
    DBMS_OUTPUT.PUT_LINE(rec.f2);
  END;
END pkg;
/

DECLARE
  TYPE rec_type IS RECORD (       -- local RECORD type
    f1 INTEGER,
    f2 VARCHAR2(4)
  );
  r1 pkg.rec_type;                -- package type
  r2     rec_type;                -- local type
 
BEGIN
  r1.f1 := 10; r1.f2 := 'abcd';
  r2.f1 := 25; r2.f2 := 'wxyz';
 
  pkg.print_rec_type(r1);  -- succeeds
  pkg.print_rec_type(r2);  -- fails
END;
/
~~~ 
![image](https://user-images.githubusercontent.com/52989294/83480842-4faec880-a4d7-11ea-9c2f-df88e545104e.png)
**에러 발생**

![image](https://user-images.githubusercontent.com/52989294/83481020-af0cd880-a4d7-11ea-8b65-3f03f3944afe.png)

그림처럼 수정해서 실행시키기

***

## %ROWTYPE Attribute
### [5-38] %ROWTYPE Variable Represents Full Database Table Row
~~~ sql
DECLARE
  dept_rec departments%ROWTYPE;
BEGIN
  -- Assign values to fields:
  
  dept_rec.department_id   := 10;
  dept_rec.department_name := 'Administration';
  dept_rec.manager_id      := 200;
  dept_rec.location_id     := 1700;
 
  -- Print fields:
 
  DBMS_OUTPUT.PUT_LINE('dept_id:   ' || dept_rec.department_id);
  DBMS_OUTPUT.PUT_LINE('dept_name: ' || dept_rec.department_name);
  DBMS_OUTPUT.PUT_LINE('mgr_id:    ' || dept_rec.manager_id);
  DBMS_OUTPUT.PUT_LINE('loc_id:    ' || dept_rec.location_id);
END;
/
~~~ 

~~~ sql
DECLARE
  EMP_RES EMP%ROWTYPE;

BEGIN
  EMP_RES.EMPNO := 111;
  EMP_RES.ENAME := 'AAA';
  EMP_RES.JOB := 'BBB';
  EMP_RES.MGR := 7839;
  EMP_RES.HIREDATE := '20/04/28';
  EMP_RES.SAL := 1000;
  EMP_RES.COMM  := 100;
  EMP_RES.DEPTNO := 10;

DBMS_OUTPUT.PUT_LINE(EMP_RES.EMPNO || ' ' ||EMP_RES.ENAME || ' ' || EMP_RES.JOB || ' ' || EMP_RES.MGR || ' ' || EMP_RES.HIREDATE || ' ' || EMP_RES.SAL || ' ' || EMP_RES.COMM || ' ' || EMP_RES.DEPTNO);
  
END;
/
~~~

![image](https://user-images.githubusercontent.com/52989294/83507368-6ec34f80-a503-11ea-9457-06beb50e2ca3.png)

### [5-39] %ROWTYPE Variable Does Not Inherit Initial Values or Constraints
~~~ sql
DROP TABLE T1;

CREATE TABLE T1(
C1 INTEGER DEFAULT 0 NOT NULL,
C2 INTEGER DEFAULT 1 NOT NULL
);

DECLARE 
T1_ROW T1%ROWTYPE;
BEGIN
DBMS_OUTPUT.PUT_LINE('T1.C1 = ' || T1_ROW.C1);
DBMS_OUTPUT.PUT_LINE('T1.C2 = ' || T1_ROW.C2);
END;
/
~~~ 
![image](https://user-images.githubusercontent.com/52989294/83508706-463c5500-a505-11ea-8f6b-69ba685986b4.png)

값을 입력하지 않았기 때문에 둘다 NULL 값 출력

### [5-40] %ROWTYPE Variable Represents Partial Database Table Row
~~~ sql
DECLARE
  CURSOR C IS
    SELECT EMPNO, ENAME, JOB
    FROM EMP;
 
  friend C%ROWTYPE;
BEGIN
  friend.EMPNO := 1234;
  friend.ENAME   := 'John';
  friend.JOB    := 'PRESIDENT';
  
  
  DBMS_OUTPUT.PUT_LINE (
    friend.EMPNO  || ' ' ||
    friend.ENAME   || ' ' ||
    friend.JOB
  );
END;
/
~~~
![image](https://user-images.githubusercontent.com/52989294/83509195-088bfc00-a506-11ea-8ba2-407637a8d20c.png)

***

<https://docs.oracle.com/cd/E11882_01/appdev.112/e25519/composites.htm#LNPLS00510>

### [5-44] Assigning Record to Another Record of Same RECORD Type
~~~ sql
DECLARE
  TYPE name_rec IS RECORD (
    NAME  EMP.ENAME%TYPE DEFAULT 'AAA',
    JOB   EMP.JOB%TYPE DEFAULT 'KING'
  );
 
  SARAM1 name_rec;
  SARAM2 name_rec;
 
BEGIN
  SARAM1.NAME := 'MOON'; SARAM1.JOB := 'PRESIDENT'; 

  DBMS_OUTPUT.PUT_LINE('SARAM1: ' || SARAM1.NAME || ' ' || SARAM1.JOB);
  SARAM2 := SARAM1;
  DBMS_OUTPUT.PUT_LINE('SARAM2: ' || SARAM2.NAME || ' ' || SARAM2.JOB); 
END;
/
~~~
![image](https://user-images.githubusercontent.com/52989294/83509693-c9aa7600-a506-11ea-82bb-364cdf15167b.png)

### [5-45] Assigning %ROWTYPE Record to RECORD Type Record
~~~ sql
DECLARE
  TYPE name_rec IS RECORD (
    first  EMP.ENAME%TYPE DEFAULT 'John',
    last   EMP.JOB%TYPE DEFAULT 'NO_JOB'
  );
 
  CURSOR c IS
    SELECT ENAME, JOB
    FROM EMP;
 
  target name_rec;
  source c%ROWTYPE;
 
BEGIN
  source.ENAME := 'Jane'; source.JOB := 'PRESIDENT';
 
  DBMS_OUTPUT.PUT_LINE (
    'source: ' || source.ENAME || ' ' || source.JOB
  );
 
 target := source;
 
 DBMS_OUTPUT.PUT_LINE (
   'target: ' || target.first || ' ' || target.last
 );
END;
/
~~~

![image](https://user-images.githubusercontent.com/52989294/83510053-55240700-a507-11ea-96c6-1c72b8e066e3.png)

***
<https://docs.oracle.com/cd/E11882_01/appdev.112/e25519/composites.htm#LNPLS99909>

### [5-47] SELECT INTO Assigns Values to Record Variable
~~~ sql
DECLARE
  TYPE RecordTyp IS RECORD (
    NAME EMP.ENAME%TYPE,
    JOB   EMP.JOB%TYPE
  );
  rec1 RecordTyp;
BEGIN
  SELECT ENAME, JOB INTO rec1
  FROM EMP
  WHERE EMPNO = 7900;

  DBMS_OUTPUT.PUT_LINE ('Employee 7900 ' || rec1.NAME || ' is '|| rec1.JOB);
END;
/
~~~

![image](https://user-images.githubusercontent.com/52989294/83510498-117dcd00-a508-11ea-92ee-d701b77f93e1.png)



### [5-48] FETCH Assigns Values to Record that Function Returns
~~~ sql
DECLARE
  TYPE EmpRecTyp IS RECORD (
    emp_id  EMP.EMPNO%TYPE,
    salary  EMP.SAL%TYPE
  );
 
  CURSOR desc_salary RETURN EmpRecTyp IS
    SELECT EMPNO, SAL
    FROM EMP
    ORDER BY SAL DESC;
 
  highest_paid_emp       EmpRecTyp;
  next_highest_paid_emp  EmpRecTyp;
 
  FUNCTION nth_highest_salary (n INTEGER) RETURN EmpRecTyp IS
    emp_rec  EmpRecTyp;
  BEGIN
    OPEN desc_salary;
    FOR i IN 1..n LOOP
      FETCH desc_salary INTO emp_rec;
    END LOOP;
    CLOSE desc_salary;
    RETURN emp_rec;
  END nth_highest_salary;
 
BEGIN
  highest_paid_emp := nth_highest_salary(1);
  next_highest_paid_emp := nth_highest_salary(2);
 
  DBMS_OUTPUT.PUT_LINE(
    'Highest Paid: #' ||
    highest_paid_emp.emp_id || ', $' ||
    highest_paid_emp.salary 
  );
  DBMS_OUTPUT.PUT_LINE(
    'Next Highest Paid: #' ||
    next_highest_paid_emp.emp_id || ', $' ||
    next_highest_paid_emp.salary 
  );
END;
/
~~~
![image](https://user-images.githubusercontent.com/52989294/83510894-b8faff80-a508-11ea-98e6-3da1f2425a5e.png)

### [5-49] UPDATE Statement Assigns Values to Record Variable
~~~ sql
DECLARE
  TYPE EmpRec IS RECORD (
    ENAME  RES_EMP.ENAME%TYPE,
    SAL     RES_EMP.SAL%TYPE
  );
  emp_info    EmpRec;
  old_salary  RES_EMP.SAL%TYPE;
BEGIN
  SELECT SAL INTO old_salary
   FROM RES_EMP
   WHERE EMPNO = 7900;
 
  UPDATE RES_EMP
    SET SAL = SAL * 1.1
    WHERE EMPNO = 7900
    RETURNING ENAME, SAL INTO emp_info;
 
  DBMS_OUTPUT.PUT_LINE (
    'Salary of ' || emp_info.ENAME || ' raised from ' ||
    old_salary || ' to ' || emp_info.SAL
  );
END;
/
~~~

~~~ sql
SELECT * FROM RES_EMP WHERE EMPNO = 7900;
~~~

***
<https://docs.oracle.com/cd/E11882_01/appdev.112/e25519/composites.htm#LNPLS460>

### [5-50]
~~~ sql
DECLARE
  TYPE age_rec IS RECORD (
    years  INTEGER DEFAULT 35,
    months INTEGER DEFAULT 6
  );
 
  TYPE name_rec IS RECORD (
    first  EMP.ENAME%TYPE DEFAULT 'John',
    last   EMP.JOB%TYPE DEFAULT 'CLERK',
    age    age_rec
  );
 
  name name_rec;
 
  PROCEDURE print_name AS
  BEGIN
    DBMS_OUTPUT.PUT(NVL(name.first, 'NULL') || ' '); 
    DBMS_OUTPUT.PUT(NVL(name.last,  'NULL') || ', ');
    DBMS_OUTPUT.PUT(NVL(TO_CHAR(name.age.years), 'NULL') || ' yrs ');
    DBMS_OUTPUT.PUT_LINE(NVL(TO_CHAR(name.age.months), 'NULL') || ' mos');
  END;
 
BEGIN
  print_name;
  name := NULL;
  print_name;
END;
/
~~~
![image](https://user-images.githubusercontent.com/52989294/83581218-2f374a80-a579-11ea-9801-377e0d9f412f.png)

***

## Record Comparisons

### [5-51]
~~~ sql
DROP TABLE schedule;

-- 테이블 생성
CREATE TABLE schedule (
  week  NUMBER,
  Mon   VARCHAR2(10),
  Tue   VARCHAR2(10),
  Wed   VARCHAR2(10),
  Thu   VARCHAR2(10),
  Fri   VARCHAR2(10),
  Sat   VARCHAR2(10),
  Sun   VARCHAR2(10)
);
 
DECLARE
  default_week  schedule%ROWTYPE;
  i             NUMBER;
BEGIN
  default_week.Mon := '0800-1700';
  default_week.Tue := '0800-1700';
  default_week.Wed := '0800-1700';
  default_week.Thu := '0800-1700';
  default_week.Fri := '0800-1700';
  default_week.Sat := 'Day Off';
  default_week.Sun := 'Day Off';
 
  FOR i IN 1..6 LOOP
    default_week.week    := i;
    
    INSERT INTO schedule VALUES default_week;
  END LOOP;
END;
/
~~~

~~~ sql 
COLUMN week FORMAT 99
COLUMN Mon  FORMAT A9
COLUMN Tue  FORMAT A9
COLUMN Wed  FORMAT A9
COLUMN Thu  FORMAT A9
COLUMN Fri  FORMAT A9
COLUMN Sat  FORMAT A9
COLUMN Sun  FORMAT A9
 
SELECT * FROM schedule;
~~~

### [5-52]
**5-51 테이블 활용함**
~~~ sql
DECLARE
  default_week  schedule%ROWTYPE;
BEGIN
  default_week.Mon := 'Day Off';
  default_week.Tue := '0900-1800';
  default_week.Wed := '0900-1800';
  default_week.Thu := '0900-1800';
  default_week.Fri := '0900-1800';
  default_week.Sat := '0900-1800';
  default_week.Sun := 'Day Off';
 
  FOR i IN 1..3 LOOP
    default_week.week    := i;
  
    UPDATE schedule
    SET ROW = default_week
    WHERE week = i;
  END LOOP;
END;
/
~~~

~~~ sql
SELECT * FROM schedule;
~~~

![image](https://user-images.githubusercontent.com/52989294/83705504-19e11f80-a650-11ea-8fc9-b6e023d13f01.png)

Q6. 사원테이블 RES_EMP 테이블에 %ROWTYPE을 이용해서 값을 입력해보자.
~~~ sql
CREATE TABLE RES_EMP
AS
SELECT * FROM EMP
WHERE 0=1;

DECLARE
MY_EMP RES_EMP%ROWTYPE;
BEGIN
FOR I IN 1..5 LOOP
MY_EMP.EMPNO := I;
MY_EMP.ENAME := I ||'님';

INSERT INTO RES_EMP VALUES MY_EMP;

END LOOP;
END;
/
~~~

~~~ sql
SELECT * FROM RES_EMP; -- 결과확인하기
~~~

Q7. 사원테이블 RES_EMP테이블에 %ROWTYPE을 이용해서 값을 수정해보자.
~~~ sql

DECLARE
MY_EMP RES_EMP%ROWTYPE;
BEGIN

MY_EMP.SAL := 1000;
MY_EMP.JOB := 'KING';
MY_EMP.DEPTNO := 30;

FOR I IN 1..5 LOOP
UPDATE RES_EMP -- RES_EMP를 업데이트하자
SET ROW = MY_EMP
WHERE EMPNO = I;
END LOOP;
END;
/
~~~

~~~ sql
SELECT * FROM RES_EMP; -- 결과확인하기
~~~

***

## Description of Static SQL

<https://docs.oracle.com/cd/E11882_01/appdev.112/e25519/static.htm#LNPLS006>

### [6-1]
~~~ sql
DROP TABLE employees_temp;

CREATE TABLE employees_temp AS
  SELECT EMPNO, ENAME, JOB 
  FROM EMP;
 
DECLARE
  emp_id          employees_temp.EMPNO%TYPE := 299;
  emp_NAME  employees_temp.ENAME%TYPE  := 'Bob';
  emp_JOB   employees_temp.JOB%TYPE   := 'CLERK';
BEGIN
  -- INSERT
  INSERT INTO employees_temp (EMPNO, ENAME, JOB) 
  VALUES (emp_id, emp_NAME, emp_JOB);
  
  -- UPDATE 
  UPDATE employees_temp
  SET ENAME = 'Robert'
  WHERE EMPNO = emp_id;
 
  -- DELETE
  DELETE FROM employees_temp
  WHERE EMPNO = emp_id
  RETURNING ENAME, JOB
  INTO emp_NAME, emp_JOB;
 
  COMMIT;
  DBMS_OUTPUT.PUT_LINE (emp_NAME || '  ' || emp_JOB);
END;
/
~~~

### [6-2]
~~~ sql

~~~

Q8. SQL% 속성을 이용해서 익명 속성 활용을 해보자.[6-3] 활용
~~~ sql
CREATE OR REPLACE PROCEDURE p (
  dept_no NUMBER
) AUTHID DEFINER AS
BEGIN
  DELETE FROM RES_EMP
  WHERE DEPTNO = dept_no;
 
  IF SQL%FOUND THEN
    DBMS_OUTPUT.PUT_LINE (
      'Delete succeeded for department number ' || dept_no
    );
  ELSE
    DBMS_OUTPUT.PUT_LINE ('No department number ' || dept_no);
  END IF;
END;
/
~~~

~~~ sql
BEGIN
  p(30);
  p(50);
END;
/
~~~

![image](https://user-images.githubusercontent.com/52989294/83484561-2b0b1e80-a4e0-11ea-8492-3e8be07e345c.png)

***

## Cursors
### Explicit Cursors
<https://docs.oracle.com/cd/E11882_01/appdev.112/e25519/static.htm#LNPLS99956>

### [6-5]
~~~ sql
DECLARE
  CURSOR c1 RETURN departments%ROWTYPE;    -- Declare c1
 
  CURSOR c2 IS                             -- Declare and define c2
    SELECT employee_id, job_id, salary FROM employees
    WHERE salary > 2000; 
 
  CURSOR c1 RETURN departments%ROWTYPE IS  -- Define c1,
    SELECT * FROM departments              -- repeating return type
    WHERE department_id = 110;
 
  CURSOR c3 RETURN locations%ROWTYPE;      -- Declare c3
 
  CURSOR c3 IS                             -- Define c3,
    SELECT * FROM locations                -- omitting return type
    WHERE country_id = 'JP';
BEGIN
  NULL;  -- 내용 없을 때 NULL로 채우기
END;
/
~~~

### [6-6]
~~~ sql

~~~

### [6-10] Explicit Cursor with Virtual Column that Needs Alias
반드시 별칭을 줘야할 때
~~~ sql

~~~

### [6-11] 
~~~ sql

~~~

### [6-12]
기본값 줄 수 있다.
~~~ sql

~~~

### [6-15]
~~~ sql

~~~

***

## Query Result Set Processing

### [6-18]
~~~ sql

~~~

### [6-19]
~~~ sql

~~~


### [6-21]
~~~ sql
-- 가상 컬럼 호출 가능하다.

~~~


Q9. 인라인 뷰를 탐색할 수 있다. [6-22]
~~~ sql
DECLARE
  CURSOR c1 IS
    SELECT t1.DEPTNO, DNAME, staff
    FROM DEPT t1,
         ( SELECT DEPTNO, COUNT(*) AS staff
           FROM EMP
           GROUP BY DEPTNO
         ) t2
    WHERE (t1.DEPTNO = t2.DEPTNO) AND staff >= 3
    ORDER BY staff;

BEGIN
   FOR DEPT_RES IN c1
   LOOP
     DBMS_OUTPUT.PUT_LINE ('Department = '
       || DEPT_RES.DNAME || ', staff = ' || DEPT_RES.staff);
   END LOOP;
END;
/
~~~
![image](https://user-images.githubusercontent.com/52989294/83487452-04e87d00-a4e6-11ea-9cb3-d9b32e6031d6.png)

### [6-23]
~~~ sql

~~~

***

## Cursor Variables

### [6-24] Q5 연관
~~~ sql

~~~

Q10. 사용자 RECORD를 선언하고 커서로 참조시켜 참조시킨 커서를 변수로 선언하여 사용자 레코드 멤버에게 값을 전달 후 출력해보자.[6-25] Q5 연관

~~~ sql
-- 방법 1
DECLARE
  TYPE EmpRecTyp IS RECORD (
    employee_id NUMBER,
    last_name VARCHAR2(25),
    salary   NUMBER(8,2)); 
    
  TYPE EmpCurTyp IS REF CURSOR RETURN EmpRecTyp; 
  emp_cv EmpCurTyp; 
   v_EmpRecTyp emp_cv%ROWTYPE;
  
BEGIN
  open emp_cv for select empno, ename, sal from emp;
  loop 
  fetch emp_cv into v_EmpRecTyp;
  exit when emp_cv%notfound;
  dbms_output.put_line(v_EmpRecTyp.employee_id||' '||v_EmpRecTyp.last_name||' '||v_EmpRecTyp.salary);
  end loop;
END;
/
~~~

~~~ sql
-- 방법 2
DECLARE
  TYPE EmpRecTyp IS RECORD (
    employee_id NUMBER,
    last_name VARCHAR2(25),
    salary   NUMBER(8,2)); 
    
  TYPE EmpCurTyp IS REF CURSOR RETURN EmpRecTyp; 
  emp_cv EmpCurTyp; 
   v_emp emp%ROWTYPE;
  
BEGIN
  open emp_cv for select empno, ename, sal from emp;
  loop 
  fetch emp_cv into v_emp.empno,v_emp.ename,v_emp.sal;
  exit when emp_cv%notfound;
  dbms_output.put_line(v_emp.empno||' '||v_emp.ename||' '||v_emp.sal);
  end loop;
END;
/
~~~

![image](https://user-images.githubusercontent.com/52989294/83497339-46ccef80-a4f5-11ea-9871-10eebec46889.png)


### [6-26]
~~~ sql

~~~

### [6-27] 중요함
~~~ sql

~~~

### [6-28]
~~~ sql
DECLARE
  sal           emp.sal%TYPE;
  sal_multiple  emp.sal%TYPE;
  factor        INTEGER := 2;
 
  cv SYS_REFCURSOR;
 
BEGIN
  OPEN cv FOR
    SELECT sal, sal*factor
    FROM emp
    WHERE job LIKE 'CL%';  -- PL/SQL evaluates factor
 
  LOOP
    FETCH cv INTO sal, sal_multiple;
    EXIT WHEN cv%NOTFOUND;
    DBMS_OUTPUT.PUT_LINE('factor = ' || factor);
    DBMS_OUTPUT.PUT_LINE('sal          = ' || sal);
    DBMS_OUTPUT.PUT_LINE('sal_multiple = ' || sal_multiple);
    factor := factor + 1;  -- Does not affect sal_multiple
  END LOOP;
 
  CLOSE cv;
END;
/
~~~

### [6-30]
~~~ sql

~~~

### [6-31] 리턴타입이 같을 때
~~~ sql

~~~


### [6-32] 리턴 타입이 다를 때
~~~ sql

~~~


### [6-33]
~~~ sql

~~~

***

## CURSOR Expressions

### [6-34]
~~~ sql

~~~













