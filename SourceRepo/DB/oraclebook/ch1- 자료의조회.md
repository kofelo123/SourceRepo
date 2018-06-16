책 - 오라클 실습
- [1장 조회]
- [null의 사용](#usenull)
- [CONCATENATION ( CONCAT) - 합성연산자](#concat)
- [USER_OBJECTS](#userobjects)
- [모두 만족하는 조건절 사용](#wheresatisfied)
- [ORDER BY](#orderby)
- [연산자](#operator)
- [GROUP BY ](#groupby)
- [HAVING](#having)

- [알아두기]
    - [옵티마이저와 실행계획(알아두기)](#optimizerExcutionPaln)
    - [INDEX 조회-DATA DICTIONARY(알아두기)](#dataDictionary)
    - [DB BLOCK 의 구조(알아두기)](#dbblock)
    - [옵티마이저에 의해 변환되는 연산자 (알아두기)](#optimizeroperator)

---


###### usenull

null의 사용
-



DML 로 자료 다룰떄 항상 NULL을 염두해야함.

NUMBER형 자료를 NULL과 연산시 항상 NULL이 된다.

NULL이 우려 있을시 숫자는 NVL 함수로 치환, 문자는 ' '(스페이스)나 특정 문자값으로 치환하는 습관필요

<같은지,다른지 비교>
WHERE A = '1'
WHERE A <> '1'  

(NULL은 이런식으로 하면 x(다른결과 가능성))

<NULL 인지,아닌지 비교>
WEHRE a IS NULL
WEHRE a IS NOT NULL





---


###### concat

CONCATENATION ( CONCAT) - 합성연산자
-

두개 이상 문자열을 하나로 연결

CONCAT 함수를 사용하거나 합성연산자(||) 이용

```sql
SELECT emp_name ||'('|| lev || ')' 성명
FROM TEMP;
```

결과)

성명            

-----------------

김길동(부장 )   

홍길동(과장 )   

박문수(과장 )   

정도령(차장 )   

이순신(대리 )   

지문덕(과장 )   



괄호가 아닌 '로 묶어주고 싶을때

"주의할 것은 작은 따옴표를 문자열로 표현하고자 할 때는 반드시 두개를 함께 사용해야 한다.

따라서 하나의 작은 따옴표를 문자열로 인식 시키기 위해서는 문자열임을 의미하는 작은 따옴표 가 문자열 앞뒤로 하나씩 붙어서 두개가 필요하고,작은따옴표를 문자열로 표현하기 위해서 또다시 두개가 필요하므로 총 4개의 작은 따옴표가 필요하다.
"

SELECT  EMP_NAME || ''''||LEV||'''' 성명

FROM    TEMP;


결과)


 성명           

 ----------------

 김길동'부장'   

 홍길동'과장'   

 박문수'과장'   

 정도령'차장'   


참고 -  문자와 숫자의 자동 변환

흔히 숫자형을 문자형으로 바꿀때 TO_CHAR라는 함수를 사용하지만 

숫자형을 문자형과 합성연산(||) 하면 문자열이 된다. 

---
또 한가지 예

사번을 앞에서 4자리만 잘라서 1을 더한 값이 1998 인 경우의 ROW만 검색하려고 하면 QUERY는 다음과 같이 될 것이다.

 
```
          SELECT EMP_ID,EMP_NAME

          FROM   TEMP 

          WHERE    SUBSTR(EMP_ID,1,4) + 1 = 1998 ;
```

결과)

          EMP_ID      EMP_NAME 

          ---------- ----------

           19970101    김길동   

           19970201    박문수   

           19970112    연흥부   

"
       도대체 어떻게 결과가 나왔을까?

        조건에서는 숫자형인 EMP_ID를 문자열 함수에 넣어서 사용했다.

        이단계에서  NUMBER형인 사번이 문자열로 변형이 일어난다.

        문자열로 변형된 결과를 이용해서 4자리를 잘라내고 다시 1과 더하려고 하다 보니 문자열과 숫자를 더하는 꼴이 된다.

       여기서 SUBSTR에 의해 잘라진 4자리가 다시 숫자로 변형이 일어나게 된다.

       이렇게 해서 얻어진 결과가 1998 이라는 숫자와 비교가 된 것이다.

       튜닝을 공부하다 보면 중요하게 다루어지는 부분이지만 이런 현상이 있다는 정도만 알고 넘어 가겠다."



---


###### userobjects

USER_OBJECTS
-

USER_OBJECTS : 현재 LOG IN한 USER가 소유하고있는 모든 OBJECT 나타냄

OBJECT의 명칭과 OBJECT가 TABLE, PROCEDURE, FUNCTION, VIEW, TRIGGER 등의 여러 OBJECT 종류 중 어떤 것인지에 대한 정보가 나옴.

```sql
SELECT OBJECT_NAME, OBJECT_TYPE 
FROM USER_OBJECTS;
```

OBJECT를 DROP 시키기 위해

DROP TABLE table_name;
DROP PROCEDURE procedure_name

과 같은식으로 일일이 DROP 할수 있겠지만

조회와 동시에 모두 DROP 하는 방법이 있다.

```sql

SELECT 'DROP' || OBJECT_TYPE||''||OBJECT_NAME||';'
FROM USER_OBJECTS;
```

위의 결과로 출력된 결과를 그대로 COPY 해서 실행하면 한번에 깨긋이 DROP 할 수있다.


결과)
-
DROP SEQUENCE PRODUCT_SEQ;
DROP SEQUENCE CART_SEQ;
DROP SEQUENCE ORDERS_SEQ;
DROP SEQUENCE ORDER_DETAIL_SEQ;
DROP SEQUENCE QNA_SEQ;
DROP TABLE SA_BOARD;
...
-


---


###### wheresatisfied

모두 만족하는 조건절 사용
-

조건절 WHERE이 모든 레코드를 만족할때는
내부적으로 해당 칼럼에 INDEX가 존재하면 실행계획에 포함된다.

WHERE이 없을때와 레코드 정렬이 다를수 있다.



---


###### optimizerExcutionPaln

옵티마이저와 실행계획(알아두기)
-

DML 수행시 옵티마이저가 관여,

최적화 경로를 찾아내기 위해 내부적으로 많은 요인 고려.

어떤 테이블을 먼저읽으며 그때 인덱스 이용여부, 어떤인덱스 , 어떻게 조인 등

실행계획에 의해 DML 수행, 옵티마이저가 선택한 최적화 경로 => PLAN으로 들여다 볼수있음.

PLAN: DML- 어떤 경로로 DB를 Access? 순서도

오라클 제공 UTLXPLAN.SQL 실행 - PLAN_TABLE 테이블 생성됨 -> 특정 SQL의 수행경로 볼때 SQL 앞에 다음 명령어 실행

EXPLAIN PLAN SET STATEMENT_ID = '임의지정' FOR

수행 -> 수행경로가 PLAN_TABLE에 결과남음

STATEMENT_ID 컬럼에 값이 들어감

아래의 sql에 대한 실행계획의 경로를 본다

```sql
SELECT EMP_ID,EMP_NAME

FROM TEMP

WHERE EMP_ID > 0;
```

1. SQL의 PLAN작성

```sql
EXPLAIN PLAN SET STATEMENT_ID = '임의지정' FOR
SELECT EMP_ID,EMP_NAME
FROM temp
WHERE emp_id > 0;

```
2. 실행계획 검색

```sql

 SELECT lpad(operation, length(operation)+2*(level-1))||

                         decode(id,0,'Cost Estimate:'||decode(position,'0',

                         'N/A',position), null)||''||options||

                         decode(object_name,null,null,':')||rpad(object_owner,

                         length(object_name)+1,',')||object_name||

                         decode(object_type,'UNIQUE',' (U) ', 'NIN_UNIQUE',

                        '(NU)',null)||decode(object_instance,null,null,'('||object_instance||')') PLAN

                 FROM PLAN_TABLE

                 START with ID=0 AND STATEMENT_ID='임의지정'

CONNECT BY PRIOR ID=PARENT_ID AND STATEMENT_ID='임의지정'

```



---


###### dataDictionary

INDEX 조회-DATA DICTIONARY(알아두기)
-

실행계획 - INDEX이름 나옴 -> 어떤 테이블에 대해 생성되되어 있고, 
어떤 칼럼으로 구성되어있는지에 대한 정보 - DICTIONARY VIEW 에서 찾을 수있음. 
->(ORACLE의 SYS USER가 소유 - DB관련 정보제공 READ-ONLY VIEW)

자신의 USER가 소유하고 있는 INDEX 조회해봄.

USER_INDEXES 와 USER_IND_COLUMNS

USER_INDEXES 는 테이블이 소유하고 있는 INDEX 가 어떤 것이 있는지 알아볼 때 편함.

```
SELECT INDEX_NAME
FROM USER_INDEXES
WHERE TABLE_NAME = 'TEMP';
```

결과)


                  INDEX_NAME                   

                  -------------

 SYS_C002696


USER_IND_COLUMNS는 인덱스가 어떤 컬럼들로 구성 되어 있는가를 확인 할 때 주로 사용

```sql
SELECT index_name, column_name
FROM user_ind_columns
WHERE table_name = 'TEMP';
```

결과)

INDEX_NAME    COLUMN_NAME

------------ ------------

SYS_C002696    EMP_ID     





---


###### orderby

ORDER BY
-

자료양이 늘어나고, 삭제,입력 등이 진행되면서 자료는 무작위로 저장된다.

순서정렬위해 ORDER BY를 쓰며, 그외에도 query시 내부적으로 sort가 일어나는경우, index 사용되는 경우 등이 있다.

기본은 asc(오름차순)

컬럼명 대신 컬럼 순서를 기술해도 사용가능

```sql
SELECT lev,emp_id,emp_name
FROM temp
ORDER BY 1,2 DESC;
```




---


###### dbblock

DB BLOCK 의 구조(알아두기)
-

DB BLOCK: 오라클 물리적 저장단위로서 입출력시의 최소단위

HEADER : 블록의 주소와 같은 BLOCK에 대한 일반정보를 포함

TABLE DIRECTORY : 블록안 ROW를 소유하는 테이블에 대한 정보를 포함

FREE SPACE : 널 칼럼의 값이 NOT NULL로 UPDATE 될 때 추가적인 공간을 요구하게 된다.
이때처럼 ROW의 변경이나 새로운 ROW 삽입 시 필요로 하게되는 추가공간을 위해 확보된 영역


  PCTUSED 와 PCTFREE

              PCTFREE

                 이미 블록에 쓰여진 ROW의 UPDATE나 INSERT를 위하여 예약되는 공간

                예를 들어

                PCTFREE  20

                과 같이 값을 잡아주면 블록의 공간 중 20%를 이미 블록에 쓰여진 ROW의 UPDATE나 INSERT를 위하여  사용되지 않고 남게 하겠다는 뜻

              PCTUSED

                PCTFREE에서 지정한 영역만큼만 FREE SPACE가 남게 되면                오라클은 더 이상 해당 블록에 새로운 ROW를 삽입하지 않다.

                사용공간이 ROW의 삭제 등으로 인하여 PCTUSED에서                지정한 값 아래로 떨어지게 되면 그때 다시 해당 BLOCK에 새로운 ROW가 삽입될 수 있다.




---


###### operator

연산자 
-

<> , != : 다른지를 묻는다.

NOT : IN, BETWEEN, LIKE,  ANY, ALL, EXISTS 등과 함께 쓰여 연산의 결과를 부정할 때 사용

   WHERE  EMP_ID NOT IN (19970824,19970825)

    :  EMP_ID 컬럼의 값이 19970824,19970825 중 어디에도 해당되지않는 ROW를 검색.


LIKE : 값의 일부를 주어주고 검색할 때 사용


부서코드가 A로 시작되는 ROW를 검색한다.

```sql

SELECT EMP_ID,EMP_NAME,DEPT_CODE
FROM TEMP
WHERE DEPT_CODE LIKE 'A%';

```
결과)
EMP_ID    EMP_NAME   DEPT_C

-------------  ------------------  ------------

19970101   김길동        AA0001

19960101   홍길동        AB0001

19970201   박문수        AC0001


위에서 부서코드 중에 A가 들어가는 ROW
'%A%'

위에서 총 6자리 부서코드 중 2번째 자리에 A 가 들어가는 ROW 

'_A____'

< BETWEEN >

상한 값과 하한 값을 주어 자료의 검색을 하고자 할 때 사용

사번이 1997로 시작하는 사원의 사번과 성명을 검색할 때 BETWEEN 연산자를 쓴다면

```sql
SELECT EMP_ID,EMP_NAME
FROM TEMP
WHERE EMP_ID BETWEEN 19970001 AND 19979999;
```

 BETWEEN 연산자를 이용하여 성명(EMP_NAME)이 ‘ㄱ’ 으로 시작되는           사람의 EMP_ID 와 EMP_NAME 을 조회

```SQL

SELECT EMP_ID , EMP_NAME
FROM TEMP
WHERE EMP_NAME BETWEEN '가' AND '나';

```

<IN>

OR 조건으로 연결된 조건을 한번에 기술 해 줄 수있는 기능을 제공


```SQL
SELECT  EMP_ID,EMP_NAME

FROM    TEMP

WHERE   EMP_NAME IN ('홍길동','김길동');

 

            EMP_ID     EMP_NAME 

           ---------- ----------

            19970101    김길동   

            19960101    홍길동   

```

 위에서처럼 IN 연산자로 기술된 문장은 OPTIMZER에 의해 실행계획 작성 전에          다음과 같이 변경 된다.
```SQL
  SELECT EMP_ID,EMP_NAME

           FROM    TEMP

           WHERE   EMP_NAME = ‘김길동’

           OR       EMP_NAME = ‘홍길동’;

```



---


###### optimizeroperator

옵티마이저에 의해 변환되는 연산자 (알아두기)
-

 ![](https://drive.google.com/uc?export=view&id=1EJyHYn8OyhCvanQeppby0IG0POwPsfUb)

옵티마이저가 보다 빨리 평가 할 수있는 동등한 식으로 조건식을 내부에서 변환한다.



---


###### groupby

GROUP BY 
-

GROUP BY는 특정 칼럼이나 값을 기준으로 ROW를 묶어서 자료를 다룰떄 사용
그룹 함수(MAX,MIN,AVG,SUM,COUNT) 적용할때 이에 해당함.

1)       TEMP 의 자료를 이용해서 LEV 별로 최고 액 연봉이 얼마인지 알아 보겠다.

```SQL
SELECT LEV, MAX(SALARY) MAX_SAL
FROM TEMP
GROUP BY LEV;
```

결과)


LEV   MAX_SAL  

-------  --------------

과장   72000000

대리   56000000

부장  100000000

사원   35000000

차장   70000000


LEV값이 가나다 순 정렬 -> GROUP BY도 쿼리시 내부적으로 SORT가 일어나는 경우 중 하나다.

2)       이번에는 전체 ROW에 대해 최고 연봉이 얼마인지 알고자 하는 경우

```SQL
SELECT MAX(SALARY)
FROM TEMP;

```

결과)

             MAX(SALARY

             ----------

             100000000

이는 GROUP BY 문장 기술하지 않아도 GROUP 함수 적용됨.
GROUP X 시 전체 ROW를 하나의 단위로 보아 GROUP BY 기술하지 않음

DISTINCT의 경우도 GROUP BY 기술 X

아래 두 문장은 결과가 동일함

``SQL
SELECT DISTINCT LEV
FROM TEMP;
```

```SQL
SELECT LEV
FROM TEMP
GROUP BY LEV;
```

3. TDEPT의 자료로 부서장중 사번이 가장 빠른 사람이 근무하는 부서 알아내기

원래는 서브쿼리나 ,인라인뷰 -> 
1) 부서테이블의 BOSS_ID에 MIN 함수- 가장작은 사번
2) 그 결과를 이용해 부서장으로 있는 부서코드를 읽어냄

아직 CH1에서 언급X 이므로 SUBSTR 함수로 결론도출시도

SUBSTR(EMP_NAME,2,2)는 성명을 2번쨰 문자부터 2자리 끊겠다는것.

```SQL
             SELECT MIN(BOSS_ID || DEPT_CODE)

 FROM  TDEPT;

```

결과)

 MIN(BOSS_ID||DEPT_CODE)

---------------------------------------

19930301BA0001        

1 row selected.

사번,부서코드를 합성연산자로 결합- 결과에 MIN을 줌

사번이 가장 작은 ROW의 사번과 부서코드가 함께 읽힘
여기서 필요한 값은 부서코드이므로 SUBSTR로 9자리부터 6자리 잘라 쓰면됨.

```SQL
SELECT SUBSTR(MIN(BOSS_ID || DEPT_CODE),9,6)
FROM TDEPT;
```


(알아두기)

GROUP BY 뒤에 ORDER BY를 사용해서 자료 정렬 가능.

```SQL
 SELECT LEV,

            MAX(SALARY)  MAX_SAL

    FROM  TEMP

             GROUP BY LEV;
```
위 결과는 자동으로 LEV순으로 정렬 되지만, LEV순서가 아닌 MAX(SALARY) 순으로 정렬할때 ORDER BY절을 추가한다.

GROUP BY 에 기술되는 컬럼 순으로 SORT가 일어나기 때문에 대개는 SORT하고 싶은 순서대로 GROUP BY에 기술한다고 한다.



---


###### having

HAVING
-

HAVING은 GROUP BY 된 결과를 제한하고자 할때 사용한다.

직급별로 연봉 평균을 구한 상태에서 평균 연봉이 5천만원 이상인 경우의 직급과 평균 연봉을 읽어 오는 경우를 본다.

```SQL
SELECT AVG(SALARY)
FROM TEMP
GROUP BY LEV
HAVING AVG(SALARY)>50000000;
```

결과)

        LEV   AVG(SALARY

        -------  -------------------

        과장   51500000

        대리   50500000

        부장  100000000

        차장   67000000



---
