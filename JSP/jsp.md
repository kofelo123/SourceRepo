- [SessionScope](#session-scope)
- [JSTL](#jstl)
  - [forEach-배열과값비교-status활용/두개의 el 객체간의 비교방법](#foreach)
  - [set사용](#jstlset)
  - [날짜관련-fmt태그](#fmttag)
  - [<c:url>](#8728_6)
  - [내장객체로 uri얻기](#180806_9)
  - [jstl - functions(fn) 태그 정리](#180811_5)


- [프로젝트의 루트경로 변경시](#rootpathmidofy)
- [escapeXml - html 태그 가 그대로 노출될때](#180811_3)
- [forEach문 쓰지않고 list 간단히 뽑을떄](#180813_2)



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

/thearc/resources 이런식으로 접근해야된다. 

target -> deploy 배포되는 형태의 경로로 이해해야 하지 않을까..

 ![](https://drive.google.com/uc?export=view&id=1qW9AAHfzl_WAG9GbCVlBKZMJcFw4tNFh)

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



-----------------------------------------

###### 180806_9

내장객체로 uri얻기
-

내장객체로 uri얻기
${pageContext.request.requestURI} = localhost:8080/thearc/sboard ~

${pageContext.request.getServerName()}  = localhost

- request 내부 객체의 요청 파라미터 관련 메소드



 메소드

설명 

String getParameter(name) 

파라미터 변수 name에 저장된 변수를 얻어내는 메소드로, 이때 변수의 값은 String으로 리턴된다. 

String[] getParameterValues(name) 

파라미터 변수 name에 저장된 모든 변수값을 얻어내는 메소드로, 이때 변수의 값은 String 배열로 리턴된다. checkbox에서 주로 사용된다.

Enumeration getParameterNames() 

요청에 의해 넘어오는 모든 파라미터 변수를 java.util.Enumeration 타입으로 리턴한다. 



- request 내장 객체의 웹 브라우저, 웹 서버 및 요청 헤더의 정보 관련 메소드



메소드

설명 

String getProtocol() 

 웹 서버로 요청 시, 사용 중인 프로토콜을 리턴한다.

String getServerName() 

웹 서버로 요청 시, 서버의 도메인 이름을 리턴한다. 

String getMethod() 

웹 서버로 요청 시, 요청에 사용된 요청 방식(GET, POST, PUT 등)을 리턴한다. 

String getQueryString() 

웹 서버로 요청 시, 요청에 사용된 QueryString을 리턴한다. 

String getRequestURI() 

웹 서버로 요청 시, 요청에 사용된 URL 로부터 URI 값을 리턴한다. 

String getRemoteAddr() 

웹 서버로 정보를 요청한 웹 브라우저의 IP주소를 리턴한다. 

int getServerPort() 

웹 서버로 요청 시, 서버의 Port번호를 리턴한다. 

String getContextPath() 

해당 JSP 페이지가 속한 웹 어플리케이션의 콘텍스트 경로를 리턴한다. 

String getHeader(name) 

웹 서버로 요청 시, HTTP 요청 헤더(header)의 헤더 이름인 name에 해당하는 속성값을 리턴한다. 

Enumeration getHeaderNames() 

웹 서버로 요청 시, HTTP 요청 헤더(header)에 있는 모든 헤더 이름을 리턴한다. 

 ![](https://drive.google.com/uc?export=view&id=1yHexQ7X4PivDVC9VXSbItbQFkmbG1Qzf)


적용 코드
```
   <c:if test="${empty login && pageContext.request.getServerName() eq 'localhost'}" >
```



-----------------------------------------

###### 180811_3

escapeXml - html 태그 가 그대로 노출될때
-

사용자가 글 내용을 입력할 때 텍스트박스(TextArea)로 입력하는 게 아니라 웹 에디터를 이용할 때가 많다.

즉, HTML 태그를 사용자가 입력하는 것이다.

사용자가 입력한 HTML 코드가 실행되지 않는다면 

글쓰기에서 글 내용에 “<B>테스트</B>”라고 입력한 후 저장했을떄

<b>테스트</b> 라고 그대로 글 읽기페이지에서 표기된다.

이렇게 출력되는 것은 Spring에서 HTML 실행을 막아놨기 때문이다.

다음과 같이 출력(out) 테그에 escapeXml="false"를 넣어주면 문제가 해결된다.

```
<tr>
    <td>내용</td> 
    <td><c:out value="${boardInfo.brdmemo}" escapeXml="false"/></td> 
</tr>

```

하지만 이러면 악의 적 사용자에 의해 잘못된 스크립트가 삽입되어 실행되는 문제가 발생할 수 있다.(alert등 js가 동작)

HTML은 실행하고 자바 스크립트는 실행하지 못하게 해야 한다.

따라서 <script>를 &lt;script>로 바꾸어 사용자가 입력한 프로그램 코드가 실행되지 않게 만들어 주면 된다. 

개발자에 따라 사용자가 입력한 내용을 DB에 저장할 때 처리하기도 하고 보여 줄 때 처리하기도 한다. 

개인적 성향으로 다음과 같이 데이터를 불러와서 보여줄 때 처리하도록 했다.

```java
public class boardVO {
    private String brdno, brdtitle, brdwriter, brdmemo, brddate, brdhit, brddeleteflag;
 ~~ 생략 ~~
    public void setBrdwriter(String brdwriter) {
        this.brdwriter = brdwriter;
    }

    public String getBrdmemo() {
        return brdmemo.replaceAll("(?i)<script", "&lt;script");
    }
```

[참고](http://needjarvis.tistory.com/51)





-----------------------------------------

###### 180811_5

jstl - functions(fn) 태그 정리
-



최상단에 다음과 같이 jstl functions 를 사용하겠다고 선언해야 함

```
<%@ taglib prefix="fn" uri="http://java.sun.com/jsp/jstl/functions"%>
cs
 ```

단독으로 사용할 수 없고 EL 표현식과 함께 사용해야 한다.

 
```
ex.

${fn:contains(str, str)}

${fn:split(str, '|')}
```
 

 

 

fn:contains(string, sbustring)

string이 substring을 포함하면 return true 반환


fn:containsIgnoreCase(string, sbustring)

대소문자 관계없이 string이 substring을 포함하면 return true 반환

 

fn:startsWith(string, prefix)

string이 prefix로 시작하면 return True


fn:endsWith(string, suffix)

string이 suffix로 끝나면 return True


fn:escapeXml(string)

stting에 XML과 HTML에서 < >& ' " 문자들이 있으면, XML엔티티 코드로 바꿔준뒤 문자열 반환


fn:indexOf(string, sbustring)

string에서 substring이 처음으로 나타나는 인덱스 반환

 

fn:split(string, separator)

string내의 문자열 separetor에 따라 나누어서 배열로 구성해서 반환


fn:join(array, separator)

array요소들을 separator를 구분자로 하여 연결해서 반환


fn:length(item)

item이 배열이나 컬렉션이면 요소의 개수를 문자열이면 문자의 개수를 반환


fn:replace(string, before, after)

string내에 있는 before 문자열을 after 문자열로 모두 변경해서 반환


fn:substring(string, begin, end)

string에서 begin인덱스에서 시작해서 end인덱스에 끝나는 부분의 문자열 반환


fn:substringAfter(string, sbustring)

string에서 substring이 나타나는 이후의 문자열 반환


fn:substringBefore(string, sbustring)

string에서 substring이 나타나는 이전의 문자열 반환


fn:toLowerCase(string)

string을 모두 소문자로 변경 후 리턴


fn:toUpperCase(string)

string을 모두 대문자로 변경 후 리턴


fn:trim(string)

string앞뒤의 공백을 모두 제거한 후 반환



출처: http://cofs.tistory.com/262 [CofS]



-----------------------------------------

###### 180813_2

forEach문 쓰지않고 list 간단히 뽑을떄
-

view 뿌려진 list 를 jstl에서 받을때

forEach문 쓰지않고 간단하게 뽑아야할떄

```
${list.board[1].getBno()}(X)
${list[1].board.getBno()}(X)
${list[1].bno} (O)
```



-----------------------------------------

