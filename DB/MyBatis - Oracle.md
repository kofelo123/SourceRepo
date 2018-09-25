## mybatis - oracle
- [Like 연산자](#like-연산자)
- [바인딩 파라미터가 객체일때](#binding-parameter)
- [매퍼의 중복된 id에 의한 에러](#180918_7)

### Like 연산자

like 연산이 mybatis 에서는 사용법이 달라진다.(dbms 종류에 따라서도)

```html
WHERE(AND) column LIKE '%'||#{search_value}||'%'
```

### Binding Parameter

> 매퍼 바인딩시 파라미터가 객체일때 헷갈리는 부분에 대해 정리

```
<!-- paramMap이 아닐때 그냥 cseq해도되는지 아니면 CartVO.cseq해야하는지?  -->
1.
  <update id="updateCartResult">  
		update cart set result = 2 where cseq=#{cseq}
	</update>

2.
<insert id="insertOrderDetail">
  insert into order_detail(odseq,oseq,pseq,quantity) values(order_detail_seq.nextval, #{maxOseq},#{cartVO.pseq},#{cartVO.quantity})
</insert>


```
> 파라미터가 객체일떄 2가지 상황이 있다. 1.파라미터가 하나일경우 / 2.파라미터가 여러개라서 Map에 담아야하는경우 /
> 1.  그냥 변수명을 적어준다 위의 코드처럼
> 2. #{객체.변수} 를 해줘서 구분해줘야 한다.(paramMap에 담을때 명시했던이름인듯->cartVO.변수)


-----------------------------------------

###### 180918_7

매퍼의 중복된 id에 의한 에러
-

IllegalArgumentException: Mapped Statements collection already contains value

매퍼xml에서 중복된 id가 들어있을경우 위같은 에러가 발생.


