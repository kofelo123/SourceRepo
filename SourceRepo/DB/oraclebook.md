책 - 오라클 실습

- [null의 사용](#usenull)
- [CONCATENATION ( CONCAT) - 합성연산자](#concat)
- [USER_OBJECTS](#userobjects)


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

