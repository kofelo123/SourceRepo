
- [2장 함수](#ch2function)
  -[단일행함수 - 문자함수]
  - [INSTR - 첫번쨰 자리수 반환](#instr)
  - [SUBSTR - 문자열반환](#substr)
  - [LENGTH - 문자열길이 반환](#length)
  - [LPAD - 문자열 좌측 채우기](#lpad)
  - [REPLACE - 문자 바꾸기](#replace)
  - [CHR(아스키반환), ASCII](#chrAscii)
  -[단일행함수 - 숫자함수]
  - [ROUND -반올림](#round)
  - [TRUNC -버림](#trunc)
  - [SIGN - 음수,정수,0 여부 반환](#sign)
  - [CEIL - 크거나 같은 정수값 중 최소값](#ceil)
  - [FLOOR - 작거나 같은 정수 중 최대값 ](#floor)
  - [MOD - 나눈 나머지](#mod)  
  - [POWER - 제곱한 값 반환](#power)
  -[단일행함수 - 날짜처리함수]
  - [날짜와 숫자의 계산](#calnum)
  - [SYSDATE(알아두기)](#sysdate)  
  - [DATE 형에 시간 더하고 빼기](#adddatetype)
  - [ADD_MONTHS  - 월 단위의 가감연산](#addmonths)
  - [MONTHS_BETWEEN - 두 일자사이의 간격이 몇개월인지](#monthbetween)
  - [LAST_DAY - 포함된 월의 마지막일 반환](#lastday)
  
  - [데이터형 변환함수](#datatranfunction)
        - [TO_CHAR - 날짜,숫자 => 문자](#tochar)
        - [TO_NUMBER - 문자열을 숫자로 변환](#tonumber)
        - [TO_DATE - 문자를 DATE형으로](#todate)
 
  - [그룹함수](#groupfunction)
        - [COUNT - 로우 수 반환](#count)
        - [MIN과 MAX](#minmax)
        - [SUM 과 AVG](#sumavg)
        
 
  -[알아두기]
  - [DUAL TABLE 이란(알아두기)](#dualTable)
  - [CREATE TABLE AS SELECT ... (알아두기)](#createas)
  - [COMMIT 과 ROLLBACK(알아두기)](#commitRollback)
  - [그룹함수 사용시 장점(알아두기)](#groupfunctionadv)
  - [평균에서의 NULL(알아두기)](#nullinavg)     
  

  

###### ch2function

2장 함수
-

내장함수는 유저가 CREATE FUNCTION 으로 만든 함수가 아닌, 오라클 자체적으로 제공하는 함수를 말한다.

오라클 함수는 크게 두가지
1. 단일 행 함수 2. GROUP 함수

공통점: 결과로 반환하는 ROW는 하나
차이점: 단일행 함수는 하나의 행 값이 함수에 적용되어 하나의 행 리턴
GROUP함수는 하나 이상의 행 값이 적용되어 하나의 값이 리턴(ex 직급별로 하면 각 직급당 하나의 결과를 돌려준다)

 ![](https://drive.google.com/uc?export=view&id=1J1Pw8U5GCT18r8jMISUVB6CqsIcHmmuH)
 ![](https://drive.google.com/uc?export=view&id=1ON8Fc_d4B5ANhyntTpYU6RnyrpbbsF4t)
 ![](https://drive.google.com/uc?export=view&id=1olfk40E9-cH1H2gXrGWYj__46WgOzBxy)
 ![](https://drive.google.com/uc?export=view&id=1OO3u1J1-X1aNsr5z02dEitjLFuBwlwHw)



---


###### instr

문자함수 - INSTR
-

문자열에서 지정한 문자의 첫번째 자리 수 를 반환

INSTR은 문자단위처리, INSTRB는 BYTES 단위로 처리

문자열내에서 D,나 의 자리수를 반환해본다.

```SQL
SELECT INSTR('ABC좋은나라DEF웃긴나라','D'),
        INSTR('ABC좋은나DEF웃긴나라','나')
FROM DUAL;
```
결과)

INSTR('ABC  INSTR('ABC

-----------------  -----------------

    8              6


'ABC좋은나라DEF웃긴나라' 문자열에서 두번째 나타나는 '나'의 위치 찾기

```SQL
SELECT INSTR('ABC좋은나라DEF웃긴나라','나',1,2)
FROM DUAL;
```
결과)
 -----------------

          13

문자열의 1번째 자리부터 두번째 나타나는 '나' 위치검색하라는것.

다르게 생각해서 첫번쨰 '나'가 6번쨰 자리에 있다는걸 안다는 가정하게 다음과도 같은 결과

```SQL
SELECT INSTR('ABC좋은나라DEF웃긴나라','나',7,1)
FROM DUAL;
```





---


###### dualTable

DUAL TABLE 이란(알아두기)
-

SYS USER가 소유 - 단일 행에 단일 컬럼만 가지는 테이블
용도: 산술 연산, 날짜연산

연산을 위해 일시적으로 쓰는 DUMMY 테이블이다.

산술 연산시에 SELECT절에 연산후 특정 일반 FROM 테이블을 지정하면 연산에 해당하는 로우가 반복적으로 모두 출력된다.

그래서 한행만 가지는 DUAL테이블로 연산을 한다.




---


###### substr

SUBSTR - 문자열반환
-

첫번째 지정숫자 - 두번쨰 지정숫자 만큼의 문자열 반환

2~10까지 문자열출력

```SQL
SELECT SUBSTR('ABC좋은나라DEF웃긴나라',2,10)
FROM DUAL;
```
결과)

SUBSTR('ABC좋은

---------------

BC좋은나라DEF웃


뒤에서 부터 몇번쨰 문자 읽기

```SQL
SELECT SUBSTR('ABC좋은나라DEF웃긴나라',-4,2)
FROM DUAL;
```
결과)


    SUBS

    -------

    웃긴



---


###### length

LENGTH - 문자열길이 반환
-

주어진 문자열의 길이를 돌려줌 (LENGTHB - 바이트단위)

```SQL
SELECT LENGTH('ABC좋은나라DEF웃긴나라')
FROM DUAL;
```
결과

14




---


###### lpad

LPAD - 문자열 좌측 채우기
-

문자열이 지정된 숫자만큼의 크기가 되도록 문자열의 왼쪽에 지정한 문자를 채워 준다.
(바이트 단위로 지원한다.)

TDEPT 테이블의 DEPT_NAME을 읽어오는데 우측정렬시키고 전체 10자리 기준으로 앞의 공란에 * 찍는문장.

```SQL
SELECT LPAD(DEPT_NAME,10,'*')
FROM TDEPT;
```
결과)
---------------------

**경영지원          

******재무          

******총무          

**기술지원     


위의 상황에서 왼쪽 공란에 순서대로 번호를 붙이는 문장.

```SQL
SELECT LPAD(DEPT_NAME,10,'123456789')
FROM TDEPT;
```
결과)
 12경영지원          

          123456재무          

          123456총무          

          12기술지원        
...


* 'RPAD'는 반대로 우측부터 채우는 함수이다.

Q TDEPT의 DEPT_NAME을 10자리에 맞추고 뒤에 남는 자리에 자신의 자릿수에 해당하는 숫자를 붙여본다.(중첩함수)

```SQL
SELECT RPAD(DEPT_NAME, 10, SUBSTR('1234567890',LENGTHB(DEPT_NAME)-3))
FROM TDEPT;
```
(LENGTHB는 바이트단위가 ORACLE의 인코딩에 따라 좀 다를수있다.(나는 3BYTE 책에서는 2BYTE)



---


###### replace

REPLACE - 문자 바꾸기
-

첫번째 지정 문자를 두번째 지정문자로 바꾼다.

```SQL
SELECT REPLACE('ABC좋은나라DEF웃긴나라','나라','**')
FROM DUAL;
```
========================

ABC좋은**DEF웃긴**

질문 :   REPLACE 함수를 중첩 사용해서 'ABC좋은나라DEF웃긴나라' 중 한글만 남도록 SQL 을 만들어 보자.

```SQL
SELECT REPLACE(REPLACE('ABC좋은나라DEF웃긴나라','ABC',''),'DEF','')
FROM DUAL;
```
(중첩사용)

========================

             좋은나라웃긴나라




---


###### chrAscii

CHR(아스키반환), ASCII
-

CHR:

지정된 수치(숫자)와 일치하는 ASCII 코드를 돌려준다.
```SQL
SELECT CHR(65) FROM DUAL;
```
============

 A


ASCII : 지정된 문자의 ASCII 값을 돌려줌

```SQL
SELECT ASCII('A') 
FROM DUAL;
```
==========

65




---


###### round

ROUND -반올림
-

반올림에 사용, 지정한 숫자가 양수이면 소수점 아래를 의미, 음수점 소수점 위를 의미

생략되면 반올림해서 정수를 만듬

```SQL

SELECT ROUND('1234.567',1),
        ROUND('1234.567',-1),
        ROUND('1234.567')
FROM DUAL;

==============================

             1234.6       1230       1235
```




---


###### trunc

TRUNC -버림
-

버림에 사용하는 함수

```SQL
SELECT TRUNC('1234.567',1),
        TRUNC('1234.567',-1),
        TRUNC('1234.567')
FROM DUAL;
```
==============================

           1234.5       1230       1234


---


###### sign

SIGN - 음수,정수,0 여부 반환
-

음수,정수,0 여부 반환 (NULL일경우 NULL반환)

```SQL
SELECT SIGN(-10),SIGN(0),SIGN(10),SIGN(NULL)
FROM DUAL;
```

======

-1    0   1   (NULL)




---


###### ceil

CEIL - 크거나 같은 정수값 중 최소값
-

주어진 숫자보다 크거나 같은 정수 중 최소값 반환

```SQL
SELECT CEIL(2),CEIL(2.1)
FROM DUAL;
```

======

2   3


---


###### floor

FLOOR - 작거나 같은 정수 중 최대값 
-

주어진 수사보다 작거나 같은 정수 중 최대값 반환

```SQL
SELECT FLOOR(2),FLOOR(2.1)
FROM DUAL;
```

======

2 2



---


###### mod

MOD - 나눈 나머지
-

```SQL
SELECT MOD(1,3), MOD (2,3), MOD(3,3), MOD(4,3)
FROM DUAL;
```

========================

1	2	0	1




---


###### createas

CREATE TABLE AS SELECT ... (알아두기)
-

```SQL
CREATE TABLE table_name
AS
SELECT ...
FROM ....
...;
```
CREATE TABLE table_name AS 뒤에 SELECT 문장이 나오는 경우 -> SELECT 한 결과로 바로 테이블을 CREATE 시키고자 할때 사용.

DDL문장이 실행되면 자동 COMMIT 이 일어나게 된다.



---


###### 

COMMIT 과 ROLLBACK(알아두기)
-

COMMIT은 저장, ROLLBACK은 취소(COMMIT이후  ~ 바로직전)
하나의 트랜잭션 완료는 COMMIT 기준이다

COMMIT을 하지않아도 자동 COMMIT 되는 경우가 있다.

1.DDL이나 DCL문장
2.SQL*PLUS에서 exit을 이용하여 정상종료시
3.Set AutoCommit on으로 환경 지정되어 있을시



---


###### power

POWER - 제곱한 값 반환
-

주어진 숫자의 지정된 수 만큼의 제곱한값을 반환

Q:2의 10제곱까지의 값을 MOD_TEST 테이블로 출력

```SQL
SELECT POWER(2,NO)
FROM MOD_TEST;
```
===

       2

      4

      8

     16
...

NO는 칼럼이고 1,2,3,4,5... 로 되어있다.
2를 기준으로 1제곱2제곱3제곱.. 한것.




---


###### calnum

날짜와 숫자의 계산
-

1. 날짜+날짜(x)
2. 날짜-날짜(O)
3. 날짜+숫자(o)
4. 날짜-숫자(o)

1) 날짜와 날짜를 더하는것은 안된다.

2)날짜에서 날짜를 빼면 그 사이의 일수가 숫자로 나온다.
```SQL
SELECT SYSDATE-BIRTH_DATE
FROM TEMP
WHERE EMP_NAME='홍길동';
```
======

 10262.4922


3,4)날짜에 숫자를 더하거나 빼면 날짜가 나온다.(날짜+숫자=일자 더하기)

```SQL
SELECT BIRTH_DATE,BIRTH_DATE+1,BIRTH_DATE-1
FROM TEMP
WHERE EMP_NAME = '홍길동';
```
====

73/03/22	73/03/23	73/03/21



---


###### sysdate

SYSDATE(알아두기)
-

SYSDATE는 시스템의 일자를 DATE 형으로 보관하고 있는 가상 칼럼이다. 시스템 일자를 보여주는 함수라고도 할 수있다.

SYSDATE를 사용하면 현재일자를 초 단위까지 알아낼 수있다.


---

###### adddatetype

DATE 형에 시간 더하고 빼기
-

(날짜 + 숫자 = 일자더하기)

<시간,분,초를 더하기 >

DATE형을 SELECT하면 일자 까지 나옴.

시,분,초를 보기위해 TO_CHAR 함수 사용

```SQL
SELECT TO_CHAR(BIRTH_DATE,'HH24:MI:SS') BIRTH_TIME,
        TO_CHAR(BIRTH_DATE+14/24,'HH24:MI:SS') AS TIME,
        TO_CHAR(BIRTH_DATE+30/(24*60),'HH24:MI:SS') AS MIN,
        TO_CHAR(BIRTH_DATE+50/(24*60*60),'HH24:MI:SS') AS SEC
FROM TEMP
WHERE EMP_NAME = '홍길동';
```

===========

00:00:00	14:00:00	00:30:00	00:00:50



---

###### addmonths

```SQL
ADD_MONTHS  - 월 단위의 가감연산
-

SELECT BIRTH_DATE, 
       ADD_MONTHS(BIRTH_DATE,1),  -- 한달 더하기
       ADD_MONTHS(BIRTH_DATE,-13) -- 13개월 빼기
FROM TEMP
WHERE EMP_NAME = '홍길동';
```
====

73/03/22	73/04/22	72/02/22


---

###### monthbetween

MONTHS_BETWEEN - 두 일자사이의 간격이 몇개월인지
-

```SQL
SELECT MONTHS_BETWEEN(SYSDATE,BIRTH_DATE) MON_TERM
FROM TEMP
WHERE EMP_NAME = '홍길동';
```
542.558094384707287933094384707287933094



---

###### lastday

LAST_DAY - 포함된 월의 마지막일 반환
-

```SQL
SELECT BIRTH_DATE,LAST_DAY(BIRTH_DATE)
FROM TEMP;
```

===

74/01/25	74/01/31
73/03/22	73/03/31
75/04/15	75/04/30
...

---

###### datatranfunction

데이터형 변환함수 
-

문자와 숫자, 문자와 날짜 간의 형 변환을 위한 함수

숫자,날짜 => 문자  - TO_CHAR
문자        => 숫자  - TO_NUMBER
문자        => 일자  - TO_DATE


---

###### tochar

TO_CHAR - 날짜,숫자 => 문자
-

<날짜를 문자로>

아래는 DATE 형을 문자열로 변환할때 자주 쓰는 형식

 ![](https://drive.google.com/uc?export=view&id=1bskOI4KO0YY9OqCHIN5vA2zvBdTtf_2G)

```SQL

SELECT TO_CHAR(SYSDATE,'YY/MM/DD') DATE1,
        TO_CHAR(SYSDATE,'YYYY.MM.DD')DATE2,
        TO_CHAR(SYSDATE,'Month DD.YYYY')DATE3,
        TO_CHAR(SYSDATE,'DY DD MON YY')DATE4,
        TO_CHAR(SYSDATE,'Day Mon DD') DATE5
FROM DUAL;
```

====

18/06/08	2018.06.08	6월  08.2018	금 08 6월  18	금요일 6월  08


> TEMP 의 BIRTH_DATE를 이용해서 사번 별로 생일과 요일을 보여주는 문장을 만들어 보자.

```SQL
SELECT EMP_ID,TO_CHAR(BIRTH_DATE,'YYYY"년" MM"월" DD"일" DAY')
FROM TEMP;
```
===

19970101	1974년 01월 25일 금요일
19960101	1973년 03월 22일 목요일
19970201	1975년 04월 15일 화요일
...


<숫자를 문자로>

 ![](https://drive.google.com/uc?export=view&id=1qXRlP0q21J1GwIOXO_qZK5FqcCM3MI_i)

```SQL
SELECT TO_CHAR(1234,'09,999') NUMBER1,
      TO_CHAR(1234.56,'99,999.99') NUMBER2,
      TO_CHAR(1234,'$99,999') NUMBER3,
      TO_CHAR(1234,'L99,999') NUMBER4,
      TO_CHAR(-1234,'L99,999MI') NUMBER5
FROM DUAL;
```
=====

 01,234	  1,234.56	  $1,234	         ￦1,234	        ￦1,234-


---

###### tonumber

TO_NUMBER - 문자열을 숫자로 변환
-


```SQL
SELECT TO_NUMBER('123456') TONUM1,
        TO_NUMBER('123,456','999,999') TONUM2
FROM DUAL;
```

======

123456	123456


---

###### todate

TO_DATE - 문자를 DATE형으로
-


```SQL
SELECT TO_DATE('1997-12-31 13:33:44','YYYY-MM-DD HH24:MI:SS')
FROM DUAL;
```
====

97/12/31


> 1970년 01월01일부터 현재일자까지 며칠인지 계산

```SQL
SELECT SYSDATE - TO_DATE('19700101','YYYYMMDD')
FROM DUAL;
```
===

17690.4016666666666666666666666666666667
-> DATE와 문자열간에는 바로 연산할수 없어서, TO_DATE 함수로 변환후 연산

> 1970년 01월01일 00시00분00초에서부터 946075441 초가 지난 날짜를 DATE 형으로 바꿔 보자.   
```SQL

SELECT TO_DATE('19700101000000','YYYYMMDDHH24MISS')+ 946075441 / (24*60*60)
FROM DUAL;
```


====

99/12/24



---

###### groupfunction

그룹함수 
-

단일행 함수와 달리 그룹함수는 행들의 집합에 대해 연산을 한 결과를 하나의 row로 반환 한다.

항상 하나의 로우가 결과로 나오는것은 아니다.

행들의 집합들에 대한 연산이 행들의 집합 수만큼 결과row가 나온다.

그룹함수의 종류

- COUNT : 그룹의 행들의 수를 보여준다.
- AVG :  그룹의 행들의 지정컬럼의 평균
- SUM :  ''   합계
- MAX : '' 최대값
- MIN : '' 최소값
- STDDEV : '' 표준편차를 계산
- VARIANCE : '' 분산을 계산

COUNT,MAX,MIN은 컬럼 형에 관계없이 사용,
나머지는 숫자형에만 사용


---

###### count

COUNT - 로우 수 반환
-

COUNT는 어떤형의 컬럼도 가능하다.

함수안에 * 를 넣어주면 로우수를 세어준다.
NULL이 없는 컬럼을 넣어주는것도 같은 결과
문자나 숫자를 넣어도 결과는 같다.
```SQL
SELECT COUNT(*), COUNT(8)
FROM TEMP;
```
===

10 10


COUNT안에 컬럼 명을 넣으면
NULL이 들어간 값은 카운트 하지않는다.
그래서 같은 테이블내에서도 카운트된 결과가 다를 수 있다.

```SQL
SELECT COUNT(EMP_ID) CNT1, COUNT(HOBBY) CNT2
FROM TEMP;
```

====

10	4

>(응용) GROUP BY 를 포함시켜 직급별로 인원수를 세어본다.

```SQL
SELECT LEV, COUNT(*), COUNT(HOBBY)
FROM TEMP
GROUP BY LEV;
```
======

과장	4	2
대리	2	0
부장	1	1


> 그룹함수가 가지는 한가지 특징 - COUNT없을시 반환X

그룹함수가 아닐떄 -> COUNT 칼럼이 없을떄 없다는 0을 반환하며 1 row selected가 되지만

그룹함수 지정시에 -> COUNT할 칼럼값이 없을때 0 row selected가 된다.(아예 반환 자체를 안함(0도 반환X))


>  TEMP 에서 LEV가 ‘과장’ 이면서 SALARY 가 40000000 이상인 사람의 수를 세어보자.

```SQL
SELECT COUNT(*)
FROM TEMP
WHERE LEV='과장'
AND SALARY >= 40000000;
```

===
3

> TEMP에서 LEV 의 종류를 세어보자.

```SQL
SELECT COUNT(DISTINCT LEV)
FROM TEMP;
```
( DINTINCT 안하면 중복때문에 10 출력)
====

5




---

###### groupfunctionadv

그룹함수 사용시 장점(알아두기)
-

PROCEDURE,FUNCTION 등 PL/SQL 블록을 이용한 프로그램 만들 때
0 rows selected 라는 결과가 나올 수 있는 query는 항상 EXCEPTION 처리를 해야한다.

EXCEPTION 처리하고 계속 진행 시키고자 한다면 다시 PL/SQL 블록으로 감싸줘야 한다.

하지만 GROUP 함수가 사용되는 경우라면 걱정없다.

일부러 GROUP 함수를 사용하는 경우가 발생한다.

---

###### minmax

MIN과 MAX
-

해당 DATA 집합의 주어진 칼럼중 최소/최대값을 돌려준다.
숫자,문자,날짜 어떤 형의 자료도 쓸수 있다.

> TEMP의 사번중 번호가 가장 빠른 사람
```SQL
SELECT MIN(EMP_ID)
FROM TEMP;
```                                                     

=====

19930331

> 직급별 가장 빠른 사번

```SQL
SELECT LEV,MIN(EMP_ID)
FROM TEMP
GROUP BY LEV;
```

====

과장	19960101
대리	19950303
부장	19970101
...


> TEMP에서 직급별로 최소연봉을 가진 직원의 사번과 연봉을 읽어 오자. (단,SUBSQUERY를 사용하면 안 된다.

```SQL
SELECT LEV, SUBSTR(MIN(LPAD(TO_CHAR(SALARY),10,'0')|| EMP_ID),11) EMP_ID, MIN(SALARY) SAL
FROM TEMP
GROUP BY LEV;
--SALARY를 LPAD하기 위해 TO_CHAR한것 - LPAD로 SAL을 10자리 기준으로 좌측공백 채우고 SUBSTR로 EMP_ID만 나오도록 11자리 부터 문자열 잘랐음
```



---

###### sumavg

SUM 과 AVG
-

DATA 집합의 주어진 컬럼의 합계/평균 반환
숫자형의 자료만 사용가능.

```SQL
SELECT LEV, SUM(SALARY) SUM_SAL, AVG(SALARY) AVG1, SUM(SALARY)/COUNT(*) AVG2
FROM TEMP
GROUP BY LEV;
```
===

과장	206000000	51500000	51500000
대리	101000000	50500000	50500000
부장	100000000	100000000	100000000
차장	134000000	67000000	67000000
사원	35000000	35000000	35000000


---


###### nullinavg

평균에서의 NULL(알아두기)
-

평균을 구할떄도 NULL 포함시키면 안됨.
평균 구할떄 0과 NULL 이 있는 경우는 다르다.

평균은 SUM/COUNT 인데 COUNT에서 제외되니 NULL과 0 은 나누는 수가 달라진다.

EX)
1.
UPDATE TEMP
SET SALARY = 0
WHERE EMP_NAME='홍길동'

2,
UPDATE TEMP
SET SALARY = NULL
WHERE EMP_NAME = '홍길동'

조회.

SELECT AVG(SALARY)
FROM TEMP;
(1. 50400000 / 2. 5600000)

위의 결과는 특정 칼럼의 SALARY가 0인경우와 NULL 인 경우에 따라 달라진다.




