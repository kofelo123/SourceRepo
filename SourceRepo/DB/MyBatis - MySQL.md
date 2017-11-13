## My-Batis MySql

- [Like 연산자](#like-연산자)

- [다중파라미터 Map에 담아서보내기](#map)




### Like 연산자

```html
WHERE(AND) column LIKE CONCAT('%' , #{search_value},'%')
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
