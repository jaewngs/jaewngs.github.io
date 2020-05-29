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


### 계층적 질의

: 테이블의 행 사이의 계층적 관련성을 바탕으로 데이터를 검색하는 질의

**[형식]**

~~~ sql
SELECT [LEVEL], COLUMN1, COLUMN2 ... 
FROM TABLE -- 조인 사용 불가
WHERE conditions
[START WITH condition]  -- LEVEL의 의사열을 가짐. ROOT의 계층 시작점을 지정(서브쿼리 사용)
[CONNECT BY PRIOR condition] -- 계층의 상하위에 대한 LEVEL를 관리. PRIOR 바로 다음에 오는 행부터 검색되며 서브쿼리를 사용할 수 없다.

~~~

**[방법]**
1. START WITH 절을 이용한 시작점 지정
	ex) START WITH COLUMN1  = VALUE : 시작 행의 위치를 지정 할 수 있다.
    만일 명시하지 않으면 테이블의 모든 ROOT가 NODE 1(LEVEL = 1)이 된다.
2. CONNECT BY 절을 사용한 트리 진행 방향 설정
3. LEVEL 의사열의 활용
4. PRIOR 키워드의 활용
5. WHERE 절 조건을 활용한 데이터 제거방법
6. CONNECT BY 절 조건을 이용한 데이터 제거
*계층적 질의는 START WITH와 CONNECT BY에 존재 여부에 따라서 식별된다.

**[사용시점]**
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

Q4. 전체 노드 중에서 'CLARK'의 노드를 제거한 후 출력 해보자.

~~~ sql
SELECT ENAME ||'의 상사 '|| PRIOR ENAME "WALK" 

FROM EMP 
WHERE ENAME != 'CLARK' 
START WITH ENAME = 'KING' 
CONNECT BY PRIOR EMPNO = MGR;

SELECT DEPTNO, EMPNO, ENAME, JOB, SAL 
FROM EMP 
WHERE ENAME != 'CLARK' 
START WITH MGR IS NULL 
CONNECT BY PRIOR EMPNO = MGR;

SELECT DEPTNO, EMPNO, ENAME, JOB, SAL 
FROM EMP 
START WITH MGR IS NULL 
CONNECT BY PRIOR EMPNO = MGR;
~~~
![image](https://user-images.githubusercontent.com/52989294/83211849-17d31880-a199-11ea-8360-6d170de39fd5.png)

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
