- [SessionScope](#session-scope)
- [JSTL-forEach-배열과값비교-status활용/두개의 el 객체간의 비교방법](#foreach)
- [JSTL-set사용](#jstlset)
- [프로젝트의 루트경로 변경시](#rootpathmidofy)
### Session scope

```html
<c:when test="${empty sessionScope.loginUser}">
```

---

## foreach

>JSTL 에서 forEach 사용시, 배열안의 값과 특정값을 비교해서 html select문의 option을 설정하는 코드에 대한 것이다.
>${객체배열[1]} 이런식으로 배열의 인덱스에 해당하는 값을 불러올수 있다는점.
>그리고 status.index해서 0인덱스부터 배열값과 productVO.kind의 값을 비교해서 html select문의 옵션에 적용하는것

```html
<select name="kind">
     <c:forEach items="${kindList}" var="kind" varStatus="status">
       <c:choose>
         <c:when test="${productVO.kind==kindList[status.index]}">
           <option value="${productVO.kind}" selected="selected">${productVO.kind}</option>
         </c:when>
         <c:otherwise>
           <option value="${kindList[status.index]}">${kindList[status.index]}</option>
         </c:otherwise>
       </c:choose>
     </c:forEach>
   </select>
```

>두개의 el객체간의 비교를 하는방법

[stackoverflow](https://stackoverflow.com/questions/1900843/how-to-compare-two-object-variables-in-el-expression-language)
```
So the particular line

<c:when test="${lang}.equals(${pageLang})">  //나도 이런식으로 접근해서 안되었다(${lang} == ${pageLang})
should be written as (note that the whole expression is inside the { and })

<c:when test="${lang == pageLang}"> //이런식으로 접근해야한다.
or, equivalently

<c:when test="${lang eq pageLang}">

```
---

## jstlset

JSTL에서 변수를 저장해서 쓰는 용도.

```html
c:set 사용

<c:forEach items="${listNewProduct }"  var="productVO">

	      <c:choose>
			<c:when test="${productVO.kind == '치킨 메뉴' }">
				<c:set var="subfolder" value="/menu_1"/>  
			</c:when>
			<c:when test="${productVO.kind == '씨푸드 메뉴' }">
				<c:set var="subfolder" value="/menu_2"/>  
			</c:when>
			<c:when test="${productVO.kind == '버거 메뉴' }">
				<c:set var="subfolder" value="/menu_3"/>  
			</c:when>
			<c:when test="${productVO.kind == '사이드 메뉴' }">
				<c:set var="subfolder" value="/menu_4"/>  
			</c:when>
			<c:when test="${productVO.kind == '음료류' }">
				<c:set var="subfolder" value="/menu_5"/>  
			</c:when>
		</c:choose>

(사용) ${subfolder}
```
---

## rootpathmidofy
**프로젝트의 루트경로 변경시**

일반적으로 웹모듈의 path를 '/'로 두면 localhost:8080/ 이경로가 루트경로가 된다
그래서 리소스의 경우 /resources 이런식으로 절대경로의 기준이 되는데(((webapp)/resources ))

한서버에 여러 프로젝트를 구동할떄는 루트path를 각기 바꿨을경우
'/thearc' 이런식으로 path를 줬을때

/thearc/resources 이런식으로 접근해야된다. ((webapp)/thearc/resources)
