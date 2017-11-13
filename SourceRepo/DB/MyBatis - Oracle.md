## mybatis - oracle
- [Like 연산자] (#like 연산자)  

### like 연산자

like 연산이 mybatis 에서는 사용법이 달라진다.(dbms 종류에 따라서도)

'''html
WHERE(AND) column LIKE '%'||#{search_value}||'%'
'''
