- [SessionScope](#session-scope)
- [JSTL](#jstl)
  - [forEach-배열과값비교-status활용/두개의 el 객체간의 비교방법](#foreach)
  - [set사용](#jstlset)
  - [날짜관련-fmt태그](#fmttag)
  - [<c:url>](#8728_6)
  
- [프로젝트의 루트경로 변경시](#rootpathmidofy)
### Session scope

```html
<c:when test="${empty sessionScope.loginUser}">
```

---
## jstl

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

---

## fmtTag

jstl 날짜관련 (fmt)
```html
<%@ taglib prefix="fmt" uri="http://java.sun.com/jsp/jstl/fmt" %>

//현재날짜
<jsp:useBean id="now" class="java.util.Date" />

<fmt:formatDate value="${now}" pattern="yyyy-MM-dd a hh:mm" />

//(변수로 저장할 수 있다 - jstl의 태그들 다수는 var를 표기안했을때는 바로 화면에 출력이되고, var로 변수를 표기시에는 var를 사용할떄만 출력되는 규칙이 있다.)
<fmt:formatDate value="${now}" pattern="yyyy-MM-dd a hh:mm" var="변수명" />

회원관련 VO 에서 Timestamp형으로 쓰고있었다.
Date형 으로 쓰는 타입이다.




[출처] JSTL 날짜 형식 바꾸기 (fmt:parseDate & fmt:formatDate)|작성자 lbiryu
<%--시간관련 계산로직 --%>
      <jsp:useBean id="curtime" class="java.util.Date" /> //현재의 날짜를 받기위함
    	<fmt:formatDate value="${curtime}" pattern="yyyy-MM-dd-hh" var="current" /> //fmt:formatDate : Date 형을 받아서 원하는 포멧으로 날짜 형태를 변경시켜 준다.
    	<fmt:parseDate value="${current}" pattern="yyyy-MM-dd-hh" var="current2" /> //fmt:parseDate : String 형을 받아서 워하는 포멧으로 자료형을 Date 형태로 변경 시켜 준다.
		<fmt:parseNumber value="${current2.time / (1000*60*60) }" integerOnly="true" var="curcal"/> //fmt:parseNumber: date형->숫자형으로 변환(계산을위해필요)

    	 <fmt:formatDate value="${orderVO.indate}" pattern="yyyy-MM-dd-hh" var="orderdate" />
    	 <fmt:parseDate value="${orderdate}"    pattern="yyyy-MM-dd-hh" var="orderdate2"/>
		 <fmt:parseNumber value="${orderdate2.time /(1000*60*60) }" integerOnly="true" var="ordercal"/>

    	<c:set var="timecal" value="${curcal-ordercal }"/>

    	<c:if test='${timecal < 24}'>
    		${timecal}시간 전
    	</c:if>
    	<c:if test='${timecal >= 24}'>
    		 <fmt:parseNumber value="${timecal/24}" integerOnly="true"/>일 전
    	</c:if>
     <%--시간관련 계산로직 --%>
	</td>
--

표현법은 java의 SimpleDateFormat 을 따른다

- 종류

   ㄴ hh : 01~12

   ㄴ HH : 00~23  //hh를 쓰다가 오전오후 시간 계산의 오차가 생겨 -표기가 되어 알아보니 HH를 써야 24시간 계산.

   ㄴ kk : 01~24

   ㄴ KK : 00~11

<fmt:formatDate value="${result.regDate}" pattern="yyyy-MM-dd HH:mm:ss"/>

<fmt:formatDate value="${result.regDate}" pattern="yyyy-MM-dd KK:mm:ss"/>


```


-----------------------------------------

###### 8728_6

<c:url>
-

jstl - <c:url>

어짜피 JQUERY를 사용하면 사용할 일이많이 있겠나 싶기도 하지만, 일단 메모해둔다.

```
	<c:forEach var="listview" items="${listview}" varStatus="status">	
				<c:url var="link" value="board1Read">
					<c:param name="brdno" value="${listview.brdno}" />
				</c:url>		
										  				
				<tr>
					<td><c:out value="${listview.brdno}"/></td>
					<td><a href="${link}"><c:out value="${listview.brdtitle}"/></a></td>
					<td><c:out value="${listview.brdwriter}"/></td>
					<td><c:out value="${listview.brddate}"/></td>
				</tr>
			</c:forEach>
```



