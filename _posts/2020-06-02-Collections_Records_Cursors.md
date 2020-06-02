---

title:  "PL/SQL Collections and Records & Cursors"

layout: post

---

# DAY 34
![image](https://user-images.githubusercontent.com/52989294/83466008-bff62380-a4b0-11ea-9c56-932cb79b42fa.png)
테스트 용 이미지

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
CREATE OR REPLACE PACKAGE My_Types AUTHID DEFINER IS
  TYPE My_Rec IS RECORD (a NUMBER, b NUMBER, name varchar2(10));
  FUNCTION Init_My_Rec RETURN My_Rec;
END My_Types;
/

CREATE OR REPLACE PACKAGE BODY My_Types IS
  FUNCTION Init_My_Rec RETURN My_Rec IS
    Rec My_Rec;
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
 
  dept_rec DeptRecTyp;
  dept_rec_2 dept_rec%TYPE;
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
    first  emp.ename%TYPE,
    last   emp.ename%TYPE
  );
 
  TYPE contact IS RECORD (
    name  name_rec,                    -- nested record
    emp_no emp.empno%TYPE
  );
 
  friend contact;
BEGIN
  friend.name.first := 'John';
  friend.name.last := 'Smith';
  friend.emp_no := '1234';
  
  DBMS_OUTPUT.PUT_LINE (
    friend.name.first  || ' ' ||
    friend.name.last   || ', ' ||
    friend.emp_no
  );
END;
/
~~~ 

![image](https://user-images.githubusercontent.com/52989294/83480326-f4300b00-a4d5-11ea-8ac0-9d267a065f38.png)

### [5-36] RECORD Type with Varray Field
~~~ sql
DECLARE
  TYPE full_name IS VARRAY(2) OF VARCHAR2(20);
 
  TYPE contact IS RECORD (
    name  full_name := full_name('John', 'Smith'),  -- varray field
    emp_no emp.empno%TYPE
  );
 
  friend contact;
BEGIN
  friend.emp_no := '1234';
  
  DBMS_OUTPUT.PUT_LINE (
    friend.name(1) || ' ' ||
    friend.name(2) || ', ' ||
    friend.emp_no 
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

~~~ 
### [5-39]
~~~ sql

~~~ 

### [5-40]
~~~ sql

~~~
### [5-44] 
~~~ sql

~~~

### [5-45]
~~~ sql

~~~

### [5-47]
~~~ sql

~~~



### [5-48]
~~~ sql

~~~


### [5-49]
~~~ sql

~~~

### [5-50]
~~~ sql

~~~

***

## Record Comparisons

### [5-51]
~~~ sql

~~~

### [5-52]
**5-51 테이블 활용함**
~~~ sql

~~~

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
### [6-1]
~~~ sql

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













