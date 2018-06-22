- [ROWNUM](#80618164)

- [ROWID](#80619100)
- [DECODE](#80619102)
    - [DECODE의 사용(등호비교  + ELSE)](#80619114)
    - [DECODE의 사용(ELSEIF로 분기되는 등호비교)](#80619142)
    - [DECODE의 사용(부등호비교)](#80619151)
    - [DECODE의 사용(OR또는 IN비교)](#80619155)
    - [AND 비교](#80619160)
    - [중첩 IF](#80619164)

- [ROWNUM을 조건으로 사용할 때(알아두기)](#8061995)
- [test](#80622_95)
---


###### 80618164

ROWNUM
-

ROWNUM은 WHERE(없으면 FROM)절에 의해 추출된 DATA SET에 ROW단위로 붙는 순번.

자료순서를 다른 목적으로 이용할때 유용

WHERE 절에 ROWNUM을 조건으로 줄때는 항상 다른 조건을 모두 만족시킨 결과에 대해 조건반영
(= ROWNUM은 WHERE 만족시킨 자료에 붙는 순번)

그러므로, ORDER BY가 추가되면 ROWNUM까지 하나의 RECORD 내의 COLUMN으로 인식되어 정렬 일어남.

Q TEMP 의 자료를 SELECT 할때 ROWNUM 을 붙여 보자.                   
```SQL
SELECT ROWNUM,EMP_ID,EMP_NAME
FROM TEMP
WHERE EMP_ID > 0
AND LEV='수습';

====

1	20000101	이태백
2	20000102	김설악
3	20000203	최오대
4	20000334	박지리
5	20000305	정북악
```

...


Q 이번에는 위의 조건에 ROWNUM 과 관련된 조건을 붙여 보자.                 
```SQL
SELECT ROWNUM,EMP_ID,EMP_NAME
FROM TEMP
WHERE EMP_ID>0
AND LEV='수습'
AND ROWNUM <5;

====

1	20000101	이태백
2	20000102	김설악
3	20000119	장금강
4	20000203	최오대
```

ROWNUM 관련 조건에 =, > , >= 로 주어지면, 원하는 결과가 나오지 않는다.

이 같은 결과를 뽑을때는 IN LINE VIEW를 사용해서 뽑을 수 있다.

Q  ROWNUM 을 SELECT 할 때 ORDER BY 가 되면 결과가 어떻게 되는지를 알아 보자.                   
```SQL
SELECT ROWNUM,EMP_ID,EMP_NAME
FROM TEMP
WHERE EMP_ID > 0
AND LEV='수습'
AND ROWNUM<5
ORDER BY EMP_NAME;

====
2	20000102	김설악
4	20000334	박지리
1	20000101	이태백
3	20000203	최오대
```
이와같이 조건절 만족시킨 행들에 ROWNUM이 붙고 난후에 ORDER BY가 이루어짐.


Q TEMP 테이블의 자료를 이용하여 SELECT 결과를 3개행씩 묶어 하나의 번호를 부여하는 SQL 을 만들어보자.
ROWNUM, 부여된번호, EMP_ID, EMP_NAME 을 보여주면 된다.          

```SQL
SELECT ROWNUM,CEIL(ROWNUM/3),EMP_ID,EMP_NAME
FROM TEMP
WHERE EMP_ID>0;


===

1	1	19970101	김길동
2	1	19960101	홍길동
3	1	19970201	박문수
4	2	19930331	정도령
5	2	19950303	이순신
6	2	19966102	지문덕
7	3	19930402	강감찬
8	3	19960303	설까치
9	3	19970112	연흥부
...

```


---

###### 8061995

ROWNUM을 조건으로 사용할 때(알아두기)
-

ROWNUM을 조건에서 사용시 항상 < (작다) , <=(작거나같다) 를 이용한다.

예외적으로 1과 비교할때는 =(같다)로 비교가능.

ROWNUM <= 10 , ROWNUM <11 은 가능하나

ROWNUM이 10인 행을 보기를 원하거나 5보다 큰행을 보기 원한다면 IN LINE VIEW를 이용해 한번 더 자료를 가공해야한다.




---

###### 80619100

ROWID
-

ROWID는 DATABASE내의 모든 ROW가 가지는 유일한 식별자이다.

ORACLE 8의 경우 FILE번호,BLOCK번호,BLOCK내의 ROW번호, 객체 고유번호까지 포함하는 4가지정보로 ROWID구성됨.

SELECT를 통해 조회가능 !> UPDATE,INSERT는 안된다.

ROW ACCESS가 어떤 방식보다 빨라서 ROWID를 안다면 조건에 두고 검색방법이 최우선적으로 바람직하다.



---

###### 80619102

DECODE
-

일반적인 프로그래밍 언어 - IF문을 SQL문장 또는 PL/SQL 에서 사용하기 위한 오라클함수.

SELECT시의 DECODE는 FROM 절 빼고 사용가능
(SELECT시 DECODE 로  테이블을 동적으로 변경X)

<DECODE의 사용 -(단순 등호 비교)

IF A = B THEN
 RETURN 'T';
END IF;

A 와 B컬럼이 같은 레코드는 'T'값을 돌려달라는것

위의 전형적인 IF를 DECODE 사용시에
DECODE(A,B,'T')가 된다.

SELECT문에서 사용하면

```SQL
SELECT DECODE(A,B,'T')AS COL1
FROM TABLE
WHERE ...
```
위에서 A와 B 컬럼의 값이 다르면 NULL이 나온다.

이와 같이 단순 비교로 사용되는 DECODE는 내부적으로 ELSE 기능을 가지고 있으며 ELSE에 해당 될 경우 NULL을 리턴한다.

위문장의 완전한 형태는 다음과 같다.(사실상 원래는 아래문장과 같다는것)

```SQL
SELECT DECODE(A,B,'T',NULL)AS COL1
FROM TABLE이름
WEHRE ...
```

Q 주당 강의 시간과 학점이 같으면 '일반' 을 돌려 받으려한다.

```SQL
SELECT LEC_ID, DECODE(LEC_TIME,LEC_POINT,'일반')
FROM LECTURE;

===

L0001	일반
L0002	
L0003	
L0004	일반
L0005	
```

Q 주당강의시간과 학점이 같은 강의의 숫자를 알고 싶다.           
```SQL
SELECT COUNT(DECODE(LEC_TIME,LEC_POINT,1)) --1은 그냥 의미없는 값인가
FROM LECTURE;
```

COUNT와  DECODE 두개의 함수가 함께 사용. 이런경우는 실제시스템에 부지기수로 사용한다.

위와 같이 쓸 수도 있지만, 이런경우는 대게 WHERE을 이용해 처리하는것이 정석이다.

```SQL
SELECT COUNT(*)
FROM LECTURE
WHERE LEC_TIME = LEC_POINT;



===

2
```

Q 주당 강의 시간과 학점이 같으면 '일반'을 리턴받은후 정렬

```SQL
SELECT LEC_ID, DECODE(LEC_TIME,LEC_POINT,'일반')
FROM LECTURE
ORDER BY DECODE(LEC_TIME,LEC_POINT,'일반');
```




---

###### 80619114

DECODE의 사용(등호비교  + ELSE)
-

IF A = B THEN
RETURN 'T';
ELSE
RETURN 'F';
END IF;

단순 비교등호의 경우와 동일하나
NULL로 RETURN되는 부분에 어떤 특정한 값을 주고 싶은 경우에 사용한다.

위의 IF문을 DECODE로 옮기면

A,B 컬럼(또는 변수)이 같다면 'T'를 리턴, 그렇지않으면 'F'를 리턴받고 싶다는 문장.

DECODE(A,B,'T','F')


Q 강의 시간과 학점이 다르면 NULL 이 리턴되는 대신 '특별'
이라고 리턴
```SQL
SELECT LEC_ID, DECODE(LEC_TIME,LEC_POINT,'일반','특별')
FROM LECTURE;


===

L0001	일반
L0002	특별
L0003	특별
L0004	일반
L0005	특별

```

---

###### 80619142

DECODE의 사용(ELSEIF로 분기되는 등호비교)
-

IF A = B THEN
 RETURN 'T';
ELSEIF A = C THEN
 RETURN 'F';
ELSE
 RETURN 'X';
END IF;

DECODE로 옮기면 DECODE(A,B,'T',C,'F','X')


이를 기반으로 A=B인지, A=C인지,A=D인지, A=E인지, 그외의 경우 각 '1','2','3','4','5' 리턴하도록

DECODE(A,B,'1',C,'2'.D,'3',E,'4','5')

Q  요일마다 LOGIN 암호를 자동으로 바꿔주는 프로그램을 만든다고 가정 하자.

월요일엔 해당일자에 01 을 붙여서 4자리 암호를 만들고, 화요일엔 11,수요일엔 21,목요일엔 31,금요일엔 41,토요일엔 51,일요일엔 61 을 붙여서 4자리 암호를 만든다고 할 때, 암호를 SELECT 하는 SQL을  만들어 보자.

먼저 해당일자를 읽어온다.

```SQL
SELECT TO_CHAR(SYSDATE,'DD')
FROM DUAL;

===

19
```

요일 읽어오기

```SQL
SELECT TO_CHAR(SYSDATE,'DAY')
FROM DUAL;

===


화요일
```

위의 두문장 조합

```SQL
SELECT TO_CHAR(SYSDATE,'DD')|| TO_CHAR(SYSDATE,'DAY')
FROM DUAL;

===

19화요일
```

~>

```SQL
SELECT TO_CHAR(SYSDATE,'DD') ||
        DECODE(TO_CHAR(SYSDATE,'DAY'),'월요일','01',
                                     '화요일','11',
                                     '수요일','21',
                                     '목요일','31',
                                     '금요일','41',
                                     '토요일','51',
                                     '일요일','61') SEC_KEY
FROM DUAL;

===

1911

``` 

---

###### 80619151

DECODE의 사용(부등호비교)
-

IF A > B THEN
 RETURN 'T';
ELSE
 RETURN 'F';
END IF

IF 문 구성에서 대소비교를 하는 경우. 

이경우는 DECODE 함수를 다른 함수와 함께 사용

다른함수를 함께 사용하되, 대소비교를 등호비교 하듯이 해야함.

함수는 주로 SIGN,LEAST,GREATEST 등.

SIGN: 음수인지,양수인지 0인지
LEAST: 최소값 , GREATEST:최대값


Q LECTURE 테이블에서 LEC_TIME 과  LEC_POINT를 비교 해 보자.
  이렇게 비교한 결과를 이용해 LEC_TIME 이 크면 '실험과목', LEC_POINT가 크면 '기타과목', 둘이 같으면 '일반과목'으로 값을 돌려 받고자 한다.

```SQL
SELECT LEC_ID,LEC_TIME,LEC_POINT, 
       DECODE(SIGN(LEC_TIME-LEC_POINT),0,'일반과목',
                                       1,'실험과목',
                                       -1,'기타과목')AS TY
FROM LECTURE;

===

L0001	3	3	일반과목
L0002	3	2	실험과목
L0003	2	3	기타과목
L0004	2	2	일반과목
L0005	3	1	실험과목

```

Q 강의시간과 학점이 같거나 강의시간이 학점보다 작으면 '일반과목'을 돌려 받고 강의시간이 학점보다 큰 경우만 '실험과목' 이라고 돌려 받고 싶다면 어떻게 처리할 수 있을까요?.     
            SIGN을 이용하는 경우는 다음과 같다.

```SQL
SELECT LEC_ID,LEC_TIME,LEC_POINT,
       DECODE(SIGN(LEC_TIME-LEC_POINT),1,'실험과목','일반과목') TY
FROM LECTURE;


===

L0001	3	3	일반과목
L0002	3	2	실험과목
L0003	2	3	일반과목
L0004	2	2	일반과목
L0005	3	1	실험과목
```


Q LEAST를 이용하는 경우

```SQL
SELECT LEC_ID,LEC_TIME,LEC_POINT,
       DECODE(LEAST(LEC_TIME,LEC_POINT),LEC_TIME,'일반과목','실험과목') AS TY
FROM LECTURE;


강의시간 - 학점 비교한 최소값이 강의시간과 같으면 '일반과목' 아니면 '실험과목' 반환(둘중 최소값이라고 해석하는게 자연스러울듯)
=> 강의 시간이 학점보다 작거나 같으면 '일반과목' 아니면 '실험과목'

===

L0001	3	3	일반과목
L0002	3	2	실험과목
L0003	2	3	일반과목
L0004	2	2	일반과목
L0005	3	1	실험과목
```

GREATEST도 LEAST와 유사하게 사용, 단순하고 편하게 사용할 수 있으면 SIGN이 낫다





---

###### 80619155

DECODE의 사용(OR또는 IN비교)
-

1. IN

IF A IN(B,C,D) THEN
 RETURN 'T';
ELSE 
 RETURN 'F';
END IF;

2. OR

IF A = B OR
 A = C THEN
 RETURN 'T';
ELSE
 RETURN 'F';
END IF;

이경우는 ELSEIF로 분기되는 등호비교와 동일한 경우

1번의 IF는 DECODE(A,B,'T',C,'T',D,'T','F')

2번의 IF는 DECODE(A,B,'T',C,'T','F')

IF문의 구성방식이 다를뿐 DECODE로 옮기면 ELSEIF와 등호비교가 동일


---

###### 80619160

AND 비교
-

IF A = B AND
 A = C THEN
 RETURN T;
ELSE
 RETURN F;
END IF;

이 경우 뒤에서 다룰 중첩 IF의 경우와 동일하다
위를 다시작성하면 다음과 같이 변경가능

IF A = B THEN
 IF A = C THEN
  RETURN 'T';
 ELSE
  RETURN 'F';
 END IF;
ELSE
 RETURN 'F';
END IF;

DECODE(A,B,DECODE(A,C,'T','F'),'F')

DECODE를 중첩해서 이렇게 표현된다.


Q  LECTURE 테이블의 자료 중 LEC_TIME과 LEC_POINT가 같다라는 조건을 만족하고 LEC_TIME = 3 인 자료는 옆에 '중요과목' 이라는 비고를 붙이고자 한다.

```SQL
SELECT LEC_ID,LEC_TIME,LEC_POINT ,
       DECODE(LEC_TIME,LEC_POINT,DECODE(LEC_TIME,3,'중요과목'))
FROM LECTURE;


===

L0001	3	3	중요과목
L0002	3	2	
L0003	2	3	
L0004	2	2	
L0005	3	1	
```


---


###### 80619164

중첩 IF
-

IF A = B THEN
 IF A = C THEN
  RETURN 'T';
 ELSE
  RETURN 'F';
 END IF;
ELSE
 RETURN 'F';
END IF;

DECODE(A,B,DECODE(C,D,'T','F'),'F')

> "실제 업무에서는 DECODE 함수가 SUM,MAX,MIN,COUNT와 같은 GROUP 함수와 함께 사용되는 경우가 대부분이다.


Q TEMP의 행을 3개씩 묶어 하나의 번호를 부여 하고, 해당 ROWNUM을 3으로 나눈 나머지가 1,2,0인 경우로 나누어 칼럼의 위치를 정하는 SQL생성하라

```SQL
SELECT CEIL(ROWNUM/3) C0,DECODE(MOD(ROWNUM,3),1,EMP_ID,NULL) C2, --(굳이 NULL안붙여도됨) 
                      DECODE(MOD(ROWNUM,3),1,EMP_NAME,NULL) C3,
                      DECODE(MOD(ROWNUM,3),2,EMP_ID,NULL) C4,
                      DECODE(MOD(ROWNUM,3),2,EMP_NAME,NULL) C5,
                      DECODE(MOD(ROWNUM,3),0,EMP_ID,NULL) C6,
                      DECODE(MOD(ROWNUM,3),0,EMP_NAME,NULL) C7
FROM TEMP
WHERE EMP_ID>0;


===

1	19970101	김길동				
1			19960101	홍길동		
1					19970201	박문수
2	19930331	정도령				
2			19950303	이순신		
2					19966102	지문덕
3	19930402	강감찬				
3			19960303	설까치		
3					19970112	연흥부
4	19960212	배뱅이				
4			20000101	이태백		
4					20000102	김설악
5	20000203	최오대				
5			20000334	박지리		
5					20000305	정북악
6	20006106	유도봉				
6			20000407	윤주왕		
6					20000308	강월악
7	20000119	장금강				
7			20000210	나한라		

```

Q TEMP의 자료를 이용하여 한 행에 사번,성명을 3명씩 보여주는  SQL 작성 (어렵다)

```SQL
SELECT CEIL(ROWNUM/3) C0,
       MAX(DECODE(MOD(ROWNUM,3),1,EMP_ID,NULL)) C1, -- 나머지가 1인 녀석만 뽑히도록 max한것으로보인다
       MAX(DECODE(MOD(ROWNUM,3),1,EMP_NAME,NULL)) C2,
       MAX(DECODE(MOD(ROWNUM,3),2,EMP_ID,NULL)) C3,
       MAX(DECODE(MOD(ROWNUM,3),2,EMP_NAME,NULL)) C4,
       MAX(DECODE(MOD(ROWNUM,3),0,EMP_ID,NULL)) C5,
       MAX(DECODE(MOD(ROWNUM,3),0,EMP_NAME,NULL)) C6
FROM TEMP
WHERE EMP_ID>0
GROUP BY CEIL(ROWNUM/3); --c0가 1,2,3,4,5 중복없이 나오게끔한다.(집합)


===

1	19970101	김길동	19960101	홍길동	19970201	박문수
6	20006106	유도봉	20000407	윤주왕	20000308	강월악
2	19930331	정도령	19950303	이순신	19966102	지문덕
4	19960212	배뱅이	20000101	이태백	20000102	김설악
5	20000203	최오대	20000334	박지리	20000305	정북악
3	19930402	강감찬	19960303	설까치	19970112	연흥부
7	20000119	장금강	20000210	나한라		
 
```

---


###### 80622_95

test
-
