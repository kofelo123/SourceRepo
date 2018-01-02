## JS 여러가지
- [라디오로 체크 여부 검사하는 코드](#radio-check)
- [체크박스 처리 테이블](#check-box-table)
- [match함수(+getElementById,innerHTML)](#match)
- [기타]
  - [onclick submit](#onclick-submit)

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

### Check Box Table

```html
<input type="hidden" name="qseq">  

<a href="#" onClick="javascript:go_view('${qnaVO.qseq}')">

<script type="text/javascript">
  function go_view(qseq) {
    var theForm = document.frm;
    theForm.qseq.value = qseq;
    theForm.action = "/admin/admin_qna_detail";
    theForm.submit();
  }
</script>


---
체크박스처리 테이블
  <td>
      <c:choose>
        <c:when test='${orderVO.result=="1"}'>
        <span style="font-weight: bold; color: blue">${orderVO.odseq}</span>
        (<input type="checkbox" name="result" value="${orderVO.odseq}"> 미처리)
        </c:when>
        <c:otherwise>
          <span style="font-weight: bold; color: red">${orderVO.odseq}</span>
          (<input type="checkbox" checked="checked" disabled="disabled">처리완료)
        </c:otherwise>
      </c:choose>
    </td>

	<script type="text/javascript">
  function go_order_save() {
    var count = 0;
    if (document.frm.result.length == undefined) {
      if (document.frm.result.checked == true) {
        count++;
      }
    } else {
      for ( var i = 0; i < document.frm.result.length; i++) {
        if (document.frm.result[i].checked == true) {
          count++;
        }
      }
    }
    if (count == 0) {
      alert("주문처리할 항목을 선택해 주세요.");
    } else {
      document.frm.action = "/admin/admin_order_save";
      document.frm.submit();
    }
  }
</script>

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
```
