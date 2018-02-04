## MySQL

- [Limit](#limit)
- [더미 데이터 생성](#dummy-data)
- [백업하기-MysqlDump](#mysqldump)

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

>cmd에서 bin경로에서 mysqld로 하는방법이 있고 워크밴치에서 하는 방법이 있으나
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
