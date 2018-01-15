- [null for parameter](#nullforparameter)
- [value too large](#valuetoolarge)

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