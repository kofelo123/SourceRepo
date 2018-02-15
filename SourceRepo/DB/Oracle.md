## oracle

- [rownum](#rownum)

- [concat](#concat)
- [PK생성과 제약조건 이름변경](#pkconstraintrename)
- [테이블 조인하기](#tablejoin)
- [칼럼속성 수정](#modifycolumn)
- [view를 쓰는이유](#view)
- [조인](#join)
  - [조인사용예제](#joinex)
- [rownum과 orderby 사용-서브쿼리](#rownumorderbysubquery)
- [다중정렬](#multisort)
- [오라클 포트변경](#modifyport)
- [기본 계정](#defaultuser)
- [권한](#privilege)
 - [유저생성](#createUser)
 - [테이블생성권한](#tableprivilege)
- [문자인코딩](#charencoding)
- [sqlplus에서 원격의 db에 접속하는방법](#remotesqlplus)
- [롤을 사용한 권한부여](#role)
- [오라클 덤프](#oracledump)
- [기타 유용한 쿼리]
  - [라인,페이지 사이즈](#linesizePagesize)
  - [현재 생성된 계정 확인](#userview)
  - [전체 테이블,뷰,시퀀스 삭제](#entiredelete)
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

---

## rownumOrderbySubquery

>order by 절과 rownum을 함께 쓸때 아래와 같이 한번에 쿼리를 쓰면 order by 정렬 전에 rownum으로 잘려버려 원하는 데이터가 나오지 않는다.

```sql
select *
from order_view
where mname like '%'||#{key}||'%' and rownum < 30 order by result,o seq desc
```

> 서브쿼리를 써서 아래와 같이 변경시킨다.
```sql
select *
from
(
  select *
  from order_view
  order by result, oseq desc
)
where rownum < 50;
// 1차적으로 정렬을 한 데이터 리스트로부터 rownum 으로 갯수제한을 하는 서브쿼리 형태로 만든다.
```
---

## multiSort

```sql
select * from member where name like '%'||#{key}||'%' order by useyn desc,indate desc
```
order by에 다중정렬 하고싶으면
desc 속성, asc 속성 이런식으로 하면 앞에꺼부터 우선순위로 다중정렬 적용된다.
근데 만약에 useyn,indate desc 이렇게 하면 useyn이 asc로 정렬된다(아마 asc는생략이라서 그런듯)

---

## modifyPort

```sql
1. sql plus 실행
> sqlplus /noglog

2. 관리자로 연결
SQL> conn /as sysdba
//ERROR:
ORA-01031: insufficient privileges
//위의 에러가 나게됨 그래서
// connect sys/oracle as sysdba( 이런식으로 비밀번호 적어줘야함)
3. 현재 포트확인
SQL> select dbms_xdb.gethttpport() from dual;

4. 포트변경 내장프로시져 실행
SQL> exec dbms_xdb.sethttpport(9090);

```

---
## defaultuser

오라클 생성시 기본적으로 제공되는 계정이 있다.
-시스템 권한을 가진 사용자인 DBA용 계정(SYS,SYSTEM)
- 교육용 계정(HR)  두 가지로 나뉜다.

SYS:오라클 Super 사용자 계정, DB 발생 모든 문제 처리할수 있는 권한을 가짐.

SYSTEM: 유지보수 관리시 사용 계정, SYS 사용자와의 차이는 DB 생성할 수 있는 권한이 없으면 불완전 복구를 할수없다(뭔소린지..)

-sqlplus sys/oracle (x)
-> sqlplus sys as sysdba  (이렇게 접근해야댐 sys의경우만) / sqlplus에서는 connect sys/oracle as sysdba


> 참고로 노트북 로컬의 system/ 기본비밀번호 oracle이더라.

---

## privilege

사용자 권한에는 시스템권한과 객체권한이 있다.

대표적 시스템권한

대표적인 시스템권한
- CREATE SESSION: 데이터베이스 연결 권한
- CREATE TABLE: 테이블 생성 권한
- CREATE SEQUANCE: 시퀀스 생성권한
-CREATE VIEW : 뷰 생성권한

객체권한:객체별로 사용할 수 있는 권한
-TABLE:alter,delete,index,insert,reffences,select,update
-VIEW: delete, insert, select,update
SEQUENCE: alter ,select
-PROCEDURE: execute

## createUser

유저 생성하기

CREATE USER user_name IDENTIFIED BY password;

사용자 생성위해서는 CREATE USER 권한을 갖는 사용자인 SYS 또는 SYSTEM으로 로그인 해야한다.

conn system/oracle;

create user usertest01 identified by pass1;

conn usertest01/pass1;
// lacks create session privilege; logon denied
//생성된 사용자에게 DB 접속 권한이 부여되지 않아서 접속이 거부된다.
CREATE SESSION 권한을 부여 받아야 한다.

conn system/oracle;

grant create session to usertest01;

//(사실은 어짜피 create user 이후에 권한을 일일이 부여하기보다 role의 connect,resource를 쓴다.)
```
create user jwprac identified by tiger;

grant connect,resource to jwprac;
```
-
## tableprivilege

테이블 생성권한
```sql
create table ~

//insufficient privileges

grant create table to usertest01;
```
다시 테이블 생성
```sql
no privilege on tablespace 'SYSTEM'
```
// 테이블 스페이스는 디스크공간을 소비하는 테이블과 뷰, 그리고 그 밖의 다른 데이터베이스 객체들이 저장되는 장소.

디폴트 테이블스페이스는  SYSTEM인데
SYSTEM에 대한 QUOTA를 설정해주지 않았기 때문에  그렇다.
QUOTA 절로 사용자가 사용할 테이블 스페이스의 영역을 할당해야한다.
```sql
conn ssytem/oracle;
alter user usertest01 quota 2m on SYSTEM;

SELECT USERNAME, USER_ID, DEFAULT_TABLESPACE, CREATED FROM DBA_USERS;
```

## linesizePagesize
```sql
set linesize 130;
set pagesize 10;
```

**권한조회**

권한조회 - (권한조회도 그 조회할수 있는 권한이 있어야한다.)
```sql
//ORACLE에서 모든 사용자의 권한
SELECT * FROM DBA_ROLE_PRIVS

//현재 세션에 부여된 권한
SELECT * FROM USER_SYS_PRIVS

//DBA에 부여된 권한
SELECT * FROM DBA_SYS_PRIVS

//특정 ROLE에 부여된 시스템 권한
SELECT * FROM ROLE_SYS_PRIVS

//특정 테이블에 부여된 권한
SELECT * FROM ROLE_TAB_PRIVS

//사용자에 의해 엑세스 가능한 ROLE
SELECT * FROM USER_ROLE_PRIVS

//사용자가 부여한 객체 권한
SELECT * FROM USER_TAB_PRIVS_MADE

//사용자가 객체의 열에 대해 부여한 객제 권한
SELECT * FROM USER_COL_PRIVS_MADE

//특정 열에 대해 사용자가 부여한 객체권한
SELECT * FROM USER_COL_PRIVS_RECD

//해당 유저의 LIMIT 권한
SELECT P.PROFILE, P.RESOURCE_NAME, P.LIMIT
FROM DBA_USER U, DBA_PROFILES P
WHERE P.PROFILE = U.PROFILE
AND USERNAME = 'USER_NAME'
```

## charEncoding

오라클은 기본적으로 utf8이 설정되어 있고 따로 변경없이 그냥 쓰는것 같다.

```sql
CHARSET 확인 쿼리문 :
select parameter, value from nls_database_parameters where parameter = 'NLS_CHARACTERSET'

CHARSET 변경

SYSTEM 계정으로 connect 한다.

update sys.props$ set value$='KO16MSWIN949' where name='NLS_CHARACTERSET';  
update sys.props$ set value$='KO16MSWIN949' where  name='NLS_NCHAR_CHARACTERSET';
update sys.props$ set value$='KOREAN_KOREA.KO16MSWIN949' where name='NLS_LANGUAGE';
commit;

SHUTDOWN IMMEDIATE;
STARTUP MOUNT;
ALTER SYSTEM ENABLE RESTRICTED SESSION;
ALTER SYSTEM SET JOB_QUEUE_PROCESSES=0;
ALTER SYSTEM SET AQ_TM_PROCESSES=0;
ALTER DATABASE OPEN;
ALTER DATABASE CHARACTER SET INTERNAL_USE KO16MSWIN949;
SHUTDOWN IMMEDIATE;
STARTUP;

```

## remoteSqlplus

sqlplus에서  원격의 db에 접속하는방법

sqlplus id/pw@ip:port/sid

```
conn momstouch/gj02(~+pw)@13.125.97.52:2521/xe
```

---

## role

롤은 관련된 권한의 묶음이다.

사용자의 번거로움을 덜어준다.

사전 정의된 롤과 사용자 정의한 롤로 구분된다.

- DBA : WITH ADMIN OPTION에 있는 모든 권한
- CONNECT : ALTER SESSION, CREATE CLUSTER, CREATE DATABASE LINK, CREATE SEQUENCE, CREATE SESSION, CREATE SYNONYM, CREATE TABLE, CREATE VIEW (여기서 create view 는 오라클 10g이후에 빠진것으로 보인다. 따로 권한 줘야될것..)
- RESOURCE : CREATE CLUSTER , CREATE PROCEDURE, CREATE SEQUENCE, CREATE TABLE, CREATE TRIGGER

DBA롤은 시스템 자원을 무제한적으로 사용하며 시스템 관리에 필요한 모든 권한을 부여 할 수 있는 강력한 권한을 보유한 롤이다.
 DB객체관리, 사용자들을 작성,변경,제거 관련된 모든권한을 갖는다.

CONNECT 롤은 사용자가 데이터베이스에 접속 가능하도록 가장 기본적인 시스템 권한 8가지를 그룹화 한것.

RESOURCE 롤은 사용자가 객체(테이블,뷰,인덱스)를 생성할 수 있도록 하기 위해서 시스템 권한을 그룹화 한것.

>'create view 는 따로 권한 줘야할듯.'
---

## oracledump



오라클 덤프- sqldeveloper에서 하는방법과, cmd에서 하는방법 두가지 시도해봤다.

**sqldeveloper에서 dump하기**

SQL Developer 메뉴에서 도구(T) -> 데이터베이스 익스포트(X)... 를 선택하여 익스포트 마법사를 실행

데이터베이스를 옮길때 고려해야 할 사항이 있다.

- 스키마 표시(S) : 스크립트에 현재 스키마가 포함되어 생성됩니다. 옮기려고 하는 데이터베이스에 스키마(아이디) 가 다르다면 체크를 해제 하면 되겠습니다.


- 저장 영역(G) : 스크립트에 테이블스페이스와 storage 관련 구문들이 포함되어 집니다. 옮기려는 데이터베이스의 테이블스페이스가 다르다면 생성 후 수정을 하거나, 체크를 해제하고 생성해서 테이블스페이스 부분은 수동으로 추가해서 사용해야 겠습니다. 만약 옮기려는 데이터베이스 버전이 현재 데이터베이스와 다르다면(11g 에서 10g 로 옮기는 등) storage 지정 구문이 달라서 import 시 문법 오류가 발생할 수 있습니다. 이런 상황이 발생한다면 스크립트 생성후에 직접 수정하여 사용하여야 겠습니다.

>'기타 몇몇 옵션들이 있는데 기본설정으로 두고 해도 무관하다. 인코딩의 경우 MS929가 기본으로 되어있어서 UTF8로 변경- 시도해보았더니
오히려 데이터가 꺠지게 나왔고, 기본으로 두면 잘되는거 같다.'

그뒤로 쭉 넘기다 보면(기억해야될 세팅 별로없음) 지정한 디렉터리에 sql파일이 생긴다.

import과정은 워크시트에서 "@스크립트 파일명" 을 실행해서 할 수도 있고, 익스포트 파일내용을 워크시트로 복사 붙여넣어서 실행을 해도 된다.
```
@C:\Users\허정원\익스포트.sql;
//새로운 유저를 만들고 import하기전에 role부여(connect,resource)+grant create view 정도 권한을 주도록 한다.
```

[sqldeveloper-dump](http://pentode.tistory.com/216)

**cmd에서 dump하기**


exp test1/test1 file=expdat.dmp full=y;  <--이것은 로컬에 있는 db백업 //이것사용

exp test1/test1@SID file=expdat.dmp full=y;  <--로컬이 아닌곳 즉 실서버가 따로 존재하는곳..

>'cmd에서 실행하는데 아래와 같은 메시지가 뜨는데 default로 하려면 그냥 엔터치면된다.'

```

(2)U(sers), or (3)T(ables): (2)U >


Export grants (yes/no): yes >


Export table data (yes/no): yes >


Compress extents (yes/no): yes >

```

dump가 떠지고 import하는 과정
```

* import
-전체 데이터베이스가 IMPORT됩니다. (Full Level)
C:>imp userid=system/manager file='C:full.dmp'  full=y

- scott의 유저 IMPORT를 실행 합니다.(User Level)
C:>imp userid=scott/tiger file='C:scott.dmp'

- 다른 계정으로 IMPORT하기
  scott유저의 데이터를 EXPORT받아서 test 유저에게 IMPORT하는 예제 입니다.
C:>exp userid=system/manager file='C:scott.dmp' owner=scott
C:>imp userid=system/manager file='C:scott.dmp' fromuser=scott touser=test   //이것사용
```

ex)imp userid=system/gj02(~pw)@13.125.91.243 file='C:\Users\허정원\0214.dmp' fromuser=jeongwon touser=momstouch
(로컬의 cmd에서)

>나 같은경우 local db의 a라는 유저로 부터 우분투서버의 b라는 유저에 import했기 때문에 '다른 계정으로 IMPORT하기를 사용했다. 이름이 다를때는 저렇게 해야한다고 한다.'



>(참고-dump와 무관)sqlplus system/gj023...(pw)@13.125...(ip):2521/xe
(들어가서 sqlplus에서 conn 시도할때도 뒤에 @아이피:포트/sid 붙여줘야됨.)

---

## userview

현재 생성된 계정 확인
```sql
SELECT * From DBA_USERS;

SELECT * FROM ALL_USERS;
```

## entiredelete

```sql

//전체테이블 삭제하는 쿼리문 만드는 쿼리
SELECT  'DROP TABLE ' || object_name || ' CASCADE CONSTRAINTS;'
  FROM    user_objects
WHERE   object_type = 'TABLE';

//전체뷰 삭제하는 쿼리문 만드는 쿼리
SELECT  'DROP VIEW ' || object_name ||';'
  FROM    user_objects
WHERE   object_type = 'VIEW';

//전체 시퀀스 삭제
SELECT  'DROP sequence ' || object_name ||';'
  FROM    user_objects
WHERE   object_type = 'SEQUENCE';

```
