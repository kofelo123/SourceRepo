- [집합 연산자](#180611a)
  - [UNION과 UNIONALL](#8061130)
  - [INTERSECT - 교집합](#8061141)
  - [MINUS - 차집합](#8061141B)

- [JOIN](#join)
  - [CARTESIAN PRODUCT](#80613114)
  - [EQUI JOIN](#80613142)
  - [NON EQUI JOIN](#8061472)
  - [OUTER JOIN](#80614160)
  - [JOINING TABLE INSELF (SELF JOIN)](#80614161)

- [서브쿼리](#80614204)
  - [SINGLE ROW 서브쿼리](#8061565)
  - [MULTI ROW 서브쿼리](#80615103)
  - [MULTI COLUMN 서브쿼리](#8061511)
  - [CORRELATED 서브쿼리](#80615144)
  - [ANY와 ALL](#80615154)
  - [EXISTS - 적어도 하나의 행을 돌려주는지](#80616155)

- [UPDATE , INSERT 기본문법(알아두기)](#80615153)

---


###### 180611a

집합 연산자
-

다른 SELECT 문의 결과 SET을 이용해 합집합,교집합,차집합 등의 새로운 결과 SET을 만드는 연산자

연산을 위해 같은 구조가 되어야 한다.

집합 연산자로 연결된 SELECT문 -> 컬럼수,데이터형,순서대로 일치해야함.

<오라클 제공 집합 연산자>

- UNION:합집합(중복 자료 배제)
- UNION ALL: 합집합(중복 포함)
- MINUS : 차집합 
- INTERSECT : 교집합

위 4가지 중 SORT 일어나지 않는것은 UNION ALL뿐



---

###### 8061130

UNION과 UNIONALL
-

<UNION>
중복 ROW시 하나의 ROW만 포함(정렬O)

<UNION ALL>
중복 ROW시 모두 포함(정렬X -> 빠르다)

```SQL
SELECT EMP_ID FROM TEMP
UNION
SELECT EMP_ID FROM TCOM;


===
(중복X, 정렬O(INDEX,와 ORDER BY없이 정렬됨)
```

>UNION 을 이용해서 TEMP 와 TCOM의 자료 중 EMP_ID, SALARY, COMM 을 보여주는 SQL 을 만들어 보자. 이때 SALARY 와 COMM 이 모두 존재하는 사번은 두 줄로 나와야 한다.

(TEMP테이블은 COMM 컬럼이 없고, TCOM 테이블은 SALARY컬럼이 없다)

```SQL
SELECT EMP_ID,SALARY,0 COMM FROM TEMP
UNION 
SELECT EMP_ID,0 SALARY,COMM FROM TCOM;


====
19930331	70000000	0
19930402	0	640000
19930402	64000000	0
19950303	56000000	0
19960101	72000000	0
19960212	0	390000
19960212	39000000	0
19960303	0	350000
19960303	35000000	0
19966102	45000000	0
19970101	100000000	0
19970112	0	450000
...
```

<UNION ALL>

```SQL
SELECT EMP_ID FROM TEMP
UNION ALL
SELECT EMP_ID FROM TCOM;


===
1. 중복허용 O 2. SORT X
```

정렬 하고자 하면 ORDER BY 사용, 대게 컬럼명이 다르므로 칼럼명보다 순서지정이좋다.

```SQL
SELECT EMP_ID FROM TEMP
UNION ALL
SELECT EMP_ID FROM TCOM
ORDER BY 1;
```


---

###### 8061141

INTERSECT - 교집합
-

```SQL
SELECT EMP_ID FROM TEMP
INTERSECT
SELECT EMP_ID FROM TCOM;
```



---

###### 8061141B

MINUS - 차집합
-

```SQL
SELECT EMP_ID FROM TEMP
MINUS
SELECT EMP_ID FROM TCOM;
```

Q. TEMP 와 TCOM에 존재하는 사번의 교집합을 구한 후, TEMP에서 TCOM에 존재하는 사번을 제외시킨 차집합을 구하고, 두개 결과의 합집합을 구해보자

```SQL
(SELECT EMP_ID FROM TEMP
INTERSECT
SELECT EMP_ID FROM TCOM)
UNION
(SELECT EMP_ID FROM TEMP
MINUS
SELECT EMP_ID FROM TCOM);
```



---

###### join

JOIN
-

두 개이상의 테이블이나 DATASET을 연결한 데이터검색방법

종류
- CARTESIAN PRODUCT
- EQUI JOIN
- NON-EQUI JOIN
- OUTER JOIN
- SELF JOIN



---

###### 80613114

CARTESIAN PRODUCT
-

조건 절에 조인을 위한 조건이 기술되지 않은 경우

테이블 단위로 나올 수 있는 레코드수의 곱으로 결과생성.

```SQL
SELECT COUNT(*)
FROM TEMP,TCOM;
```

>Q : TEMP에 있는 자료 중 직급이 ‘과장’ 인 직원을 읽어 오는데 결과가 실제 데이터베이스에 있는 내용이 두 번 중복되어 나오게 읽어 보자. 한번은 1, 나머지 한번은 2 라는 속성을 달고 나올 수 있도록 하며, 사번, 성명, SALARY를 보여 준다.  (힌트 : TEMP 와 MOD_TEST 테이블의 CARTESIAN PRODUCT를 이용하면 된다.)

```SQL
SELECT NO,A.EMP_ID,A.EMP_NAME,A.SALARY
FROM TEMP A, MOD_TEST B
WHERE B.NO <= 2
AND A.LEV = '과장';
```

(참고 MOD_TEST 테이블)
```SQL
CREATE TABLE MOD_TEST
AS
SELECT ROWNUM NO FROM TEMP;
```

ROWNUM 속성으로 만들었던 테이블이다.


---

###### 80613142

EQUI JOIN
-

조건 절에 기술된 조인 조건을 EQUAL 연산자로 연결하는 경우

> Q TEMP에서 사번, 성명, 부서코드를 읽어 내는데 부서명칭을 함께 보고싶은 경우를 생각해 보자. 부서명은 TEMP에 존재하지 않는다. 이때 TDEPT를 연결해 부서명칭을 함께 읽어 와야 한다. 두 테이블이 연결되는 조건은 부서코드가 되어야 될 것이다?.

```SQL
SELECT A.EMP_ID,A.EMP_NAME,A.DEPT_CODE,B.DEPT_NAME
FROM TEMP A ,TDEPT B
WHERE A.DEPT_CODE=B.DEPT_CODE;

===

EMP_ID     EMP_NAME   DEPT_C DEPT_NAME           

---------- ---------- ------ --------------------

  19970101 김길동     AA0001 경영지원           

  19960101 홍길동     AB0001 재무               

  19970201 박문수     AC0001 총무               

  19930331 정도령     BA0001 기술지원     
...
```

PK가 두개이상으로 구성된 복합키를 가진 테이블간의 조인은 실수로 그 중 하나를 조인조건에서 뺼경우
의도하지 않은 엉뚱한 결과가 나올수 있다고 한다.

Q. TCOM의 WORK_YEAR = ‘2001’인 자료와 TEMP를 사번으로 연결해서 JOIN 한 후 COMM을 받는 직원의 성명, SALARY+ COMM  을 조회 해 보자. 

```SQL
SELECT B.EMP_NAME,B.SALARY+A.COMM
FROM TCOM A,TEMP B
WHERE A.WORK_YEAR='2001'
AND A.EMP_ID=B.EMP_ID;
```


---

###### 8061472

NON EQUI JOIN
-

조건 절에서 조인 조건이 '=' 이 아닌 다른 연산기호로 주어지는 경우

Q EMP_LEVEL에 직급별로 나이의 상하한이 존재한다. FROM_AGE, TO_AGE 가 그 값이다. 이 값을 이용해 과연 TEMP에 존재하는 직원들 중 과장 직급을 가질만한 나이에 포함되는 사람이 누군지를 현재 직급에 관계없이 읽어오는 QUERY를 만들어 보자.

방법은 현재일자를 기준으로 FROM_AGE 만큼의 년도를 빼고 TO_AGE 만큼의 년도를 빼서 그 사이에 생일이 포함되는 사람을 직급별로 보여주는 방법을 사용한다.

```SQL
SELECT B.LEV 직급, 
       A.EMP_ID 사번,
       A.EMP_NAME 성명,
       A.BIRTH_DATE 나이,
       A.LEV 현재직급
FROM TEMP A, EMP_LEVEL B
WHERE A.BIRTH_DATE BETWEEN ADD_MONTHS(SYSDATE, -1*TO_AGE*12) -- ADD_MONTHS는 개월수 단위의 가감계산( 현재시간 - 나이*12개월한것)
      AND ADD_MONTHS(SYSDATE, -1*FROM_AGE*12)
      AND B.LEV = '과장';
```


Q. 질문 : TEMP 와 EMP_LEVEL 을 이용해 EMP_LEVEL 의 과장 직급의 연봉 상한/하한 범위 내에 드는 직원의 사번과,성명,직급,SALARY를 읽어 보자.

```SQL
SELECT A.EMP_ID,A.EMP_NAME,A.LEV,A.SALARY
 FROM TEMP A, EMP_LEVEL B
 WHERE A.SALARY BETWEEN FROM_SAL AND TO_SAL
 AND B.LEV = '과장';
``` 


---

###### 80614160

OUTER JOIN
-

한쪽 테이블 행에 대해 다른쪽 테이블에 일치하는 행이 없더라도 다른 쪽 테이블을 NULL로 하여 행을 RETURN 한다.
(조인시 한쪽 값만 있을때 , 있는 쪽 기준으로 값을 본다는 얘기)

Q 질문 : 각 사번의 성명, 이름, SALARY, 연봉하한금액, 연봉상한금액을 보고자 한다. TEMP와 EMP_LEVEL 을 조인하여 결과를 보여주되, 연봉의 상하한이 등록되어 있지 않은 ‘수습’ 사원은 성명, 이름, SALARY 까지만이라도 나올 수 있도록 QUERY를 구성해 보라.

```SQL
 SELECT A.EMP_NAME, A.SALARY, B.FROM_SAL,B.TO_SAL
 FROM TEMP A , EMP_LEVEL B
 WHERE A.LEV=B.LEV(+);
```


---

###### 80614161

JOINING TABLE INSELF (SELF JOIN)
-

같은 테이블을 다른 테이블처럼 인식 시켜서 JOIN하는 기법
반드시 ALIAS를 사용한다.

1단계 순환 참조 OR 한 ROW에 동일 두개 레코드를 보여주고자 할떄 유용

SELF JOIN이라고도 한다.

Q 1)  TDEPT  테이블에 자신의 상위 부서 정보를 관리하고 있다. 이 테이블을 이용하여 부서코드, 부서명, 상위부서코드, 상위부서명을 읽어오는 SQL 을 만들어 보자.

```SQL
SELECT A.DEPT_CODE 부서코드, A.DEPT_NAME 부서명, B.DEPT_CODE 상위부서코드,B.DEPT_NAME 상위부서명
 FROM TDEPT A,TDEPT B
 WHERE A.PARENT_DEPT = B.DEPT_CODE;

```

QTEMP의 자료를 이용해 NON-EQUI JOIN이면서, SELF JOIN이고, OUTER JOIN인 QUERY를 하나 만들어 보자. 
TEMP 를 이용해 “사번”, ”성명”, ”생일”, ”자신보다 생일이 빠른 사람의 수” 를 읽어와 “자신보다 생일이 빠른 사람의 수”  순서로 정렬하는 QUERY를 만들되 위에서 언급한 3개의 조인을 한 문장에 모두 사용해 보라.

```SQL
SELECT A.EMP_ID 사번, A.EMP_NAME 성명 , A.BIRTH_DATE 생일 , COUNT(B.BIRTH_DATE)
FROM TEMP A, TEMP B
WHERE B.BIRTH_DATE(+) < A.BIRTH_DATE
GROUP BY A.EMP_ID,A.EMP_NAME,A.BIRTH_DATE
ORDER BY COUNT(B.BIRTH_DATE);
```


Q TEMP 와 TDEPT를 이용하여 다음 컬럼을 보여주는SQL 을 만들어 보자.   상위부서가 ‘CA0001’ 인 부서에 소속된 직원을 1.사번,  2.성명, 3.부서코드, 4.부서명, 5.상위부서코드, 6.상위부서명,  7.상위부서장코드,  8.상위부서장성명 순서로 보여주면 된다.

```SQL
SELECT A.EMP_ID, 
       B.DEPT_CODE,
       B.DEPT_NAME,
       C.DEPT_CODE,
       C.DEPT_NAME,
       D.EMP_ID,
       D.EMP_NAME
FROM TEMP A, TDEPT B,TDEPT C,TEMP D
WHERE B.DEPT_CODE = A.DEPT_CODE
AND C.DEPT_CODE = B.PARENT_DEPT
AND C.DEPT_CODE = 'CA0001'
AND D.EMP_ID = C.BOSS_ID;
```



---

###### 80614204

서브쿼리
-

SELECT 한 결과를 조건 비교에 사용하거나
UPDATE, INSERT에 사용될 떄

InnerQuery와 OuterQuery 로 구성된다.

조건에서 다른 쿼리의 결과가 사용될때
또 다른 쿼리를 InnerQuery(서브쿼리)라고 하고 InnerQuery의 결과를 비교조건으로 사용하는 쿼리를 OuterQuery(메인쿼리)라 칭한다.

종류

- SINGLE ROW
- MULTI ROW
- MULTI COLUM
- CORRELATED

에서 중복되어 사용되면

- SINGLE ROW MULTI COLUM 
- MULTI ROW MULTI COLUMN 
- SINGLE ROW CORRELATED 
- MULTIROW CORRELATED 등으로 세분화된다.


---

###### 8061565

SINGLE ROW 서브쿼리
-

SELECT에서 결과로 나오는 행의 수가 한 행이며, 이 중 한 컬럼을 SUBQUERY로 이용하는 경우

Q TEMP에서 연봉이 가장 많은 직원의 ROW를 찾아서 이 금액과 동일한 금액을 받는 직원의 사번과 성명을 보여 주는 문장이다.

```SQL
SELECT EMP_ID,EMP_NAME
FROM TEMP 
WHERE SALARY = (SELECT MAX(SALARY)
                FROM TEMP);
```

Q TEMP 의 자료를 이용해 SALARY의 평균을 구하고 이보다 큰 금액을 SALARY로 받는 직원의 사번과,성명,연봉을 보여주자.

```SQL
SELECT EMP_ID,EMP_NAME,SALARY
FROM TEMP
WHERE SALARY > (SELECT AVG(SALARY)
                FROM TEMP);
```



---

###### 80615103

MULTI ROW 서브쿼리
-

서브쿼리의 결과 행이 한 행 이상 될 수있는 경우
IN,ANY,ALL,EXISTS등 연산시에만 가능.

Q TEMP의 직원 중 인천에 근무하는 직원의 사번과 성명을 읽어오는 SQL을 SUB QUERY 를 이용해 만들어 보자. 이때 부서가 위치하는 있는 AREA 가 근무지가 된다.

```SQL
SELECT EMP_ID,EMP_NAME
FROM TEMP
WHERE DEPT_CODE IN (SELECT DEPT_CODE
                   FROM TDEPT
                   WHERE AREA = '인천');
```


Q TCOM에 연봉 외에 COMMISSION을 받는 직원의 사번이 보관되어 있다. 이 정보를 SUB QUERY로 SELECT 하여 부서 명칭별로 COMMISSION 을 받는 인원수를 세는 문장을 만들어 보자.

```SQL
SELECT B.DEPT_NAME,COUNT(*)
FROM TEMP A,TDEPT B
WHERE B.DEPT_CODE = A.DEPT_CODE
AND A.EMP_ID IN(SELECT EMP_ID FROM TCOM)
GROUP BY B.DEPT_NAME;
```



---

###### 8061511

MULTI COLUMN 서브쿼리
-

서브쿼리에서 결과로 나오는 행(들)이 한 컬럼이 아닌 두개 이상을 가지는 경우

보통은 PRIMARY  KEY 컬럼이 두개 이상인 경우에 KEY값을 한꺼번에 묶어서 비교하기 위해 자주 사용함.

Q TDEPT에서 부서코드와 BOSS_ID를 읽어서, 이 두개의 컬럼이 TEMP의 부서코드와 사번에 일치하는 사람의 사번과 성명을 읽어오는 쿼리를 만들어 보자.

```SQL
SELECT EMP_ID,EMP_NAME
FROM TEMP
WHERE (DEPT_CODE,EMP_ID) IN 
                        (SELECT DEPT_CODE,BOSS_ID
                         FROM TDEPT);


====

19960101	홍길동
19970201	박문수
19950303	이순신
19966102	지문덕
```


질문 : TEMP에서 부서별 최고 연봉금액을 읽어서,해당 부서와 최고 연봉금액이 동시에 일치하는 사원의 사번, 성명, 연봉을 읽어 보라.

```SQL
SELECT EMP_ID,EMP_NAME,SALARY
FROM TEMP
WHERE (DEPT_CODE,SALARY) IN (SELECT DEPT_CODE,MAX(SALARY)
                        FROM TEMP                        
                        GROUP BY DEPT_CODE);


===

19930331	정도령	70000000
19960212	배뱅이	39000000
19970101	김길동	100000000
19966102	지문덕	45000000
...
```

---

###### 80615144

CORRELATED 서브쿼리
-

InnerQuery에서 OuterQuery의 어떤 값을 사용하는 경우를 CORRELATED 서브쿼리 라고 한다.

일반적인 서브쿼리는 InnerQuery(서브쿼리)의 결과를 OuterQuery(메인쿼리) 가 사용하지만,

CORRELATED 서브쿼리는 반대로 서브쿼리가 메인쿼리의 값을 이용하고 , 그렇게 구해진 서브쿼리의 값을 다시 메인쿼리가 사용한다.

Q 직원 중 자신의 연봉이 자신과 같은 LEV에 해당하는 직원의 평균 SALARY 보다 많은 경우에 이 사원의 사번과 성명을 읽어 오는 QUERY 를 만들어 보자.

```SQL
SELECT EMP_ID, EMP_NAME
FROM TEMP A
WHERE SALARY > (SELECT AVG(SALARY)
                FROM TEMP B
                WHERE B.LEV = A.LEV);
```

서브쿼리가 늦다는 생각을 가지는 이유

Correlated Subquery - 반드시 하나의 레코드가 조건 절에서 비교되기 위해 InnerQuery 가 한번씩 수행되어야 하기 때문

만건 이라면 sql 수행위해 InnerQuery가 만번 수행되야한다.

그런데 위의 정보에 본인의 연봉과 평균연봉을 함께 보고싶다면? -> 무용지물

서브쿼리에서 뽑아낸 정보인 평균연봉을 select에 포함시킬 수 없기 때문.
그때는 inline view등 다른방법이 필요

Q 해당부서에 속하는 사원을 찾아서 이 중 사번이 가장 빠른 사람을 부서장으로 임명한다고 가정하고 결과를 TDEPT에 반영하는UPDATE 문장을 만들어 보자.

```SQL
UPDATE TDEPT A
SET A.BOSS_ID = (SELECT MIN (B.EMP_ID)
                 FROM TEMP B
                 WHERE B.DEPT_CODE = A.DEPT_CODE
                 );
```


---

###### 80615153

UPDATE , INSERT 기본문법(알아두기)
-

<UPDATE>
UPDATE table_name
SET column_name1=변경값1,
       column_name2=변경값2
	...

<INSERT>

1.일부컬럼
INSERT INTO table명(컬럼1,컬럼2)
	       VALUES(값1,값2);

2.모든컬럼
INSERT INTO table명
 	       VALUES(값1,값2...)

3.SELECT문 사용시

INSERT INTO table1
	SELECT * FROM table2


EX)  TEMP 테이블의 각 사원별 연봉의 10%를 COMMISSION으로 하는 새로운 자료를 TCOM에 INSERT 하자.

                 이때 WORK_YEAR 는 ‘2002’,BONUS_RATE = 0.1 이 된다.                       

```SQL
INSERT INTO TCOM (WORK_YEAR,EMP_ID,BONUS_RATE,COMM)
                  SELECT '2002',EMP_ID,0.1,SALARY*0.1
                  FROM TEMP;
```


---

###### 80615154

ANY와 ALL
-

서브쿼리와 함께 쓰이거나 값 들의 집합과 함께 쓰여 비교된다.

나올 값이 뭔지 이미 알때는 굳이 ANY를 쓸필요가없다.
( SALARY > ANY (100,200,300) )
같은경우 그냥 SALARY > 100 해도 같은 결과다.

동등비교의 경우도 IN이라는 연산자가 있으니 굳이 ANY를 쓸필요가 없는데,

대개 비교되는 값의 집합이 서브쿼리에 의해 나오는 결과일때 사용한다. 어떤값인지는 모르지만, 비교하고자 할때 사용.

ALL 또한 마찬가지로 서브쿼리에서 사용 된다.


Q  서브쿼리를 통해 LEV = ‘과장’ 인 직원의 SALARY를 TEMP 에서 읽고 이중의 어떤 값 하나 보다 큰 연봉을 가진 직원의 사번, 성명, 연봉을 읽어 오는 쿼리를 만들어 보자.

```SQL
SELECT EMP_ID, EMP_NAME, SALARY
FROM TEMP
WHERE SALARY > ANY(SELECT SALARY
                   FROM TEMP
                   WHERE LEV ='과장');

====

19970101	김길동	100000000
19960101	홍길동	72000000
19930331	정도령	70000000
19930402	강감찬	64000000
...
```

Q 어떤 과장 보다도 연봉이 많은 직원을 읽어 보자.

```SQL
SELECT EMP_ID, EMP_NAME, SALARY
FROM TEMP
WHERE SALARY > ALL(SELECT SALARY
                   FROM TEMP
                   WHERE LEV = '과장');



====

19970101	김길동	100000000
```



---


###### 80616155

EXISTS - 적어도 하나의 행을 돌려주는지
-

서브쿼리가 적어도 하나의 행을 돌려 주는지를 체크 하고자 할 때 사용
(한개 이상 존재할 경우 SELECT 실행됨, SELECT,UPDATE, INSERT, DELETE에서 사용가능)

NOT EXISTS의 경우 반대로 일치하지 않는 레코드를 반환


EXIST는 단지 해당 ROW에 존재하는지 확인한다.
IN은 실제 존재하는 데이터의 모든 값까지 확인
NOT IN의 경우 조건에 맞는 데이터가 있다해도 NULL이 존재하면 "no rows selected"라고 나온다. 그래서 nvl을 이용한 null 처리가 꼭 필요하다.


Q ANY 와 ALL 의 첫번쨰 예제를 EXIST로 바꿔본다.                   

```SQL
SELECT EMP_ID, EMP_NAME, SALARY
FROM TEMP A
WHERE EXISTS (SELECT B.SALARY
              FROM TEMP B
              WHERE B.LEV = '과장'
              AND A.SALARY > B.SALARY
              );`


======

19970101	김길동	100000000
19960101	홍길동	72000000
19930331	정도령	70000000
19930402	강감찬	64000000
19950303	이순신	56000000
...
```

