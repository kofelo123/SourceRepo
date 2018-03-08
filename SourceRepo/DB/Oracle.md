## oracle

- [rownum](#rownum)

- [concat](#concat)
- [PK생성과 제약조건 이름변경](#pkconstraintrename)
- [테이블 조인하기](#tablejoin)
- [칼럼속성 수정](#modifycolumn)
- [view를 쓰는이유](#view)

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
  - [라인,페이지 사이즈](#linesizepagesize)
  - [현재 생성된 계정 확인](#userview)
  - [전체 테이블,뷰,시퀀스 삭제](#entiredelete)
- [SELECT 문장의 패턴](#selectpattern)
- [조인](#join)
  - [오라클조인](#oraclejoin)
    - [EQUI JOIN](#equijoin)
    - [NON EQUI JOIN](#nonequijoin)
    - [OUTER JOIN](#outerjoin)
      - [LEFT OUTER JOIN](#leftouterjoin)
      - [RIGHT OUTER JOIN](#rightouterjoin)
      - [FULL OUTER JOIN](#fullouterjoin)
    - [SELF JOIN](#selfjoin)
  - [ANSI 조인](#ansijoin)
    - [JOIN ON](#joinon)
    - [NATURAL JOIN](#naturaljoin)
    - [JOIN USING](#joinusing)
  - [3집합 이상의 조인](#threewayjoin)
    - [오라클조인에서](#threewayjoinbyoracle)
    - [ANSI조인에서](#threewayjoinbyansi)

  - [조인사용예제](#joinex)
- [maven dependency관련-ojdbc6등록](#ojdbc6)
- [ERD보기](#erd)
- [뷰](#view)

- [Error]
  - [null for parameter](#nullforparameter)
  - [value too large](#valuetoolarge)
  - [방화벽문제](#firewallissue)
  - [접속문제](#connectissue)
  - [오라클 설치에러](#oracleinstallerror)
  - [복수테이블-같은이름칼럼조회](#columnduplicate)
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

col tname for a20; (특정컬럼간격)
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

```
Export table data (yes/no): yes >


Compress extents (yes/no): yes >


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


## nullforparameter

mapper로 전달되는 VO같은 파라미터에 null이 들어가거나 등의 경우..
두번쨰중에 null for parameter #9 with jdbcType OTHER 이라고 되어있는데 저 #9가 mapper에서의 전달되는 9번째 파라미터를 말하는것이다.
```sql
org.springframework.web.util.NestedServletException: Request processing failed; nested exception is org.springframework.jdbc.UncategorizedSQLException: Error setting null for parameter #9 with JdbcType OTHER . Try setting a different JdbcType for this parameter or a different jdbcTypeForNull configuration property. Cause: java.sql.SQLException: 부적합한 열 유형: 1111
; uncategorized SQLException for SQL []; SQL state [99999]; error code [17004]; 부적합한 열 유형: 1111; nested exception is java.sql.SQLException: 부적합한 열 유형: 1111
```
---

## valuetoolarge

들어가야할 칼럼의 속성에 비해 많은 값이 들어갔다.
```sql
                                                              //DB명     //테이블명 //칼럼명   //현재값3  //최대값1
java.sql.SQLException: ORA-12899: value too large for column "JEONGWON"."PRODUCT"."BESTYN" (actual: 3, maximum: 1)
```


## firewallissue

The Network Adapter could not establish the connection
(방화벽문제)

sldeveloper 에서 원격 우분투에 접속하기

이름과 비밀번호 입력.
호스트이름 : aws ip
포트 : 2521(원래는 1521인데 우분투설정을 그렇게 해놨다)
aws - 보안그룹에서 2521 포트를 열어놔야한타(커스텀)

---

## connectissue

//errorMessage
oracle network adapter could not establish the connection

위의 메세지는

1. 접속정보가 잘못됨

2. 방화벽 문제
  - 오라클이 설치된 서버가 방화벽에 있는 경우, 클라이언트 컴퓨터의 IP를 서버 방화벽에 접속허용

3. 오라클과 연동프로그램 (ex: JSP Server)가 같은 머신에 있지 않은 경우

4. DB서버에 리스너 ON(시작됨)이 아닌 경우

--



오라클 재설치를 반복하다보면

포트세팅에서 이미 세팅되었다고 표기될때가 종종 있는데, 그런과정때문이었는지

2521로 최종설정했었던 포트가 1521로 리스너에 세팅이 되어있어서

네트워크 접속이 안되는 현상이 생겼다.
(sqldeveloper-네트워크 접속에러(oracle network adapter could not establish the connection) 메세지, sqlplus에서 원격접속 시도 -ORA-12541: TNS:no listener 메세지)

```
lsnrctl status/stop/start
```
를 통해 리스너에 동작을 줄수 있다.

그로인해 포트가 1521로 설정되어있음을 확인하고 다시 1521로 접속 해보니

ORA-12514, TNS:listener does not currently know of service requested in connect descriptor

위와 같은 리스너 문제를 호소했다.

분명 세팅할떄 2521로 설정했는데 싶어서,  리스너 설정파일인 listener.ora 를 찾았다.
(/u01/app/oracle/product/11.2.0/xe/network/admin/listener.ora)

들어가서 포트를 2521로 수정후 적용을 위해 oracle-xe를 stop->start한후

lsnrctl status/stop/star 로 리스너도 중단후 다시 시도하니 포트설정이 2521로 돌아온후 접속도 성공적으로 되었다.



---
## oracleinstallerror
**오라클 설치에러**

Configuring database...
Database Configuration failed.

오라클 설치 - 마지막 포트세팅할떄
톰캣,mysql등을 켜놔서 메모리가 부족하면 설치가 안된다.



---

## columnduplicate

ORA-00918: column ambiguously defined

- 복수의 테이블에 같은 이름으로 존재하는 칼럼을 조회할떄. -> 어떤 테이블인지 명확히 기술필요.

---
## join

from 절에 두개 이상의 테이블 또는 결과 집합
```
12건의 사원과 4건의 부서정보에 대한 모든경우 출력
- 카르티시안,크로스 조인
select ename, dname from emp,dept

```
조인의 종류에는 오라클조인 / ANSI JOIN(표준) 두가지가 있다.


 ![image](https://drive.google.com/uc?export=view&id=1IaTEEmO5YEwBdCUhPRnwWlZQjGjyR6rC)
## oraclejoin

오라클조인:EQUI JOIN, NON EQUI JOIN, OUTERJOIN(LEFT/RIGHT/FULL),SELF JOIN

## equijoin

조인에 대한 동등조건이 사용된 조인

```sql
SELECT e.ename, d.dname
FROM emp e, dept d
WHERE e.deptno = d.deptno;
```

## nonequijoin

```sql
SELECT e.ename, e.sal, s.grade
FROM emp e, salgrade s
WHERE s.losal <= e.sal and e.sal <= s.hisal;
```

위는 아래와 같다.
```sql
SELECT e.ename, e.sal, s.grade
FROM emp e, salgrade s
WHERE e.sal between s.losal and s.hisal;
```
## outerjoin

조인 조건에 포함되지 않은 레코드는 결과 집합에서 제외된다. 어느 한쪽의 집합의 레코드만 모두 출력하고 싶을떄 사용.
왼쪽은 left,오른쪽은 right, 양쪽집합 모두는 FULL OUTER JOIN

## leftouterjoin
:만약 동등조건이었다면 NULL값이 들어간 데이터에 대해서는 출력이 안되고 누락되는경우가 생긴다.
그럴떄 아래와 같이 조인조건의 반대편(이표현이 중요한듯)에 +를 붙이면 된다.
(조인조건의 반대쪽에 +를 해줌으로써 null인 데이터에 대한 것도 출력한다-left조인 집합 그림의 교집합을뺀 좌측부분)
```sql
SELECT e.ename, d.dname
FROM emp e, dept d
WHERE e.deptno = d.deptno(+);
```
만약 대기중인 사원(null데이터)을 wating으로 출력하고 싶다면 아래와 같이 nvl 함수를 이용하면된다.

```sql
SELECT e.ename, nvl(d.dname,'WAITING') as dname
FROM emp e, dept d
WHERE e.deptno = d.deptno(+);
```

## rightouterjoin
:레프트 조인과 그냥 반대라고 보면된다.
개념은같다.
```sql
SELECT e.ename, d.dname
FROM emp e, dept d
WHERE e.deptno(+) = d.deptno;
```

## fullouterjoin
:오라클 조인에서는 FULLOUTERJOIN을 양쪽에 + 를 주는 방법이 지원되지 않는다.
이를 구현하기 위해서 각각 OUTER조인을 구한후 합집합(UNION)구문을 사용해야 한다.
혹은 ANSI SQL JOIN구문으로 쉽게 해결

## selfjoin

하나의 테이블에서 자기자신의 테이블을 조인해야 할때 사용한다.

대부분의 조인은 FROM절의 테이블 별명은 편리함을 위한 선택사항이지만
SELF JOIN에서는 필수사항이다.(이름이 같아서)
```sql
SELECT e.ename AS mentee, m.ename AS mentor
FROM emp e, emp m
WHERE e.mgr = m.empno;
```
여기서 데이터가 없는쪽을 출력하고싶을때 LEFT,RIGHT JOIN처럼 (+) 처리해주면된다.
```sql
SELECT e.last_name 사원, nvl(o.last_name,'NONE') 상사
FROM employees e,employees o
WHERE e.manager_id = o.employee_id(+);
```


## ansijoin
ANSI조인
1. JOIN ON
2. OUTER JOIN
3. NATURAL JOIN
4. JOIN USING
5. CROSS JOIN

## joinon

JOIN ON 하나로 모든 조인결과 집합을 만들어 낼 수 있다.
기존의 오라클쿼리에서 JOIN ON으로 바꿔본다.

기존의 ORACLE JOIN 의 [EQUI JOIN]
```sql
SELECT e.ename, d.dname
FROM emp e, dept d
WHERE e.deptno = d.deptno;
```
에서 FROM 절의 콤마대신 JOIN을, WHERE 대신  ON을 사용
```sql
SELECT e.ename, d.dname
FROM emp e JOIN dept d
ON e.deptno = d.deptno;
(이떄 JOIN을 INNER JOIN 이라고 기술해도 같은 결과가 출력됨.)
```

기존의 ORACLE JOIN 의 [NON EQUI JOIN]
```sql
SELECT e.ename, e.sal, s.grade
FROM emp e, salgrade s
WHERE e.sal between s.losal and s.hisal;
```
JOIN ON 으로 변경
```sql
SELECT e.ename, e.sal, s.grade
FROM emp e JOIN salgrade s
ON e.sal between s.losal and s.hisal;
```

기존의 ORACLE JOIN 의 [LEFT OUTER JOIN]
```sql
SELECT e.ename, nvl(d.dname, 'WAITING') as dname
FROM emp e, dept d
WHERE e.deptno = d.deptno(+);
```
JOIN 조건의 반대편에 (+)를 붙이는 대신 명시적으로 LEFT OUTER JOIN라고 적어준다.
```sql
SELECT e.ename, nvl(d.dname, 'WATING') as dname
FROM emp e LEFT OUTER JOIN dept d
ON e.deptno = d.deptno;
```
기존의 ORACLE JOIN 의 [RIGHT OUTER JOIN]
```sql
SELECT e.ename, d.dname
FROM emp e, dept d
WHERE e.deptno(+) = d.deptno;
```
에서
```sql
SELECT e.ename, d.dname
FROM emp e RIGHT OUTER JOIN dept d
ON e.deptno = d.deptno;
```
[FULL OUTER JOIN]
:오라클 조인에서는 문법상으로 지원되지 않는 FULL OUTER JOIN 도 쉽게 구현가능
```sql
//아래와 같이 FULL OUTER JOIN 기술만으로 구현가능
SELECT e.ename, d.dname
FROM emp e FULL OUTER JOIN dept d
ON e.deptno = d.deptno;
```

기존의 ORACLE JOIN 의 [SELF JOIN]
```sql
SELECT e.ename mentee, m.ename mentor
FROM emp e, emp m
WHERE e.mgr = m.empno(+);
```
+대신에 명시적으로 LEFT OUTER라고 기술하면된다.
```sql
SELECT e.ename AS mentee, m.ename AS mentor
FROM emp e LEFT OUTER JOIN emp m
ON e.mgr = m.empno;
```
(이렇게 ANSI 조인은 JOIN ON 하나만으로도 모든 형태의 JOIN 을 할 수 있다. 익숙해져야 한다)

## naturaljoin
:무조건 사용하지 않는게 좋다.

조인조건없이 자동으로 양쪽 테이블 집합의 이름이 같은 칼럼끼리 EQUI JOIN처리를 해주는건데,
만약 이름은 같으나 의미가 다른 칼럼이 있을떄 데 완전히 잘못된 결과를 가져올수 있으므로 사용 지양하는게 좋다.
```sql
SELECT ename,dname
FROM emp NATURAL JOIN dept;
```

## joinusing
위의 NATURAL JOIN이 자동으로 결정되는 조인 조건때문에 신뢰가 어려운데,
써야한다면  명시적으로 조인 컬럼을 지정할수 있는 JOIN USING 을 써서 USING에 조인 조건으로 사용할 칼럼을 지정해주되 괄호로 감싸서 쓴다.
```sql
SELECT ename, dname
FROM emp JOIN dept
USING (deptno);
```

## threewayjoin

JOIN 하는 집합의 개수가 3개 이상인 JOIN을 Three Way JOIN 이라고 부른다.
기본적으로 집합의 개수가 몇 개든 2개의 테이블을 JOIN 하는것과 큰차이는 없다.


## threewayjoinbyoracle

오라클에서의 조인
[Oracle Three way JOIN]
결과 집합을 하나씩 확인하면서 하나씩 집합을 JOIN해 나가는것이 실수를 하지 않는 방법이다.

사원이름(emp.ename), 급여(emp.sal), 급여등급(salgrade.grade), 부서이름(dept.dname)을 출력해본다.

우선 emp 테이블과 salgrade 테이블을 조인한다.
```sql
SELECT e.ename, e.sal, s.grade
FROM emp e, salgrade s
WHERE e.sal between s.losal and s.hisal;
```
이 결과집합과 다시 DEPT 집합을 JOIN한다고 생각하면 간단하다.
```sql
//(추가적인 조인에 대한것들을 추가해준다)
SELECT e.ename, e.sal, s.grade,d.dname -- d.dname 컬럼 추가
FROM emp e, salgrade s,dept d --DEPT 테이블 JOIN
WHERE e.sal between s.losal and s.hisal
and e.deptno = d.deptno(+); --DEPT 테이블 JOIN 조건(+를 통해서 LEFT OUTER JOIN)
```
```sql
ex2)
SELECT e.last_name 이름,e.salary 급여,j.grade_level 급여등급,d.department_name 부서이름, l.city 도시, c.country_name 나라
FROM employees e, job_grades j, departments d,locations l, countries c
WHERE e.salary between j.lowest_sal and j.highest_sal
and e.department_id = d.department_id(+)
and d.location_id = l.location_id(+)
and l.country_id = c.country_id(+);
```

[ANSI Three Way JOIN]
:사원이름(emp.ename), 급여(emp.sal), 급여등급(salgrade.grade), 부서이름(dept.dname)을 출력해본다.
```sql
(단계별로 접근할것- 우선 emp테이블과 salgrade 테이블을 join)
SELECT e.ename, e.sal, s.grade
FROM emp e JOIN salgrade s
ON e.sal between s.losal and s.hisal;
```
오라클 조인은 FROM 절 옆에 집합 이름을 추가하는 형식이지만
ANSI 조인은 결과 집합과 JOIN 한다는 것을 잘 나타내주는 구문 형태이다.
그리고 대기발령중인 사원도 출력하기 위해 LEFT OUTER를 JOIN 왼쪽에 기술한다.
```sql
SELECT e.ename, e.sal, s.grade ,d.dname -- d.dname 컬럼 추가
FROM emp e JOIN salgrade s
ON e.sal between s.losal and s.hisal
LEFT OUTER JOIN dept d  -- DEPT 테이블 JOIN , LEFT OUTER 추가
ON e.deptno = d.deptno; -- DEPT 테이블 JOIN 조건
```

```sql
ex2)
SELECT e.last_name 이름,e.salary 급여, j.grade_level 급여등급, d.department_name 부서명,l.city 도시,c.country_name 국가
FROM employees e JOIN job_grades j
ON e.salary between j.lowest_sal and j.highest_sal
LEFT OUTER JOIN departments d
ON e.department_id = d.department_id
LEFT OUTER JOIN locations l
ON d.location_id = l.location_id
LEFT OUTER JOIN countries c
ON l.country_id = c.country_id;
```
---


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
## ojdbc6

oracle의 경우 저작권문제가 공식적으로 maven repository에 등록되지 않는다.
oracle14버전만 지원되는데 jdk1.6이상의 경우 적합 x
jdbc의 로그를 기록하지 못한다.

JDBC드라이버를 사용하는 방법에는 크게 다음과 같은 방법을 고려할 수 있습니다.

1. 복잡하지만 Maven에 JDBC드라이버를 추가해서 사용하는 방법
2. STS상의 프로젝트에 라이브러리를 추가해서 사용하는 방법
3. 클래스 패스를 이용해서 JDBC드라이버를 설정하고 사용하는 방법

1번의 경우 Maven 프로그램이 설치되어야 하기 때문에 복잡하므로,  초급자에게는 권장하고 싶지는 않습니다.

3번의 경우는 시스템 전체에 영향을 주기 때문에, 여러 프로젝트를 진행해야 하는 개발자들에게는 유용하지 않습니다.

이 블로그에서는 2번을 기준으로 설명하도록 합니다.

sqldeveloper가 깔려있다면
폴더안에 ojdbc6.jar가 이미 들어있다.

-> dependency에 ojdbc6 정보넣어주면
<dependency>
		    <groupId>oracle</groupId>
		    <artifactId>ojdbc6</artifactId>
		    <version>11.2.0.3</version>
</dependency>

빨간줄 나면서 apache-maven 디렉터리의 oracle repository쪽에 jar파일이 없다고
하는데 그쪽(maven의 repository)에다가 jar파일을 버전에 맞게 이름맞춰서 넣어주고 maven-update project해주면된다.




---
## selectpattern

SELECT 문장의 해석/작성 권장순서

4.SELECT
1.FROM
2.WHERE
3.GROUP BY: 데이터를 더 작은 그룹으로 나눌 떄 사용(대개 복수행 함수(그룹함수 또는 집계 함수라고 부르기도함),WHERE는 GROUP BY에 적용된다.(where에서 걸러진후 groupby적용) ~'별'로 나눈다. 해석
5.HAVING : GROUP BY 절의 결과를 필터링 할 때 사용
6.ORDER BY :결과 데이터의 정렬


SELECT 문장의 패턴

1.SELECT FROM
2.SELECT FROM WHERE
3.SELECT FROM GROUP BY
4.SELECT FROM  WHERE GROUP BY
5.SELECT FROM GROUP BY HAVING
6.SELECT FROM  WHERE GROUP BY HAVING

위의 모든패턴에 order by 를 사용할 수 있다.


Q.10% 인상된 급여 쿼리하기
```sql
SELECT empno, ename, sal, sal*1.1
FROM emp;
```
Q.NULL 값이 포함/미포함 된 행을 찾으세요
```sql
SELECT empno, ename, comm
FROM emp
WHERE comm is null; (is not null)
```
Q.오름차순과 null
오름차순정렬의 경우 (asc) null값이 마지막에 나타난다.
오름차순인데 null 값이 먼저 나오게 하도록 쿼리할 수 있다.
```sql
SELECT empno, ename ,sal ,comm, deptno
FROM emp
ORDER BY comm nulls first;
```
반대로 내림차순은 null 값이 먼저 나오지만, 마지막에 나오게 할수 있다.
```sql
SELECT empno ,ename ,sal comm, deptno
FROM emp
ORDER BY comm desc nulls last;
```
Q.select절에 없는 컬럼으로 정렬 가능
```sql
SELECT empno, ename, job
FROM emp
ORDER BY sal desc;
```
Q.중복제거
```sql
SELECT distinct job
FROM emp;

SELECT unique job
FROM emp;
```
Q 리터럴
```sql
SELECT ename , ' 사원의 업무는 ', job ,'입니다'
FROM emp;
```
이런식으로 실제 데이터는 아니지만 문자를 넣는것..
문자 리터럴은 작은 따옴표로 감싸야 한다.

각각 ename, '사원의 업무는',job,'입니다' 가 끊어서 나오지만
보기좋은 결과를 위해 연결 연산자 || 를사용할 수 있다.
```sql
SELECT ename||' 사원의 업무는 '||job||'입니다.'
FROM emp;
//위또한 alias 할수있다.
```

2.

Q.IN 연산자(포함여부)
```sql
WHERE department_id in (20,50,80);
//셋중에 포함되는것이 있는 데이터를 출력
```
```sql
Q. TO_CHAR(SYSDATE,'YYYY-MM-DD')
-----------
2015-03-16
```
이런식으로 날짜와 문자화 해주는 그런 함수들이 있다는것..

3.SELECT,FROM,GROUP BY

GROUP BY 절은 데이터를 더 작은 그룹으로 나누어 연산할 떄 사용한다.
그룹별 평균,최대,최소 등을 구하기 위해 복수행 함수(그룹함수 또는 집계함수라고 해도됨)와 함께 사용한다.
(집계연산 함수 쓸때 반드시 group by 함께 써야 오류안난다.(무엇을 기준으로 연산할지에 대한것이라서)
GROUP BY  와 복수행 함수를 사용하면 데이터 더미의 의미를 다양하게 파악할 수 있다.
```sql
SELECT deptno, count(*) # (3) 부서별로 count
FROM emp		# (1) 사원 테이블의 데이터(행)를
GROUP BY deptno	# (2) 부서 번호 같은 데이터(행)끼리 모아
ORDER BY deptno;	# (4) 결과를 정렬
```
ex)
Q 부서별, 업무별 사원수, 급여합,급여 평균 구하라
```sql
SELECT deptno, job, count(*), sum(sal),avg(sal)
FROM emp
GROUP BY deptno, job
ORDER BY 1, 2;
```
Q.SELECT 절을 작성할때 한 줄에 하나의 칼럼을 나열하는 것은 자주 권장되는 작성 방법이다.
```sql
SELECT deptno,
	job,
	max(hiredate),
	min(hiredate)
FROM emp
GROUP BY deptno, job
ORDER BY 1,2;
```
4.SELECT FROM WHERE GROUP BY

순서는 WHERE 에서 거른후 GROUP BY 에서 그룹'별'로 분류

5.SELECT FROM GROUP BY HAVING

순서: FROM - GROUPBY SELECT HAVING 순서로 해석
(HAVING은 SELECT 다음에 해석)
```sql
SELECT deptno, count(*) # (3) 인원수를 헤어린 뒤
FROM emp #(1) 사원 테이블에서
GROUP BY deptno #(2) 같은 부서에 근무하는 사원끼리 헤쳐모아
HAVING count(*) >= 5 #(4) 인원수가 5명 이상인 결과를 남긴다
ORDER BY count(*); #(5) 정렬수행
```
컬럼의 별명(alias)는 GROUP BY 및 HAVING 절에 사용할 수 없다.

6.SELECT, FROM, WHERE, GROUP BY, HAVING

FROM > WHERE > GROUP BY > SELECT > HAVING 순으로 작성 및 해석.

Q. WHERE 절과 HAVING 절을 비교 설명
기능이 비슷해보여서 고민할 수 있다.

필터링할 조건을 WHERE절에 적용해보고 문법에 오류가 있으면 HAVING에 조건을 포현하면 된다.
문법 규칙중에 WHERE 절에는 집계 함수(복수행함수)를 사용할 수 없다는 것을 기억하고있으면 도움이 된다.

2가지 문제
1.조건을 WHERE 절과  HAVING절 어디에든 사용할 수 있는 경우
2.조건을 반드시 HAVING 절에만 사용 할 수 있는 경우.

1.의경우 WHERE을 쓰는게 좀더 효율적이다.
왜냐하면, 테이블에서 조건에 맞는 것만 남기고 나은 결과로 집단화하고 연산을 수행한다.
HAVING을 쓸경우 전체 데이터를 집단화하고 연산을 수행한후 그 결과에서 뒤늦게 필터링 처리하기 때문에 결과에 포함시키지 않는 데이터까지 집단화시키고 연산하는 작업을 하게 되므로 비효율적이다.

---
- [집합연산자](#setoperator)

## setOperator


수학에서의 합집합(UNION 또는 UNION ALL),교집합(INTERSECT),차집합(MINUS)

@ 합집합 (UNION, UNION ALL)
```sql
SELECT n FROM 집합A
UNION
SELECT n FROM 집합B;
````
개별 SELECT 문의 컬럼 이름이 다르더라도 상관없다.
집합 연산은 SELECT 하는 컬럼의 개수와 데이터 타입의 순서만 일치하면 수행된다. 단, 컬럼의 이름이 다른 경우, 출력 결과의 컬럼 이름은 첫 번째 SELECT 문의 컬럼 이름으로 출력된다.


UNION 과 UNION ALL 의 차이점은
UNION ALL은
1.중복된 레코드를 제거하지 않는다,
2.정렬하지 않은채로 출력.(UNION ALL 외의 모든집합은 정렬된 상태로 출력된다. 정렬은 많은 시스템 자원을 소모하므로 중복 레코드가 없다는 확신이 서거나, 결과가 중복되어서 무방할때는 UNION ALL 을 사용하는게 좋다.)

@ 그 외의 규칙

1. 서로 다른 테이블에 대한 집합연산을 적용해도 무방하다
출력하는 컬럼의 개수와 데이터 타입만 일치하면 어떤 집합이라도 무방.
```sql
SELECT last_name FROM employees
UNION
SELECT ename FROM emp;
```
2. 컬럼의 개수 및 SELECT 순서가 일치해야 한다.

3.집합 연산에서 ORDER BY 절은 마지막에 한 번만 기술한다.
```sql
SELECT * FROM 집합A ORDER BY 1 desc //에러남
UNION ALL
SELECT * FROM 집합B;
```
```sql
SELECT *
FROM (SELECT * FROM 집합A ORDER BY 1 desc) -- 인라인 뷰를 사용하면 된다.
UNION ALL
SELECT * FROM 집합B;
```
@ 두 쿼리문의 쿼리 항목 개수나 데이터 타입이 다름에도 불구하고 집합연산을 해야할때.

Q.EMP테이블과 DEPT 테이블을 UNION ALL 하여 ename,deptno,dname을 출력하시오.
(emp테이블은 dname이 없고 dept테이블은 ename컬럼이 없다)

-> 없는컬럼을 null로 비우고 UNION ALL 한다.
```sql
SELECT ename,deptno,null FROM emp
UNION ALL
SELECT null,deptno,dname FROM dept;
```

# 교집합

집합 A 와 집합 B 의 교집합은 INTERSECT를 이용하여 구한다.
```sql
SELECT n FROM 집합A
INTERSECT
SELECT n FROM 집합B;
```
# 차집합

MINUS 를 이용한다.
```sql
SELECT n FROM 집합A
MINUS
SELCT n FROM 집합B;
```


---

## erd

ERD보기
파일 -> Data Modeler -> 임포트 -> 데이터 딕셔너리
 ![](https://drive.google.com/uc?export=view&id=19tPqEVW116gm4mr0VWhHsE42qE9da4Ag)

---



 ## view

 뷰는 하나 이상의 테이블이나 다른뷰를 이용하여 생성되는 가상테이블을 말한다.
 테이블은 디스크에 공간이 할당되어 데이터를 저장하고 있지만 뷰는 디스크 저장 공간이 할당되지 않는다.


 @뷰의 종류

 단순뷰:하나의 기본 테이블로 생성한 뷰이며 DML 명령문을 실행할 수 있다. DML명령문의 처리 결과는 기본테이블에 반영된다.

 복합뷰: 두 개 이상의 기본 테이블로 생성한 뷰이다.
 무결성 제약조건, 표현식, GROUP BY 절의 유무에 따라 DML 명령문을 제한적으로 사용한다.
 복합뷰는 DISTINCT,그룹함수, GROUP BY, ROWNUM을  포함시킬수 없다.

 @뷰 사용이유
 :뷰를 사용하는 이유는 보안과 사용의 편의성 때문이다.
 전체 데이터가 아닌 일부만 접근 할 수 있도록 뷰를 정의하면 일반 사용자에게 해당 뷰만 접근 가능하도록
 허용하여 중요한 데이터가 외부에 공개되는 것을 막을 수 있다.

 @ OR REPLACE
 :존재 하는 뷰에 대해서 그 내용을 새롭게 변경하여 재 생성한다. 뷰를 정의할떄 일반적으로 CREATE VIEW 보다 CREATE OR REPLACE VIEW 를 사용하여 융통성 있게 뷰를 생성한다.

 @장점
 1.DB의 선택적인 부분만 보여주므로 접근을 제한
 2.다양한 접근 경로 설정
 3.복잡한 질의를 단순화
 4.데이터 독립성 제공
 5.동일한 데이터를 또 다른 뷰로 표현
 6.한개의 뷰에 여러 테이블의 데이터를 검색 가능
 7.한 개의 테이블로부터 여러 뷰를 생성가능

 @단점
 - 뷰의 정의를 변경할 수 없고 isnert,delete,update에 많은 제한이 있음

 [참조](http://sjs0270.tistory.com/54)
