## oracle

- [rownum](#rownum)
- [concat](#concat)
- [PK생성과 제약조건 이름변경](#pkconstraintrename)
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

> concat 함수는 **두개의 문자열** 을 연결하는 함수이다. mysql에도 있지만 차이가있다.
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
---



### PKConstraintRename

**PK생성과 제약조건 이름변경**

Primary Key생성에 있어서

1.테이블생성시에 바로 primary key 라고 선언하는 방법이 있고

2.테이블생성후에 alter table 테이블명 add constraint pk이름(제약조건이름) primary key(칼럼명) 이런식으로 하는 방법이 있다.

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
