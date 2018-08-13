<<개발자를 위한 Oracle SQL - 허진경>>

- [like 연산자](#8726_4)
- [문자조작함수](#180813_1)
- [정규표현식 함수](#180813_3)
- [숫자함수, 날짜함수](#180813_4)
- [변환 함수](#180813_5)
- [Null 치환 함수 NVL, NVL2, COALESCE](#180813_6)
- [기타 변환 함수](#180813_7)

---

###### 8726_4

like 연산자
-


검색하고자 하는 값을 정확하게 알수 없을때

like 연산자를 사용하면 문자 패턴과 일치하는 행을 선택할 수 있다.

문자 패턴 일치 연산을 와일드카드 검색이라고 부른다.

```
% : 문자가 없거나 하나 이상을 나타냄

_ : 단일 문자를 나타냄
```

```sql
SELECT FIRST_NAME, LAST_NAME, JOB_ID, DEPARTMENT_ID
FROM EMPLOYEES
WHERE JOB_ID LIKE 'IT%';

```

%와 _ 를 섞어서 사용해도 된다.

만일 % 와 _ 문자를 포함하는 열을 검색할 필요가 있을때는 ESCAPE 옵션을 사용

"SA_M"을 포함하는 사원의 이름과 잡 아이디를 출력하려면 다음과 같다

```SQL
SELECT FIRST_NAME, JOB_ID
FROM EMPLOYEES
WHERE JOB_ID LIKE 'SA\_M%' ESCAPE '\';
```

ESCAPE 옵션은 회피문자로서 백슬래시(\)를 사용한다. 회피문자는 밑줄(_) 앞에 둔다.




-----------------------------------------

###### 180813_1

문자조작함수
-



문자조작함수

 ![](https://drive.google.com/uc?export=view&id=1swXpfz7V_bSXcE1nfpMLcVSWReUQpHX2)

LOWER: 모두 소문자

INITCAP:첫 문자만 대문자

UPPER : 모두 대문자

LOWER,UPPER를 통해 데이터의 대/소문자 구분이 

명확하지 않을경우 WEHRE조건에서 유용하게 사용가능


LENGTH:문자열 길이 반환

INSTR: 주어진 문자 위치 반환

SUBSTR:시작위치부터 지정개수만큼 문자열 반환

CONCAT: 두 문자열 연결

LPAD : 왼쪽 남은 부분 주어진 문자로 채우기 정의된 

문장의 왼쪽 나머지 공간을 지정한 문자로 채움

RPAD: 반대

LTRIM: 정의된 문자열의 왼쪽부터 지정된 문자가 발견되면 제거.

RTRIM은 반대

REPLACE: 지정한 문자열을 새로운 문자열로 대체

이를 통해 공백 제거도 가능
```
SQL> SELECT REPLACE('JavaSpecialist', 'Java', 'BigData') FROM dual;
SQL> SELECT REPLACE('Java Specialist', ' ', '') FROM dual;
```

TRANSLATE: 정의된 문자열을 1대1로 대응 , 대응하는 문자가 없으면 해당 문자를 삭제
대응하는 문자가 남을 경우 남는 문자는 무시




-----------------------------------------

###### 180813_3

정규표현식 함수
-

정규표현식 함수

정규표현식을 이용하여 문자열을 다루는 함수
 ![](https://drive.google.com/uc?export=view&id=1ETmOqn0z_NogitILBaJB3keVBktXzrSD)

 ![](https://drive.google.com/uc?export=view&id=1jjgHLN8VZ9nlLvLXK_JkEcJsJgDWYPuh)

 ![](https://drive.google.com/uc?export=view&id=1AKgGCDRvYSOeYVj4Tsbw2D2vurR-t378)

REGEXP_LIKE 함수

정규표현 조건을 사용해 애매한 조건 검색을 실시함. 문자 데이터 타입을 사용

값 세팅
```SQL
 CREATE TABLE test_regexp (col1 VARCHAR2(10));
 INSERT INTO test_regexp VALUES('ABCDE01234');
 INSERT INTO test_regexp VALUES('01234ABCDE');
 INSERT INTO test_regexp VALUES('abcde01234');
INSERT INTO test_regexp VALUES('01234abcde');
```

다음 구문은 REGEXP_LIKE함수를 이용해 숫자로 시작하고 영문자 소문자로 끝나는 행을 찾는다.

```SQL
SELECT *
FROM test_regexp
WHERE REGEXP_LIKE(col1, '[0-9][a-z]');
===
   COL1
1 01234abcde
```

REGEXP 함수는 제약조건에 사용할 수 있다.

다음은 Q/A관리 마스터에 QA 번호를 등록할 때의 서식 체크의 예를 들어 본다.

```SQL
SQL> CREATE TABLE QA_MASTER (QA_NO VARCHAR2(10));
SQL> ALTER TABLE QA_MASTER ADD CONSTRAINT QA_NO_CHK CHECK
 2 (REGEXP_LIKE(QA_NO,
 3 '^([[:alpha:]]{2}-[[:digit:]]{2}-[[:digit:]]{4})$'));
SQL> INSERT INTO QA_MASTER VALUES('QA-01-0001');
1 개의 행이 만들어졌습니다.
SQL> INSERT INTO QA_MASTER VALUES('00-01-0001');
INSERT INTO QA_MASTER VALUES('00-01-0001')
*
1행에 오류:
ORA-02290: 체크 제약조건(SCOTT.QA_NO_CHK)이 위배되었습니다.
```

- REGEXP_INSTR 함수

: 지정한 조건(정규 표현)을 만족하는 부분의 최초위치(무슨 문자인지)를 돌려준다.
또한 검색을 시작하는 시작 위치를 지정 하는것도 가능.

```
SQL> INSERT INTO test_regexp VALUES('@!=)(9&%$#');
SQL> INSERT INTO test_regexp VALUES('자바3');
```

다음구문은 REGEXP_INSTR을 이용하여 특정 문자열의 위치를 출력

```SQL
SQL> SELECT col1,
 2 REGEXP_INSTR(col1, '[0-9]') AS data1,
 3 REGEXP_INSTR(col1, '%') AS data2
 4 FROM test_regexp;
```

 ![](https://drive.google.com/uc?export=view&id=1SJ9hmaKDqrmYkZ6sHn7GfCBPR3ZP8pQv)


REGEXP_SUBSTR 함수

지정한 정규 표현을 만족하는 부분 문자열을 반환

다음 구문은 C에서 Z까지의 알파벳을 뽑아낸다.

```
SQL> SELECT col1, REGEXP_SUBSTR(col1,'[C-Z]+')
 2 FROM test_regexp;
```
 ![](https://drive.google.com/uc?export=view&id=13638FHEe2dvXm8Qu-l1usVl8QI23314q)


REGEXP_REPLACE 함수

지정한 정규 표현을 만족하는 부분을, 지정한 다른 문자열로 치환.. 복잡한 치환/검색 조작을 가능하게 함.

다음 구문을 0부터 2까지의 문자를 *로 변환시킴

```SQL
SQL> SELECT col1, REGEXP_REPLACE(col1,'[0-2]+','*')
 2 FROM test_regexp;
```

 ![](https://drive.google.com/uc?export=view&id=1eGyHoV9F5zznQ3k3AmqwLdxJ3TmTle1Z)


실전문제

EMPLOYEES 테이블의 PHONE_NUMBER 열에서 XXX.XXX.XXXX형식 전화번호를 출력

```SQL
SQL> SELECT first_name, phone_number
 2 FROM employees
 3 WHERE regexp_like (phone_number,
 4 '^[0-9]{3}.[0-9]{3}.[0-9]{4}$');
```

 ![](https://drive.google.com/uc?export=view&id=1t7R5EcOCMHTUFHO9cMJgp3lmujbiD8iX)


위의 구문은 아래 구문처럼 사전 정의된 문자 클래스로도 표현이 가능하다.

```SQL
SQL> SELECT first_name, phone_number
 2 FROM employees
 3 WHERE regexp_like (phone_number,
'^[[:digit:]]{3}.[[:digit:]]{3}.[[:digit:]]{4}$');
```

문제 1. XXX.XXX.XXXX형식 전화번호를 갖는 사원의 이름과 전화번호를 출력하되  전화번호맨 뒤 4자리를 *로 마스킹해서 출력하라.
그리고 맨 뒤 4자리만 별도로 출력하라.


 ![](https://drive.google.com/uc?export=view&id=1qZNp5orMThG5bX6ytaUMvja6aIySIeib)

```SQL
SQL> SELECT first_name,
 2 regexp_replace(phone_number,
'[[:digit:]]{4}$', '****') AS phone,
 3 regexp_substr(phone_number, '[[:digit:]]{4}$') AS phone2
 4 FROM employees
 5 WHERE regexp_like (phone_number, '^[0-9]{3}.[0-9]{3}.[0-9]{4}$');
```



-----------------------------------------

###### 180813_4

숫자함수, 날짜함수
-

숫자 함수 : 숫자를 입력받고 숫자 값을 반환한다.

 ![](https://drive.google.com/uc?export=view&id=1DK0gJ7uhguItOtRh9iv3u_o1NJahvHLY)

날짜 함수

디폴트 날짜 형식은 'DD-MON-YY' , 시스템에 따라 'YY/MM/DD'가 될 수 있다.

SYSDATE 는 현재의 날짜를 반환하는 함수
SYSTIMESTAMP는 현재의 날짜와 시간을 반환하는 함수
DUAL 은 SYSDATE를 보기 위해 사용된 dummy 테이블


SYSDATE: 현재 날짜 반환함수, DUAL이라는 더미 테이블로 부터 SYSDATE를 선택하는 것이 관례

다음 구문은 현재 날짜를 출력한다.

```SQL
SELECT SYSDATE
FROM DUAL;
```

 ![](https://drive.google.com/uc?export=view&id=12K40d22zUxBELWzSkaA19jIUah0TPyil)

SYSDATE는 현재 날짜를 출력한다. 현재 날짜와 함께 시간에 대한 정보까지 출력하고 싶다면 아래 구문처럼 SYSTIMESTAMP를 사용한다.

```SQL
SELECT SYSTIMESTAMP
FROM DUAL;
```

 ![](https://drive.google.com/uc?export=view&id=1jiNohWGKKvqXhb0kafcYv66nX77lW_ax)

- 날짜 연산

날짜에서 숫자를 더하거나 빼서 날짜 결과 반환

날짜 사이의 일(DAY) 수를 알 기 위해서 두 개의 날짜를 뺀다.

시간을 24로 나누어 날짜에 더한다.

 ![](https://drive.google.com/uc?export=view&id=14Wt0vzV0BULCr3FtLlp05IhmmEOr_qKu)

부서 60에 속하는 모든 사원에 대해 이름과 근무한 주의 합계를 출력
현재 날짜(SYSDATE)에서 사원이 입사한 날짜를 빼고 근무한 주의 합계를 구하기 위해서 7로 나눈다.

```SQL
SELECT first_name, (SYSDATE - hire_date)/7 AS 'Weeks"
FROM employees
WHERE department_id=60;
```

 ![](https://drive.google.com/uc?export=view&id=1OyMSpNWEOZrxMdX6R5RwaLElrvdEtkyZ)


- 날짜 함수

오라클 날짜에 대해 연산을 한다. 모든 날짜 함수는 MONTHS_BETWEEN(숫자 값을 반환)을 제외하고는 DATE 데이터타입의 값을 반환함.

 ![](https://drive.google.com/uc?export=view&id=1DBb5hL4b0kCCUUAbd54aEejY4f1vTPkT)





-----------------------------------------

###### 180813_5

변환 함수
-

- 변환 함수

데이터 형변환에는 암시적데이터형 변환, 명시적 데이터형 변환이 있으며

SQL문장의 안정성을 위해 명시적 형변환이 권장된다.


<암시적 형 변환>

 ![](https://drive.google.com/uc?export=view&id=1snb0OUrsEJIoLvjQo9a8PkGf3cP0bkmA)

(CHAR -> NUMBER 변환은 오직 문자 스트링이 적절한 숫자를 나타낼 때만 가능
CHAR -> DATE로의 변환은 문자 스트링이 디폴트 형식('DD-MON-YY')을 가질 경우에만 성공)

```SQL
SQL> SELECT employee_id, first_name, hire_date
 2 FROM employees
 3 WHERE hire_date='03/06/17';
```

 ![](https://drive.google.com/uc?export=view&id=1O6Koz9fwex9ZleR9qbR8G48ke15yDUSO)

WHERE절 구문에서 DEPARTMENT_ID열의 형은 NUMBER 형이지만 같은 문자열이다.
문자는 NUMBER 타입으로 자동 형변환 되므로 아래 구문들은 정상 실행된다.

```SQL
SQL> SELECT employee_id, first_name, department_id
 2 FROM employees
 3 WHERE department_id='100';
```

 ![](https://drive.google.com/uc?export=view&id=1fy8s2qTVlIvEgJjnyRWj3jMswHUyBpG9)


- 명시적 형 변환

 ![](https://drive.google.com/uc?export=view&id=1OgAVlEIXibAyJOEvYzIH-NgWH0S8jF-n)


 ![](https://drive.google.com/uc?export=view&id=1Uyz1wTTsvqoOsoyBtKnsU5oiaTTpqpmN)

<날짜를 문자로 변환>

날짜 형식 모델 요소

 ![](https://drive.google.com/uc?export=view&id=1tGKFnR8hicX4vSMHgAEkg6wT9IXwGDGj)


 ![](https://drive.google.com/uc?export=view&id=1SJhAgUfqKjrVHyIVb7CYiVa1y0hg7Nee)


 ![](https://drive.google.com/uc?export=view&id=17E1-6g8LNjtrbLPtNOmgoBcaK27IzIG7)


<숫자를 문자로 변환>

숫자 형식 모델 요소

 ![](https://drive.google.com/uc?export=view&id=1d5EC52m68jqD_b2x4WAYHWIjYYr5BRj3)

TO_CHAR 함수는 NUMBER 데이터타입의 값을 VARCHAR2로 전환한다.


저장된 소수 값을 형식 모델에서 제공하는 소수점 자리수로반올림 한다.

```SQL
SQL> SELECT first_name, last_name,
 2 salary*0.123456 SALARY1,
 3 TO_CHAR(salary*0.123456, '$999,999.99') SALARY2
 4 FROM employees
 5 WHERE first_name='David';
```

 ![](https://drive.google.com/uc?export=view&id=1HiKbyYCSfC751-emBicErlqTiRWEdEkZ)


<TO_NUMBER 함수>

문자 스트링을 숫자형식으로변환

오직 숫자로 이루어진 문자 스트링은 숫자 형식으로 자동 형변환되나, $ 또는 , 등을 포함한 문자 스트링은 fmt를 지정해서 형 변환해야 함

예를 들어 다음과 같은 구문은 오류

```SQL
SQL> SELECT '$5,500.00' - 4000 FROM dual;
```
(invalid number)

이는 다음과 같이 TO_NUMBER 함수 사용하여 형식을 지정하여 형 변환해야 한다.

```SQL
SQL> SELECT to_number('$5,500.00', '$99,999.99') - 4000
 2 FROM dual;
```

 ![](https://drive.google.com/uc?export=view&id=15FnDfsDUzG5s0E7wnKZpP9x7RVlxuZ79)

위 구문은 $ 기호를 포함시키고, 세자리마다 콤마 를 인식하며 , 소수점 이하 두자리까지 인식시키는 포맷을 지정한것.

<TO_DATE>

문자스트링 -> 날짜 형식으로 변환

 ![](https://drive.google.com/uc?export=view&id=1Qj9RdmgBmnCnk-bz_I1My1661ErNpiin)


문제)


 ![](https://drive.google.com/uc?export=view&id=1kyANznmdb3QA6fOxCqoFVvJtv4Ir0Q1Q)






-----------------------------------------

###### 180813_6

Null 치환 함수 NVL, NVL2, COALESCE
-


<NVL>

NVL(expr1, expr2)

expr1 와 expr2는 같은데이터 타입이어야 한다.

expr1이 null이면 expr2 를 출력한다.

```sql
SQL> SELECT first_name,
 2 salary + salary*NVL(commission_pct,0) AS ann_sal
 3 FROM employees;
```

<NVL2 함수>

NVL2(expr1, expr2, expr3)

expr1의 값이 Null이 아닐경우 expr2 값을 반환,
Null일경우 expr3의 값을 반환

expr1의 타입과 expr2,expr3 의 타입은 같지 않아도 됨

```SQL
SQL> SELECT first_name,
 2 NVL2(commission_pct, salary+(salary*commission_pct), salary) ann_sal
 3 FROM employees;
```

<COALESCE 함수>

표현식에서 NULL 값이 아닌 최초의 표현식을 나타낸다. 모든 표현식이 NULL이면 NULL을 반환

오라클에서 각 expr들은 NULL과 함께 같은 타입 변수만 기술할  수 있다.

고객 데이터베이스에서 고객의 전화번호를 출력하고 싶을 경우 고객의 전화번호가 휴대폰,집전화,회사전화 등 여러개 필드에 저장되어 있을 경우 유용하게 사용 가능.

```SQL
SQL> SELECT first_name,
 2 COALESCE(salary + (salary*commission_pct), salary) AS ann_sal
 3 FROM employees;
```






-----------------------------------------

###### 180813_7

기타 변환 함수
-

- 기타 변환 함수

<LNNVL 함수>

LNNVL 함수는 expr1 결과가 FALSE 또는
UNKNOWN 이면 TRUE를 반환  
expr1 결과가 TRUE이면 FALSE를 반환

LNNVL(expr1)

상황) 보너스가 650 달러보다 적은 사원에게 상품권을 지급하려 한다.

해당 사원들의 이름과 보너스를 출력하세요

보너스는 SALARY* COMMISSION_PCT이다

이를 다음과 같이 구문을 작성하면 4개 행만 출력된다. 이유는 COMMISSION_PCT열이 NULL인 모든 행을 조건에서 제외시키기 떄문

```SQL
SQL> SELECT first_name, COALESCE(salary*commission_pct, 0) AS bonus
 2 FROM employees
 3 WHERE salary*commission_pct < 650;
```

 ![](https://drive.google.com/uc?export=view&id=1bh9hpvIBtCruh1PRAZtPnMrWFoV3nF6l)


다음 구문은 COALESCE 함수를 이용하여 WHERE 절을 작성하여 문제를 해결한것..

```SQL
SQL> SELECT first_name, COALESCE(salary*commission_pct, 0) AS bonus
 2 FROM employees
 3 WHERE COALESCE(salary*commission_pct, 0) < 650;
```
이는 LNNVL을 이용하여 다음과 같이 작성 할 수있다.

WHERE절 구문이 반댇여야 함에 주의

```SQL
SQL> SELECT first_name, COALESCE(salary*commission_pct, 0) AS bonus
 2 FROM employees
 3 WHERE LNNVL(salary*commission_pct >= 650);
```

<DECODE 함수>

IF-THEN-ELSE 구문과 유사한 방법으로 표현식을 해독함.

 ![](https://drive.google.com/uc?export=view&id=1y3VMoMx9DBAVTRf-hquRj0lHFiIn5Hyz)
			
		
ex)

JOB_ID가 IT_PORG이면 급여 증가율은 10%
FI_MGR이면 급여 증가율은 15% ... 다른 직무에 대해서는 급여증가분이 없다 이를 출력

```SQL
SQL> SELECT job_id, salary,
 2 DECODE(job_id, 'IT_PROG', salary*1.10,
 3 'FI_MGR', salary*1.15,
 4 'FI_ACCOUNT', salary*1.20,
 5 salary)
 6 AS REVISED_SALARY
 7 FROM employees;
```

<CASE 문>

자바 언어의 IF -ELSE와 같은 기능을 한다.

```SQL
SQL> SELECT job_id, salary,
 2 CASE WHEN job_id='IT_PROG' THEN salary*1.10
 3 WHEN job_id='FI_MGR' THEN salary*1.15
 4 WHEN job_id='FI_ACCOUNT' THEN salary*1.20
 5 ELSE salary
 6 END AS "RevisedSalary"
 7 FROM employees;
```

단일 행 함수는 어떤 레벨까지도 중첩가능하다 .


	   


