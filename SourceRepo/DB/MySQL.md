## MySQL

-[Limit](#limit)

-[더미 데이터 생성](#dummy-data)

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
