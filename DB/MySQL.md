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


- [Error]
  - [버퍼풀 메모리 관련 오류](#bufferpoolmemoryisuue)
  - [mysql접속 오류1045](#mysqlconnectissue)

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





