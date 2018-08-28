## oracle

- [rownum](#rownum)

- [concat](#concat)
- [PK생성과 제약조건 이름변경](#pkconstraintrename)
- [칼럼속성 수정](#modifycolumn)
- [테이블 이름 변경](#180826_3)
- [테이블 주석 설정](#180826_4)

- [rownum과 orderby 사용-서브쿼리](#rownumorderbysubquery)
- [다중정렬](#multisort)
- [정렬의 null순서 임의변경](#180826_5)
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


- [단일행 함수](#180828_1)
- [DECODE](#180828_2)
- [GREATEST](#180828_3)
- [DUAL](#180828_4)
- [VIZE - 바이트길이반환 , USER -현재유저 확인](#180828_5)
- [NVL,NVL2,COALESCE,NULLIF](#180828_6)
- [숫자함수](#180828_7)
- [문자함수](#180828_8)
- [숫자로만 되어있는 데이터만 반환하기](#180828_9)
- [단어가 n개 이상인 이상인 행 찾기](#180828_10)
- [날짜(시간)함수](#180828_11)
- [변환 함수](#180828_12)

- [조인사용예제](#joinex)
- [집합연산자](#setoperator)
- [서브쿼리](#subquery)
- [maven dependency관련-ojdbc6등록](#ojdbc6)
- [ERD보기](#erd)
- [뷰](#view)
- [테이블삭제관련](#tabledrop)

- [Error]
  - [null for parameter](#nullforparameter)
  - [value too large](#valuetoolarge)
  - [방화벽문제](#firewallissue)
  - [접속문제](#connectissue)
  - [오라클 설치에러](#oracleinstallerror)
  - [복수테이블-같은이름칼럼조회](#columnduplicate)
  - [Group by expression](#groupbyexpression)


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


## modifycolumn

**칼럼의 속성수정하기**
```sql
alter table product modify kind varchar(10);
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

4 ) SELECT
1 ) FROM
2 ) WHERE
3 ) GROUP BY: 데이터를 더 작은 그룹으로 나눌 떄 사용(대개 복수행 함수(그룹함수 또는 집계 함수라고 부르기도함),WHERE는 GROUP BY에 적용된다.(where에서 걸러진후 groupby적용) ~'별'로 나눈다. 해석
5 ) HAVING : GROUP BY 절의 결과를 필터링 할 때 사용
6 ) ORDER BY :결과 데이터의 정렬


SELECT 문장의 패턴

1) SELECT FROM
2) SELECT FROM WHERE
3) SELECT FROM GROUP BY
4) SELECT FROM  WHERE GROUP BY
5) SELECT FROM GROUP BY HAVING
6) SELECT FROM  WHERE GROUP BY HAVING

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

###### 180826_6

FROM -> WHERE -> GROUP BY -> SELECT -> ORDER BY

FROM -> GROUP BY -> SELECT -> HAVING -> ORDER BY

WGSH 잘생각해야 순서.

WHERE 과 HAVING의 차이

WHERE은 그룹함수를 쓸수없다.

WHERE에서 거른후 GROUP BY->SELECT에서 그룹함수 연산이후

HAVING에서 집단연산결과를 거른다.테이블 이름 변경 

---


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

 ---


 ## subquery

 ### 서브쿼리

 서브쿼리는 메인쿼리에 비해 먼저 수행되고 그 결과를 메인쿼리가 이용하는 형태이다.

 조인이 2개 이상의 집합에서 데이터 검색을 할떄 유용하다면
 서브쿼리는 데이터값의 비교가 목적이다.
 조인보다 서브쿼리의구조가 더직관적이고 이해하기 쉽다.

 서브쿼리는 단일 행 서브쿼리, 다중 행 서브쿼리, 상호관련 서브쿼리로 분류할 수있다.

 ### Single Row 서브쿼리

 메인쿼리에 적용될 결과 집합이 하나의 레코드만을 갖는 쿼리를 의미한다.

 ex) BLAKE와 같은 부서에서 근무하는 모든 사원을 출력하라.
 ```sql
 SELECT ename
 FROM emp
 WHERE deptno = (SELECT deptno FROM emp
  		WHERE ename='BLAKE');
 ```

 #### Multiple Row 서브쿼리
 :메인쿼리에 적용할 **서브쿼리** 결과 집합의 레코드 수가 2개 이상인 서브쿼리를 말한다.

 서브쿼리 결과집합이 하나일때는 = 동등조건이 가능하지만
 다중행 서브쿼리는 안된다.

 이때 조건으로 사용되는 것이 in, any, all과 같은 연산자이다.

 @ in 연산자

 ex) BLAKE 또는 MILLER 와 같은 부서에 일하는 사원 출력

 ```sql
 SELECT ename FROM emp
 WHRE deptno in (SELECT deptno FROM emp
 		WHERE ename = 'BLAKE' OR ename = 'MILLER');
 ```

 만약 서브쿼리 결과 중 NULL 값이 하나라도 포함된다면 WHERE 절의 결과는 결과적으로 NULL이 된다.(메인쿼리의 결과는 아무것도 반환하지 않게 됨)

 따라서 IN 조건을 사용했는데 아무런 결과를 얻지 못하는 경우

 정말로 해당 조건을 만족하는 로우가 없는지 아니면 서브쿼리 결과에 NULL 이 포함되었기 때문인지를 확인해 봐야한다.

 (참고)
 null은 값이 없음을 나타내는데 값이 없으니 그 값도 없어서
 비교대상이 되지 않는다. 떄문에 = null 이 아닌 is null , is not null의 형태로 사용해야한다.

 @ any 연산자

 ```sql
 SELECT ename, sal
 FROM emp
 WHERE sal > any (SELECT sal FROM emp
 		WEHRE job = 'MANAGER');
 ```

 ->job이 MANAGER인 어떤 요소들이든 간에 보다 더 많은 급여를 받는 사원을 조회.(세명중 가장 작은 급여를 받는 사원보다 많은 급여를 받는 사원 조회)

 @ all 연산자
```sql
 SELECT ename, sal ,job
 FROM emp
 WHERE sal > all (SELECT sal FROM emp
 		WHERE job= 'MANAGER');
```

 -> job이 MANAGER인 모든 사람들보다 많은 급여를 받는 사원 쿼리(MANAGER인 사람중 가장많은 급여를 받는 사람보다 많은 급여를 받는 사원조회)

 @ exists 연산자

 :exists 연산자는 메인쿼리의 로우 하나하나(후보행)에 대해 서브쿼리를 적용시킨다.
 서브쿼리의 결과가 하나라도 존재하면 해당 후보행이 WHERE절의 조건을 만족한 것이 된다.
 상호관련 서브쿼리의 방식과 유사하나 , 차이점은 서브쿼리에 있는 테이블의 모든 로우를  검색하지 않을 수도 있다는 점이다.
 서브쿼리의 WHERE 절을 만족하는 로우가 나타나자마자 다음 후보행으로 넘어가기 때문이다.
 또한 exists 연산자의 사용 목적이 로우의 컬럼값을 얻기 위한 것이 아니라 단지 결과가 존재하는지를 판단하는 것이기 때문에 서브쿼리의 SELECT 절에 숫자 리터럴이나 문자 리터럴을 사용한다.

 후보행마다 서브쿼리의 결과가 달라져야 exists 연산자를 사용하는 의미가 있으므로
 서브쿼리에는 WHERE 절이 있어야하며 그 WHERE 절에 메인쿼리 테이블의 컬럼이 사용되어야 한다.

 ->예제를 통해서 보면
 ```sql
 SELECT deptno, dname FROM dept
 WHERE exists (SELECT 1 FROM emp
               WHERE emp.deptno = dept.deptno);
 ```
 :메인쿼리의 첫 후보행의 deptno컬럼값에 해당하는  서브쿼리의 emp.depno  의 일치값이 있는 로우가 존재하는지 판단한다.
 만약 첫번쨰 로우가 해당하는 값이 있으면 존재하는것이므로
 emp 테이블의 나머지 로우에 대해서는 비교하지 않는다.

 그러므로 exists 라는 영어단어의 뜻처럼 메인쿼리의 후보행에 대해서 서브쿼리의 결과가 하나라도 존재하기만 하면 된다.
 (존재여부를 파악하는 것이 exists)


 @ Multiple Column 서브쿼리

 과일의 정보가 담긴 fruits테이블이 있다.
 ('딸기','A'),('딸기','B')
 ('바나나','A')('바나나','B')('바나나','C')

 구매 테이블인 purchases가 있다.
 ('딸기','A')('바나나','B')

 이렇게 데이터가 있을때.

 fruits 테이블에서 구매된 적 있는 제품만 출력하고 싶을때 쿼리 하려고 할떄.

 ```sql
 SELECT *
 FROM fruits
 WHERE name in (SELECT name FROM purchases)
 AND grade in (SELECT grade FROM purchases);
 ```

 위 쿼리는 딸기 A/ 바나나 B를 찾는게 아니라, name이 딸기 이거나 바나나이며

 grade가 A이거나 B인 레코드, 다시말해 딸기 A/ 딸기 B 또는 바나나A,바나나B를 fruits

 테이블에서 검색하게 된다.

 이렇게 각 칼럼을 별개의 조건으로 사용 할 경우를 Non-pairwise 라고 부른다.

 반대로 칼럼들을 조합하여 컬럼들에 대한 조건을 딸기 A/ 바나나 B 의 형태로 서브쿼리

 를 할경우 이를 pairwise 라고 하며 아래와 같이 사용하면 된다.

 ```sql
 SELECT *
 FROM fruits
 WHERE (name,grade) in (SELECT name,grade FROM purchases);
 ```
 서브쿼리에서 구매된 적 있는 과일과 등급을 결과집합으로 얻고 그 결과집합을 메인쿼리의 WHERE 절에서 컬럼대 컬럼으로 비교하여 해당 레코드를 최종결과집합으로 얻게된다.

 ex) 위 테이블에서 purchases 에서 구매하지 않은 과일들을 출력하라
 ```sql
 SELECT *
 FROM fruits
 WHERE (name,grade) not in (SELECT name,grade FROM purchases);
 ```
 ex) scott 계정의 dept 테이블과 emp 테이블을 이용하여 각 부서별 최저 연봉을 받고 있는 사원의 이름,부서번호, 급여를 출력하라

 ```sql
 SELECT ename, sal, deptno FROM emp
 WHERE (dept, sal ) in (SELECT deptno, min(sal)
 			FROM emp
 			GROUP BY deptno);
 ```
 서브쿼리의 결과의 어느 한 칼럼이라도 null 값이 들어갈 경우 메인쿼리에 반영 안된다.

 @ 상호관련 서브쿼리(Correlated Subquery)

 기존의 서브쿼리->메인쿼리 적용형태

 그러나 상호관련 서브쿼리는 서브쿼리가 메인쿼리의 후보행을 참조하여 수행된후, 그

 서브쿼리의 결과가 메인쿼리의 후보행에 적용되는 경우이다.

 -

 소속부터의 평균 급여보다 많은 급여를 받는 사원들을 출력하기 위해 아래와 같은 서브쿼리가 있다.

 ```sql
 SELECT ename, sal
 FROM emp A
 WHERE sal > (SELECT avg(sal)
 		FROM emp B
 		WHERE B.deptno = A.deptno);
 ```

 서브쿼리의 WHERE 절에서 메인쿼리의 부서번호를 참조하고 있는 것을 볼 수 있다.

 메인쿼리의 FROM 절에 있는 emp 테이블의 레코드를 한건 읽는다. 이것을 후보행이라 한다.

 그러면 서브쿼리에서 후보행의 deptno 컬럼을 참조하여 후보행의 사원이 소속된 부서의

 평균급여를 구한다. 서브쿼리의 결과, 즉 후보행 사원이 속한 부서의 평균 급여를 후보

 행에적용해 평균급여보다 많은 급여를 받는 사원인지를 판단한다. 이로써 첫번쨰 후보

 행에 대한 상호관련 서브쿼리 연산이 끝난다.

 메인쿼리의 나머지 레코드에 대해서도 같은 과정을 적용한다.

 > '서브쿼리에서 전체 연산후 메인쿼리의 레코드 하나씩 적용하는 과정반복'

 따라서 메인 쿼리 레코드가 많을 경우 상호관련 서브쿼리는 일반적으로 성능이 떨어지게 된다.

 이런경우 아래와 같이 JOIN 문을 이용하는것이 좋다.

 ```sql
 SELECT ename, sal
 FROM emp A JOIN (SELECT deptno, avg(sal) as avg_sal
 		FROM emp
 		GROUP BY deptno) B
 ON A.deptno = B.deptno
 AND A.sal > B.avg_sal;
 ```

 @ 서브쿼리 기타

 @ CTAS

 기존 테이블의 구조나 레코드를 복제하여 손쉽게 테이블을 생성할 수 있다.

 이떄 Create Table 테이블명 As SELECT 형태의 구문을 수행하여 머리글자를 따서 CTAS라고 부른다.

 서브쿼리의 결과집합 처럼 생긴 테이블을 만들라는 뜻으로 서브쿼리의 SELECT 절에

 사용된 컬럼을 테이블의 컬럼으로 하는 테이블을 생성하고 결과 레코드를 삽입한다.

 (만약 서브쿼리의 SELECT 절에 수식이나 함수가 들어갈경우 반드시 별명을 삽입해야)

 모든 서브쿼리는 괄호로 감싸져야 하지만 CTAS는 예외적으로 괄호를 생략 가능하다.
 (99.9% 생략하여 사용한다.)

 ex) emp 테이블의 ename 과 sal 컬럼을 복제한 emp_temp를 생성한다.

 ```sql
 CREATE TABLE emp_temp
 AS
 SELECT ename, sal FROM emp;
 ```
 (데이터까지 다 들어간다.)


 @ insert

 서브쿼리의 결과집합을 테이블에 삽입하기 위해 INSERT 문에 서브쿼리를 사용한다.

 이경우에도 예외적으로 괄호를 생략할 수있으며 대체로 생략하는것을 선호한다.

 ```sql
 INSERT INTO emp_temp2
 SELECT * FROM emp WHERE deptno=10;
 ```

 @ update

 ```sql
 UPDATE emp_temp2
 SET job = (SELECT job FROM emp_temp2
 		WHERE ename = 'MILLER')
 WHERE ename = 'CLARK';
 ```

 @ delete

 ```sql
 DELETE FROM emp_temp2
 WHERE job = (SELECT job FROM emp_temp2
 		WHERE ename = 'MILLER');
```

---

## tabledrop

* 테이블 삭제 시

drop table 테이블명;

* 테이블 완전 삭제하기 (휴지통에 저장되지 않음)
DROP TABLE 테이블명 purge;

* 휴지통 비우기
purge recyclebin;


* 휴지통에 있는 테이블 복원
FLASHBACK TABLE 테이블명 TO BEFORE DROP



---


###### groupbyexpression

Group by expression
-

ORA-00979: not a GROUP BY expression
00979. 00000 -  "not a GROUP BY expression"

```
SELECT LEV , MAX(EMP_ID), EMP_NAME
FROM TEMP
GROUP BY LEV
HAVING MAX(EMP_ID) LIKE '1997%';
```

위에서 SELECT절의 EMP_NAME 때문에 나는 에러인데

연산하는 집합 MAX(EMP_ID)외의 칼럼은

group by 컬럼으로 표시되어야 한다.

또, HAVING절에서도 MAX(EMP_ID)가 EMP_ID 로 표기할경우도 같은 에러 발생



###### 180826_3

테이블 이름 변경 
-


rename 테이블이름 to 변경테이블이름;

-----------------------------------------

###### 180826_4

테이블 주석 설정
-
```
commnet on table t_books 
is '주석내용'
```


확인

```
select table_name, commnets
from user_tab_commnets
where table_name = '테이블명';
```
-----------------------------------------

###### 180826_5

정렬의 null순서 임의변경
-

오름차순(asc)정렬은 null이 마지막에
내림차순(desc)정렬은 null이 처음나타남

오름차순임에도 null이 먼저 나오게 할 수있다.

ORDER BY colum nulls first;

내림차순인데 null이 나중에 나타나도록

ORDER BY column nulls last;

//

참고로 select절에 당장 없는 컬럼도 정렬가능하다.



-----------------------------------------

###### 180828_1

단일행 함수
-


![](https://drive.google.com/uc?export=view&id=1b1isSXM9Ra4U0z-egJT1ba9-dvH7h5GJ)

![](https://drive.google.com/uc?export=view&id=1IBc0YRvSvWMg8d5A3zjCXK9GaB9SGCgC)




-----------------------------------------

###### 180828_2

DECODE
-

job 칼럼의 값이 PRESIDENT일 경우 A를 나머지  경우는 B를 반환하도록 

```SQL
SELECT empno,
        job,
        decode(job,
                'PRESIDENT', 'A',
                'B'
                ) 구분
FROM emp;
```

만약 위에서 'B'에 해당하는 즉 ELSE에 해당하는 부분이 없으면 DECODE함수는 ELSE의 경우 NULL을 반환한다.

예2)
depno가 10번이면 ACCOUNTING을 반환, 20번이면 RESEARCH를 반환 ...... 

```SQL
SELECT empno,
       ename,
       deptno,
       decode(deptno,
              10, 'ACCOUNTING',
              20, 'RESEARCH',
              30, 'SALES',
              40, 'OPERATIONS'
              ) as dname
FROM emp;
```



-----------------------------------------

###### 180828_3

GREATEST
-

나열된 값 가운데 가장 큰 값을 반환

여러개면 그 가운데 하나를 반환함.


그리고 나열된 값 중에 가장 작은 값을 반환하려면 LEAST 함수를 사용.

```sql
SELECT greatest(2,8,15,9)as G,
      least(2,8,15,9)as L
FROM dual;
```

NULL이 포함되어있으면 NULL을 반환(비교자체가 불가능하기 때문)





-----------------------------------------

###### 180828_4

DUAL
-

자동으로 생성되는 테이블, 
소유자는 SYS 유저지만

모든 유저가 DUAL이라는 이름으로 사용가능

VARCHAR2(1)타입의 DUMMY라는 이름의 단 하나의 칼럼과 X라는 값 하나의 행으로 구성, 

SELECT시 한건만 반환되기 때문에 테이블 데이터가 아니라 행이 한 건이라는 사실이 필요할때 유용하다.






-----------------------------------------

###### 180828_5

VIZE - 바이트길이반환 , USER -현재유저 확인
-

VIZE - 바이트 길이 반환

USER - 현재 유저를 확인 할떄 사용 함수


-----------------------------------------

###### 180828_6

NVL,NVL2,COALESCE,NULLIF
-

@NVL

NVL(식1, 식2)

식1이 NULL이 아니면 식를 반환. NULL이면 식2를 반환

둘의 데이터타입이 같아야한다. 그렇지 않으면 식1의 타입으로 식2의 데이터를 자동적으로 변환하려고 시도하고 안되면 에러발생

@NVL2

아규먼트가 3개이다. 반환값의 데이터가 달라도 된다는 특징이 있다.

NVL2(식1,식2,식3)

식1이 NULL이 아니면 식2반환 ,식1이 NULL 이면 식3을 반환 / 

이때 식2,식3의 데이터타입이 식1과 달라도됨

그러나 식 2, 식3의 데이터타입은 같아야함.


@COALESCE

COALESCE(식1,식2[, ... , EXPRN])

나열된 식에서 NULL이 아닌 첫번쨰 값을 반환

@ NULL IF

두 식이 같지 않으면 첫 번째 식을 반환한다. 두 식이 같으면 NULL 값을 반환한다.

NULLIF(식1,식2)

-----------------------------------------

###### 180828_7

숫자함수
-


ABS

인자로 받은 값의 절대값 반환

@CEIL

주어진 값보다 크거나 같은 정수를 반환

반대는  FLOOR

<FLOOR>

주어진 값보다 작거나 같은 정수 반환


<MOD>

나눗셈의 나머지 반환

MOD(값,나눌숫자)

<POWER>

거듭 제곱 값을 구할때 사용

POWER(A,B)

A를 B로 거듭제곱함


<ROUND>

반올림을 수행

ROUND(A[, B])

B는 반올림 기준선의 위치를 나타냄 B가 양수이면 오른쪽으로 값만큼 반올림 기준선을 이동 음수면 왼쪽로 기준선 이동

ROUND(123.456,2 )

-> 123.46

ROUND(123.456, -2)

-> 100

<TRUNC>

반올림 대신 버림을 수행 ROUND함수와 같은 방식으로 동작


<SIGN>

1개의 매개변수를 가지며, 매개변수가 음수면 -1, 0이면 0, 양수면 1을 반환

응용하면 어떤 값의 결과를 SIGN으로 해서 음수, 0 ,양수 여부에 따라서 DECODE함수 등으로로 뭔가 처리할 수 도 있다.


-----------------------------------------

###### 180828_8

문자함수
-

<UPPER>

문자열의 모든 문자를 대문자로 변경

<LOWER>

모든 문자를 소문자로 변경

<INITCAP>

모든 단어의 첫 글자를 대문자로 변경


<CONCAT>

첫 번쨰 문자열에 두번째 문자열을 연결

연결 연산자(||)와 동일한 기능 수행

(CONCAT안에 CONCAT 중첩해서 사용가능)

<INSTR>

찾고자 하는 문자열이 분석 대상 문자열의 어디에 위치하는지 알고자 할때 숫자로 반환가능

INSTR(분석대상문자열, 찾고자 하는 문자열,[, 탐색 시작위치 [, 탐색된횟수]])

```sql
SELECT ename,
	instr(ename, 'A') -- ename 컬럼에 A가 처음으로 나타나는 위치값
FROM emp
ORDER BY ename;
```

이를 3번째 자리부터 확인 할 경우 A가 나타나는 위치값은?

```sql
SELECT ename,
	instr(ename, 'A',3) # ename 컬럼에 A가 처음으로 나타나는 위치값
FROM emp
ORDER BY ename;
```
여기서 A가 두번쨰 나타나는 위치가 어딘지 찾으려면 

```sql
SELECT ename,
	instr(ename, 'A',1,2) 
FROM emp
ORDER BY ename;
```
1번째 문자부터 시작해서 A가 2번째 나타난 위치를 찾는다.


<LENGTH>

문자열 길이 반환

LENGTH 함수를 이용해서 이름이 5자인 행을 반환해본다

```SQL
SELECT EMPNO, ENAME
FROM EMP 
WHERE length(ENAME) =5;
```

이는 like 연산자로 아래와 같이 변경 가능

```sql
SELECT EMPNO, ENAME
FROM EMP 
WHERE ename like '_____';
```

<LPAD>

주어진 문자열 출력에서 문자를 우측 정렬하여 출력하고 남은 왼쪽 공간을 주어진 문자열로 채운다.

LPAD(식1, N[,식2])

식1의 길이를 N 길이로 조정하고 부족한 자리를 식2로 채운다.

```SQL
SELECT lpad('Page 1', 15, '*.') ret
FROM dual;

===
*.*.*.*.*Page 1
```

<REVERSE>

입력된 문자열의 좌우를 뒤집은 문자열을 반환

<REPLACE>

원하는 문자열을 찾아서 전부 대신할 문자열로 변경하는 함수

찾은 문자열을 삭제하고 싶으면 대신할 문자열을 주지않으면 됨.

<SUBSTR>

문자열의 일부를 반환

SUBSTR(char, position [, substring_length])

char의 position 위치에서 substring_length 길이만큼 잘아서 결과를 반환

```sql
SELECT ename,
       substr(ename, 1, 2) a, -- 첫 번째 위치에서 2자리 잘라서 반환
       substr(ename,3) b -- 세번째 위치에서 끝가지 잘라서 반환
FROM emp
WHERE ename like 'A%'
or ename like 'B%'
ORDER BY ename;
```

두번쨰 매개변수를 음수로 둘 경우 뒤에서 부터 왼쪽으로 위치 찾음

```sql
SELECT ename,
       substr(ename, -1, 1) a, -- 뒤에서 첫 번째 글자 반환
       substr(ename, -3,2) b -- 뒤에서 세 번째부터 두 글자 반환
FROM emp
WHERE ename like 'A%'
or ename like 'B%'
ORDER BY ename;
```

<LTRIM>

문자의 왼쪽에서 요구된 문자들을 제거(TRIM)해 나가다가 해당 문자가 아닌 문자가 나타나면 제거작업을 멈춘다.

LTRIM(char [, set])

set 설정하지 않으면 스페이스가 제거된다. 비슷한 기능의 함수로 RTRIM 기능이 있다.

동시에 사용하려면 TRIM 함수를 사용할 수 있다.

```sql
SELECT ltrim('ABBBACD', 'AB') ret
FROM dual;

===

CD
```
-> 좌측 첫번째 글자 기준으로  A또는 B인지 검사하여 삭제하고 계속 반복한다.

A또는B가 아닌 C가 오는 순간 멈추고 출력


<TRIM>

```sql
SELECT trim(leading 'S' FROM 'SOS')  -- SOS에서 왼쪽 S 제거
FROM dual;

SELECT trim(trailing 'S' FROM 'SOS')  -- SOS 에서 우측 S 제거
FROM dual;

SELECT trim(both 'S' FROM 'SOS') -- SOS 에서 양쪽 S 모두 제거
FROM dual;
```

TRIM 의 한계: 

한 글자(character)만 가능해서 'AB'이런식으로 못쓴다.

그떄 LTRIM, RTRIM을 사용하면 'AB'로 지정하면 A또는 B라는 뜻이 된다.

<TRANSLATE>

문자를 찾아서 대응하는 문자로 변경

TRANSLATE(식, FROM_string, to_string)

FROM_string의 첫번째 문자를 to_string의 첫번쨰 문자로, 

FROM_string의 두번 째 문자를to_String 의 두번쨰 문자로 변경한다.

FROM_string의 대응되는 to_string이 없으면 식에서 FROM_string이 제거된다.


```sql
SELECT translate('ABCD', 'ABCD', '1122') 
FROM dual;

===
1122


SELECT translate('ABCD', 'ABCD', '11')
FROM dual;

====
11

```

to_string에 빈문자열을''을 사용할수없다 -> 오라클에서 (빈문자열이) Null으로 취급된다 


-----------------------------------------

###### 180828_9

숫자로만 되어있는 데이터만 반환하기
-

1. UPPER, LOWER 함수를 사용해서 숫자로만 되어있는 데이터만 반환할 수있다.

```SQL
SELECT 칼럼
FROM 테이블
WHERE UPPER(칼럼) = LOWER(칼럼);
```


2.  ltrim을 사용한 상법

```sql
SELECT col1,
      ltrim(col1, '0123456789') -- 숫자로만 이루어진 행은 Null 반환
FROM t1;
```

```sql
SELECT col1
FROM t1
WHERE ltrim(col1, '0123456789') is null; 
```
-> null만 만 반환시키면 숫자로만 이루어진 행만 출력됨.


3.translate 사용

```sql
SELECT col1
FROM t1
WHERE translate(col1, 'x0123456789' , 'x') is null;

===

40953
48756
```

-----------------------------------------

###### 180828_10

단어가 n개 이상인 이상인 행 찾기
-

단어가 3개 이상인 것을 찾는다고 했을때, 다르게 표현하면 space가 두 개 이상이라고 볼수있다.

instr 함수를 사용한다.

space가 두번째 나타는 곳의 위치값을 확인해본다.

```SQL
SELECT col2
FROM t1
WHERE instr(col2, ' ' , 1 , 2 ) > 0;
```

단 주의점은 스페이스 연속 2개로 사용했을때 단어가 두개인데도 세개로 판별할 수있다는점..


-----------------------------------------

###### 180828_11

날짜(시간)함수
-

SYSDATE, SYSTIMESTAMP, CURRENT_DATE, CURRENT_TIMESTAMP, ADD_MONTHS, MONTHS_BETWEEN, NEXT_DATY, LAST_DAY 함수 등이 있다.


시간대

한국 : +09: 00

미국서부 : -07:00



<SYSDATE>

접속한 데이터베이스 서버가 위치한 운영체제의 날짜와 시간 반환

반환 타입은 DATE타입 -> 초단위까지 표현가능

초단위를 더 정밀하게 확인하려면 

SYSTIMESTAMP 함수 사용하면 된다


SYSTIMESTAMP 함수는 시간대(TIME ZONE)가지 반환한다.


<CURRENT_DATE>

접속한 사용자 자신의 날짜와 시간을 반환

타입은 DATE . 초 단위를 더 정밀하게 확인하거나 시간대(TIMEZONE)까지 확인하고 싶으면 CURRENT_TIMESTAMP를 사용하면 됨.

유저의 위치가 미국 서부 지역으로 인식되도록 하기

```SQL
ALTER SESSION SET TIME_ZONE='-7:00';
```

<ADD_MONTH>

주어진 날짜에 N개월을 더하거나 뺴는 함수

ADD_MONTHS(date, integer)

integer가 양수면 n개월을 더하고 , 음수면 n개월을 뺀다

```sql
SELECT sysdate,
      add_months(sysdate, 16) a,
    	add_months(sysdate, -8) b
FROM dual;
```

<MONTHS_BETWEEN>

주어진 두 날짜의 간격을 개월로 표현한 숫자를 반환.

두 날짜의 차이가 3개월 20일이면 3.64516129 처럼 소숫점으로 반환됨.

MONTH_BETWEEN(DATE1, DATE2)

```SQL
SELECT MONTHS_BETWEEN(SYSDATE, HIREDATE) 
FROM EMP;
``

<NEXT_DAY>

주어진 날짜를 지나서 첫 번쨰로 오는 특정 요일의 날짜를 반환

NEXT_DAY(DATE1, CHAR)

```SQL
SELECT NEXT_DAY(SYSDATE, '월') A,
FROM DUAL;
```

<LAST_DAY>

주어진 날짜가 포함된 달의 마지막 날짜 반환

```SQL
SELECT LAST_DAY(SYSDATE) A,
	LAST_DAY(TO_DATE('2015/02/12')) B
FROM DUAL;
```

이번달로 부터 남은 일수 구하기

```SQL
SELECT LAST_DAY(SYSDATE) - SYSDATE 
FROM DUAL;
```


(참고 )

날짜는 상식적인 합,차 연산 가능하나
곱,나누기 는 에러.

날짜에 날짜를 더하며 에러
```
SELECT SYSDATE + (SYSDATE + 1) A
FROM DAUL;
```

메시지 : DATE + DATE NOT ALLOWED



-----------------------------------------

###### 180828_12

변환 함수
-

TO_NUMBER , TO_DATE , TO_CHAR

Format Element

![](https://drive.google.com/uc?export=view&id=1bBpYXCirhtfXA7SicEZCAec_7Kf46Vr8)

![](https://drive.google.com/uc?export=view&id=1zd3vKaeYG9YyDtEbOQpnqRbo1uNPsfCo)


<TO_NUMBER>

문자 데이터를 숫자 데이터로 변환한다.

포맷이 설정된 문자를 변환할 경우 포맷 모델을 설정해야 한다.

```SQL
SELECT TO_NUMBER('-1234')
FROM DUAL;
```

아래는 문제 데이터에 포맷이 있어서 위처럼 변환시 에러가 발생 하므로 적절한 포맷을 사용해야한다.

```SQL
SELECT TO_NUMBER('$12,345.67', '$999,999.999')
FROM DUAL;
```

<TO_DATE>

문자데이터 -> 날짜 데이터 변환




