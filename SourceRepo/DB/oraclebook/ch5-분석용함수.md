- [5장 분석용함수](#8629_155)

---


###### 8629_155

5장 분석용함수
-

오라클 8.1.6 버전 이후부터 분석용 함수제공



---
- [RANKING](#8629_160)

###### 8629_160

RANKING
-

주어진 기준값에 근거하여 DATASET내의 다른 RECORD와 비교되는 RECORD의 순서를 계산해낸다.


 1) TEMP 테이블의 자료를 이용하여 SALARY 값으로 순위를 부여하는 SQL 을 만들어 보자.

```SQL
SELECT EMP_ID, EMP_NAME, SALARY,

       DENSE_RANK() OVER (ORDER BY SALARY  DESC) AS C1,

       RANK()        OVER (ORDER BY SALARY  DESC) AS C2,

       RANK()        OVER (ORDER BY SALARY) AS C3

FROM    TEMP;

===================

EMP_ID     EMP_NAME   SALARY     C1         C2         C3       

---------------- ---------------------- ---------------- --------------- -------------- --------------

20000101     이태백       30000000     10         11          1

  20000102     김설악       30000000     10         11          1

  20000334     박지리       30000000     10         11          1

  20000203     최오대       30000000     10         11          1

  20006106     유도봉       30000000     10         11          1

  20000308     강월악       30000000     10         11          1

  20000210     나한라       30000000     10         11          1

  20000119     장금강       30000000     10         11          1

  20000407     윤주왕       30000000     10         11          1

  20000305     정북악       30000000     10         11          1

  19960303     설까치       35000000      9         10         11

  19960212     배뱅이       39000000      8          9         12

  19966102     지문덕       45000000      7          7         13

  19970112     연흥부       45000000      7          7         13

  19970201     박문수       50000000      6          6         15

  19950303     이순신       56000000      5          5         16

  19930402     강감찬       64000000      4          4         17

  19930331     정도령       70000000      3          3         18

  19960101     홍길동       72000000      2          2         19

  19970101     김길동      100000000      1          1         20
. .  .

```
RANK와 DENSE_RANK 의 차이점은 순위를 부여하는 간격에 있다.

       7등이 두 명 나오면 다음 등수는 RANK 의 경우 9등이 되며, DENSE_RANK 의 경우 8등이 된다.

       함수 뒤에 붙는 OVER의 의미는 해당 함수가 QUERY의 RESULTSET을 이용하여 동작 하는 함수라는 표시


 2) 다음은 PARTITION BY 의 기능이다.

       TEMP 의 직원들을 부서별로 급여기준 순위를 부여하는 문장을 만들어 보자.
```SQL
SELECT DEPT_CODE,EMP_ID,EMP_NAME,SUM(SALARY),

       RANK() OVER

             (PARTITION BY DEPT_CODE

              ORDER     BY SUM(SALARY)  DESC) AS C1

FROM   TEMP

GROUP BY  DEPT_CODE,EMP_ID,EMP_NAME;

 

DEPT_C    EMP_ID   EMP_NAME   SUM(SALARY     C1  

----------- -------------------- ------------------ ---------------------- ---------------

AA0001   19970101      김길동      100000000          1

AA0001   20000101      이태백       30000000          2

AB0001   19960101      홍길동       72000000          1

AB0001   20000102      김설악       30000000          2

AC0001   19970201      박문수       50000000          1

AC0001   20000203      최오대       30000000          2

BA0001   19930331      정도령       70000000          1

BA0001   20000334      박지리       30000000          2

BB0001   19950303      이순신       56000000          1

BB0001   20000305      정북악       30000000          2

BC0001   19966102      지문덕       45000000          1

BC0001   20006106      유도봉       30000000          2

CA0001   19930402      강감찬       64000000          1

CA0001   20000407      윤주왕       30000000          2

CB0001   19960303      설까치       35000000          1

CB0001   20000308      강월악       30000000          2

CC0001   19970112      연흥부       45000000          1

CC0001   20000119      장금강       30000000          2

CD0001   19960212      배뱅이       39000000          1

CD0001   20000210      나한라       30000000          2
```
 PARTITION BY DEPT_CODE 라는 문장에 의해 부서별 급여 RANKING이 구해졌다.


  질문 : SALE_HIST의 자료를 이용하여 일자별 매출순위와 순위별 사업장, 품목을 보여라

```SQL
SELECT SALE_DATE,SALE_SITE,SALE_ITEM,SALE_AMT ,

       RANK() OVER (PARTITION BY SALE_DATE ORDER BY SALE_AMT  DESC) AS C1

FROM   SALE_HIST;          

   결과 :

SALE_DATE  SALE_SITE  SALE_ITEM  SALE_AMT    C1       

----------------- ------------------- ------------------- ------------------- ---------

01/05/01         01       NOTEBOOK       9000        1

01/05/01         02       NOTEBOOK       7000        2

01/05/01         03       NOTEBOOK       7000        2

01/05/01         03       ERASER          6000        4

01/05/01         01       PENCIL           5000        5

01/05/01         01       ERASER          4500        6

01/05/01         02       ERASER          3000        7

01/05/01         02       PENCIL           2500        8

01/05/01         03       PENCIL           2500        8

...중략

01/05/04         01       ERASER           3500        7

01/05/04         03       PENCIL            3500        7

01/05/04         03       ERASER           3000        9

```

(CUME_DIST 라고 0~1까지 값으로 표현하는것으로 RANK와 비슷한 기능이지만, STUDY DEEP 목표에 의해 생략)


---
- [CASE - 조건에 따른 처리](#8710_083)

###### 8710_083

CASE - 조건에 따른 처리
-

기존에는 DECODE를 이용해 처리되던 부분들이 CASE를 통해 보다 간편하게 처리 될 수 있다.

 1) TEMP 의 자료를 SALARY로 분류 하여 30000000 이하는 'D',

                                            30000000 초과 50000000 이하는 'C'

                                            50000000 초과 70000000 이하는 'B'

                                            70000000 초과는 'A'

         라고 등급을 분류하여 등급별 인원수를 알고 싶다.

```SQL
SELECT CASE WHEN SALARY <= 30000000 THEN   'D' 

            WHEN SALARY BETWEEN 30000001 AND 50000000 THEN   'C' 

            WHEN SALARY BETWEEN 50000001 AND 70000000 THEN   'B' 

            WHEN SALARY >   70000000  THEN   'A'  END,

             COUNT(*)

FROM   TEMP

GROUP BY

       CASE WHEN SALARY <= 30000000 THEN   'D' 

            WHEN SALARY BETWEEN 30000001 AND 50000000 THEN   'C' 

            WHEN SALARY BETWEEN 50000001 AND 70000000 THEN   'B' 

            WHEN SALARY >   70000000  THEN   'A'  END;

C COUNT(*) 

- ----------

A          2

B          3

C          5

D         10

4 rows selected.
```

      2) 행단위로 나오는 값을 컬럼단위로 표현해 보자.

```SQL
SELECT COUNT(CASE WHEN SALARY >   70000000  THEN   'A'  END) AS A,

       COUNT(CASE WHEN SALARY BETWEEN 50000001 AND 70000000 THEN   'B'  END) AS B,

       COUNT(CASE WHEN SALARY BETWEEN 30000001 AND 50000000 THEN   'C'  END) AS C,

       COUNT(CASE WHEN SALARY <= 30000000 THEN   'D'  END) AS D

FROM   TEMP;

A    B      C      D        

 --------- ---------- ---------- ----------

      2    3       5      10

1 row selected.

```

 질문 : SALE_HIST의 자료를 이용하여 일자별 품목별로 '01','02' 사업장 판매 금액합,'02', '03' 사업장 판매 금액 합을 구하시오.

    
```SQL
SELECT SALE_DATE,SALE_ITEM,

       SUM(CASE WHEN SALE_SITE BETWEEN  '01' AND '02' THEN   SALE_AMT  END) AS S01,

       SUM(CASE WHEN SALE_SITE BETWEEN  '02' AND '03' THEN   SALE_AMT  END) AS S02

FROM   SALE_HIST

GROUP BY SALE_DATE,SALE_ITEM;         

          결과 :

SALE_DATE  SALE_ITEM    S01        S02      

-----------------  ------------------  -----------  --------------

01/05/01       ERASER        7500       9000

01/05/01       NOTEBOOK    16000      14000

01/05/01       PENCIL         7500       5000

01/05/02       ERASER        9500       9000

01/05/02       NOTEBOOK    12000      11500

01/05/02       PENCIL         9500       9000

01/05/03       ERASER        11500      12000

01/05/03       NOTEBOOK     11000       8500

01/05/03       PENCIL         10500      10000

01/05/04       ERASER          7500       7000

01/05/04       NOTEBOOK      11500      10500

01/05/04       PENCIL        13000      11000

12 rows selected.     

```


---
- [통계함수, 회귀 분석용 함수(#8710_084)

###### 8710_084

통계함수, 회귀 분석용 함수
-


 5-4 통계 함수

  VAR_POP : 모집단의 분산을 구해준다.

  AVR_SAMP : 표본집단의 분산을 구해준다.         

  STDDEV_POP : 모집단의 표준편차를 구해준다.

  SDTDEV_SAMP : 표본집단의 표준편차를 구해준다.

  CORVAR_POP : 모집단의 공 분산을 구해준다.

  CORVAR_SAMP : 표본집단의 공 분산을 구해준다.  

  CORR  : 상관계수을 구해준다.

 

 5-5 회귀 분석용 함수

  REGR_COUNT : 회귀선상에 찍히는 값들의 숫자를 구해준다.

  REGR_AVGY,

  REGR_AVGX : 회귀선의 독립변수와 종속변수의 평균을 구해준다.

  REGR_SLOPE,

  REGR_INTERCEPT : 회귀선의 기울기를 구해준다.

  REGR_R2 : 회귀선의 적합도를 구해준다.

  REGR_SXX,

  REGR_SYY,

  REGR_SXY : 회귀분석용 함수.


(test)

    SELECT SALE_DATE,SALE_ITEM,

        SUM(CASE WHEN SALE_SITE BETWEEN  '01' AND '02' THEN   SALE_AMT  END) AS S01,

        SUM(CASE WHEN SALE_SITE BETWEEN  '02' AND '03' THEN   SALE_AMT  END) AS S02

    FROM   SALE_HIST

    GROUP BY SALE_DATE,SALE_ITEM;         