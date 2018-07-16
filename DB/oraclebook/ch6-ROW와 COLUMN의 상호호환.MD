- [ROW 단위 -> COLUMN 단위](#8710_091)

---

###### 8710_091

ROW 단위 -> COLUMN 단위
-

ROW -> COLUMN이 반대의 경우보다 많이 일어난다.

1)   먼저 19건의 자료에 번호를 붙여서 SELECT를 해 본다.
```SQL
SELECT ROWNUM NO,

        GUBUN

FROM  SAM_TAB02;

 

       NO         GUBUN

       ---------- -----

                1 F107

                2 F108

                3 F109

. . . . . .  중략

               17 F123

               18 F124

               19 F125

19 rows selected.
```

ROWNUM 에 ALIAS 를 준 것은 나중에 IN LINE VIEW 로 이용하기 위해서이다.


2)   이번에 할 일은 번호를 이용해서 앞에서부터 4개씩 다시 동일한 번호를 순차적으로 부여 한다.

   이렇게 부여된 번호는 4개 씩이 같은 줄에 들어갈 것이라는 구분을 주는 것이다.

```SQL
   SELECT CEIL(NO/4) RNO,NO,

           GUBUN

FROM  (SELECT ROWNUM NO,

        GUBUN

           FROM  SAM_TAB02

          );

        RNO        NO         GUBUN

        ---------- ---------- -----

                 1          1  F107

                 1          2  F108

                 1          3  F109

                 1          4  F110

                 2          5  F111

                 2          6  F112

                 2          7  F113

                 2          8  F114

                 3          9  F115

                 3         10  F116

                 3         11  F117

                 3         12  F118

                 4         13  F119

                 4         14  F120

                 4         15  F121

                 4         16  F122

                 5         17  F123

                 5         18  F124

                 5         19  F125

   19 rows selected.
```

3)   이제 남은 것은 4개씩 묶인 것들에 다시 순서를 줘야 그것이 몇 번째 COLUMN으로 쓰일 것인지를 구분 할 수 있다.

바로 위의 SQL에 컬럼을 하나 더 추가 하자.
```SQL
   SELECT CEIL(NO/4) RNO,

           MOD(NO,4) CNO,  

           NO,          

           GUBUN

FROM  (SELECT ROWNUM NO,

        GUBUN

           FROM  SAM_TAB02

          );

       RNO        CNO        NO         GUBUN

       ---------- ---------- ---------- -----

                1          1          1  F107

                1          2          2  F108

                1          3          3  F109

                1          0          4  F110

                2          1          5  F111

                2          2          6  F112

                2          3          7  F113

                2          0          8  F114

                3          1          9  F115

                3          2         10  F116

                3          3         11  F117

                3          0         12  F118

                4          1         13  F119

                4          2         14  F120

                4          3         15  F121

                4          0         16  F122

                5          1         17  F123

                5          2         18  F124

                5          3         19  F125

19 rows selected.
```
새로 추가된 CNO 컬럼은 ROWNUM 을 4로 나눈 나머지를 이용하여 구했다.

몇번째 컬럼에 위치할 ROW인지를 구분하기 위해 만들어 놓은 값인 것이다.

여기까지 만들어 놓고 살펴보면 1번째 행은 1번 행1번 열,2번째 행은 1번 행 2번 열, 3번째 행은 1번 행 3번 열에 위치하며, 이런 식으로 마지막 행까지 몇 번 행 몇 번 열에 위치해야 되는지를 결정 지었다. CNO 가0으로 나온 행은 4번째 열에 위치 시키면 된다.

4)   이번에는 현재 같은 열에 나오고 있는 GUBUN 을 CNO 의 수에 의해 각기 다른 행으로 분산 시킨다.

이제 더 이상 NO 의 값은 필요 없게 된다.
```SQL
   SELECT CEIL(NO/4) RNO,

           MOD(NO,4) CNO,  

           DECODE(MOD(NO,4),1,GUBUN)  C1,

           DECODE(MOD(NO,4),2,GUBUN)  C2,

           DECODE(MOD(NO,4),3,GUBUN)  C3,

           DECODE(MOD(NO,4),0,GUBUN)  C4

FROM  (SELECT ROWNUM NO,

        GUBUN

           FROM  SAM_TAB02

          );

 

       RNO        CNO        C1    C2    C3    C4  

       ---------- ---------- ----- ----- ----- -----

                1          1 F107                  

                1          2       F108            

                1          3             F109      

                1          0                   F110

                2          1 F111                  

                2          2       F112            

                2          3             F113      

                2          0                   F114

                3          1 F115                  

                3          2       F116            

                3          3             F117      

                3          0                   F118

                4          1 F119                  

                4          2       F120            

                4          3             F121      

                4          0                   F122

                5          1 F123                  

                5          2       F124            

                5          3             F125      

19 rows selected.
```

이제는 각 행의 값을 자기가 찾아가야 할 위치의 COLUMN 에 위치시킨다.

문제는 아직 같은 줄에 나와야 하는 RNO 가 각기 다른 줄에 나오고 있다는 것이다.

RNO 가 같은 값을 한 줄로 묶어 줘야 한다.

5)   각 행을  컬럼 별로 위치시켰으니 CNO 컬럼도 더 이상 필요 없게 된다.
```SQL
   SELECT CEIL(NO/4) RNO,

           MAX(DECODE(MOD(NO,4),1,GUBUN) )  C1,

           MAX(DECODE(MOD(NO,4),2,GUBUN) )  C2,

           MAX(DECODE(MOD(NO,4),3,GUBUN) )  C3,

           MAX(DECODE(MOD(NO,4),0,GUBUN) )  C4

FROM  (SELECT ROWNUM NO,

        GUBUN

           FROM  SAM_TAB02

          )

GROUP BY CEIL(NO/4);

 

       RNO        C1    C2    C3    C4  

       ---------- ----- ----- ----- -----

                1 F107  F108  F109  F110

                2 F111  F112  F113  F114

                3 F115  F116  F117  F118

                4 F119  F120  F121  F122

                5 F123  F124  F125      

5 rows selected.
```
(
 여러 개의 행이 하나의 행으로 나오게 해 줘야 하는데 이 방법은 이미 GROUP 함수를 다룰 때 언급이 되었다.

그룹함수는 여러 개의 행을 받아서 하나의 행을 결과로 돌려준다고…….

   RNO가 1인 처음 4개의 행만을 놓고 잠깐 생각을 해 보자.

4개를 이용해 하나의 ROW를 만드는 건 GROUP BY를 이용하여 GROUP 함수를 적용하면 되는데 각 컬럼의 값은 어떻게 할 것인가?.

물론 4개 행을 하나로 통과시키는 역할은 GROUP BY 뒤에 오는 컬럼 값에 의해 결정되므로 RNO 컬럼이 GROUP BY 단위가 된다.

4개의 행을 각 COLUMN 별로 보면 C1에 한 행만 값이 있고 나머지는 NULL 이다. 나머지 C2,C3,C4 열도 마찬가지가 된다.

모든 함수에 널이 들어가면 연산에서 제외한다고 했으니까 4개의 행이 그룹함수를 통과하면서 제외되고 값이 있는 하나의 행만이 통과 될 것이다.

문제는 어떤 GROUP 함수를 이용하느냐 이다.  그룹 함수 중 자료형에 무관하게 쓸 수 있는 함수로는 MAX와 MIN 이 있다. 어느것을 적용해도 NULL값을 제외하면 C1 부터 C4 까지 각 컬럼 별로 하나의 행만 통과되므로 결과는 동일하다.

)


         질문: TEMP 의 자료를 이용해 한 행에 5명의 사번과 성명을 보여주는 QUERY 를 작성해 보라.

         답변:
```SQL
          SELECT MAX(DECODE(MOD(NO,5),1,EMP_ID))         ID1 ,

                 MAX(DECODE(MOD(NO,5),1,EMP_NAME))       NAME1 ,

                 MAX(DECODE(MOD(NO,5),2,EMP_ID))         ID2 ,

                 MAX(DECODE(MOD(NO,5),2,EMP_NAME))       NAME2 ,

                 MAX(DECODE(MOD(NO,5),3,EMP_ID))         ID3 ,

                 MAX(DECODE(MOD(NO,5),3,EMP_NAME))       NAME3 ,

                 MAX(DECODE(MOD(NO,5),4,EMP_ID))         ID4 ,

                 MAX(DECODE(MOD(NO,5),4,EMP_NAME))       NAME4 ,

                 MAX(DECODE(MOD(NO,5),0,EMP_ID))         ID5 ,

                 MAX(DECODE(MOD(NO,5),0,EMP_NAME))       NAME5

          FROM (SELECT ROWNUM NO,EMP_ID,EMP_NAME

                FROM   TEMP

                WHERE EMP_ID > 0

                )

        GROUP BY CEIL(NO/5);

         결과:

      ID1       NAME1 ID2       NAME2 ID3       NAME3 ID4       NAME4 ID5       NAME5

      -------- ------ -------- ------ -------- ------ -------- ------ -------- ------

      19930331 정도령 19930402 강감찬 19950303 이순신 19960101 홍길동 19960212 배뱅이

      19960303 설까치 19966102 지문덕 19970101 김길동 19970112 연흥부 19970201 박문수

      20000101 이태백 20000102 김설악 20000119 장금강 20000203 최오대 20000210 나한라

      20000305 정북악 20000308 강월악 20000334 박지리 20000407 윤주왕 20006106 유도봉

    4 rows selected.
```


2.  Column단위 자료를 Row단위로

 Column단위 자료를 Row단위로 바꾸는 방식은 Cartesian Product 을 이용한다.

 먼저 선행되어 학습해야 할 부분이 11장 의 DATA 복사를 이용한 QUERY 일 수도 있지만 이미 Cartesian Product 을 이용한 Query를 기본 문법에서 다루었으므로 진행에 지장이 없으리라 여겨진다.

 이 방법은 방금 학습한 내용을 원위치로 돌리고자 할 때 이용한다.

 Row로 만들고 싶은 컬럼의 수 만큼 존재하는 행을 부풀린 후 DECODE를 이용해 경우에 맞는 컬럼을 한 컬럼으로 모아 보여주는 방식이다.

(어려워서 생략)
