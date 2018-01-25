- [HTTP에러코드정리](#http-errorcode)
- [checkbox의 값전달에 대해](#checkbox)
- [링크 새창 열기(blank)](#blank)

### Http errorcode

| 200 | OK, 에러 없이 전송 성공                                                    |
|-----|----------------------------------------------------------------------------|
| 404 | Not Found,요청한 문서를 찾을 수 없음                                       |
| 500 | Internal Server Error, 서버 내부 오류 . 웹 서버 요청사항을 수행할 수 없음. |


---

## checkbox

>checkbox의 경우 체크가 안될떄 아예 값이 서버로 전달이 안된다(null)는 것을 알고있어야 한다.

```html
//에러발생코드
      <c:choose>
        <c:when test='${productVO.bestyn=="y"}'>
          <input type="checkbox" name="bestyn" value="y" checked="checked"> //체크일때는 정상적으로 값전달
        </c:when>
        <c:otherwise>
          <input type="checkbox" name="bestyn" value="n"> //이떄 n이 전달되는게 아니라 값이 아예 전달이 안되서 null 이 되어 에러발생 가능성이 생긴다.
        </c:otherwise>
      </c:choose>
      //(자바스크립트상의 콘솔에서는 'n'값이 뜨지만, 최종적으로 submit되어 controller에 전달이 안된다.)
```

이같은 경우, hidden 태그를 사용하는 방법으로 해결한다.


```html
//방법1
Add a hidden input for the checkbox with a different ID:

<input id='testName' type='checkbox' value='Yes' name='testName'>
<input id='testNameHidden' type='hidden' value='No' name='testName'> //이름은 같지만 id가 다른 hidden태그를 둔다.

Before submitting the form, disable the hidden input based on the checked condition:

if(document.getElementById("testName").checked) {
    document.getElementById('testNameHidden').disabled = true; //여기서 disabled는 태그를 사용못하게 하는역할.(boolean타입)
}

//방법2

Html
<input type="checkbox" name="sampleCheckbox" />
<input type="hidden" name="sampleHidden" />

JavaScript
if ($('input[name=sampleCheckbox]').is(":checked")) { //checkbox는 체크여부를 위함이고, 값은 hidden으로만 사용하는 방법이다.
    $('input[name=sampleHidden]').val('Y');
} else {
    $('input[name=sampleHidden]').val('N');
}

```html
//최종으로 바꾼코드
<input type="hidden" id="uncheckeduse" name="useyn" value="n"> //unchecked일때 사용될값

<c:choose>
       <c:when test='${productVO.useyn=="y"}'>
         <input type="checkbox"  id="checkboxuse" name="useyn" value="y" checked="checked">
       </c:when>
     <c:otherwise>
       <input type="checkbox" id="checkboxuse" name="useyn" value="y">
     </c:otherwise>
   </c:choose>


   if(document.getElementById("checkboxuse").checked){// checked일때 hidden을 사용못하게함.
   				document.getElementById("uncheckeduse").disabled=true;
   			}
```



## blank
**링크 새창으로 열기**

```html
target="_blank"

<li><a href="https://github.com/kofelo123/smartitcording" target="_blank"><i class="fa fa-github "></i> <span>깃허브</span></a></li>
```
