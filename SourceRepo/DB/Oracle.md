## oracle

- [rownum](#rownum)

### RowNum

> 오라클에서는 mysql처럼 limit가 없기 때문에 Rownum을 사용해야 한다.

```sql
ex) mysql에서 20개 가져오기
SELECT * FROM 테이블 LIMIT 20;

ex) 오라클에서 20개 가져오기
SELECT * FROM 테이블 WHERE ROWNUM >= 1 AND ROWNUM <= 20;

ex) 오라클에서 시작과 끝사이의 검색된 결과 20개 가져오기
SELECT * FROM 테이블 WHERE ROWNUM >= 1 AND ROWNUM <= 20 AND 컬럼 BETWEEN 시작 AND 끝;

```
