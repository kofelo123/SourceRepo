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



---

```
@RestController
@RequestMapping("/replies")
public class ReplyController{

	@Inject
	private ReplyService service;

	@Requestmapping(value = "", method = RequestMethod.POST)
	public ResponseEntity<String> register(@RequestBody ReplyVO vo){

	ResponseEntity<String> entity = null;
	try{
		service.addReply(vo);
		entity = new ResponseEntity<String>("SUCCESS",HttpStatus.OK);
	}catch(Exception e){
		e.printStackTrace();
		entity = new ResponseEntity<String>(e.getMessage(), HttpStatus.BAD_REQUEST);
	}
	return entity;
	}
}
```

```java
@RequestMapping(value = "/all/{bno}", method = RequestMethod.GET)
public ResponseEntity<List<ReplyVO>> list(@PathVariable("bno") Integer bno){

	ResponseEntity<List<ReplyVO>> entity = null;
	try{
			entity = new ResponseEntity<>(service.listReply(bno),HttpStatus.OK);

	}catch(Exception e){
		e.printStackTrace();
		entity = new ResponseEntity<>(HttpStatus.BAD_REQUEST);
	}
	return entity;
}
```


---
HiddenMethod의 활용, OverloadedPOST
```
$.ajax({
	type:'delete',
	url:'/replies/'+rno
	headers: {
			"Content-Type" : "application/json",
			"X-HTTP-Method-Override": "DELETE" },
	dataType:'text',
	success:function(result){
		console.log("result: " + result);
		if(result == 'SUCCESS'){
			alert("삭제 되었습니다.");
			getPage("/replies/" + bno +"/"+replyPage);
		}
	}
});
```

---
Ajax 전체 목록에 대한 함수처리

```js
function getAllList(){

	$.getJSON("/replies/all/" + bno, function(data){

		var str ="";
		console.log(data.length);

		$(data).each(
			function(){
				str += "<li data-rno='"+this.rno+"' class='replyLi'>"
						+ this.rno + ":" + this.replytext
						+ "</li>";
			});

		$("#replies").html(str);
	});
}
```

사용자가 ADD REPLY 버튼을 누를때 이벤트 처리

```js

$(#replyAddBtn").on("click", function(){

		var replyer = $("#newReplyWriter").val();
		var replytext = $("#newReplyText").val();

		$.ajax({
			type : 'post',
			url : '/replies',
			headers : {
				"Content-Type" : "application/json",
				"X-HTTP-Method-Override" : "POST"
			},
			dataType : 'text',
			data : JSON.stringify({
				bno : bno,
				replyer : replyer,
				replytext : replytext
			}),
			success : function(result) {

				if (result == 'SUCCESS'){
					alert("등록 되었습니다.");
				}
			}
		});
	});
```


각 버튼에 대한 이벤트를 처리하는 작업은 다음과 같이 처리한다.

```js

$("#replies").on("click", ".replyLi button", function(){

	var reply = $(this).parent();

	var rno = reply.attr("data-rno");
	var replytext = reply.text();

	alert(rno + " : " + replytext);
});

```
