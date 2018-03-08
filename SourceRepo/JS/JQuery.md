- [Query 속성선택자](#propertyselector)
- [Form 제어](#form-control)
- [.html()](#html)
- [.submit](#submit)
- [.each](#each)
- [.attr](#attr)
- [Ajax](#ajax)
	- [success()](#success)
	- [getJSON()](#getjson)
- [.show , .hide()](#showhide)
- [$().html(.append()와 차이점](#htmlappend)
- [.find()](#find)
- [document.ready](#documentready)
- [자손과 후손](#descendants)
- [충돌방지 noconflict](#noconflict)
- [체크박스체크예제](#checkboxex)
- [val()](#val)
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
// html() 메소드는 html의 태그까지 같이 get 하거나 set한다.
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

.attr( "attributeName" ) //매칭되는 엘리먼트의 값을 get함.

.attr( "attributeName", "value" ) // 매칭되는 엘리먼트에 값을 set함.

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
---


## htmlappend
```html
$('#').html() will completely replace the content inside the tag that the selector corresponds to. $('#').append() will just append to the end of the content inside the tag.

ex.

$('#foo').html("New content here!");

before: <span id="foo">Old content here!</span>
after: <span id="foo">New content here!</span>
and with append

 $('#foo').append("New content here!");

before: <span id="foo">Old content here!</span>
after: <span id="foo">Old content here!New content here!</span>
```
---

## find

개요 : 선택된 요소 집합에서 선택자, jQuery 객체, 요소와 같은 인자에 맞는 요소들을 걸러내어 줍니다.

item II 를 기준으로 li 요소들을 모두 찾아내는 스크립트는 아래와 같습니다.
```js
$('li.item-ii').find('li').css('background-color', 'red');
```

```js
$("p").find("span").css('color','red'); p 하위의 모든 span태그를 찾아서 color를 red로 바꾼다.
```

[jqueryAPI](http://api.jquery.com/find/)


---

## documentReady

$(document).ready(function(){
});
는 약식으로

$(function(){
});
이렇게 쓸수있다.

$는 제이쿼리 식별자로
window.jQuery =window.$ = jQuery의미.

---

## descendants

자손과 후손
```
<body>
	<div>
		<ul>
			<li>
			<li>
body의 자손:div
body의 후손: div,ul,li 모두

자손 선택자
$('body > *').css~~

후손선택자
$('body *').css ~~
```

## noconflict

jQuery 충돌 방지

$.noConflict() -> 사용하면 더이상 jQuery의 식별자 $ 못씀
```js
<script>

//플러그인 간의 충돌 제거
$.noConflict();
var J = jQuery; //jQuery 기니까 불편해서 변수에 담음

//jQuery 사용
J(document).ready(function(){
	J('h1').removeClass('high-light');
});

</script>
```

---

## checkBoxEx


```js
$(document).ready(function(){
	$("#userBan").on("click", function(){

		var count = 0;

		var frm = $("form[name='frm']");

		var checkbox = $('input[name="useyn"]');

		/* console.log(checkbox.length); */


		if(checkbox.length == undefined && checkbox.checked == true){
			count++;
			console.log(count);
		}else{
			for( var i = 0;  i< checkbox.length; i++){
				if(checkbox[i].checked == true){
					count ++;
				}
			}

			if(count == 0){
				alert("정지시킬 계정을 선택해 주세요.")
			}else{
				frm.attr("method","post");
				frm.attr("action","/momstouch/admin/userBan");
				frm.submit();
			}
		}


	});
});  
```

---


## propertySelector

Query 속성선택자

 요소[속성]

 특정 속성을 가지고 있는 문서 객체를 선택함

 요소[속성=값]

 속성 안의 값이 특정 값과 같은 문서 객체를 선택함
 요소[속성~=값]	 속성 안의 값이 특정 값을 단어로써 포함하는 문서 객체를 선택함
 요소[속성^=값]

 속성 안의 값이 특정 값으로 시작하는 문서 객체를 선택함
 요소[속성$=값]	 속성 안의 값이 특정 값으로 끝나는 문서 객체를 선택함
 요소[속성*=값]

 속성 안의 값이 특정 값을 포함하는 문서 객체를 선택함



사용 예)

<inuput type="text" />



$('input[type=text]').val("텍스트 값추가");



---


## val

사용자가 폼 내부의 input에 무엇을 입력했는지 알고싶을 때, 일반적으로 val()을 사용합니다:
```js
var answer = $("#answer").val();
```
반대로 value에 값을 세팅하는 용도로 사용되기도 한다.
```js
var answer2 = $("#answer").val("settingvalue");
```
