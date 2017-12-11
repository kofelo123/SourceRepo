## My-Batis MySql

- [Like 연산자](#like-연산자)

- [다중파라미터 Map에 담아서보내기](#map)

- [쿼리문안에 include넣기(+동적SQL)](#include-refid)


### Like 연산자

```html
WHERE(AND) column LIKE CONCAT('%' , #{search_value},'%')
 <!-- (concat은 몇가지 단어를 하나로 묶어주는 역할을 한다.%value% 이렇게 되도록인듯..)  -->
```


### map

```html
@Override
public List<OrderVO> listOrderById(String id, String result, int oseq) {
	Map<String,Object> paramMap = new HashMap<String, Object>();
	paramMap.put("id",id);
	paramMap.put("result", result);
	paramMap.put("oseq", oseq);

return session.selectList(namespace + ".listOrderById", paramMap) ;
}
```

### include refid

```sql

<select id="listSearchCount" resultType="int">
<![CDATA[  
  select count(bno)
  from tbl_board
  where bno > 0 and category = #{category}
]]>
		*<include refid="search"></include>*

	</select>



	<sql id="search">
			<if test="cri.searchType != null">
				<if test="cri.searchType == 't'.toString()">
					and title like CONCAT('%', #{cri.keyword}, '%')
				</if>
				<if test="cri.searchType == 'c'.toString()">
					and content like CONCAT('%', #{cri.keyword}, '%')
				</if>
				<if test="cri.searchType == 'w'.toString()">
					and writer like CONCAT('%', #{cri.keyword}, '%')
				</if>
				<if test="cri.searchType == 'tc'.toString()">
					and ( title like CONCAT('%', #{cri.keyword}, '%') OR content like
					CONCAT('%', #{cri.keyword}, '%'))
				</if>
				<if test="cri.searchType == 'cw'.toString()">
					and ( content like CONCAT('%', #{cri.keyword}, '%') OR writer like
					CONCAT('%', #{cri.keyword}, '%'))
				</if>
				<if test="cri.searchType == 'tcw'.toString()">
					and ( title like CONCAT('%', #{cri.keyword}, '%')
					OR
					content like CONCAT('%', #{cri.keyword}, '%')
					OR
					writer like CONCAT('%', #{cri.keyword}, '%'))
				</if>
			</if>
		</sql>
```
