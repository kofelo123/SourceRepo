## MySQL

- [사용자생성및 권한](#createuserprivilige)
- [Limit](#limit)
- [더미 데이터 생성](#dummy-data)
- [백업하기-MysqlDump](#mysqldump)
- [mysql 원격접속](#mysqlremoteconnect)
- [전체사용자조회,현재사용자](#selectuser)
- [DB조회,사용, 테이블조회](#showdatabase)
- [특정사용자권한보기](#showgrant)
- [IF - 조건에 따른 값 변화](#8709_100)
- [COALESCE , IFNULL - null을 특정값으로](#8709_2200)
- [DATE_FORMAT - 날짜표기 ](#8728_4)
- [DATETIME과 TIMESTAMP의 차이](#8728_5)
- [mysql 대소문자구별](#180808_7)
- [auto commit 여부 확인](#180819_12)
- [우분투서버 - mybatis 값을 못가져올떄](#180820_1)
- [mysql 유저생성(워크밴치)](#180830_5)
- [제약조건 조회](#181024_4)
- [외래키추가](#181024_6)
- [중복 값을 가지고 있는 필드를 조회](#181112_4)
- [foreign key (외래키) 추가,삭제 확인](#181124_6)


- [Error]
  - [버퍼풀 메모리 관련 오류](#bufferpoolmemoryisuue)
  - [mysql접속 오류1045](#mysqlconnectissue)
  - [부모,자식 외래키 간의 데이터 불이치 에러](#181024_5)

---

###### createuserprivilige

사용자,스키마 생성 및 권한
-

```sql

create user 'id'@'localhost' identified by 'password';
GRANT ALL PRIVILEGES ON *.* TO 'momstouch'@'localhost' WITH GRANT OPTION;
FLUSH PRIVILEGES;
```

기존에 사용하던 계정에 외부 접근 권한을 부여하려면, Host를 '%' 로 하여 똑같은 계정을 추가한다

mysql > create user 'userid'@'%' identified by '비밀번호';  // '%' 의 의미는 외부에서의 접근을 허용


//스키마 생성
create database ssuserdb character set = utf8;



(spring4에게 ssuserdb 데이터베이스(스키마)에 접근할수 있는 권한을 준다 -> spring4접속시 스키마에 ssuerdb만 보임)

grant all privileges on ssuserdb.* to 'spring4'@'localhost';


(spring4에게 모든 데이터베이스(스키마)에 접근 할 수 있는 권한을 준다.

grant all privileges on *.* to 'spring4'@'localhost' with grant option;

---

### Limit

> MySql의 경우 전체 중 일부 데이터만 출력할때 limit를 사용해서 처리한다.
```sql
select
...
where xxx
order by xxx
limit 시작데이터, 데이터의 개수
ex)1page - limit 0,10 / 2page - limit 10,10
select * from tbl_board where bno > 0 order by bno desc limit 0, 10
```


### Dummy Data

```sql
insert into tbl_board (title,content,writer)(select title, content,writer from tbl_board);
```

## mysqldump

>cmd에서 bin경로에서 mysqld로 하는방법이 있고 워크밴치에서 하는 방법이 있으나,
둘다 안되었다. 특히 workbench의 경우 아스키 문제가 잇었는데, 사용자 계정이 한글이라서 그런것 같기도 한데,
경로를 바뀌어도 같은문제에 검색해도 딱히 해결방법이 나오지 않았다.
cmd방법을 다시 시도했고 시키는 대로 했으나 엑세스 거부 문제가 생겼다.
그런데 우연히 경로 없이 해보니(다양한관점) 덤프가 진행되는것을 보고 이것은 bin의 경로자체의 권한 문제라는 실마리를 찾았고
bin경로의 폴더의 권한을 everyone으로 변경하여 진행해서 성공했다.
그런데 문제는, 우분투 서버쪽은 나의 로컬쪽과 위치가 달라서 그런지 cmd로 import하는게 안되었는데(없는 데이터베이스라고 함)
그래서 그냥 sql파일을 그대로 서버쪽 스키마에서 워크밴치에서 그냥 sql파일을 전체실행하니 데이터가 다 import되었다.

물리적백업 - 논리적 백업이 있다.

논리적 백업이 mysqldump 같은 툴으로 sql문을 갖는 텍스트 파일을 생성하는것.
```
3. mysqldump 사용법
 $ mysqldump -u[userId] -p[password] --all-databases > dump.sql  // 서버에 있는 모든 것의 논리 백업 생성
 $ mysqldump -u[userId] -p[password] --databases [DB명] > dump.sql // 특정 데이터베이스만의 논리 백업 생성
 $ mysqldump -u[userId] -p[password] [DB명] [테이블명]

4. dump 파일 복원 방법
 - mysql -u [userId] -p [password] [DB명] < dump.sql
 - mysql 접속한 후 아래의 명령 실행
     mysql> source dump.sql
```

```
mysqld.exe 가 있는 bin은 C:\Program Files\MySQL\MySQL Server 5.7\bin

mysqldump -uroot -pgj02~(pw) book_ex > smart.sql
                                             (스키마명(db명))
-> sql파일로 그냥 전체실행

mysqldump: [Warning] Using a password on the command line interface can be insecure.
위의 경고(에러아님)이 뜨긴한데 비밀번호를 직접 보이게 하지말라는건데(안보이게 하는 setting이 있다고 함)
경고일뿐 실행하는데 문제는 없음.
```


---
## mysqlRemoteConnect
//원격접속할떄는 -h 아이피를 넣음

```
//MyLocal - CMD
C:\Program Files\MySQL\MySQL Server 5.7\bin>mysql -h jeongwon.me -uroot -p(pw)
```
mysqldump -h 192.168.12.1 -u 아이디 -p패스워드 DB명 > dump.sql

//sql문 실행시키는것 (오라클의 @이것과같은듯)	(워크밴치에서 쓰는 방법은 아니고 mysql 접속후 cmd에서 쓰는방법..)
source d:\mysql\test.sql;

import시에 해당스키마(db라고부름)를 선택해주고 sql import 해줘야해서

db선택하는방법이 mysql 접속후에
use DB(스키마명)명 이렇게 해주고들어가면 된다.

그후  source C:\Program Files\MySQL\MySQL Server 5.7\bin\0216.sql

이런식으로..

//세팅후 재시작
service mysql restart


---
## bufferpoolmemoryisuue

**버퍼풀 메모리 관련 오류**

(우분투에서 mysql start 안되는 상황..)

//에러메세지
Job for mysql.service failed because the control process exited with error code. See "systemctl status mysql.service" and "journalctl -xe" for details.

/var/log/mysql/error.log
살펴보면
cannot allocate memory for the buffer pool
-> 버퍼풀 메모리 관련 오류

my.cnf 를 찾아서 수정해야.

find / -name 'my.cnf'
찾아서 (/etc/mysql/my.cnf)
연후에

[mysqld] 아래에 기술해준다.
```
innodb_buffer_pool_size = 16M
(기본 128M이며 나는 64부터 잘되더라)
mysql> show variables like "innodb_buffer_pool_size";
버퍼풀 변경 확인(재시작 해줘야함)
```

---

## mysqlconnectissue

mysql 접속 오류
ERROR 1045 (28000): Access denied for user

-p 옵션을 붙여 패스워드를 입력하자.

---

###### selectuser

사용자조회
-

//전체 사용자
```
select * from mysql.user;
```

//현재사용자
```
select user();
```

---

###### showdatabase

DB조회,사용, 테이블조회
-

데이터 베이스 조회
```
show databases;
```


```
user DBName;
```


테이블 조회

```
show tables;
```

###### showgrant

특정사용자권한보기
-


현재 우분투 ec2에 root 사용자가 2개가 있다(root@localhost, root@%)
원래 localhost가 기본적으로 있는데 원격접속을 위해서 뒤에껄 만든것으로 보인다.
(root의 경우는 '%'(모든외부접근허용)를 사용자 추가로 따로 만들어야 하는 것 같다.)


```
SHOW GRANTS FOR 'root'@'localhost';
```




---

###### 8709_100

IF - 조건에 따른 값 변화
-



IF(조건, 참값, 거짓값)

test테이블 number컬럼의 값이 1일 경우 A가 select되고...
그 외에는 본래의 값이 select되도록 하려면...
```sql
select IF(number=1, 'A', number) from test
```
응용>>
타입(type)이 'A'인 것의 개수를 구할때...
```sql
select SUM( IF( type='A', 1, 0)) AS cnt from test
```




---


###### 8709_2200

COALESCE , IFNULL - null을 특정값으로
-

oracle - nvl = mysql -COALESCE

oracle nvl2 = mysql - ifnull


지정한 값이 NULL 이면 다른값으로 변환
```sql
SELECT COALESCE (변수명,  1)  FROM 테이블명;
```

```sql
SELECT IFNULL(필드명, "대체할 값") FROM 테이블명; 
```

차이점:
```sql
ifnull(val, '')

 : 항목은 두 개 뿐으로, 하나가 null이면 다른 값을 출력한다. 순서는 상관없다.

coalesce(val1, val2, val3, val4...)

 : 항목은 여러 개 가능하고, 처음으로 만나는 null이 아닌 값을 출력한다.
```


-----------------------------------------

###### 8728_4

DATE_FORMAT - 날짜표기 
-

```
 SELECT BRDNO, BRDTITLE, BRDWRITER, DATE_FORMAT(BRDDATE,'%Y-%m-%d') BRDDATE
          FROM TBL_BOARD
         ORDER BY BRDNO DESC 
```
기존 데이터: 

2018-07-29 13:58:08

select 로 가져온 데이터:

2018-07-29

 ![](https://drive.google.com/uc?export=view&id=1s8KPOfwvIigT0114A3XNA-UY7iapg_VI)


-----------------------------------------

###### 8728_5

DATETIME과 TIMESTAMP의 차이
-

CREATE TABLE TBL_BOARD (

  BRDNO int(11) NOT NULL AUTO_INCREMENT,         -- 글 번호

  BRDTITLE varchar(255),                         -- 제목

  BRDWRITER varchar(20),                         -- 작성자

  BRDMEMO   varchar(4000),                       -- 내용

  BRDDATE     datetime,                          -- 작성일자

  PRIMARY KEY (BRDNO)

) ;

여기서 BRDDATE의 DATETIME 데이터베이스에 저장 INSERT할때 시스템에서 읽어와서 같이 저장하도록 된것.

그렇다면 MYSQL에서 사용되는 DATETIME과 TIMESTAMP의 차이?

두 자료형은 둘다 데이터를 다루는 자료형 이지만, 몇가지 차이점을 가진다.
 

1) 지원되는 범위에서 차이를 보인다.

datetime은 1000-01-01 00:00:00 ~ 9999-12-31 23:59:59 까지 가능하다.

timestamp는 1970-01-01 00:00:00  ~ 2037-12-31 23:59:59 까지 가능하다.


2) DB에 저장 될 때에

datetime은 문자형으로

timestamp는 숫자형으로 변환되어 저장되고


3) 저장되는 공간의 크기도

 

datetime은 8byte를

timestamp는 4byte를 차지한다.


4) 그리고 중요한 차이점이라고 생각되는 것은 자동입력 여부이다.

 

datetime은 수정할 일이 있으면 그때마다 날짜를 입력해 줘야 하지만

timestamp는 날짜를 따로 입력 안해줘도 자동으로 입력이 가능 (CURRENT_TIMESTAMP 사용시 ) 하다.

 

 

두 자료형 모두 위와같이 구분되는 각각의 특징을 지니고 있기 때문에

시간을 다루어야 할 경우에 무조건 어느 것이 좋다고 하나만 고집 할 것이 아니라

각각의 경우에 맞춰 알맞은 자료형을 선택하면 사용하면 좋을 것이다.

 

가령 예를 들어 하나의 글을 등록 할 시에

등록일에는 datetime을 사용하고

수정일에는 timestamp를 사용한다면

수정시 일일히 수정일에 날짜를 입력하지 않아도 될 것이다.






-----------------------------------------

###### 180808_7

mysql 대소문자구별
-

에러메시지 table '테이블명' doesn't exist

단순한 에러메시지 임에도 의아했다.

기존에 써왔던 테이블인데 왜 우분투서버에서만 

안써지는가.. 알고보니 쿼리를 최근에 대문자로 

쓰다보니 생긴것으로 보였다.

테이블 대소문자 구분하는가..

MYSQL에서 놀랍게도 윈도우에서는 대소문자를 구분하지않고 리눅스기반에서는  구분한다고 한다.

그것을 구분하지 않게 하는 세팅이 있다.

(MySQL 에서 테이블 사용시 Windows 에서는 대소문자 구분이 안 되는데 Linux나 Un*x 계열에서는 대소문자 구분을 해야 하는 이유는 바로 table 과 database 명이 directory와 file 명이기 때문이다.

Windows 에서는 디렉토리와 파일에 접근할때 대소문자 구분을 하지 않으나 Linux/Unix 는 구분하므로 select 나 insert 시에 테이블의 대소문자를 구분하게 된다.)

```
 mysql> show variables like 'lower%';
```
이의 결과가 

 ![](https://drive.google.com/uc?export=view&id=1BophcOrqZFno-ITcU5uy665xS6Ld92ms)

기본적으로 윈도우는 1 , 리눅스는 0으로 되어있을것이다.

세팅 변경을위해 

윈도우에서는 MySQL 설치 디렉토리\my.inf 파일을 수정하면 되고

리눅스 같은거에서는 /etc/my.cnf 파일을 수정하면 된다.

(이경로에 있긴한데 파일이 빈파일이라, 과거에 이 파일의 설정을 변경했던 기억이 나서 검색해보니 /etc/mysql/my.cnf 에 있는 곳에서 세팅해놨던게 있다. 거기다 변경)
```
[mysqld]
lower_case_table_names = 1
```
세팅변경 해주고 MYSQL 리스타트 한다.

lower_case_table_names 를 1로 설정해도 기존에 생성한 데이타베이스와 테이블은 대소문자를 구분하므로 기존 테이블과 DB 의 이름을 변경해야 한다.

※ 데이터베이스나 테이블을 생성할때 디비명과 테이블명은 "소문자" 로 생성해야 한다. 
※  lower_case_table_names=1 요 옵션의 의미가 명령어가 대문자로 들어오던 소문자로 들어오던 명령어를 소문자로 바꿔서 실행하게 하는 옵션인듯하다.






-----------------------------------------

###### 180819_12

auto commit 여부 확인
-

SELECT @@AUTOCOMMIT;   // AUTOCOMMIT 여부 확인

SET AUTOCOMMIT = TRUE;   // AUTOCOMMIT 설정
SET AUTOCOMMIT = FALSE;   // AUTOCOMMIT 해제

COMMIT; // 커밋
ROLLBACK; // 롤백


참고로 일반적으로 기본 DB Engine으로 지정되는 MyISAM 은 TRANSACTION 을 지원하지 않기 때문에 
ROLLBACK이니 COMMIT이니 무의미 하다.
TRANSACTION을 지원하지 않으면 무조건 AUTOCOMMIT된다고 보면 된다.

그러므로 TRANSACTION기능을 사용하고자 한다면 TABLE을 생성할때 TRANSACTION을 지원하는 DB엔진을 사용하면 된다.
TRANSACTION기능을 지원하지 않는 DB엔진을 사용하면 AUTOCOMMIT이 FALSE라도 바로바로 적용되니 주의하도록 하자.


"나는 지금 MySQL 5.6  버전을 사용 중인데, AUTOCOMMIT 을 FALSE 로 하니, TRANSACTION 이 적용되었다. 나는 디폴트로 사용 중인데, Table Engine을 보니 InnoDB 로 되어 있었다. 지금은 디폴트가 InnoDB 로 되어 있나보다."

( 기본 스토리지 엔진 확인)

```
mysql> SELECT engine, support FROM information_schema.engines WHERE support='DEFAULT';

+--------+---------+
| engine | support |
+--------+---------+
| InnoDB | DEFAULT |
+--------+---------+
1 row in set (0.00 sec)
```




-----------------------------------------

###### 180820_1

우분투서버 - mybatis 값을 못가져올떄
-

양쪽 세팅을다 맞춰도 우분투서버쪽에서만 mybatis에서 db갑을 못가져 오는 경우가 있다.

그떄는 mybatis에서 한글인코딩의 문제가 있는것.

![](https://drive.google.com/uc?export=view&id=1k_SEnsbqpHhNVitP49bhA1H1JTgdZq-H)


기본이 라틴으로 되어있다.(클라이언트는 utf로 되어있는데 아마 스키마 에서 설정한것이지 안을까..
server는 라틴으로 되어있는데 이걸 수정해야할것

한글 설정관련..참고 블로그
https://nesoy.github.io/articles/2017-05/mysql-UTF8

설정파일 변경

/etc/mysql/my.cnf 파일 변경하기
```
[client]
default-character-set=utf8

[mysql]
default-character-set=utf8


[mysqld]
collation-server = utf8_unicode_ci
init-connect='SET NAMES utf8'
character-set-server = utf8
```

주의할 점은 기존의 만들어져있던 Database나 Table들의 Character-Set이 변경되는 것이 아니기 때문에 직접 변경을 해줘야 한다.

```
ALTER DATABASE [DB명] DEFAULT CHARACTER SET utf8;
```

현재 Character-Set 확인하기
```
show variables like ‘c%’
status
```


세팅후
![](https://drive.google.com/uc?export=view&id=1FqR9yLGkQUt2go3wtDfHH-R5qE8MZAaC)

service mysql restart 해줘야함




-----------------------------------------

###### 180830_5

mysql 유저생성(워크밴치)
-

mysql 유저생성(워크밴치)

좌측 MANAGEMENT - Users and Privileges -> Add Account -> Schema Privileges에서 권한 주면됨



-----------------------------------------

###### 181024_4

제약조건 조회
-

mysql [test_db] > SELECT constraint_name, constraint_type
    -> FROM information_schema.table_constraints
    -> WHERE table_name = 'test05';





-----------------------------------------

###### 181024_5

부모,자식 외래키 간의 데이터 불이치 에러
-

Cannot add or update a child row: a foreign key constraint fails 


조금 더 확인해보니 참조테이블에 들어갈 수 있는 값은 부모테이블의 부모키에 
존재하는 값만 들어갈 수 있더군요 (중복은 OK) 

'외래키를 추가하려고 했는데 안된다. -> 이유는 두 테이블간의데이터 일치하지않았기 때문..'

-----------------------------------------

###### 181024_6

외래키추가
-

ALTER TABLE tbl_member_auth ADD FOREIGN KEY (uid) REFERENCES tbl_user(uid);

-----------------------------------------

###### 181114_6

중복 값을 가지고 있는 필드를 조회
-

디비에서 중복 값을 가지고 있는 필드를 조회하기위해 아래의 쿼리를 입력한다.

SELECT * FROM `테이블명` GROUP BY 필드 HAVING COUNT(필드) > 1

이 값은 같은 필드가 2개 이상인 값을 보여준다.

예) SELECT * FROM `Coupon` GROUP BY Code HAVING COUNT(Code) > 1


-----------------------------------------

###### 181124_6

foreign key (외래키) 추가,삭제 확인
-

1.foreign key 추가
alter table [추가할테이블명] add constraint [제약조건명] foreign key(컬럼명)
references [부모테이블명] (PK컬럼명) [ON DELETE CASCADE / ON UPDATE CASECADE];

ON DELETE CASCADE
외래 키에서 참조하는 키가 포함된 행을 삭제하려고 하면 해당 외래 키가 포함되어 있는 모든 행도 삭제

ON UPDATE CASCADE
외래 키에서 참조하는 키 값이 포함된 행에서 키 값을 업데이트 하면 해당 외래 키를 구성하는 모든 값도 키에 지정된 새 값으로 업데이트되도록 지정 

2. foreign key 삭제
alter table [테이블명] drop foreign key [제약조건명];
3. foreign key 확인 방법
3.1 테이블 기준 확인
select * from information_schema.table_constraints where table_name = '테이블명';
3.2 데이터베이스 기준 확인
select * from information_schema.table_constraints where constraint_schema = '데이터베이스명';


출처: http://allg.tistory.com/37 [프로그래밍 해볼까]

