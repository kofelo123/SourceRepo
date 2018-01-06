- [Form 제어](#form-control)
- [.html()](#html)
- [.submit](#submit)
- [.each](#each)
- [.attr](#attr)
- [Ajax](#ajax)
	- [success()](#success)
	- [getJSON()](#getjson)
- [.show , .hide()](#showhide)


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
});

```


### html

```
Definition and Usage

//1.return 하는 용도로 쓸때는 첫번째 매칭 요소를 반환한다.
//2.set하는 용도로 쓸때는 모두 덮어쓴다

The html() method sets or returns the content (innerHTML) of the selected elements.

When this method is used to return content, it returns the content of the FIRST matched element.

When this method is used to set content, it overwrites the content of ALL matched elements.

Tip: To set or return only the text content of the selected elements, use the text() method.

Syntax
Return content:

$(selector).html()//get함

$(selector).html(content) //set함
Set content using a function:

$(selector).html(function(index,currentcontent))

```

[w3chools](https://www.w3schools.com/jquery/html_html.asp)
[jQueryAPI](http://api.jquery.com/html/)

---

### ajax

jQuery는 Ajax의 중요성을 인식하여 Ajax와 관련된 다양한 종류의 메서드를 제공한다.

| 메서드 이름                  | 설명                                                    |
|------------------------------|---------------------------------------------------------|
| $.ajax()                     | 범용적인 Ajax 관련 메서드                               |
| $.get()                      | get 방식으로 Ajax를 수행                                |
| $.post()                     | post 방식으로 Ajax를 수행                               |
| $.getJSON()                  | get 방식으로 Ajax를 수행해 JSON 데이터를 가져옴         |
| $.getScript()                | get 방식으로 Ajax를 수행해 Script 데이터를 가져옴       |
| $(<선택자>).load()           | Ajax를 수행하고 선택자로 선택한 문서 객체 안에 집어넣음 |
| $(<선택자>).serialize()      | 입력 양식의 내용을 요청 매개변수 문자열로 만듬          |
| $(<선택자>).serializeArray() | 입력 양식의 내용을 객체로 만듬                          |
| $.param()                    | 객체의 내용을 요청 매개변수 문자열로 만듬               |


### success

```js
<script>
	$(document).ready(funtion(){
		$.ajax('/data.html',{
			success:function(data){ //success 속성은 이벤트이다. $.ajax()메서드는 Ajax가 성공했을때 자동으로 success 이벤트를 실행한다.success 이벤트 리스너의 첫 번째 매개변수는 Ajax가 성공했을 때 받은 데이터이다.
				$('body').append(data);
			}
		})
	});

</script>
```

---

### each

[Each API](http://api.jquery.com/each/)

```
Description: Iterate over a jQuery object, executing a function for each matched element.

version added: 1.0.each( function )
function
Type: Function( Integer index, Element element )
A function to execute for each matched element.
The .each() method is designed to make DOM looping constructs concise and less error-prone. When called it iterates over the DOM elements that are part of the jQuery object. Each time the callback runs, it is passed the current loop iteration, beginning from 0. More importantly, the callback is fired in the context of the current DOM element, so the keyword this refers to the element.

Suppose you have a simple unordered list on the page:

1
2
3
4
<ul>
  <li>foo</li>
  <li>bar</li>
</ul>
You can select the list items and iterate across them:

1
2
3
$( "li" ).each(function( index ) {
  console.log( index + ": " + $( this ).text() );
});
A message is thus logged for each item in the list:

0: foo
1: bar

You can stop the loop from within the callback function by returning false.
```

### submit

[Each API](http://api.jquery.com/submit/)

```
Description: Bind an event handler to the "submit" JavaScript event, or trigger that event on an element.

This method is a shortcut for .on( "submit", handler ) in the first variation, and .trigger( "submit" ) in the third.

The submit event is sent to an element when the user is attempting to submit a form. It can only be attached to <form> elements. Forms can be submitted either by clicking an explicit <input type="submit">, <input type="image">, or <button type="submit">, or by pressing Enter when certain form elements have focus.

For example, consider the HTML:

1
2
3
4
5
6
7
<form id="target" action="destination.html">
  <input type="text" value="Hello there">
  <input type="submit" value="Go">
</form>
<div id="other">
  Trigger the handler
</div>
The event handler can be bound to the form:

1
2
3
4
$( "#target" ).submit(function( event ) {
  alert( "Handler for .submit() called." );
  event.preventDefault();
});
Now when the form is submitted, the message is alerted. This happens prior to the actual submission, so we can cancel the submit action by calling .preventDefault() on the event object or by returning false from our handler. We can trigger the event manually when another element is clicked:

1
2
3
$( "#other" ).click(function() {
  $( "#target" ).submit();
});
After this code executes, clicks on Trigger the handler will also display the message. In addition, the default submit action on the form will be fired, so the form will be submitted.

The JavaScript submit event does not bubble in Internet Explorer. However, scripts that rely on event delegation with the submit event will work consistently across browsers as of jQuery 1.4, which has normalized the event's behavior.

Additional Notes:
As the .submit() method is just a shorthand for .on( "submit", handler ), detaching is possible using .off( "submit" ).
Forms and their child elements should not use input names or ids that conflict with properties of a form, such as submit, length, or method. Name conflicts can cause confusing failures. For a complete list of rules and to check your markup for these problems, see DOMLint.
```

### attr

[attrAPI](http://api.jquery.com/attr)
```
.attr()

Get the value of an attribute for the first element in the set of matched elements or set one or more attributes for every matched element.

//매칭되는 엘리먼트를 얻거나, 매칭되는 엘리먼트에 값을 입력하는 역할을 한다.

Contents:

.attr( attributeName ) //매칭되는 엘리먼트의 값을 get함.

.attr( attributeName, value ) // 매칭되는 엘리먼트에 값을 set함.
.attr( attributes )
.attr( attributeName, function )
```

---

## getjson

get방식으로 Ajax를 수행해서 JSON 데이터를 가져온다.
URL과 별개로 비동기식으로 COntroller 요청해서 데이터를 뽑아오는것 같다.

[getJSON-API](http://api.jquery.com/jQuery.getJSON/)

```
Description: Load JSON-encoded data from the server using a GET HTTP request.

version added: 1.0jQuery.getJSON( url [, data ] [, success ] )


url
Type: String
A string containing the URL to which the request is sent.

data
Type: PlainObject or String
A plain object or string that is sent to the server with the request.

success
Type: Function( PlainObject data, String textStatus, jqXHR jqXHR )
A callback function that is executed if the request succeeds.

```

## showhide

**Show**

선택된 엘리먼트를 show(보이게)한다. 옵션을 여러가지 지정할수있다.

[jqueryAPI](http://api.jquery.com/show/)

```
<div id="clickme">
  Click here
</div>
<img id="book" src="book.png" alt="" width="100" height="123">
With the element initially hidden, we can show it slowly:
$( "#clickme" ).click(function() {
  $( "#book" ).show( "slow", function() {
    // Animation complete.
  });
});
```

**hide**

선택된 엘리먼트를 hide(숨김)한다.

[jquery](http://api.jquery.com/hide/)
```
$( "#clickme" ).click(function() {
  $( "#book" ).hide( "slow", function() {
    alert( "Animation complete." );
  });
});
```
