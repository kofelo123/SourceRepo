## JS 여러가지
- [라디오로 체크 여부 검사하는 코드](#radio-check)
- [match함수(+getElementById,innerHTML)](#match)
- [html()과 append() 차이](#htmlappend)
- [기타]
  - [onclick submit](#onclick-submit)
- [replace정규표현식조건](#replaceregex)
- [.addclass()](#addclass)
- [체크박스 사용하기](#checkbox)
- [명시적(선언)함수와 익명함수차이](#function)
- [onblur](#onblur)
- [원 단위 찍기](#numformat)
- [1회만 새로고침하기](#oncereload)
### radio check

```html
  <div style="text-align: center;">
      <input type="radio" name="okon1" checked> 동의함 &nbsp; &nbsp; &nbsp;
      <input type="radio" name="okon1" checked> 동의안함
      </div>
      <input type="button" value="Next" class="submit"  onclick="go_next()" style="float: right;">
    </form>


function go_next() {
  if (document.formm.okon1[0].checked == true) {
    document.formm.action = "/member/join_form";
    document.formm.submit();
  } else if (document.formm.okon1[1].checked == true) {
    alert('약관에 동의하셔야만 합니다.');
  }
}
```


### Onclick Submit
```html
<input type="button" value="아이디 비밀번호 찾기" class="submit"
                onclick="location='/member/find_id_form'">
```    

---

### match

>문자열에서 특정 단어등을 검색한다

```html
<!DOCTYPE html>
<html>
<body>

<p>Click the button to perfom a global search for the letters "ain" in a string, and display the matches.</p>

<button onclick="myFunction()">Try it</button>

<p id="demo"></p>

<script>
function myFunction() {
    var str = "The rain in SPAIN stays mainly in the plain";
    **var res = str.match(/ain/g);**
    **document.getElementById("demo").innerHTML = res;**
}
</script>

</body>
</html>


function checkImageType(fileName){

	var pattern = /jpg|gif|png|jpeg/i;   //i의 의미는 대,소문자 구분없음.

	return fileName.match(pattern);

}
```
---

## replaceregex
```js

.replace(/[^0-9]/g, '')는 숫자가 아닌값을 null값 처리한다. 아래는 예

// 8자리 이하인 숫자인지 Check 하는 Function
// 사용자가 Key를 입력할 때마다 Function이 호출되도록 구현하였습니다.
function checkNumber (data) {  
    // 사용자가 입력한 값을 Check를 위해 변수에 넣습니다.
    var checkData = data.value;
    // 입력한 값이 8자리가 넘어가는지 Check를 합니다.
    if ( checkData.length > 8 ) {
        // 8자리가 넘어가면 8자리까지만 표현하고 나머지는 제외합니다.
        data.value = checkData.substring(0,8);
    } else {
        // 8자리 이하일 경우
        // Number형이 아닌값이 입력되면 입력값을 null값으로 대체합니다.
        data.value = checkData.replace(/[^0-9]/g, '');
    }
}
```
---
## addclass

The addClass() method adds one or more class names to the selected elements.

This method does not remove existing class attributes, it only adds one or more class names to the class attribute.

Tip: To add more than one class, separate the class names with spaces.

addClass () 메소드는 선택된 요소에 하나 이상의 클래스 이름을 추가한다.

이 메소드는 기존 클래스 속성을 제거하지 않고 하나 이상의 클래스 이름 만 class 속성에 추가한다

팁 : 둘 이상의 클래스를 추가하려면 클래스 이름을 공백으로 구분해야함

```
$("button").click(function(){
    $("p:first").addClass("intro");
});
//첫번째p에 intro 클래스를 적용한다.
```
[w3school](https://www.w3schools.com/jquery/html_addclass.asp)

---

## checkbox

> html에서 체크박스를 사용할때 js에서 받아서 처리하는 코드

(장바구니에서 삭제)체크박스를 배열로 받아와서 길이를 기준으로 처리할떄 체크박스 변수가 하나일떄는 배열처리가 안된다(undefined)
그래서 undefined일때 배열의 길이를 1로 처리해준다
```
//(체크가 된것을 뽑아야하는데 그 체크된 체크박스가 하나일때는 undefined가 되니까.)
function go_cart_delete() {
  var count = 0;
  if(document.formm.cseq.length == undefined && document.formm.cseq.checked == true){
	  	count++;
  }else{
  for ( var i = 0; i < document.formm.cseq.length; i++) {
    if (document.formm.cseq[i].checked == true) {
      count++;
    }
  }
  }
  console.log("test3:"+count);
  if (count == 0) {
    alert("삭제할 항목을 선택해 주세요.");

  } else {
    document.formm.action = "/momstouch/product/cart_delete";
    document.formm.submit();
  }
}

```

//자바단에서 처리
```java
@RequestMapping(value = "cart_delete" , method = RequestMethod.POST)
public String cart_delete(Model model,@RequestParam("cseq")String[] cseq){

  for(String cseq2:cseq){
    service.deleteCart(Integer.parseInt(cseq2));
  }

  return "redirect:/product/cart_list";
}
```
```//html단에서는 안보이지만 체크박스 안에 value를 넣은것으로 전달한다.
<input type="checkbox" name="cseq" value= "${cartVO.cseq}">
```

---

## function

<명시적(선언) 함수>

function 함수이름 (){
로직
}

함수이름();
html 가장먼저실행

<익명 함수>

var 변수이름 = function(){
로직
}

변수이름();
위에서 순차적으로 실행

-> 그래서 아마 thearc 에서
익명함수 들이 큰틀에 감싸져 안에 있는데
그 큰틀이 $(document).ready(function(){
이렇게 감싸주는 이유가 그것인것같다.
자바스크립트에서는 onload가 그 기능을 하는녀석 이었다고 한다.


---


## onblur


객체 내에있던 포커스를 잃었을떄 발생하는 이벤트 핸들러이다.

선언된 객체 외에 다른 객체를 클릭하거나 탭 키를 이용해서 포커스가

주어지는 경우 이벤트가 발생

```js
<script type="text/javascript">
<!--
  function eventonblur() {
    alert("focus를 잃었습니다.");
  }
-->
</script>
</head>
<body>
<input type="text" id="Rangs" onblur="eventonblur()">
</body>
```


---


## numformat

원 단위 찍기
-

<input type="text" name="price2" size="11" onBlur="go_ab()" onKeyUp='NumFormat(this)' placeholder="판매가입력..">

onkeyUp이기 때문에 키를 눌렀다가 뗄때마다 수행된다.

```js
function NumFormat(t) // 원 단위, 찍어주기
{
	s = t.value;
	s = s.replace(/\D/g, '');
	l = s.length - 3;
	while (l > 0) {
		s = s.substr(0, l) + ',' + s.substr(l);
		l -= 3;
	}
	t.value = s;
	return t;
}
```


---




###### oncereload

1회만 새로고침하기
-

```js
 <script>

				     if (self.name != 'reload') {
				         self.name = 'reload';
				         self.location.reload(true);
				     }
				     else self.name = '';
					 </script>
```
