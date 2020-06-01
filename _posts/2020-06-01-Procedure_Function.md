---

title:  "PL/SQL PROCEDURE, FUNCTION 구현 해보기"

layout: post

---

# DAY 33


## [오늘 할 내용]
1. 함수와 프로시저를 구현 할 수 있다.
2. 쿼리를 사용하여 함수와 프로시저를 접목 할 수 있다.
3. JDBC를 사용하여 프로시저를 구현 할 수 있다.

***

**CMD에서 입력받는 키워드 : accept**

~~~ sql
accept a -- a값을 받아 올 수 있게 커서 깜빡임

accept a prompt 'input a :'  -- 다음행 'input a :' 에 값 입력하면 a에 대입
~~~

### 참조 변수 ###
#### [형태]
**변수명**  테이블명.필드명%Type

ex)
~~~ sql
empNo emp.employee_id%TYPE
~~~
- 현재 emp 테이블의 employee_id의 타입을 그대로 가져오겠다.
- emp 테이블의 employee_id와 동일한 데이터 타입으로 선언한다는 의미.

~~~ sql
emp_name emp.first_name%TYPE
~~~
- emp 테이블의 first_name과 동일한 데이터 타입으로 선언


~~~ sql
empRow emp%ROWTYPE
~~~
- emp 테이블의 모든 컬럼을 한꺼번에 저장하기 위한 변수로 선언

***

#### ex) 사원의 이름을 입력받아 출력하는 구문을 만들어보자.
~~~ sql
-- 이름이 'KING'인 사원의 이름과 봉급을 출력해보자.
SELECT ENAME, SAL
FROM EMP
WHERE ENAME = 'KING'; 
~~~

~~~ sql
SET SERVEROUTPUT ON; -- 결과물 출력 안될 시 사용

ACCEPT INPUT_NAME
SQL > 'KING' -- 'KING' 입력하면 INPUT_NAME 에 저장

DECLARE
NAME EMP.ENAME%TYPE;
SAL_RES EMP.SAL%TYPE;
BEGIN
SELECT ENAME, SAL INTO NAME, SAL_RES
FROM EMP
WHERE ENAME = &INPUT_NAME;
DBMS_OUTPUT.PUT_LINE(NAME || '이 받는 월급 '||SAL_RES);
END;
/
~~~

![image](https://user-images.githubusercontent.com/52989294/83391528-909ed280-a42e-11ea-8b65-31b9a6603985.png)

~~~ sql
SET SERVEROUTPUT ON; -- 결과물 출력 안될 시 사용

ACCEPT INPUT_NAME
SQL > 'KING' -- 'KING' 입력하면 INPUT_NAME 에 저장

DECLARE
EMP_RES EMP%ROWTYPE; 
-- EMP 테이블의 모든 컬럼을 한꺼번에 저장하기 위한 변수로 선언

BEGIN
SELECT ENAME, SAL INTO EMP_RES.ENAME, EMP_RES.SAL
FROM EMP
WHERE ENAME = &INPUT_NAME;
DBMS_OUTPUT.PUT_LINE(EMP_RES.ENAME || '이 받는 월급 '||EMP_RES.SAL);
END;
/
~~~

![image](https://user-images.githubusercontent.com/52989294/83392171-9c3ec900-a42f-11ea-946a-07a1ed0b7a89.png)


## 프로시져(PROCEDURE)

: 특정 작업을 수행 할 수 있고 이름이 있는 PL/SQL 블록으로서 매개 변수를 받을 수 있고 반복적으로 사용 할 수 있는 모듈

### [형식]
~~~ sql
CREATE PROCEDURE 프로시져 이름
in argument -- (택1) in -- 실행 환경에서 program으로 값을 전달
out argument -- (택1) out -- program에서 실행 환경으로 값을 전달
in out argument -- (택1) in out -- 실행 환경에서 program으로 값을 전달하고 다시 프로그램에서 실행환경으로 변경된 값을 전달

is 
[변수의 선언]
begin
[PL/SQL Block] 
--SQL 문장, PL/SQL 제어 문장
[exception]   -- 선택
-- error가 발생 할 때 수행하는 문장
end 프로시져 이름;

SET SERVEROUTPUT ON; -- 결과물 출력 안될 시 사용
~~~

**만들어진 stored procedure list 보기**
~~~ sql
SELECT * FROM USER_PROCEDURES;
~~~

**프로시져의 내용까지 확인**
~~~ sql
SELECT * FROM USER_SOURCE;
~~~

***

ex01) 부서 번호 20번인 사원의 사원번호, 이름, 봉급을 구하는 프로시져를 만들자.

~~~ sql
SELECT EMPNO, ENAME, SAL FROM EMP WHERE DEPTNO = 20;
~~~
**방법1)**
~~~ sql
SELECT EMPNO, ENAME, SAL INTO :a, :b, :c
FROM EMP
WHERE DEPTNO = 20;
~~~

~~~ sql
SET SERVEROUTPUT ON; -- 결과물 출력 안될 시 사용

CREATE OR REPLACE PROCEDURE EMP_EX01
IS
V_EMPNO EMP.EMPNO%TYPE; -- 변수선언
V_ENAME EMP.ENAME%TYPE;
V_SAL NUMBER(7,2);

CURSOR EMP_CURSOR IS -- 1. 커서 선언 : 하나 이상의 row를 담을 객체
SELECT EMPNO,ENAME,SAL FROM EMP WHERE DEPTNO = 20;

BEGIN-- 실제 실행 코드
OPEN EMP_CURSOR; -- 2. 커서 시작
LOOP--한줄 이상 대입 변수 출력 , loop 구문 사용
FETCH EMP_CURSOR INTO V_EMPNO,V_ENAME,V_SAL; -- 3. 변수를 대입

EXIT WHEN EMP_CURSOR%ROWCOUNT > 5 OR EMP_CURSOR%NOTFOUND;
DBMS_OUTPUT.PUT_LINE(V_EMPNO||'  '||V_ENAME||'  '||V_SAL);
END LOOP;
CLOSE EMP_CURSOR;
END EMP_EX01;
/
~~~

~~~ sql
EXEC EMP_EX01; --  실행하기
~~~

![image](https://user-images.githubusercontent.com/52989294/83380218-5c201c00-a418-11ea-959d-128614fdefd3.png)

**방법2)**
~~~ sql
SET SERVEROUTPUT ON; -- 결과물 출력 안될 시 사용

CREATE OR REPLACE PROCEDURE EMP_EX02
IS
V_EMP EMP%ROWTYPE;

CURSOR EMP_CURSOR IS
SELECT EMPNO, ENAME, SAL
FROM EMP
WHERE DEPTNO = 20;

BEGIN
FOR V_EMP IN EMP_CURSOR LOOP

EXIT WHEN EMP_CURSOR%ROWCOUNT > 5 OR EMP_CURSOR%NOTFOUND;
DBMS_OUTPUT.PUT_LINE(V_EMP.EMPNO||' '||V_EMP.ENAME||' '||V_EMP.SAL);
END LOOP;
END EMP_EX02;
/
~~~

~~~ sql
SET SERVEROUTPUT ON; -- 결과물 출력 안될 시 사용

EXEC EMP_EX02; --실행
~~~

![image](https://user-images.githubusercontent.com/52989294/83382967-27fc2980-a41f-11ea-9d1a-e5889f33ca40.png)

Q1. 사원 테이블에서 사원의 이름과 봉급을 출력해보자.

~~~ sql
SET SERVEROUTPUT ON; -- 결과물 출력 안될 시 사용

CREATE OR REPLACE PROCEDURE EX_VIEW
IS
R_ENAME EMP.ENAME%TYPE; -- 변수선언
R_SAL EMP.SAL%TYPE; -- 변수선언

CURSOR RES IS -- 1. 커서 선언 : 하나 이상의 row를 담을 객체
SELECT ENAME,SAL FROM EMP;

BEGIN -- 실제 실행 코드
OPEN RES; -- 2. 커서 시작
LOOP -- 한줄 이상 대입 변수 출력 , loop 구문 사용
FETCH RES INTO R_ENAME,R_SAL; -- 3. 변수를 대입

EXIT WHEN RES%NOTFOUND OR RES%ROWCOUNT > 3;
DBMS_OUTPUT.PUT_LINE(R_ENAME || '  '||R_SAL||'  '||RES%ROWCOUNT);
END LOOP;
CLOSE RES;
END EX_VIEW;
/
~~~

![image](https://user-images.githubusercontent.com/52989294/83394972-7962e380-a434-11ea-9e46-c6610403682c.png)

~~~ sql
EXEC EX_VIEW; --  실행하기
~~~

![image](https://user-images.githubusercontent.com/52989294/83395036-8da6e080-a434-11ea-8e02-1fac44b31c32.png)

Q2. EX_VIEW01을 호출하게 되면 사원의 이름, 봉급, 커미션이 측정된 사원만 출력된다.
~~~ sql
SELECT ENAME, SAL, COMM 
FROM EMP 
WHERE COMM IS NOT NULL;
~~~

~~~ sql
SET SERVEROUTPUT ON; -- 결과물 출력 안될 시 사용

CREATE OR REPLACE PROCEDURE EX_VIEW01
IS
R_EMP EMP%ROWTYPE;

CURSOR RES IS
SELECT ENAME, SAL, COMM FROM EMP WHERE COMM IS NOT NULL;

BEGIN
OPEN RES;
LOOP
FETCH RES INTO R_EMP.ENAME, R_EMP.SAL, R_EMP.COMM;

EXIT WHEN RES%NOTFOUND;
DBMS_OUTPUT.PUT_LINE(R_EMP.ENAME||' '||R_EMP.SAL||' '||R_EMP.COMM);
END LOOP;
CLOSE RES;
END EX_VIEW01;
/
~~~

~~~ sql
EXEC EX_VIEW01; -- 실행
~~~

![image](https://user-images.githubusercontent.com/52989294/83382132-642e8a80-a41d-11ea-9023-a6ce44237c48.png)

Q3. 실행 시 부서번호를 전달[커서를 통해 전달]해서 해당 부서번호를 출력하는 프로시져를 만들자.
~~~ sql
SELECT EMPNO, ENAME, SAL
FROM EMP
WHERE DEPTNO = 10;  -- V_DEPTNO

SELECT EMPNO, ENAME, SAL
FROM EMP
WHERE DEPTNO = 20; -- V_DEPTNO
~~~

~~~ sql
SET SERVEROUTPUT ON; -- 결과물 출력 안될 시 사용

CREATE OR REPLACE PROCEDURE EMP_EX03
IS
V_EMPNO EMP.EMPNO%TYPE;
V_ENAME EMP.ENAME%TYPE;
V_SAL NUMBER(7,2);

CURSOR EMP_CURSOR(V_DEPTNO NUMBER) IS
SELECT EMPNO, ENAME, SAL
FROM EMP
WHERE DEPTNO = V_DEPTNO;

BEGIN
OPEN EMP_CURSOR(10);
LOOP
FETCH EMP_CURSOR INTO V_EMPNO, V_ENAME,V_SAL;

EXIT WHEN EMP_CURSOR%ROWCOUNT > 5 OR EMP_CURSOR%NOTFOUND;
DBMS_OUTPUT.PUT_LINE(V_EMPNO||' '||V_ENAME||' '||V_SAL);
END LOOP;
CLOSE EMP_CURSOR;

OPEN EMP_CURSOR(20);
LOOP
FETCH EMP_CURSOR INTO V_EMPNO, V_ENAME, V_SAL;
EXIT WHEN EMP_CURSOR%ROWCOUNT > 5 OR EMP_CURSOR%NOTFOUND;
DBMS_OUTPUT.PUT_LINE(V_EMPNO||' '||V_ENAME||' '||V_SAL);
END LOOP;
CLOSE EMP_CURSOR;
END EMP_EX03;
/
~~~

![image](https://user-images.githubusercontent.com/52989294/83383934-1ca9fd80-a421-11ea-87d5-6c9ffbdaeb44.png)

Q4. ' EXEC EMP_EX04(7369) '를 입력하게 되면 해당 번호의 사원을 찾아 봉급을 인상하고 인상된 사원의 수를 리턴하자.

~~~ sql
SET SERVEROUTPUT ON; -- 결과물 출력 안될 시 사용

CREATE OR REPLACE PROCEDURE EMP_EX04(P_EMPNO IN EMP.EMPNO%TYPE)
IS
V_SAL EMP.SAL%TYPE;
V_UPDATE_ROW NUMBER;

BEGIN
SELECT SAL INTO V_SAL
FROM EMP
WHERE EMPNO = P_EMPNO;

IF SQL%FOUND THEN --명시적 커서(SQL%)을 찾았을 때 명령을 수행
DBMS_OUTPUT.PUT_LINE('검색한 데이터가 존재합니다.:'||V_SAL);

UPDATE EMP SET SAL = SAL*1.1
WHERE EMPNO = P_EMPNO;
V_UPDATE_ROW := SQL%ROWCOUNT;
DBMS_OUTPUT.PUT_LINE('급여가 인상된 사원수 :' || V_UPDATE_ROW);
END IF;
END;
/
~~~

~~~ sql
EXECUTE EMP_EX04(7369);
~~~

![image](https://user-images.githubusercontent.com/52989294/83384454-457ec280-a422-11ea-8d3f-9a95ab3b4101.png)

Q5. 부서번호에 해당하는 테이블의 봉급을 가져와서 10% 인상된 금액으로 수정하고 사원의 번호와 이름, 봉급을 출력하자.
~~~ sql
SET SERVEROUTPUT ON; -- 결과물 출력 안될 시 사용

CREATE OR REPLACE PROCEDURE EMP_EX05(P_DEPTNO IN EMP.DEPTNO%TYPE)
IS
CURSOR EMP_CURSOR IS
SELECT EMPNO, ENAME, SAL
FROM EMP
WHERE DEPTNO = P_DEPTNO FOR UPDATE;

BEGIN
FOR ABC IN EMP_CURSOR LOOP
UPDATE EMP
SET SAL = SAL * 1.1 -- 10% 급여인상
WHERE CURRENT OF EMP_CURSOR; -- 현재 커서의 ROW를 가져와서 수정한다.
DBMS_OUTPUT.PUT_LINE(ABC.EMPNO||' '||ABC.ENAME||' '||ABC.SAL);
END LOOP;
END EMP_EX05;
/
~~~
![image](https://user-images.githubusercontent.com/52989294/83386026-46fdba00-a425-11ea-9c30-b09eae5d4d1e.png)

~~~ sql
SELECT ENAME, SAL FROM EMP WHERE DEPTNO = 30; -- 먼저 값 확인
~~~
![image](https://user-images.githubusercontent.com/52989294/83386155-7dd3d000-a425-11ea-8c48-1ae4826a4775.png)

~~~ sql
EXEC EMP_EX05(30);  -- 실행
~~~
![image](https://user-images.githubusercontent.com/52989294/83386822-a14b4a80-a426-11ea-84b1-3b1066281c41.png)

~~~ sql
SELECT ENAME, SAL FROM EMP WHERE DEPTNO = 30; -- 변경된 값 확인
~~~
![image](https://user-images.githubusercontent.com/52989294/83386884-bd4eec00-a426-11ea-97d2-f472eb8ea830.png)

### [함수]
- 보통 값을 계산하고 결과값을 반환하기 위해서 함수를 많이 사용
- 대부분 구성이 프로시져와 유사하지만 in 파라미터만 사용
- 반드시 반환될 값의 데이터 타입을 return문에 선언하고 return문을 통해서 반드시 값을 반환

### [형식]
~~~ sql
CREATE FUNCTION 함수명[파라미터] return datatype
is
[변수 선언 부분]
BEGIN
-- pl/sql 블록에는 적어도 한개의 return 문이 있어야한다.
-- pl/sql 블록은 함수가 수행 할 내용을 정의한 몸체 부분이다.
end;
~~~

Q6. 사번을 입력 받아 연봉을 리턴하는 함수를 작성하자.
~~~ sql
SET SERVEROUTPUT ON; -- 결과물 출력 안될 시 사용

CREATE FUNCTION getAvg(V_ID IN EMP.EMPNO%TYPE) RETURN NUMBER
IS
V_SAL EMP.SAL%TYPE :=0;
VTOT NUMBER := 0;
VCOMM EMP.COMM%TYPE;

BEGIN
SELECT SAL, COMM INTO V_SAL, VCOMM
FROM EMP WHERE EMPNO = V_ID; -- 받은 사번을 입력한다.

VTOT := V_SAL *12 + NVL(VCOMM,0); -- 정의된 변수를 사용한다.(연산)
RETURN VTOT; --리턴을 명시한다.
END;
/
~~~

![image](https://user-images.githubusercontent.com/52989294/83387004-f25b3e80-a426-11ea-9f3a-6c7497ffb008.png)

~~~ sql
var salary number; -- 반환 값을 저장할 변수 선언
execute :salary := getAvg(7900);  -- 값 전달 실행 확인
print -- 출력 확인
~~~

![image](https://user-images.githubusercontent.com/52989294/83387152-33535300-a427-11ea-8d89-5163ef0a3075.png)

~~~ sql
select empno, getAvg(empno) from emp;  -- 조회
~~~

![image](https://user-images.githubusercontent.com/52989294/83387191-436b3280-a427-11ea-8dd6-261ed5853dcb.png)

#### 참고
![image](https://user-images.githubusercontent.com/52989294/83387835-5f230880-a428-11ea-9c16-49f46f4030a0.png)


Q7. 사번을 입력 받아서 부서명을 리턴하는 함수를 작성하자.
~~~ sql
SET SERVEROUTPUT ON; -- 결과물 출력 안될 시 사용

CREATE FUNCTION getDept(P_EMPNO IN EMP.EMPNO%TYPE) RETURN VARCHAR2 
--문자열로 리턴하겠다.
IS
V_DEPTNO EMP.DEPTNO%TYPE;  --SELECT 문에서 리턴을 하는 부서번호 대입
V_RES VARCHAR2(60); -- 출력구문, 부서명을 저장하는 변수

BEGIN
SELECT DEPTNO INTO V_DEPTNO FROM EMP
WHERE EMPNO = P_EMPNO;  -- 사원번호에 해당하는 부서번호 출력

IF V_DEPTNO = 10 THEN
V_RES := 'ACCOUNT 부서 사원입니다.';
ELSIF V_DEPTNO = 20 THEN
V_RES := 'RESEARCH 부서 사원입니다.';
ELSIF V_DEPTNO = 30 THEN
V_RES := 'SALES 부서 사원입니다.';
ELSIF V_DEPTNO = 40 THEN
V_RES := 'OPERATIONS 부서 사원입니다.';
END IF;

RETURN V_RES;
END;
/
~~~

![image](https://user-images.githubusercontent.com/52989294/83388573-a067e800-a429-11ea-883e-e5a8ef1c1599.png)

~~~ sql
var res varchar2(20); -- varchar2형 res 변수 선언
execute :res := getDept(7900); -- getDept(7900) 실행 결과를 res에 저장
~~~

~~~ sql
-- 결과값 확인
SELECT ENAME, EMPNO, DEPTNO, getDept(empno) FROM EMP;
~~~

![image](https://user-images.githubusercontent.com/52989294/83388673-cd1bff80-a429-11ea-98c6-5349d07ba590.png)

Q8. Q7을 PROCEDURE로 바꾸기
~~~ sql
SET SERVEROUTPUT ON; -- 결과물 출력 안될 시 사용

CREATE OR REPLACE PROCEDURE ex_getDept(P_EMPNO IN EMP.EMPNO%TYPE, V_RES OUT VARCHAR2)
IS

V_DEPTNO EMP.DEPTNO%TYPE;  --SELECT 문에서 리턴을 하는 부서번호 대입

BEGIN
SELECT DEPTNO INTO V_DEPTNO FROM EMP
WHERE EMPNO = P_EMPNO;  -- 사원번호에 해당하는 부서번호 출력

IF V_DEPTNO = 10 THEN
V_RES := 'ACCOUNT 부서 사원입니다.';
ELSIF V_DEPTNO = 20 THEN
V_RES := 'RESEARCH 부서 사원입니다.';
ELSIF V_DEPTNO = 30 THEN
V_RES := 'SALES 부서 사원입니다.';
ELSIF V_DEPTNO = 40 THEN
V_RES := 'OPERATIONS 부서 사원입니다.';
END IF;
END;
/
~~~

![image](https://user-images.githubusercontent.com/52989294/83389358-13259300-a42b-11ea-8024-f773152f6937.png)

~~~ sql
var res varchar2(50)
execute ex_getDept(7900,:res); -- ex_getDept(7900) 실행한 결과를 res에 저장
print res;
~~~

**':res'**  -->  out 변수 호출 해줘야함

![image](https://user-images.githubusercontent.com/52989294/83390589-14f05600-a42d-11ea-92da-b30907ac2b8a.png)

