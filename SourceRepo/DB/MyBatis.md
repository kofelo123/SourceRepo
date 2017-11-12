여러파라미터를 Map에 담아서 보내기

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

## Table of contents
- [Online Editor](#online-editor)
	- [Markup](#markup)
	
	



## Online Editor
