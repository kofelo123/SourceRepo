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
- [find()](#find)
- [값계산](#valcal)
- [onKey](#onkey)
- [전체 replace, 모든 공백제거](#replace)
- [location.href](#href)
- [keypress,엔터키인식](#keypress)
- [is()참,거짓여부](#is)
- [html,text,val,attr차이](#htmltextvalattr)
- [부모요소 선택 방법 parent(),parents(),closest()](#parentparentsclosest)
- [isEmptyObject - 객체의 empty여부](#8703_75)
- [jquery next() ,prev() - 다음,이전요소 반환](#8705_203)
- [one() - 1회성 이벤트처리](#8705_205)
- [after() - 요소 뒤에 새로운 요소추가](#8705_120)
- [js의 this 와 jquery의 $(this) 차이](#8705_122)
- [jQuery.isEmptyObject() - 객체가 empty 인지 체크](#8705_130)
- [~(틸드) - 다음요소에 오는것 찾기](#8705_125)
- [remove() - 요소제거](#8705_123)
- [:contains(text)' - 특정단어 포함 요소 선택](#8705_124)
- [prev + next   -  다음 요소 선택하기](#8706_164)
- [jquery - css 여러개 적용](#8721_1)
- [jQuery ready와 load의 차이](#8726_2)
- [크롬 개발자도구의 콘솔에서 jQuery사용하기](#8727_2)
- [jQuery session plugin](#180730_1)
- [el태그 값을 jQuery에서 사용시 ''처리](#180730_2)
- [이벤트 위임 - delegate](#180806_28)
- [jquery에서 폼만들어서 post 전송](#180806_4)
- [현재 URL, 경로 가져오는 방법](#180806_7)
- [jQuery의 append(), appendTo() 메서드의 차이](#180808_3)

---

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


.ajax() 대신 .post() 이런것들을 쓸때는 일반적인 데이터일 경우이다.

JSON 데이터를 보내서 @RequestBody에서 객체로 처리할때 .post()로 해버리면 에러가난다(415)

그래서 .ajax()를 통해 넣을수있는 다양한 옵션중에  headers에 'application/json'이라고 명시한 콘텐츠 타입에서 전송된다.

그리고 전송되는 데이터에는 JSON>stringify()를 이용해 JSON 데이터를 구성하므로 잘 전송이 된다.


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

attr을 여러개 한번에 처리할떄

```
attr({method:"GET",action:"/blabla.do"}) 
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
$('#').html() will completely replace the content inside the tag that the selector corresponds to.
$('#').append() will just append to the end of the content inside the tag.

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


		if(checkbox.length == undefined && checkbox.checked == true){//.checked가 안되는경우 .is(":checked") 사용
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
---


## find

콜백 함수 내부에서, this 키워드를 사용해서 폼 요소를 참조할 수 있습니다. 흔한 패턴은 폼 요소 내부의 입력값만을 찾기 위해서 폼 요소에서 find()를 호출하는 것입니다:
```js
    $("form").on("submit", function() {
      // 입력란에 name='age' 값을 저장합니다
       var age = $(this).find('[name=age]').val();
    });
```
form 안에 여러 input type 요소들이 있을건데 그것을 접근할떄 javascript dom에서는
form.요소 이런식으로 접근하면 되지만
jquery에서는
var formObj =$("form[name='formm']");
formObj.find("[name=id"];
이런식으로 find를 쓴다.
값을 불러오려면 .val()을 뒤에 붙여주고..

아니면 form에서 접근하지말고
$("input[name=id]").val()
이렇게 직접적으로 접근해도 된다.


---


## onKey

[javascript] 키보드 이벤트 (onkeydown, onkeyup, onkeypress)

onkeydown, onkeyup, onkeypress 이벤트는 키보드 입력시 동작 시점에 따라 발생하는 키보드 이벤트 이다.

onkeydown : 키를 눌렀을때 이벤트이다 (shift, alt, controll, capslock 등의 모든 키에 동작한다. 단 한영변환, 한자 등의 특수키는 인식 못한다).

onkeyup : 키를 눌렀다가 땠을 때 이벤트이다 (onkeydown 에서 인식하는 키들을 인식 한다).

onkeypress : 실제로 글자가 써질때 이벤트이다 (shift, tap, enter 등의 특수키는 인식 못한다).


아래 예제는 jQuery  

```html
<!DOCTYPE html>

<html>

<head>

	<title></title>

</head>

<body>

	남은 글자수

	<h1>150</h1>

	<textarea></textarea>

	<script type="text/javascript" src="//code.jquery.com/jquery-1.11.1.min.js"></script>

	<script type="text/javascript">

		$('textarea').keyup(function () {

			var inputLength = $(this).val().length;

			var remain = 150 - inputLength;			 
			$('h1').html(remain);			 
			if (remain >= 0) {

				$('h1').css('color','blue');

			} else {

				$('h1').css('color','red');

			}



		});

	</script>



</body>

</html>
```

 ![](https://drive.google.com/uc?export=view&id=1cY-UeMkcANLlJmGPgyenM2t5b1k-Js_6)

onkeypress 이벤트는 한글을 지원하지 않는다.



---


## valcal

<input type="text" name="price2" size="11" onBlur="go_ab()" onKeyUp='NumFormat(this)' placeholder="판매가입력..">


function go_ab(){ // 판매가-원가=순매출을 replace해서 계산해 준다.

	var formObj = $("[name=frm]");

	var a = formObj.find("[name=price2]").val().replace(/,/g , '');
	var b = formObj.find("[name=price1]").val().replace(/,/g, '');
	var ab = parseInt(a) - parseInt(b);
	formObj.find("[name=price3]").val(ab);
}

---


## replace

```js

$('#id').val().replace(" ", "") -> 첫번째 공백 1개만 없어짐.
$.trim($('#id').val()) -> 글자사이 공백은 안없어짐.﻿



$('#id').val().replace(/ /g, ''); -> 모든 공백 없어짐.﻿

$('#id').val().replace(/,/g, ''); -> 모든 콤마(,) 없어짐.
```


---

###### href

jQuery에서는 document.location.href를 어떻게 표현?

```js
$(location).attr('href', url);
```

zerock에서는 버튼에 jquery로

self.location=""; 으로 사용했었다.

제일 원초적인 방법은

<button type="button" onclick="location.href='joinUs.jsp' ">회원가입</button>
---

###### keypress
keypress,엔터키인식

1.
텍스트 박스에서 언터키 이벤틀 줄려면
<input name="pwd" type="password" id="pwd" class = "log_pwd" size="25" tabindex="2" onkeypress="if(event.keyCode==13) {chkLogIn();}">
이처럼 텍스트 박스에 onkeypress 이벤트를 주면 된다.
혹시 html 전체에 줄려면 body태그에 위에 같이 주면된다.


2.
$( "#target" ).keypress(function( event ) {
  if ( event.which == 13 ) {
     event.preventDefault();
  }
...
}

---


######is
is()참,거짓여부
-

javascript에서 checked여부를 판단하는것을
jquery에서 $("#checkedboxid").checked 이런식으로 접근하니까안됬다.
is메소드를 사용해준다.
그리고 속성의 변경도 $("#checkedboxid").disabled=true 와 같은 접근은 안되고 속성이니 jquery의 attr을 사용한다.

javascript 에서
```js
if(document.getElementById("checkboxuse").checked){
				document.getElementById("uncheckeduse").disabled=true;
			}
```
jquery에서
```js
if($("#checkboxuse").is(":checked")){
				$("#checkboxuse").attr("disabled","true");
			}
```

체크박스배열로 순회하려면 아래와같이 활용
```js
 $('input:checkbox[name="checkbox_name"]').each(function() {

     if(this.value == "비교값"){ //값 비교

            this.checked = true; //checked 처리

      }

 });
```


---


###### htmltextvalattr

html,text,val,attr차이
-

★ val()
-
 : Form Element 의 값을 받아오는데 쓰인다. (주로 input 이나 textarea 정도?)

- 주의해야할 점은 Form Element 이외의 값은 받아오질 못한다는 점.

★ val(value) : value의 경우 string 또는 string의 배열(이 경우 value들의 matching을 잘 시켜야 오류를 피할 수 있다)

또는 함수(function(index, value) 이런 형태)로 넣을 수 있다.

이 함수 역시 Form Element의 Value 값을 Set할 때 주로 쓰인다.  




● text()
-
 : XML과 HTML 문서에서 둘다 사용될 수 있다. input elements 의 value를 받아오지 못한다(이 경우 val을 사용)

아주 쉬운 예로 <button>1</button> <button>2</button> <button>3</button> : button 대신 li 라던가.

$('button').text() 의 결과는 1 2 3 이다. 이런 식으로 사용된다.

tag attribute의 value가 아니라(대표적으로 form 요소들) <tag>_____</tag>에서 _____에 해당하는 value를 get한다.

참고 사이트에 의하면 The .text() method cannot be used on form inputs or scripts.

라고 적혀있다. ( form inputs 의 경우 val()를 말하는 거고 scripts 의 경우 html()을 가르킨다. )

● text(value) : textString 또는 함수(function(index, text) 이런 형태)로 넣을 수 있다. 이 역시 Set 하기위해 쓰인다.  




◆ html()
-
 : XML 문서는 사용 불가, HTML만 가능. value가 아니라 html code(contents)자체를 get한다.

class 네임이 여러 개 매칭되는 경우 제일 처음 매칭되는 class 안에 속하는 html code만 가져온다.

api 설명에 의하면 IE의 경우 알파벳 문자를 포함하고 있는 속성값들의 경우 " ' " (quote)를 빼먹고 get 할 수도 있다고 한다.

◆ html(value) : htmlString 또는 함수(function(index, oldhtml) 이런 형태)로 넣을 수 있다. 이 역시 Set 하기위해 쓰인다.

class 네임이 여러 개 매칭되는 경우 매칭되는 모든 class 내부에 html code를 삽입.

만약 class 내부에 이미 어떤 code가 삽입되있는 경우, Set 할 html code로 완전히 Replace 해버린다.  



attr() 
-

- 속성에 값을 부여하거나(없는속성도 부여가능) 가져온다.

여러개의 속성을 한번에 세팅하기 : Setting several attributes at once

alt 와 title 속성을 한번에 바꿔봅니다. map 을 사용해서 바꿔 보겠습니다. 각각 속성명이 키가 되고 새로운 값이 내용이 됩니다.(json과 같은 구조네요.)
```
$('#greatphoto').attr({
  alt: 'Beijing Brush Seller',
  title: 'photo by Kelly Clark'
});

```
여러 속성을 부여할 때, 속성명에 따옴표는 선택사항입니다.단, 'class' 속성을 세팅할 때는 반드시 따옴표를 사용해야 합니다.(그냥 항상 사용하면 되겠네요.)


---


###### parentparentsclosest

부모요소 선택 방법 parent(),parents(),closest()
-

parent : 바로 위 부모요소

parents : 모든부모요소( 바로위 뿐만아니라 그 상위까지 모두 포함)

closest : 모든 부모 요소 대상으로 원하는 요소만 선택자로 가져올 수 있음. (단일결과리턴)



---

###### 8705_203

jquery next() ,prev()
-

jquery next() ,prev()

prev() - 이전 요소를 선택하도록 반환

next() - 다음 요소를 선택하도록 반환


```js
// 다음 요소에 대한 색상 변경
$('.button1').click(function() {
   $(this).next().css('color', 'red');
});

// 이전 요소에 대한 색상 변경
$('.button2').click(function() {
   $(this).prev().css('color', 'red');
});
```



---

###### 8705_205

one() - 1회성 이벤트처리
-

```js
$("#foo").one("click", function() {
    alert("This will be displayed only once.");
});
```
이코드 실행시 foo의 요소를 클릭하면 알림창이 나타나는데, 그 이후로는 클릭에도 아무일도 일어나지 않는다.

아래의 코드와 동일하다.
```js
$("#foo").bind("click", function( event ) { 
	alert("This will be displayed only once."); 
	$(this).unbind( event );
 });

```
즉, 바인딩 했다가 정확하게 .unbind() 함수를 실행시키는 것과 동일한 효과




---

###### 8705_120

after() - 요소 뒤에 새로운 요소추가
-

아래와 같은  html 코드에
```html
<div class="container"> 
	<h2>Greetings</h2> 
	<div class="inner">Hello</div> 
	<div class="inner">Goodbye</div>
</div>

```
아래와 같이 after을 적용하면
```js
$('.inner').after('<p>Test</p>');


```
다음처럼 된다.
```html
<div class="container"> <h2>Greetings</h2> 
	<div class="inner">Hello</div> <p>Test</p> 
	<div class="inner">Goodbye</div> <p>Test</p> 
</div>

```


DOM에 있는 특정요소를 선택해서 다른 요소 뒤쪽에 위치 시킬수도 있다.

```js
$('.container').after($('h2'));

```

위는 복사되는것이 아니라 이동처리가 된다.

```html
<div class="container"> 
	<div class="inner">Hello</div>
 	<div class="inner">Goodbye</div> 
</div> <h2>Greetings</h2>

```

연결이 끊긴 DOM 노드 작업을 할 수 있다.

```js
$('<div/>').after('<p></p>').addClass('foo')
.filter('p').attr('id', 'bar').html('hello')
.end()
.appendTo('body');
```


또한 함수에도 적용가능

```js
$('p').after(function() { return '<div>' + this.className + '</div>'; });


```


---

###### 8705_122

js의 this 와 jquery의 $(this) 차이
-

console.log(this)  
console.log($(this));

js의 this의 경우 이벤트 발생 태그요소가 표시된다.

jquery의 발생 요소의 정보들이 Object로 표시된다.

간혹 js의 this를 참조해야할때 jQuery로는 다음과 같이 표현가능 

(JS의) this == (jQuery의) $(this)[0]


---


###### 8705_130

jQuery.isEmptyObject() - 객체가 empty 인지 체크
-

```js
jQuery.isEmptyObject({}) // true 
jQuery.isEmptyObject({ foo: "bar" }) // false

```
($.isEmptyObject 와 같다.)

---

###### 8705_125

next ~ siblings(틸드사용)  -  다음 형제 요소 찾기
 
-

$("#upw ~ #successFail:first").text();

upw의 다음 요소중 첫번째오는 #successFail을 찾아서 text()를 뽑아오는 것.

~ 은 같은 부모를 둔 형제관계여야 한다.

```js
console.log(jQuery.isEmptyObject($("#upw ~ #successFail:first").text()));

else if($("#upw ~ #successFail:first").text()=="비밀번호를 입력해주세요"){  
        	 	$("#upw ~ #successFail:first").text("");  
			}  
``



---

###### 8705_123

remove() - 요소제거
-

empty()와 remove()함수는 DOM에서 요소를 걷어내는 점에서 비슷하다.

remove()를 사용하면 요소 자체 뿐만 아니라 그 안에 있는 모든 요소들이 제거된다. 추가적으로 요소들과 관련된 모든 이벤트와 jQuery 데이터들도 모두 제거된다.

데이터와 이벤트는 제거하지 않으러면 detach()를 사용해야한다.

```html
<div class="container"> 
	<div class="hello">Hello</div> 
	<div class="goodbye">Goodbye</div> 
</div>

```

```js
$('.hello').remove();
```

아래처럼 된다.

```html
<div class="container"> 
	<div class="goodbye">Goodbye</div>
</div>
```


<div class="hello"> 안에 다른 요소들이 있다면 그것들도 제거된다.


---


###### 8705_124

:contains(text) - 특정단어 포함 요소 선택
-


'text'에 해당하는 단어가 포함된 컨텐츠를 가진 요소를 찾는 선택자. (대소문자 구분함)

아래 div 영역의 내용 중에 'john' 이라는 글자가 있으면 그 영역의 텍스트에 밑줄을 친다.

```html
<!DOCTYPE html> 
<html> 
<head> 
<script src="http://code.jquery.com/jquery-1.4.4.js"></script> 
</head> 
<body> 
	<div>John Resig</div> 
	<div>George Martin</div> 
	<div>Malcom John Sinclair</div> 
	<div>J. Ohn</div> 
<script> 
$("div:contains('John')").css("text-decoration", "underline");
</script> 
</body> 
</html>


```




---


###### 8706_164

prev + next   -  다음 요소 선택하기
-

현재 선택된 요소를 기준으로 그 다음 선택자와 일치하는 첫번째 요소를 찾아준다.

(prev + next) 와 (prev ~ sibilings) 인데요. 약간의 차이점이 있다.

prev + next 는 prev에 해당하는 선택자 기준으로 next에 해당하는 요소를 깊이에 상관없이 선택한다.

반면 prev ~ sibilings는 같은 깊이의 요소를 선택한다(형제)

```js
<script>$("label + input").css("color", "blue").val("Labeled!")</script>
```


###### 8721_1

jquery - css 여러개 적용
-

jQuery.css() 함수에 오브젝트를 전달하면 한번에 여러개의 CSS를 적용할 수 있어요.
```
$('textarea').css({
  'width': '600px',
  'height': '400px',
  'font-size': '16px'
});
class
또는, CSS의 class를 사용하면 더 효율적이에요.
/* CSS */
.something {
  width: 600px;
  height: 400px;
  font-size: 16px;
}
/* jQuery */
$('textarea').addClass('something');

```

-----------------------------------------

###### 8726_2

jQuery ready와 load의 차이
-

```js
$(document).ready(function(){
    alert('ojtiger.com');
});
```

```js
$(window).load(function(){
    alert('ojtiger.com');
});
```

위의 두가지의 차이점. 

 ![](https://drive.google.com/uc?export=view&id=1LsE5GgRwGV0V2Lxm6FHOdaYnL56ArXBd)

-----------------------------------------

###### 8727_2

크롬 개발자도구의 콘솔에서 jQuery사용하기
-

크롬 Developer Tools에서 Console에 아래 내용을 복사해서 넣으면 jQuery의 기능을 사용할 수 있다.

```js
var jq = document.createElement('script');
jq.src = "//ajax.googleapis.com/ajax/libs/jquery/1/jquery.min.js";
document.getElementsByTagName('head')[0].appendChild(jq);
// ... give time for script to load, then type.
jQuery.noConflict();
```


-----------------------------------------

###### 180730_1

jQuery session plugin
-

jquery session plugin 을 통해 jquery에서 세션을 편하게 사용

[](https://github.com/AlexChittock/JQuery-Session-Plugin)



-----------------------------------------

###### 180730_2

el태그 값을 jQuery에서 사용시 ''처리
-

el 태그 값 -> jquery에서 쓸때
```
var category = ${boardVO.category}; (X)
```
아래와 같은 에러가 난다.

Uncaught ReferenceError: free is not defined
    at HTMLDivElement.<anonymous>
```
var category = '${boardVO.category}';  (O)
```
이와 같이 ' ' 따옴표로 감싸서 자바스크립트 문자열로 처리해줘야 한다.


-----------------------------------------

###### 180806_1

이벤트 위임 - delegate
-

on() 메서드는 현재 존재하는 태그에 대해서만 이벤트를 연결한다.

아래와 같은 상황은

```js
<button id="test">Test</button>
    $(function(){

        $("#test").click(function(){

            console.log("test");
            $(this).attr("id","test2");

        });

        $("#test2").click(function(){

            console.log("test2");

        });

    });
```
attr을로 id를 test2로 변경해도 다음번의 버튼클릭시 test2이벤트는 발생하지않는다.

따라서 이러한 경우 상위 태그에 이벤트를 연결하고 특정태그를 클릭했을때 를 검출해야한다.

```
<div id="upcase">
        <button id="test">Test</button>
    </div>
</body>


<script>

    $(function(){

        $("#upcase").on("click","#test",function(){

            console.log("test");

            $(this).attr("id","test2");

        });

        $("#upcase").on("click","#test2",function(){

            console.log("test2");

        });

    });

```
(상위의 div 등이 없거나 애매한경우 document 객체에 이벤트 연결하면된다 )

```
$(document).on('click','h1',function(){});
```



(참고 - 두가지 엘리먼트이상을 위임할때 )

$(document).on('click',"#ntoy, #default",function(){ (O)

$(document).on('click',"#ntoy","#default",function(){ (X)

-----------------------------------------

###### 180806_4

jquery에서 폼만들어서 post 전송
-

예제참고

1.간단히 서브밋 이 필요할 때, 폼 하나 만들고 바로 서브밋 날린다.
```js
function test(){
 	$('<form/>').attr({method:"GET",action:"/blabla.do"}).submit();
}
```
2.

$('#btn_submit').click( function() {
     var $form = $('<form></form>');
     $form.attr('action', 'order.asp');
     $form.attr('method', 'post');
     $form.attr('target', 'iFrm');
     $form.appendTo('body');
     
     var idx = $('<input type="hidden" value="<%=idx%>" name="idx">');
     var page = $('<input type="hidden" value="<%=page%>" name="page">');
     var category = $('<input type="hidden" value="<%=category%>" name="category">');
     var keyfield = $('<input type="hidden" value="<%=keyfield%>" name="keyfield">');
     var keyword = $('<input type="hidden" value="<%=keyword%>" name="keyword">');
 
     $form.append(idx).append(page).append(category).append(keyfield).append(keyword);
     $form.submit();
});

3.
동적 폼 엘리먼트 생성 및 ajax 를 이용한 전송



[동적으로 FORM 생성]



/* 화면 refresh없이 계속 submit해야하는 경우 form이 중복으로 document에 추가되기 때문에 reset하는 과정이 필요함. */

var $form = $("#myForm");
 if($form.length < 1) {
  $form = $("<form/>").attr({id:"myForm", method:'POST'});
  $(document.body).append($form);
 }
 $form.empty();

 

/* 정보 세팅 */

$("<input></input>").attr({type:"hidden", name:"aaaa", value:$.trim('aaaa')}).appendTo($form);

$("<input></input>").attr({type:"hidden", name:"bbbb", value:$.trim('bbbb')}).appendTo($form);

[Ajax 를 이용한 폼 전송]



/* ajax로 submit*/

$.ajax({

   url : '/testPjt/myPjt/test.do',

   data : $form.serialize(),

   dataType : 'json',

   success : function(json, textStatus, jqXHR){

     if(!json.result){
         alert('결과가 없습니다.');
     }else{
         alert('결과가 있습니다.');
     }

   }

});
 
적용 코드

```

                       <%-- 개발시 로그인 편하게 하기위해--%>
                <c:if test="${empty login && pageContext.request.getServerName() eq 'localhost'}" >
                <script type="text/javascript" src="/thearc/resources/bootstrap/js/jquery-1.10.2.min.js"></script>
                 <script></script>
                <button id="devlogin" style="width:40px;height:30px; margin-top:10px;"></button>
                    <script>
                        $(function(){
                            $("#devlogin").click(function(){

                                var form = $('<form></form>');

                                form.attr({action:"/thearc/user/loginPost" , method:"post"});
                                form.appendTo('body');

                                $("<input></input>").attr({type:"hidden",name:"uid",value:"kofelo12"}).appendTo(form);
                                $("<input></input>").attr({type:"hidden",name:"upw",value:"gj0123"}).appendTo(form);
                                form.submit();

                            });
                        })

                    </script>
                </c:if>
```


-----------------------------------------



###### 180806_7

[jQuery] 현재 URL, 경로 가져오는 방법
-

jQuery
$(location).attr('host');        #returns host
$(location).attr('hostname');    #returns hostname
$(location).attr('path');        #returns path
$(location).attr('href');        #returns href
$(location).attr('port');        #returns port
$(location).attr('protocol');    #returns protocol



-----------------------------------------

###### 180808_3

jQuery의 append(), appendTo() 메서드의 차이
-

jQuery의 append(), appendTo() 메서드의 차이(구분)
두 메서드의 실행 결과는 똑같습니다.

단지 차이라면, 주체가 무엇이냐입니다.

append 는 객체가 주체로, 나(객체)에 무엇(자료)를 append하는 것이고,

appendTo는 자료가 주체로, 나(자료)를 어떤것(객체)에 append하는 겁니다.

아래 비교한 소스를 참조하면 이해가 쉽습니다.

 

<div class=tObj>

</div>

 

append() : $('.tObj').append("<p>자료</p>");
appendTo() : $("<p>자료</p>").appendTo('.tObj');


