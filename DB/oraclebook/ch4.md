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

<DECODE의 사용 -(단순 등호 비교) >

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



---
- [NOT IN 의 함정](#8625_100)

###### 8625_100

NOT IN 의 함정
-

IN 연산자의 반대개념.

Q
1.HOBBY가 NULL 또는 '등산' 인 경우
2.HOBBY가 NULL 또는 '낚시' 모두에 속하지 않는 경우

1의경우
```SQL
SELECT EMP_ID,EMP_NAME,HOBBY
FROM TEMP
WHERE HOBBY IN (NULL,'등산');

===

  19970101    김길동      등산                          

   20000101   이태백      등산     
```
HOBBY가 NULL 인 경우에 해당되는 6건은 빠졌다.

위의 조건을 다시쓰면
```
WHERE HOBBY = NULL
OR HOBBY = '등산'
```
의 OR이 되고 HOBBY=NULL 이란 조건은 항상 FALSE가 RETURN된다.

?> NULL은 항상 IS NULL 또는 IS NOT NULL로 비교되야 하기 때문에 검색이 안된다.

2번의 경우 

HOBBY NOT IN (NULL,'낚시')

했을떄, 예상과 달리 한건도 검색 못한다.

?> NOT IN을 풀어쓰면
```
WEHRE HOBBY <> NULL
AND HOBBY <> '낚시';
```

이와 같이 AND 연산을 하게되므로 
모든 RECORD가 HOBBY<> NULL 조건에 위배된다.


---
- [IN LINE VIEW](#8625_102)

###### 8625_102

IN LINE VIEW
-

VIEW를 사용하는데 CREATE 시키지 않고 바로 SQL에서 사용한다.

VIEW를 CREATE 했을때, 관리 차원에서 자주쓰지 않는경우 곤란해지는데.

일회성으로 쓰기 위해  VIEW를 SQL안으로 포함시켜서 편리하게 쓴다.

VIEW안에서 다른 VIEW가 필요할때도 마찬가지

'(서브쿼리는 WHERE의 조건에 , 인라인뷰는 FROM에 - JOIN 으로 쓰는것 같다.)'

Q 사원테이블에서 사원을 골라내는데 해당 사원의 연봉이  동일한 직급을 가진 사원의 평균 연봉보다 많은 사원을 찾아오고 싶은 경우

서브쿼리로 다룰수 있다. 

x> 자신의 연봉과 평균연봉을 비교해보기 위해 두 칼럼을 모두 조회하고자 하면?
-> 서브쿼리만으로는 방법이 없다.
?>조인이 일어나야 하기 떄문(자신의테이블과)

x> 단순히 조인으로 -> 가능하지만,어렵고 결과가 신통치않다.

기자신의 테이블과 조인을 하면서 한쪽 테이블의 레코드가 가진 직급 정보를 이용하여 다른 쪽 테이블과 NON EQUAL 조인을 걸고 그 결과를 이용하여 GROUP BY를 해야 한다.  그런데 NON EQUAL 조인이 성능을 보장하는 데는 어려움이 많이 따른다.

각 직급별로 미리 평균연봉을 읽어놓은 VIEW 만들어놓고 이 VIEW를 사용하면 편하다.

VIEW를 만들어서 이름을 '연봉VIEW'라고 주었다면 문장이 아래와같이 된다.

```SQL
SELECT A.EMP_ID,A.EMP_NAME,
	A.SALARY,B.AVG_SAL
FROM TEMP A , 연봉VIEW B
WHERE A.LEV = B.LEV
AND S.SALARY>B.AVG_SAL;
```
(직급별로 GROUP을 지어놓은 VIEW이므로 사원의 직급과 VIEW의 직급을 조인으로 연결만 해주면된다.)

이 VIEW를 SQL 안으로 바로 포함시키면 다음과 같다. (연봉 VIEW 부분이 SQL로 변경)

```SQL
SELECT A.EMP_ID,A.EMP_NAME,
       A.SALARY,B.AVG_SAL
FROM TEMP A,
     (SELECT LEV,AVG(SALARY) AVG_SAL
     FROM TEMP
     GROUP BY LEV
     )B
WHERE A.LEV=B.LEV
AND A.SALARY > B.AVG_SAL;
```

Q TEMP 의 자료에서 ROWNUM을 채취해 값이 5와 10시이에 있는 행의 ROWNUM, 사번, 성명을 조회하는 SQL을 만들어본다.

//아래와 같이 하면 안된다.(ROWNUM은 조회된 결과에 대해서 카운트 하는것이기 때문에 5~10을 조회할수가없다.(1부터 나와야)

```SQL
SELECT ROWNUM,EMP_ID,EMP_NAME
FROM TEMP
WHERE ROWNUM BETWEEN 5 AND 10;
```

//올바른예
```SQL
SELECT NO,EMP_ID,EMP_NAME
FROM (SELECT ROWNUM NO,EMP_ID,EMP_NAME
      FROM TEMP
      WHERE EMP_ID > 0
      )
      WHERE NO BETWEEN 5 AND 10;

===

5	19950303	이순신
6	19966102	지문덕
7	19930402	강감찬
8	19960303	설까치
9	19970112	연흥부
10	19960212	배뱅이

```

'( FROM절의 인라인뷰 하나로 된것이라서 NO 가 된다는것 유의)


---
- [VIEW  - 알아두기](#8625_132)

###### 8625_132

VIEW  - 알아두기
-

VIEW를 쉽게 생각해서 창문, 테이블을 방이라고 생각하면..

창문을 통해 방안의 모든 물건을 볼순 없지만, 볼것만 정해놓고 창앞에 가져다놓고 그것만 볼 수있다.

창문을 통해 보는것은 실제로 물건을 가지고 있는 것은 아니다. 하지만 방(테이블)에 있는 실제 사물의 상태 그대로 보는것이다.

VIEW의 의미는 하나의 SELECT문과 같다고 볼 수있따. 물론 INSERT, UPDATE, DELETE도 가능하지만 대게 SELECT만을 위해 사용.

단지 SQL을 PC에 저장해놓고 실행하는게 아니라
실제 DB에 저장해놓고 필요할때 실행시켜 테이블처럼 사용가능.

뷰를 통해 볼수 있는 영역이 정해져 있어서 권한 관리의 수단으로도 사용


IF>TEMP에서 자료를 많이 SELECT한다면  

대부분 '정규'사원만 보기 위한경우이며, 취미는 안보는게 편하고, 부서명도 함께 나오길 바라며, 컬럼 명을 한글로 바꾸어 봤으면 좋겟다

이중 하나라도 원하는 경우 VIEW를 CREATE하여 테이블 처럼 이용하면 편하다.


```SQL
CREATE VIEW TEMP_VIEW
AS
SELECT A.EMP_ID     AS "사번",
       A.EMP_NAME   AS "성명",
       A.BIRTH_DATE AS "생일",
       A.DEPT_CODE  AS "부서",
       B.DEPT_NAME  AS "부서명",
       A.SALARY     AS "연봉",
       A.LEV        AS "직급"
FROM TEMP A, TDEPT B
WHERE B.DEPT_CODE = A.DEPT_CODE
AND A.EMP_TYPE = '정규';

// view를 create 한후

SELECT *
FROM TEMP_VIEW;    

===

19970101	김길동	74/01/25	AA0001	경영지원	100000000	부장
19960101	홍길동	73/03/22	AB0001	재무	72000000	과장
19970201	박문수	75/04/15	AC0001	총무	50000000	과장
19930331	정도령	76/05/25	BA0001	기술지원	70000000	차장
19950303	이순신	73/06/15	BB0001	H/W지원	56000000	대리
19966102	지문덕	72/07/05	BC0001	S/W지원	45000000	과장
19930402	강감찬	72/08/15	CA0001	영업	64000000	차장
19960303	설까치	71/09/25	CB0001	영업기획	35000000	사원
19970112	연흥부	76/11/05	CC0001	영업1	45000000	대리
19960212	배뱅이	72/12/15	CD0001	영업2	39000000	과장
   
```



---
- [HIERACHICAL SELECT(계층형 SELECT)](#8626_130)

###### 8626_130

HIERACHICAL SELECT(계층형 SELECT)
-

HIERACHICAL SELECT

CONNECT BY ...
START WITH 형식은
자료의 구조가 계층적으로 이루어진 경우 상위자료부터 하위자료로의 자료전개 및 하위자료에서 상위자료로의 역 전개를 위하여 사용되는 SELECT 형식.

E> TDEPT에서 부모노드의 DEPT_CODE = 자식노드의 PARENT_DEPT의 논리식이
CONNECT BY PRIOR DEPT_CODE = PARENT_DEPT로 표현됨

START WITH 구문은 전개 또는 역전개시 전개를 시작할 행을 지정해준다.

E> START WITH DEPT_CODE = '000000' 지정시 DEPT_CODE가 '000000' 인행부터 전개를 시작

(테스트를 위해 TDEPT의 자료 추가, 변경)

Q 1.다음과 같이 새로운 부서 추가

```SQL
INSERT INTO TDEPT (DEPT_CODE, DEPT_NAME, PARENT_DEPT, USE_YN, AREA, BOSS_ID ) VALUES
                  ('000000','사장실','  ', 'Y','서울','');
```

Q 2.자기자신이 상위부서로 되어있는 부서의 상위 부서를 새로 만든 부서로 바꾼다.

```SQL
UPDATE TDEPT 
SET PARENT_DEPT = '000000'
WHERE DEPT_CODE = PARENT_DEPT;
```


/
Q 사장실('000000')을 기준으로 전개를 하면 다음과 같다.

```SQL
SELECT LEVEL, DEPT_CODE,DEPT_NAME
FROM TDEPT
CONNECT BY PRIOR DEPT_CODE = PARENT_DEPT
START WITH DEPT_CODE = '000000';

===
1	000000	사장실
2	AA0001	경영지원
3	AB0001	재무
3	AC0001	총무
2	BA0001	기술지원
3	BB0001	H/W지원
3	BC0001	S/W지원
2	CA0001	영업
3	CB0001	영업기획
3	CC0001	영업1
3	CD0001	영업2

```
LEVEL이 1이면 최상위 ROOT가 된다.
ROOT의 바로 하위가 LEVEL이 2가되며 
이를 상위부서로 가지는 부서가 LEVEL3이된다.

이를 한눈에 구분하기 위해 수정

```SQL
SELECT LPAD(DEPT_CODE,LEVEL*5, '  '),DEPT_NAME
FROM TDEPT
CONNECT BY PRIOR DEPT_CODE = PARENT_DEPT
START WITH DEPT_CODE = '000000';

===

00000	사장실
    AA0001	경영지원
         AB0001	재무
         AC0001	총무
    BA0001	기술지원
         BB0001	H/W지원
         BC0001	S/W지원
    CA0001	영업
         CB0001	영업기획
         CC0001	영업1
         CD0001	영업2

```


아래는 선택되는 행의 제어방식이다

1.
```SQL
SELECT LPAD(DEPT_CODE,LEVEL*5,'  '),DEPT_NAME
FROM TDEPT
WHERE DEPT_CODE <> 'CA0001'
CONNECT BY PRIOR DEPT_CODE = PARENT_DEPT
START WITH DEPT_CODE = '000000';

===

00000	사장실
    AA0001	경영지원
         AB0001	재무
         AC0001	총무
    BA0001	기술지원
         BB0001	H/W지원
         BC0001	S/W지원
         CB0001	영업기획
         CC0001	영업1
         CD0001	영업2

```
WHERE의 조건을 통해 제외된 행 - 전개결과에서 해당행만 제외됨.

2.
```SQL
SELECT LPAD(DEPT_CODE, LEVEL*5, '  '),DEPT_NAME
FROM TDEPT
CONNECT BY PRIOR DEPT_CODE = PARENT_DEPT
AND DEPT_CODE <> 'CA0001'
START WITH DEPT_CODE = '000000';

===

00000	사장실
    AA0001	경영지원
         AB0001	재무
         AC0001	총무
    BA0001	기술지원
         BB0001	H/W지원
         BC0001	S/W지원
```
CONNECT BY 에서 동일한 행을 제외했는데, 해당행을 포함한 하위의 행들이 모두 제외됨.


3.역전전개의 예. 이전에는 최상위부서->하위부터 전개, X> 이번에는 특정부서를 기준으로 자신의 상위부서를 찾아낸다.

```SQL
SELECT LPAD(DEPT_CODE, LEVEL*5, '  '),DEPT_NAME,LEVEL
FROM TDEPT 
CONNECT BY PRIOR PARENT_DEPT = DEPT_CODE
START WITH DEPT_CODE = 'CD0001';

===

CD000	영업2	1
    CA0001	영업	2
         000000	사장실	3

```
차이는 CONNECT BY 의 PRIOR 뒤에 DEPT_CODE와 PARENT_DEPT의 순서가 바뀌면서 전개가 역으로 이루어진다.

CONNECT BY 구문에서 PRIOR 연산자는 탐색방향을 의미한다.

<순서요약>
1.START WITH -> ROOT 이동
2.CONNECT BY -> 트리작성
3.PRIOR에 기술된 방향으로 트리탐색
4.CONNECT BY 의 조건 -> 필요시 트리의 가지를 제외
5.WHERE의 조건 -> 각 행 제외
6.ORDER BY -> 순서대로 행 정렬

Q 부서별로 부서장과 성명을 보여주는데, 부서의 전개에 의해 도출된 결과와 같은 형시긍로 보고 싶은경우의 SQL생성

```SQL
SELECT A.LEV,A.BOSS,B.EMP_NAME
FROM TEMP B,
     (SELECT LEVEL LEV,LPAD(BOSS_ID,LEVEL*5,'  ') BOSS
     FROM TDEPT
     CONNECT BY PRIOR DEPT_CODE = PARENT_DEPT
     START WITH DEPT_CODE = '000000'
     ) A
     WHERE B.EMP_ID(+) = A.BOSS;

===

1		
2	  19970101	김길동
3	       19960101	홍길동
3	       19970201	박문수
2	  19930331	정도령
3	       19950303	이순신
3	       19966102	지문덕
2	  19930402	강감찬
3	       19960303	설까치
3	       19970112	연흥부
3	       19960212	배뱅이

```


---
- [HINTS](#8626_153)

###### 8626_153

HINTS
-

DML을 수행할때 OPTIMIZER가 관여하게 된다.
이는 가장 효율적으로 처리할 수 있는 최적화 SOLUTION(수행경로)을 찾게된다.

최적화 대안을 찾기위해 많은 요인을 고려한다

어떤 테이블을 먼저 읽을지?
테이블 읽을때 인덱스를 이용할것인지?
어떤 인덱스를 이용할것인지?
조인 필요시 어떤방식으로 조인할것인지? 등

여러요인 고려, 실행계획이 작성되면 이에 의해 DML이 수행된다.

OPTIMIZER가 선택한 최적화 경로인 수행경로를 
PLAN을 이용해 들여다 볼 수있다.

HINTS는 이 OPTIMIZER가 실행계획을 작성하는 단계에 인위적으로 개발자가 관여 하겠다는 의도를 가질때 사용.

?> OPTIMIZER가 만든 실행계획보다 더 좋은 경로를 개발자가 알고 있거나, 강제로 제어할 필요가 있있는경우사용

관여방식에도 DATABASE의 초기값에 지정하는방법,
특정 SESSION에 수행되는 전체 DML 관여방법,
특정 하나의 DML만 관여하는 방법 등이있다.
(더 깊은 부분은 튜닝서적 참조)

-흰트의 종류와 간략한 사용법-

A. initialization parameter중 OPTIMIZER_MODE 지정가능 값

 1. ALL_ROWS

      Goal : Best Throughput

      용도 : 전체 RESOURCE 소비를 최소화 시키기 위한 힌트.

             Cost-Based 접근방식.  

 ```SQL
	SELECT /*+ALL_ROWS */ EMP_ID,EMP_NAME

             FROM   TEMP

             WHERE  EMP_ID = 19960101;  
```

2. FIRST_ROWS

      Goal : Best Response Time

      용도 : 조건에 맞는 첫번째 row를 리턴하기 위하여 Resource소비를 최소화 시키기 위한 힌트.

             Cost-Based 접근방식.

      특징 : - Index Scan이 가능 하다면 Optimizer가 Full Table Scan 대신               Index Scan을 선택한다.

             - Index Scan이 가능하다면 Optimizer가 Sort-Merge 보다 Nested  Loop 을 선택한다.

             - Order By절에 의해 Index Scan 이 가능하다면, Sort과정을 피하기 위해 Index Scan을 선택한다.

             - Delete/Update Block에서는 무시된다.     

             - 다음을 포함한 Select 문에서도 제외된다.

               집합연산자 (Union,Intersect,Minus,Union All)

               Group By

               For UpDate

               Group 함수

               Distinct   

  
```SQL
             SELECT /*+FIRST_ROWS */ EMP_ID,EMP_NAME

             FROM   TEMP

             WHERE  EMP_ID = 7655;  
```

3.CHOOSE

      Goal : Acess되는 테이블에 통계치 존재여부에 따라

             Optimizer로 하여금 Rule-Based Approach와 Cost-Based Approach             중 하나를 선택할 수 있게 한다.

      용도 : Data Dictionary가 해당테이블에 대해 통계정보를 가지고 있다면             Optimizer는 Cost-Based Approach를 선택하고, 그렇지 않다면 Rule-Based Approach를 선택한다.   

```SQL
 	 SELECT /*+CHOOSE */ EMP_ID,EMP_NAME

             FROM   TEMP

             WHERE  EMP_ID = 19960101;  
```



   4.RULE

      용도 : Rule-Based 최적화를 사용하기위해.   
```SQL
	SELECT /*+RULE */ EMP_ID,EMP_NAME

             FROM   TEMP

             WHERE  EMP_ID = 19960101;  
```


B. Access Methods 로써의 Hints 

   1.FULL

      용도 : 해당테이블의 Full Table Scan을 유도.     
```SQL
	 SELECT /*+FULL(TEMP) */ EMP_ID,EMP_NAME

             FROM   TEMP

             WHERE  EMP_ID = 19960101;
```

           * 테이블 Alias 가 있는 경우는 Alias사용.

              Schema Name은 사용 안함(From에 SCOTT.EMP 라고 기술해도 hint에는 EMP사용).


 2.ROWID

      용도 : 지정된 테이블의 ROWID를 이용한 Scan 유도

   3.CLUSTER

      용도 : 지정된 테이블Access에 Cluster Scan 유도.

             Cluster된 Objects에만 적용가능.     

             /*+CLUSTER(table) */

   4.HASH

      용도 : 지정된 테이블Access에 HASH Scan 유도.

             /*+HASH(table) */

   5.HASH_AJ

      용도 : NOT IN SubQuery 를 HASH anti-join으로 변형

             /*+HASH_AJ */  

   6.HASH_SJ

      용도 : correlated Exists SubQuery 를 HASH semi-join으로 변형

             /*+HASH_SJ */

7.INDEX

      용도 : 지정된 테이블Access에 Index Scan 유도.

           * 하나의 index만 지정되면 optimizer는 해당index를 이용.

           * 여러 개의 인덱스가 지정되면  optimizer가 각 index의 scan시 cost를 분석 한 후 최소비용이 드는 index사용.

             경우에 따라 optimizer는 여러 index를 사용한 후 결과를 merge하는 aceess방식도 선택.

           * index가 지정되지 않으면 optimizer는 테이블의 이용 가능한 모든 index에 대해 scan cost를 고려한 후 최저비용이 드는  index scan을 선택한다.

      예   : SELECT /*+INDEX(TEMP  temp테이블index_name) */ EMP_ID,EMP_NAME

             FROM   TEMP

             WHERE  DEPTNO= ‘AB0001’

  8.INDEX_ASC

      용도 : INDEX HINT와 동일. 단, ASCENDING으로 SCAN함을 확실히 하기 위함.

   9.INDEX_COMBINE

      용도 : INDEX명이 주어지지 않으면 OPTIMIZER는 해당 테이블의 best cost로 선택된 Boolean combination index를 사용한다.

             index 명이 주어지면 주어진 특정 bitmap index 의 boolean combination 의 사용을 시도한다.         

             /*+INDEX_COMBINE(table index) */

  10. INDEX_DESC

      용도 : 지정된 테이블의 지정된 index를 이용 descending으로 scan 하려고 할 때 사용.

             /*+INDEX_DESC(table index) */

  11. INDEX_FFS

      용도 : full table scan보다 빠른 full index scan을 유도.

             /*+INDEX_FFS(table index) */

  12. MERGE_AJ

      용도 : not in subquery를 merge anti-join으로 변형

             /*+MERGE_AJ */

13.MERGE_SJ

      용도 : correalted EXISTS subquery를 merge semi-join으로 변형

             /*+MERGE_SJ */

  14.AND_EQUAL

      용도 : single-column index의 merge를 이용한 access path 선택. 적어도 두개 이상의 index가 지정되어야 한다.

            /*+AND_EQUAL(table index1,index2...) */            

  15.USE_CONCAT

      용도 : 조건절의 OR를 Union ALL 형식으로 변형한다.

             일반적으로 변형은 비용측면에서 효율적일 때만 일어난다.

           /*+USE_CONCAT */                           

C. JOIN 순서를 결정하는 Hints

   1. ORDERED

      용도 : from절에 기술된 테이블 순서대로 join이 일어나도록 유도.         

           /*+ORDERED */

      예   : SELECT /*+ORDERED */ TAB1.COL1,TAB2.COL2,TAB3.COL3

             FROM   TAB1,TAB2,TAB3

             WHERE  TAB1.COL1=TAB2.COL1

             AND    TAB2.COL1=TAB3.COL1; 

 2. STAR

      용도 : STAR QUERY PLAN이 사용 가능하다면 이를 이용하기위한 HINT.

             STAR PLAN은 규모가 가장 큰 테이블이 QUERY에서 JOIN ORDER상             마지막으로 위치하게 하고 NESTED LOOP로 JOIN이 일어나도록 유도한다.

 적어도 3개 테이블 이상이 조인에 참여해야 하며 LARGE TABLE의             CONCATENATED INDEX는 최소 3컬럼 이상을 INDEX에 포함해야 한다.

 테이블이 ANALYZE 되어 있다면 OPTIMIZER가 가장 효율적인 STAR PLAN을             선택한다. 

          /*+STAR */ 

D. JOIN OPERATION을 결정하는 HINTS.

   1.USE_NL

      용도 : 테이블의 JOIN 시 테이블의 각 ROW가 INNER 테이블을 NESTED LOOP

             형식으로 JOIN 한다.

          /*+USE_NL(inner_table) */   

     예   : SELECT /*+ORDERD USE_NL(CUSTOMER) */

           FROM ACCOUNT.BALANCE,CUSTOMER.LAST_NAME, USTOMER.FIRST_NAME

           WHERE  ACCOUNT.CUSTNO = CUSTOMER.CUSTNO;

   2.USE_MERGE

      용도 : 지정된 테이블들의 조인이 SORT-MERGE형식으로 일어나도록 유도.

          /*+USE_MERGE(table) */

           * 괄호 안의 테이블은 JOIN ORDER상의 뒤의 테이블(?)

   3.USE_HASH

      용도 : 각 테이블간 HASH JOIN이 일어나도록 유도.

          /*+USE_HASH(table) */

           * 괄호 안의 테이블은 JOIN ORDER상의 뒤의 테이블(?)

   4.DRIVING_SITE

      용도 : QUERY의 실행이 ORACLE에 의해 선택된 SITE가 아닌 다른 SITE에서

             일어나도록 유도.

          /*+DRIVING_SITE(table) */

      예   : SELECT /*+DRIVING_SITE(DEPT)  */

             FROM   EMP,DEPT@RSITE

             WHERE  EMP.DEPTNO = DEPT.DEPTNO;     

             DRIVING_SITE 힌트를 쓰지 않으면 DEPT의 ROW가 LOCAL SITE로 보내져             LOCAL SITE에서 JOIN이 일어나지만, DRIVING_SITE 힌트를 쓰면 EMP의 ROW들이REMOTE SITE로 보내져  QUERY가 실행된 후 LOCAL SITE로 결과가 RETURN된다.

 

Q  HINTS를 사용한 경우와 그렇지 않은 경우의 PLAN을 비교해 보자.
HINTS 미사용
```SQL
SELECT A.EMP_ID, A.EMP_NAME, A.DEPT_CODE, B.DEPT_NAME
FROM TDEPT B, TEMP A
WHERE B.DEPT_CODE = A.DEPT_CODE;

--------------------------------------------------

        SELECT STATEMENTCost Estimate:                                                 

         NESTED LOOPS                                                                 

           TABLE ACCESSFULL:SCOTTTEMP(1)                                              

           TABLE ACCESSBY ROWID:SCOTT,TDEPT(2)                                        

             INDEXUNIQUE SCAN:SCOTT,,,,,,,SYS_C002701 (U)                             

5 rows selected.

```

Q 흰트사용

```SQL
SELECT STATEMENTCost Estimate:
NESTED LOOPS
TABLE ACCESSFULL:SCOTTTEMP(1)
TABLE ACCESSBY ROWID:SCOTT,TDEPT(2)
INDEXUNIQUE SCAN:SCOTT,,,,,,,SYS_C002701 (U);


        --------------------------------------------------

        SELECT STATEMENTCost Estimate:8                                                 

         HASH JOIN                                                                    

           TABLE ACCESSFULL:SCOTT,TDEPT(1)                                            

           TABLE ACCESSFULL:SCOTTTEMP(2)                                              

4 rows selected.

```
