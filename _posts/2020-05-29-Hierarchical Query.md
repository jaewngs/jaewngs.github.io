---

title:  "계층적 질의 & PL/SQL"

layout: post

---

# DAY 32


## [오늘 할 내용]
1. 계층적 질의를 살펴보고 사용 할 수 있다.
2. PL/SQL을 사용 할 수 있다.
3. PL/SQL의 구문을 이용해서 기본 문법과 SQL 구문을 접목 할 수 있다.
4. 함수와 프로시저를 사용 할 수 있다.


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


- KING (LEVEL1 : ROOT)
- CLARK JONES BLAKE (LEVEL2 : PARENT/CHILD)
- MILLER FORD ALLEN WARD MARTIN TUNNER JAMES (LEVEL3 : PARENT/CHILD/LEAF)
- SMITH


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
[형식]
~~~ sql
CREATE SEQUENCE customers_seq
 START WITH     1000   -- 1000부터
 INCREMENT BY   1   -- 1씩 증가
 NOCACHE
 NOCYCLE;
~~~ 

[예제]
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


