- [ROWNUM](#80618164)

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
```
====

1	20000101	이태백
2	20000102	김설악
3	20000203	최오대
4	20000334	박지리
5	20000305	정북악
...


Q 이번에는 위의 조건에 ROWNUM 과 관련된 조건을 붙여 보자.                 
```SQL
SELECT ROWNUM,EMP_ID,EMP_NAME
FROM TEMP
WHERE EMP_ID>0
AND LEV='수습'
AND ROWNUM <5;
```
====

1	20000101	이태백
2	20000102	김설악
3	20000119	장금강
4	20000203	최오대


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
```
====
2	20000102	김설악
4	20000334	박지리
1	20000101	이태백
3	20000203	최오대

이와같이 조건절 만족시킨 행들에 ROWNUM이 붙고 난후에 ORDER BY가 이루어짐.


Q TEMP 테이블의 자료를 이용하여 SELECT 결과를 3개행씩 묶어 하나의 번호를 부여하는 SQL 을 만들어보자.
ROWNUM, 부여된번호, EMP_ID, EMP_NAME 을 보여주면 된다.          

```SQL
SELECT ROWNUM,CEIL(ROWNUM/3),EMP_ID,EMP_NAME
FROM TEMP
WHERE EMP_ID>0;
```

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

