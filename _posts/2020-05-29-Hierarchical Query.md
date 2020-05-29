---

title:  "Hierarchical Querry(계층적 질의) & PL/SQL 개념"

layout: post

---

# DAY 32


## [오늘 할 내용]
1. 계층적 질의를 살펴보고 사용 할 수 있다.
2. PL/SQL을 사용 할 수 있다.
3. PL/SQL의 구문을 이용해서 기본 문법과 SQL 구문을 접목 할 수 있다.


## 계층적 질의

: 테이블의 행 사이의 계층적 관련성을 바탕으로 데이터를 검색하는 질의

### [형식]

~~~ sql
SELECT [LEVEL], COLUMN1, COLUMN2 ... 
FROM TABLE -- 조인 사용 불가
WHERE conditions
[START WITH condition]  -- LEVEL의 의사열을 가짐. ROOT의 계층 시작점을 지정(서브쿼리 사용)
[CONNECT BY PRIOR condition] -- 계층의 상하위에 대한 LEVEL를 관리. PRIOR 바로 다음에 오는 행부터 검색되며 서브쿼리를 사용할 수 없다.

~~~

### [방법]
1. START WITH 절을 이용한 시작점 지정
	ex) START WITH COLUMN1  = VALUE : 시작 행의 위치를 지정 할 수 있다.
    만일 명시하지 않으면 테이블의 모든 ROOT가 NODE 1(LEVEL = 1)이 된다.
2. CONNECT BY 절을 사용한 트리 진행 방향 설정
3. LEVEL 의사열의 활용
4. PRIOR 키워드의 활용
5. WHERE 절 조건을 활용한 데이터 제거방법
6. CONNECT BY 절 조건을 이용한 데이터 제거
*계층적 질의는 START WITH와 CONNECT BY에 존재 여부에 따라서 식별된다.

### [사용시점]
- 관계형 데이터베이스는 레코드를 계층적인 방법으로 저장하지 않는다. 그러나 계층적 관련성이 단일 테이블의 행 사이에 존재하며 계층적 질의는 하나의 테이블에 있는 행들 사이에 어떤 관련성이 존재할 때 가능하다.
ex) 매니저 직책에 있는 사원이 KING에게 보고할 때 사용
- ANSI-SQL에서는 계층관계를 표현하는 Hierarchical Query를 사용하는 것은 불가능하다.
- 확장된 형태의 recursive PL/SQL이나 CONNECT BY를 이용하여 Hierarchical Query(계층쿼리) 표현이 가능하다.
- 오라클에서는 CONNECT BY라는 확장된 구문을 통해 계층쿼리를 지원한다.
- 조직구조, 답글게시판, 디렉터리 구조 등에 주로 이용된다.
 ex) CONNECT BY PRIOR 탑키 = 하위키 : TOP DOWN 방향
 CONNECT BY PRIOR 하위키 = 탑키 : BOTTOM UP 방향
 상위를 부모, 하위를 자식으로 연결 시켜서 열 위치에 의해 의사 결정을 사용한다.
 항상 현재 부모 행에 관련된 CONNECT BY 조건에 의해 자식을 선택한다.

Q1. 계층적 쿼리를 사용하여 위에서 아래로 사원의 이름과 관리자 이름을 조회하자.
~~~ sql
SELECT ENAME ||'의 상사 '|| PRIOR ENAME "WALK" 
FROM EMP 
START WITH ENAME = 'KING' 
CONNECT BY PRIOR EMPNO = MGR;
~~~
![image](https://user-images.githubusercontent.com/52989294/83211746-e2c6c600-a198-11ea-8f35-ac23cb7b8a93.png)


Q2. 'SMITH'부터 시작하여 아래서 위로 해당 사원의 매니저 정보가 나오게 출력하자.
~~~ sql
SELECT ENAME, JOB, MGR 
FROM EMP 
START WITH ENAME = 'SMITH' 
CONNECT BY PRIOR MGR = EMPNO;
~~~
![image](https://user-images.githubusercontent.com/52989294/83211807-feca6780-a198-11ea-9c99-75b0943a5c6e.png)


![image](https://user-images.githubusercontent.com/52989294/83208426-98414b80-a190-11ea-90b5-add1c45113f9.png)

![image](https://user-images.githubusercontent.com/52989294/83234778-c85a1000-a1cb-11ea-91fc-19fd5d9cc24f.png)

- KING (LEVEL1 : ROOT)
- CLARK / JONES / BLAKE (LEVEL2 : PARENT/CHILD)
- MILLER / FORD / ALLEN WARD MARTIN TUNNER JAMES (LEVEL3 : PARENT/CHILD/LEAF)
- / SMITH /


- ROOT : 최상위 레벨
- PARENT/CHILD : 부모이자 자식 입장
- LEAF : 가장 마지막 노드를 말함 

Q3. 가장 높은 레벨을 시작으로 가장 낮은 레벨의 각각의 다음 레벨을 들여 쓰기로 출력하자.
ex) KING -> CLARK -> MILLER
~~~ sql
SELECT LPAD(' ', 4*LEVEL - 4) || ENAME RES, LEVEL, EMPNO, MGR, DEPTNO 
FROM EMP 
START WITH MGR IS NULL 
CONNECT BY PRIOR EMPNO = MGR;
~~~
![image](https://user-images.githubusercontent.com/52989294/83212479-b1e79080-a19a-11ea-9e6d-46245d91c52f.png)

Q4. 전체 노드 중에서 'CLARK'의 노드를 제거한 후 출력 해보자.

~~~ sql
SELECT ENAME ||'의 상사 '|| PRIOR ENAME "WALK" 
FROM EMP 
WHERE ENAME != 'CLARK' 
START WITH ENAME = 'KING' 
CONNECT BY PRIOR EMPNO = MGR;
~~~
![image](https://user-images.githubusercontent.com/52989294/83211849-17d31880-a199-11ea-8360-6d170de39fd5.png)
~~~ sql
SELECT DEPTNO, EMPNO, ENAME, JOB, SAL 
FROM EMP 
WHERE ENAME != 'CLARK' 
START WITH MGR IS NULL 
CONNECT BY PRIOR EMPNO = MGR;
~~~
~~~ sql
SELECT DEPTNO, EMPNO, ENAME, JOB, SAL 
FROM EMP 
START WITH MGR IS NULL 
CONNECT BY PRIOR EMPNO = MGR;
~~~


Q5. 'BLAKE'와 그의 라인들을 모두 제거하자.
~~~ sql
SELECT DEPTNO, EMPNO, ENAME, JOB, SAL 
FROM EMP 
START WITH MGR IS NULL 
CONNECT BY PRIOR EMPNO = MGR AND ENAME != 'BLAKE';
~~~

~~~ sql
SELECT LPAD(' ', 4*LEVEL - 4) || ENAME RES, LEVEL, EMPNO, MGR, DEPTNO 
FROM EMP 
START WITH MGR IS NULL 
CONNECT BY PRIOR EMPNO = MGR AND ENAME != 'BLAKE';
~~~

Q6. LEVEL 정렬을 해보자.
~~~ sql
SELECT LPAD(' ', 4*LEVEL - 4) || ENAME RES, LEVEL, EMPNO, MGR, DEPTNO 
FROM EMP 
START WITH MGR IS NULL 
CONNECT BY PRIOR EMPNO = MGR 
ORDER BY ENAME;

-- Q2에 ORDER BY 주기
SELECT ENAME, JOB, MGR 
FROM EMP 
START WITH ENAME = 'SMITH' 
CONNECT BY PRIOR MGR = EMPNO 
ORDER BY ENAME;
~~~

Q7. Q6의 쿼리를 실행하게 되면 TREE 구조로 만들어 정렬을 하기 때문에 계층이 틀어진다라고 본다. 
TREE 구조를 만들어서 계층을 두고 대상을 정렬 하려면 ORDER SIBLINGS BY 라는 키워드를 사용한다.(보고서를 작성할 때 유용)
~~~ sql
SELECT LPAD(' ', 4*LEVEL - 4) || ENAME RES, LEVEL, EMPNO, MGR, DEPTNO 
FROM EMP 
START WITH MGR IS NULL 
CONNECT BY PRIOR EMPNO = MGR 
ORDER SIBLINGS BY ENAME;
~~~

Q8. SYS_CONNECT_BY_PATH(대상, '구분자')를 이용해서 EMP의 TREE구조를 출력해보자.
~~~ sql
SELECT LPAD(' ', 4*LEVEL - 4) || ENAME RES, JOB, SYS_CONNECT_BY_PATH(ENAME, '/') 
FROM EMP 
START WITH MGR IS NULL 
CONNECT BY PRIOR EMPNO = MGR 
ORDER SIBLINGS BY ENAME;
~~~

Q9. CONNECT_BY_ISLEAF : 마지막 노드의 유무를 찾을 수 있다.
CONNECT_BY_ISLEAF = 0 : 현재 ROW가 자식노드를 가지고 있을 때(LEAF NODE가 아니다.)
CONNECT_BY_ISLEAF = 1 : 현재 ROW가 자식노드를 가지고 있지 않을 때(LEAF NODE이다.)

~~~ sql
SELECT LPAD(' ', 4*LEVEL - 4) || ENAME RES, JOB, SYS_CONNECT_BY_PATH(ENAME, '/') RES02, CONNECT_BY_ISLEAF 
FROM EMP 
START WITH MGR IS NULL 
CONNECT BY PRIOR EMPNO = MGR;
~~~

Q10. 사원테이블의 계층 구조에서 LEAF 노드만 보자.
~~~ sql
SELECT LPAD(' ', 4*LEVEL - 4) || ENAME RES, JOB 
FROM EMP 
WHERE CONNECT_BY_ISLEAF = 1 
START WITH MGR IS NULL 
CONNECT BY PRIOR EMPNO = MGR;
~~~

Q11. 사원테이블의 계층 구조에서 LEAF 노드가 아닌 사원을 보자.
~~~ sql
SELECT LPAD(' ', 4*LEVEL - 4) || ENAME RES, JOB 
FROM EMP 
WHERE CONNECT_BY_ISLEAF = 0 
START WITH MGR IS NULL 
CONNECT BY PRIOR EMPNO = MGR;
~~~

***

## MERGE

### [형식]

~~~ sql
MERGE INTO TABLE_NAME -- 변경 또는 추가할 대상 테이블
USING (쿼리) -- 변경 추가할 데이터의 원본을 지정, 테이블, 뷰, 서브 쿼리를 사용
ON(조인 조건) -- MERGE 연산이 변경, 추가 되는 조건
WHEN MATCHED THEN -- 해당 행이 존재하면 UPDATE
UPDATE SET 
WHEN NOT MATCHED THEN -- 존재 하지 않으면 INSERT
INSERT ..  VALUES ..
~~~
~~~ sql
CREATE TABLE EMP_MAIN 
AS 
SELECT * FROM EMP;

CREATE TABLE EMP_MASTER 
AS 
SELECT * FROM EMP WHERE DEPTNO = 30;

INSERT INTO EMP_MASTER(EMPNO) VALUES(111); 
INSERT INTO EMP_MASTER(EMPNO) VALUES(222); 
INSERT INTO EMP_MASTER(EMPNO) VALUES(333);
~~~

Q12. 사원 EMP_MAIN 테이블을 EMP_MASTER 테이블과 비교해서 동일한 사원번호의 데이터가 있으면 EMP_MASTER 테이블의 급여, COMM를 EMP테이블의 값으로 수정하고 해당 사원번호를 가진 사원이 없으면 EMP_MASTER에 EMP테이블의 데이터를 이용해서 입력하는 작업을 수행하자.

~~~ sql
MERGE INTO EMP_MASTER T 
USING EMP_MAIN E 
ON (T.EMPNO = E.EMPNO) 
WHEN MATCHED THEN 
UPDATE SET T.SAL = E.SAL, T.COMM = E.COMM 
WHEN NOT MATCHED THEN 
INSERT (EMPNO, ENAME, SAL, COMM) VALUES(E.EMPNO, E.ENAME, E.SAL,E.COMM);
~~~

**EMP_MASTER 테이블의 봉급과 커미션을 0으로 수정 후 실행**

~~~ sql
UPDATE EMP_MASTER 
SET SAL = 0, COMM = 0;

MERGE INTO EMP_MASTER T 
USING EMP_MAIN E 
ON (T.EMPNO = E.EMPNO) 
WHEN MATCHED THEN 
UPDATE SET T.SAL = E.SAL, T.COMM = E.COMM 
WHEN NOT MATCHED THEN 
INSERT (EMPNO, ENAME, SAL, COMM) VALUES(E.EMPNO, E.ENAME, E.SAL,E.COMM);
~~~ 

~~~ sql
UPDATE EMP_MASTER 
SET SAL = 0, COMM = 0;

MERGE INTO EMP_MASTER T 
USING EMP_MAIN E 
ON (T.EMPNO = E.EMPNO) 
WHEN MATCHED THEN 
UPDATE SET T.SAL = E.SAL, T.COMM = E.COMM 
WHEN NOT MATCHED THEN 
INSERT (T.EMPNO, T.ENAME, T.SAL, T.COMM) VALUES(E.EMPNO, E.ENAME, E.SAL,E.COMM);
~~~

***

## 트랜잭션(TRANSACTION)
: 논리 단위를 형성하는 DML 문의 모음을 말함
: 하나 또는 두개 이상의 SQL 문으로 이루어진 단위
: 하나의 트랜잭션 안에 모든 SQL은 동일한 효과를 가짐
: COMMIT, ROLLBACK 으로 모듈 단위를 실행함



### [트랜잭션 이벤트(COMMIT, ROLLBACK, SQL 실행종료, 시스템 장애 고장, DDL 발생_CREATE) 발생 원리]
- 실행 가능한 첫번째 SQL 문이 실행 될 때, 시작되어 다음 이벤트가 발생하면 종료된다.
- 트랜잭션이 종료 되면 실행 가능한 SQL 구문이 다음 트랜잭션을 자동으로 시작한다.
	- 데이터 추가(INSERT) -> 데이터 삭제(DELETE) -> COMMIT;  ... 트랜잭션 종료
	- 트랜잭션 시작 -> 데이터 추가(INSERT) -> 데이터 삭제(DELETE) -> ROLLBACK; ... 트랜잭션 종료
	- 트랜잭션 시작 -> 데이터 추가(INSERT) -> 데이터 삭제(DELETE) -> CREATE; ... 트랜잭션 종료

### [트랜잭션 명령어]
- COMMIT, ROLLBACK, SAVEPOINT 이름
- 암시적 트랜잭션
	- 자동 COMMIT 이 발생할 경우 : DDL, DCL이 실행되는 경우, COMMIT, ROLLBACK 명시하지 않고 정상적으로 SQLPLUS를 종료할 경우
	- 자동 ROLLBACK 이 발생할 경우 : 비정상적으로 SQLPLUS를 종료할 경우, 시스템 장애가 있을 때
	
- 명시적 트랜잭션 제어 
	- COMMIT : 보류중인 모든 데이터의 변경 내용 영구저장하고 현재의 트랜잭션을 종료 할 때
	- SAVEPOINT 이름 : 현재에 트랜잭션 내에 저장 점을 표시한다.
	- ROLLBACK : 보류 중인 모든 데이터의 변경 내용을 버리고 현재 트랜잭션을 종료한다.
	- ROLLBACK TO SAVEPOINT 이름 : 트랜잭션 저장점으로 ROLLBACK 하여 저장점 이후에 생성된 SQL 문장이 있는 내용 및 저장점을 버린다. 

**SAVEPOINT 실습**
~~~ sql
CREATE TABLE EMP_RES 
AS 
SELECT EMPNO, ENAME 
FROM EMP;

DELETE FROM EMP_RES;

INSERT INTO EMP_RES VALUES(111,'111');

SAVEPOINT A; -- INSERT 문의 실행까지를 표시

UPDATE EMP_RES 
SET ENAME = 333 
WHERE EMPNO = 111;

ROLLBACK TO A;  -- UPDATE 문만 취소가 되고 INSERT TRANSACTION은 유효함

DELETE FROM EMP_RES;

COMMIT; -- SAVEPOINT A 이후에 실행된 INSERT, DELETE 결과가 테이블에 영구적으로 저장됨
~~~

***
### [형식]
~~~ sql
CREATE SEQUENCE customers_seq
 START WITH     1000   -- 1000부터
 INCREMENT BY   1   -- 1씩 증가
 NOCACHE
 NOCYCLE;
~~~ 

### [예제]
~~~ sql
-- 1부터 5씩 증가, 20까지, 20 넘어가면 에러남
CREATE SEQUENCE MYSC 
START WITH 1 
INCREMENT BY 5 
MAXVALUE 20 
NOCACHE 
NOCYCLE;

MYSC.CURRVAL  -- 현재 시퀀스 값 확인
MYSC.NEXTVAL -- 시퀀스 실행

SELECT MYSC.CURRVAL FROM DUAL; -- 에러남

SELECT MYSC.NEXTVAL FROM DUAL; -- NEXTVAL로 실행시키고

SELECT MYSC.CURRVAL FROM DUAL; -- 실행하기

SELECT MYSC.NEXTVAL FROM DUAL; -- NEXTVAL로 실행시키고

SELECT MYSC.CURRVAL FROM DUAL; -- 실행하기

SELECT MYSC.NEXTVAL FROM DUAL; -- NEXTVAL로 실행시키고

SELECT MYSC.CURRVAL FROM DUAL; -- 실행하기

DROP SEQUENCE MYSC;
~~~

~~~ sql
-- 1부터 5씩 증가, 20 넘어가도 처음부터 다시 시작
CREATE SEQUENCE MYSC 
START WITH 1 
INCREMENT BY 5 
MAXVALUE 20 
CYCLE 
NOCACHE; 

SELECT MYSC.NEXTVAL FROM DUAL; -- NEXTVAL로 실행시키고

SELECT MYSC.CURRVAL FROM DUAL; -- 실행하기

DROP SEQUENCE MYSC;
~~~

~~~ sql
-- 처음에만 3부터 5씩 증가, 20까지, 20 넘어가면 1부터 5씩 증가 
CREATE SEQUENCE MYSC 
START WITH 3
INCREMENT BY 5 
MAXVALUE 20 
MINVALUE 1 
CYCLE 
NOCACHE;

SELECT MYSC.NEXTVAL FROM DUAL; -- NEXTVAL로 실행시키고

SELECT MYSC.CURRVAL FROM DUAL; -- 실행하기

DROP SEQUENCE MYSC;
~~~

~~~ sql
INSERT INTO EMP_RES VALUES(MTSC.NEXTVAL, '111');

SELECT * FROM EMP_RES;
-- EMPNO가 5씩 증가하면서 INSERT문 실행됨
~~~

*** 
## Data Type
###[FLOAT Data Type]
~~~ sql
CREATE TABLE test (col1 NUMBER(5,2), col2 FLOAT(5));

INSERT INTO test VALUES (1.23, 1.23);
INSERT INTO test VALUES (7.89, 7.89);
INSERT INTO test VALUES (12.79, 12.79);
INSERT INTO test VALUES (123.45, 123.45);
INSERT INTO test VALUES (123.45, 125.45);
INSERT INTO test VALUES (123.45, 125.42);
~~~

***

## FUNCTION
### DEREF  .. 참조 정의 
https://docs.oracle.com/cd/E11882_01/server.112/e41084/functions054.htm#SQLRF00634

*** 
## CREATE TABLESPACE
1. sys 계정 : system 계정이 가장 상위 계정. ID 생성, 권한을 설정함
2. 테이블 영역을 저장 할 수 있는 .dbf 생성
3. 선언된 ID에게 .dbf 지정(id별 저장소 맵핑)
4. 기존 서비스 (.dbf) 를 중단하고 새롭게 맵핑된 ID로 접속하는 서비스를 실행한다.
5. 생성된 ID로 접속한다. 

### [실습]

1. SQLPLUS SYS 계정에서 진행한다.
2. SYS 계정 접속하기(id : sys , pw : admin1234)
~~~ sql
CONN AS SYSDBA
~~~

3. TABLESPACE 생성하기
~~~ sql
CREATE TABLESPACE TS_TEST 
LOGGING 
DATAFILE 'c:\Test\ts_test.dbf' 
size 32m 
autoextend on 
next 32m 
maxsize 2048m 
extent management local;
~~~

4. 계정 만들기
~~~ sql
 create user testman identified by admin1234 default tablespace ts_test temporary tablespace temp;
~~~

5. 권한 부여
~~~ sql
grant connect, resource to testman;
~~~

6. testman으로 접속
~~~ sql
conn testman/admin1234
~~~

### 테이블 영역생성을 잘못했을 경우
1. 재연결
~~~ sql
CONN AS SYSDBA 
~~~

2. 실행하는 .DBF 서비스를 중단 시킨다.
~~~ sql
SHUTDOWN immediate;
~~~

3. 수작업으로 C:\TEST02_1\ 로 ts_test.DBF 파일을 이동시킨다.
4. 경로 수정한다.
~~~ sql
-- 서비스 다시 시작(인스턴스 실행)
STARTUP MOUNT;
ALTER DATABASE RENAME FILE 'C:\Test\TS_TEST.DBF' TO 'C:\TEST02_1\TS_TEST.DBF'
~~~

5. 재시작 후 접속한다.
~~~ sql
ALTER DATABASE OPEN;
~~~

6. 재연결
~~~ sql
CONN TESTMAN/admin1234
~~~

### 권한에 대한 5개의 대표적인 롤
- connect : 접속 권한
- resource : 객체 생성, 변경, 삭제 등 시스템에 대한 기본 권한
- dba : 데이터베이스 관리에 대한 권한
- sysdba : 데이터베이스 시작과 종료 및 관리 권한
- sysoper : sysdba + 데이터베이스 생성에 관한 권한
~~~ sql
SELECT * FROM DBA_SYS_PRIVS;  -- sys 권한
SELECT * FROM ROLE_SYS_PRIVS;  -- Role 확인
~~~

***

## PL / SQL
https://docs.oracle.com/cd/E11882_01/appdev.112/e25519/toc.htm
### [형식]
~~~ sql
<< label >> (optional)
DECLARE    -- Declarative part (optional)
  -- Declarations of local types, variables, & subprograms

BEGIN      -- Executable part (required)
  -- Statements (which can use items declared in declarative part)

[EXCEPTION -- Exception-handling part (optional)
  -- Exception handlers for exceptions (errors) raised in executable part]
END;
~~~
### [원리]
![image](https://user-images.githubusercontent.com/52989294/83228941-081bfa00-a1c2-11ea-97b4-4cc98fc5b0f8.png)

### [연습]
~~~ sql
conn big6/admin1234;  -- big6로 접속하기

? set -- 도움말 호출 
~~~
~~~ sql
SET SERVEROUTPUT ON; -- 돌려야 DBMS_OUTPUT.PUT_LINE()출력됨
~~~
Q1. 기본 문장을 출력하자.

~~~ sql
-- 복붙 X, 한줄씩 입력하기
BEGIN 
DBMS_OUTPUT.ENABLE; 
DBMS_OUTPUT.PUT_LINE('1.HELLO ORACLE'); 
DBMS_OUTPUT.DISABLE; 
DBMS_OUTPUT.PUT_LINE('2. HELLO JAVA'); 
END; 
/     --'/'까지 입력하면 실행됨
~~~
![image](https://user-images.githubusercontent.com/52989294/83229705-69909880-a1c3-11ea-9e86-c33940bd28f1.png)


Q2. i 라는 변수를 선언하고 20을 대입 후 출력 해보자.
~~~ sql
-- 복붙 X, 한줄씩 입력하기
DECLARE 
i integer := 20;
BEGIN
DBMS_OUTPUT.PUT_LINE('i의 값은 '|| i);
END;
/
~~~
![image](https://user-images.githubusercontent.com/52989294/83229967-e3288680-a1c3-11ea-8011-9c5e177d0f6b.png)

**데이터 타입**
https://docs.oracle.com/cd/E11882_01/appdev.112/e25519/datatypes.htm#LNPLS332

Q3. 사칙 연산을 출력해보자.
~~~ sql
-- 복붙 X, 한줄씩 입력하기
DECLARE
i int := 100;  j int := 200;  hap int := 0;
sub int := 0; mul int := 0; div int := 0;
BEGIN
hap := i + j; sub := j - i;  mul := i*j;  div := j/i;
DBMS_OUTPUT.PUT_LINE(i || '+' || j || '=' || hap);
DBMS_OUTPUT.PUT_LINE(j || '-' || i || '=' || sub);
DBMS_OUTPUT.PUT_LINE(i || '*' || j || '=' || mul);
DBMS_OUTPUT.PUT_LINE(j || '/' || i || '=' || div);
END;
/
~~~
![image](https://user-images.githubusercontent.com/52989294/83230435-b45ee000-a1c4-11ea-955a-2409e85ae416.png)

---

**예제**
https://docs.oracle.com/cd/E11882_01/appdev.112/e25519/controlstatements.htm#LNPLS411

~~~sql
-- 복붙 X, 한줄씩 입력하기
DECLARE
  first_name  CHAR(10 CHAR);
  last_name   VARCHAR2(10 CHAR);
BEGIN
  first_name := 'John ';
  last_name  := 'Chen ';
 
  DBMS_OUTPUT.PUT_LINE('*' || first_name || '*');
  DBMS_OUTPUT.PUT_LINE('*' || last_name || '*');
END;
/
~~~
![image](https://user-images.githubusercontent.com/52989294/83231984-58498b00-a1c7-11ea-8386-a17e385b8881.png)




~~~ sql
-- 복붙 X, 한줄씩 입력하기
DECLARE
  grade CHAR(1);
BEGIN
  grade := 'B';
  
  IF grade = 'A' THEN
    DBMS_OUTPUT.PUT_LINE('Excellent');
  ELSIF grade = 'B' THEN
    DBMS_OUTPUT.PUT_LINE('Very Good');
  ELSIF grade = 'C' THEN
    DBMS_OUTPUT.PUT_LINE('Good');
  ELSIF grade = 'D' THEN
    DBMS_OUTPUT. PUT_LINE('Fair');
  ELSIF grade = 'F' THEN
    DBMS_OUTPUT.PUT_LINE('Poor');
  ELSE
    DBMS_OUTPUT.PUT_LINE('No such grade');
  END IF;
END;
/
~~~
![image](https://user-images.githubusercontent.com/52989294/83232059-78794a00-a1c7-11ea-8b1c-66042c93fc22.png)


### for문
~~~ sql
-- 복붙 X, 한줄씩 입력하기
BEGIN
  DBMS_OUTPUT.PUT_LINE ('lower_bound < upper_bound');
 
  FOR i IN 1..3 LOOP
    DBMS_OUTPUT.PUT_LINE (i);
  END LOOP;
 
  DBMS_OUTPUT.PUT_LINE ('lower_bound = upper_bound');
 
  FOR i IN 2..2 LOOP
    DBMS_OUTPUT.PUT_LINE (i);
  END LOOP;
 
  DBMS_OUTPUT.PUT_LINE ('lower_bound > upper_bound');
 
  FOR i IN 3..1 LOOP
    DBMS_OUTPUT.PUT_LINE (i);
  END LOOP;
END;
/
~~~
![image](https://user-images.githubusercontent.com/52989294/83231569-a742f080-a1c6-11ea-8bab-c6d338ce1d4a.png)

~~~ sql
BEGIN
  FOR i IN 1..3 LOOP
    IF i < 3 THEN
      DBMS_OUTPUT.PUT_LINE (TO_CHAR(i));
    ELSE
        DBMS_OUTPUT.PUT_LINE ('====================');
      
    END IF;
  END LOOP;
END;
/
~~~

![image](https://user-images.githubusercontent.com/52989294/83232319-f0e00b00-a1c7-11ea-9974-467fcff0c44e.png)

Q5. 테이블을 생성하자.
~~~ sql
CREATE TABLE TEST01(
NO NUMBER(3),
IRUM VARCHAR2(20));

-- 데이터 넣기
BEGIN
FOR I IN 1..10 LOOP
INSERT INTO TEST01 VALUES(I, '홍길동' || I);
END LOOP;
END;
/

SELECT * FROM TEST01; -- 테이블 생성 및 데이터 입력 확인

~~~
![image](https://user-images.githubusercontent.com/52989294/83232588-61872780-a1c8-11ea-91d9-67d0dfb4493a.png)

Q6. 사원테이블의 10번 부서의 평균 월급을 구하는 구문을 작성하자.
~~~ sql
SELECT AVG(SAL) FROM EMP WHERE DEPTNO = 10; -- 결과를 잘 가져오나 확인해보자.

DECLARE
AVG01 NUMBER(7) := 0;
DEPTNO01 NUMBER(7) := 10;
BEGIN 
SELECT AVG(SAL) INTO AVG01
FROM EMP 
WHERE DEPTNO = DEPTNO01
GROUP BY DEPTNO;

DBMS_OUTPUT.PUT_LINE(DEPTNO01 ||'번 부서의 평균은 [' || AVG01 || ']' );
END;
/
~~~
![image](https://user-images.githubusercontent.com/52989294/83233363-8d56dd00-a1c9-11ea-8d4b-b5f8068cceb3.png)


