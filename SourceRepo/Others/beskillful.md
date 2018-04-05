```html
<select name="searchType">
	<option value="n"
		<c:out value="${cri.searchType == null? 'selected' : ''}"/>>
	---</option>
	<option value="t"
		<c:out value="${cri.searchType eq 't'?'selected' : ''}"/>>
	Title</option>
	...
</select>
```
---

```java
public String makeSearch(int page){

	UriComponents uriComponents = UriCompoonentsBuilder.newInstance().queryParam("page",page)
	.queryParam("perPageNum", cri.getPerPageNum())
	.queryparam("searchType", ((SearchCriteria) cri).getSearchType())
	.queryParam("keywrod", encoding(((SearchCriteria) cri).getKeyword())).build();

	return uriComponents.toUriString();
}

private String encoding(String keyword){
  if(keyword == null || keyword.trim().length() == 0){
    return "";
  }
  try{
    return URLEncoder.encode(keyword, "UTF-8");
  }catch(UnsupportedEncodingException e){
    return "";
  }
}
```

---

```html
<ul class="pagenation">

  <c:if test="${pageMaker.prev}">
    <li><a href="list${pageMaker.makeSearch(pageMaker.startPage - 1) }">&laquo;</a>
  </c:if>

  <c:forEach begin="${pageMaker.startPage }" end="${pageMaker.endPage }" var="idx">
    <li <c:out value="${pageMaker.cri.page == idx? 'class =active' : ''}"/>>
      <a href="list${pageMaker.makeSearch(idx)}">${idx}</a>
    </li>
</ul>

```

```js
<script>
  $(document).ready(function(){

    $('#searchBtn').on("click",function(event){
      self.location = "list"
      + '${pageMaker.makeQuery(1)}'
      + "&searchType="
      + $("select option:selected").val()
      + "&keyword=" + encodeURIComponent($('#keywordInput').val());
    });


  });
```

---

```
<if test="title != null">
  AND title like #{title}
</if>
```

```
<choose>
  <when test="title != null">
    AND title like #{title""
  </when>
  <when test="author != null and author.name != null">
  AND author_name like #{author.name}
  </when>
  <otherwise>
  AND featured =1
  </otherwise>
```

```
<trim prefix="WHERE" prefixOverrides="AND|OR ">
...
</trim>
```

```
<foreach item="item" index="index" collection="list"
open="(" separator=," close="">
#{item}
</foreach>
```
