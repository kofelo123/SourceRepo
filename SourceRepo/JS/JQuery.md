- [Form 제어](#form-control)

### Form control

```js
$(document).ready(function(){

	var formObj = $("form[role='form']");

	console.log(formObj);

	$("#modifyBtn").on("click", function(){
		formObj.attr("action", "/sboard/modifyPage/${category}");
		formObj.attr("method", "get");		
		formObj.submit();
	});
```
