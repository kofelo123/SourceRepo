## oracle

- [rownum](#rownum)
- [concat](#concat)
- [PK생성과 제약조건 이름변경](#pkconstraintrename)
- [테이블 조인하기](#tablejoin)
- [칼럼속성 수정](#modifycolumn)
- [view를 쓰는이유](#view)
- [조인](#join)
  - [조인사용예제](#joinex)
---

### RowNum

> 오라클에서는 mysql처럼 limit가 없기 때문에 Rownum을 사용해야 한다.
> zerock오라클 코드를 참조하자, rownum은 limit와 다른개념으로 인덱스를 새겨서 사용하는 개념이다. [페이징-rownum사용](https://github.com/kofelo123/SourceRepo/blob/master/SourceRepo/Spring/MVC.md#oraclepaging)
```sql
ex) mysql에서 20개 가져오기
SELECT * FROM 테이블 LIMIT 20;

ex) 오라클에서 20개 가져오기
SELECT * FROM 테이블 WHERE ROWNUM >= 1 AND ROWNUM <= 20;

ex) 오라클에서 시작과 끝사이의 검색된 결과 20개 가져오기
SELECT * FROM 테이블 WHERE ROWNUM >= 1 AND ROWNUM <= 20 AND 컬럼 BETWEEN 시작 AND 끝;


-- ex)
--   select
-- 			pseq, indate, name, price1, price2, useyn, bestyn
-- 		from
-- 			product
-- 		where
-- 			name
-- 		like CONCAT
-- 			('%',CONCAT('%','%'))
--        AND
--     ROWNUM
-- 			>= 1
-- 		AND ROWNUM
-- 			<= 10
-- 		order by
-- 			pseq
-- 		desc   
-- 		;
-- rownum을 orderby 뒤에쓰니까 에러나는거 같다. limit와 위치가 다름을 유의
```
---

### concat

> concat 함수는 **두개의 문자열** 을 연결하는 함수이다. mysql에도 있지만 차이가있다. 문자 추가,
>Mysql에서는

```SQL
title like CONCAT('%', #{cri.keyword}, '%')
```

>이런식으로 3개의 문자열을 합쳐서 사용가능하다.
>그러나 Oracle에서는 기본적으로 두개의 문자열을 합치는 함수이나, 응용해서 3래 문자열을 묶을수 있다.

```SQL
select * from product where name like CONCAT('%','조청','%'); ?--에러
select * from product where name like CONCAT('조청','%');--성공(기본)
select * from product where name like CONCAT('%',CONCAT('조청','%'));--성공(3개문자열묶기)
```

```SQL
select * from product where name like '%'||'조청'||'%'; --이렇게 써도된다.(mysql에서는 이렇게 쓰면 모든 데이터가 출력되었다.(concat써야할듯))
```
//

```SQL
ex)
BD01, BD02, BD03  값들이 저장되어 있는 항목명이 AAA이고
모두 값을 S2BD01, S2BD02,S2BD03 식으로 변경해야 한다면
방법은 UPDATE 뿐이며, 모두 변경한다면 조건식은 따로 없어도 될거 같습니다.
UPDATE TABLE_NAME SET AAA = CONCAT('S2', AAA);
OR
UPDATE TABLE_NAME SET AAA = 'S2'||AAA; //Oracle Only

///
ex)
update 칠 컬럼이 문자형인 가정 아래서(varchar varchar2 ....)

UPDATE 내테이블 SET 컬럼 = 컬럼||'신규값' WHERE ... <==oracle

UPDATE 내테이블 SET 컬럼 = 컬럼 + '신규값' WHERE ... <== mssql

UPDATE 내테이블 SET 컬럼 = CONCAT(컬럼,'신규값') WHERE ... <== mysql
```

```sql
//사용 - 기존의 image칼럼 뒤에 확장자명 붙이기.
update product set image= CONCAT(image,'.jpg') where pseq>1 and pseq <73;
```

---



### PKConstraintRename

**PK생성과 제약조건 이름변경**

Primary Key생성에 있어서

1.테이블생성시에 바로 primary key 라고 선언하는 방법이 있고

2.테이블생성후에 alter table 테이블명 add constraint pk이름(~~제약조건이름~~ ->인덱스이름) primary key(칼럼명) 이런식으로 하는 방법이 있다.

오라클의 흰트를 사용하던중  ~~제약조건 이름~~(인덱스이름)이 필요했는데, 위의 1번방법으로 pk가 이미 만들어졌을떄,

제약조건(및 인덱스이름)의 이름을 따로 추가해야한다고 생각 했으나, 그게 아니라 미지정시 default로 특정값이 들어가 있게된다.

gui에서 볼수있으며, 아마 그것을 보는 쿼리가 있을것이다. 그리고 수정하면된다.

```sql
~~alter table 테이블명 rename constraint PK to PK1;~~
--제약조건이름 변경과 힌트와 상관없음을 후에 알게됨(인덱스이름과 관련있음, 아래)
```


>현재 테스트 해본결과 이름 변경은 되었는데, hint 적용이 안된다. 기존의 default로 정해진값으로만 적용이되는 현상..
>indexname의 변경이 필요한것이다. 제약조건이름과 상관없이 hint는 index이름으로 ..

```sql
alter index 인덱스이름 rename to NEW_INDEX;     

```

---

### tablejoin

두 가지이상의 테이블로 부터 조회해야 할때 - from 절에 두개 이상의 테이블이 나타날때 조인이라고 한다.

(select * from tab) - 모든 테이블,뷰

1. 어떤 테이블을 FROM에 둘까?

2.



---


## modifycolumn

**칼럼의 속성수정하기**
```sql
alter table product modify kind varchar(10);
```

## view

. view 는 table 과 유사하고 table 처럼 사용하지만 table과 달리 data를 저장하기 위한 물리적 공간을 필요로 하지 않는다.
. data 를 물리적이 아닌 논리적 집합을 갖는다.
. table 과 마찬가지로 select , insert, update, delete 가 가능
. view 를 생상하면 select 문장이 dictionnary에 저장된다.
. view 를 조회하면 dictionary에 저장되어 있는 해당 view의 sql 문장을 이용하여 근간이 되는 table을 access한다.


VIEW 만들기
```sql
CREATE  OR  REPLACE VIEW view_name
AS query
[WITH CHECK OPTION]
[WITH READ ONLY];
```
참고로 만들어진 VIEW를 삭제하려면,
DROP VIEW view_name; 라고 하면 됩니다.

뷰를 만들 때 CREATE OR RELPACE VIEW 대신 그냥 CREATE VIEW만 사용해도 됩니다. 그러나 그냥 CREATE VIEW를 통해 만들어진 뷰의 구조를 바꾸려면 뷰를 삭제하고 다시 만들어야 되는 반면, CREATE OR REPLACE VIEW는 새로운 뷰를 만들거나 기존의 뷰를 통해 새로운 구조의 뷰를 만들 수도 있습니다. 그래서 대부분 뷰를 만들 때는 CREATE VIEW 대신 CREATE OR REPLACE VIEW를 사용하는 편입니다.

VIEW에는 VIEW를 생성하는 SELECT 문만 저장됩니다. 즉 실제로 테이블은 존재하지 않으며, VIEW를 SELECT 문으로 검색하는 순간 실제 테이블을 참조하여 보여줍니다.
VIEW의 query문에는 ORDER BY 절을 사용할 수 없습니다.


VIEW를 사용하는 이유
- 보안 관리을 위한 VIEW
   . 보안 등급에 맞추어 컬럼 및 범위를 정하고 privilege 부여
   . 연산 결과만 제공하고 Algorithm 을 숨기기 위해 사용
   . SELECT List 를 Function으로 가공하여 UPDATE, INSERT를 원천적으로 봉쇄
   . Table 의 명칭이나 Column의 명칭을 숨기기 위한 View

- 사용 편의를 위한 View
   . 검색조건의 단순화
   . End User를 위한 Table 명, Column의 한글화
   . join 문장의 단순화를 위한 View

- 수행속도 향상을 위한 View
   . 미리 Tuning 된 SQL문으로 생성한 View
   . 특정한 절차로 수행시키기 위해 View 의 SELECT List에 HINT등을 사용한 View

- 융통성 향상을 위한 View
   . 업무 규칙의 변경이 빈번하여 응용프로그램의 수정이 자주 발생 경우의 해결


- 보안관리를 위한 View
CREATE VIEW v_emp(empno, ename) AS SELECT NVL(empno,NULL), ename FROM emp;
 - > NVL(empno,NULL) 같이 view에 함수를 이용한 가공된 Column은 INSERT, UPDATA 불가

CREATE VIEW v_emp2(empno,ename,annual_sal) AS SELECT empno, ename, (sal + NVL(comm,0)) * 12 annual_sal FROM emp;
 - > 연산을 숨길수 있다.



읽기전용으로 만들기
CREATE VIEW v_emp_read_only(empno, ename) AS SELECT empno, ename FROM emp WITH READ ONLY;

```sql

```
---
## join

## joinex
```sql
cart_view 라는 view가상 테이블에서
3개의 테이블로부터의 정보를 조합해서
주문에 대한 정보를 표기하는 쿼리.(조인)

create or replace view cart_view
as
select o.cseq, o.id, o.pseq, m.name mname, p.name pname,
o.quantity, o.indate, p.price2, o.result
from cart o, member m, product p
where o.id = m.id and o.pseq = p.pseq
and result='1';

cartinsert 할떄 실제로는 몇개의 정보를 카트테이블에 넣어주고
카트뷰(가상테이블)에서 조인으로 여러 테이블에서의 값(가격등)을 가져오는

```
