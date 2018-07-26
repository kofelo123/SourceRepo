<<개발자를 위한 Oracle SQL - 허진경>>

- [like 연산자](#8726_4)


---

###### 8726_4

like 연산자
-


검색하고자 하는 값을 정확하게 알수 없을때

like 연산자를 사용하면 문자 패턴과 일치하는 행을 선택할 수 있다.

문자 패턴 일치 연산을 와일드카드 검색이라고 부른다.

```
% : 문자가 없거나 하나 이상을 나타냄

_ : 단일 문자를 나타냄
```

```sql
SELECT FIRST_NAME, LAST_NAME, JOB_ID, DEPARTMENT_ID
FROM EMPLOYEES
WHERE JOB_ID LIKE 'IT%';

```

%와 _ 를 섞어서 사용해도 된다.

만일 % 와 _ 문자를 포함하는 열을 검색할 필요가 있을때는 ESCAPE 옵션을 사용

"SA_M"을 포함하는 사원의 이름과 잡 아이디를 출력하려면 다음과 같다

```SQL
SELECT FIRST_NAME, JOB_ID
FROM EMPLOYEES
WHERE JOB_ID LIKE 'SA\_M%' ESCAPE '\';
```

ESCAPE 옵션은 회피문자로서 백슬래시(\)를 사용한다. 회피문자는 밑줄(_) 앞에 둔다.



