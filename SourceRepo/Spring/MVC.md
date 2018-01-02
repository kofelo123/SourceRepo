- [페이징](#paging)
  - [Limit](#limit)
  - [DAO 처리를 도와줄 Criteria , 페이지계산](#criteria)
  - [화면 하단의 페이징 처리](#paging-button)
    - [endPage 구하기](#end-page)
    - [startPage 구하기](#start-page)
    - [totalCount와 endPage의 재계산](#totalcount-endpage)
    - [prev와 next의 계산](#prev-next)
  - [페이징 처리용 클래스 설계하기](#paging-class)
  - [BoardController와 뷰 처리](#boardcontroller-view)
  - [페이징 처리의 파라미터 처리](#paging-parameter)
    - [스프링 MVC의 UriComponentsBuilder를 이용하는 방식](#uricomponentsbuilder)
    - [JavaScript를 이용하는 링크의 처리](#javascript-use-makelink)
    - [목록 페이지와 정보 유지하기](#link-inform-keep)
  - [오라클 페이징기법](#oraclepaging)
- [업로드](#upload)
  -[UploadController에서 Ajax 처리하기](#uploadcontrollerajax)
  -[파일업로드용 클래스 설계](#fileuploadclass)
  -[썸네일 이미지 생성코드](#thumnail)
  -[UploadController의 재구성](#uploadcontroller)
  -[JSP에서 파일출력하기](#jspfileoutput)
  -[UploadController의 삭제처리](#uploadcontrollerdelete)
### Paging

파라미터를 직접 입력 받는 방법 / 객체로 받는 방법

### Limit

> MySql의 경우 전체 중 일부 데이터만 출력할때 limit를 사용해서 처리한다. (Oracle의 경우 rownum사용)
```sql
select
...
where xxx
order by xxx
limit 시작데이터, 데이터의 개수
ex)1page - limit 0,10 / 2page - limit 10,10
select * from tbl_board where bno > 0 order by bno desc limit 0, 10
```



### Criteria

> 파라미터가 여러 개로 늘어나면 관리하기 어려워지기 떄문에, 클래스로 만들어서 객체로 묶어 처리하는것이 더 바람직하다.
> 시작 데이터 번호 = (페이지 번호 -1) * 페이지 당 보여지는 개수

```java
public class Criteria {

  private int page;
  private int perPageNum;

  public Criteria(){
    this.page = 1;
    this.perPageNum = 10;
  }

  public void setPage(int page){
    if (page <= 0){
      this.page = 1;
      return;
    }
  }

  public void setPerPageNum(int perPageNum){

    if(perPageNum <= 0 || perPageNum > 100){
      this.perPageNum = 10;
      return;
    }
    this.perPageNum = perPageNum;
  }

  public int getPage(){
    return page;
  }

    // method for MyBatis SQL Mapper
    ///(set된 page를 limit로 쓰기위해 가공용도)
  public int getPageStart(){
    return (this.page - 1) * perPageNum;
  }
  // method for MyBatis SQL Mapper
  public int getPerPageNum(){
    return this.perPageNum;
  }
@Override
  public String toString(){
    return "Criteria [page=" + page + ", " + "perPageNum=" + perPageNum + "]";
  }
}
```

```java
@Override
public List<BoardVO> listCriteria(Criteria cri) throws Exception{

  return session.selectList(namespace+".listCriteria", cri);
}
```

```xml
<select id="listCriteria" resultType="BoardVO">
  <![CDATA[
    select
     bno, title, content, writer, regdate, viewcnt
    from
      tbl_board
    order by bno desc, regdate desc
    limit #{pageStart}, #{perPageNum}
  ]]>
</select>
```

### paging button

#### 화면 하단의 페이징 처리

- 시작 페이지 번호(startPage)
> 시작페이지 번호가 1이 아니라면 이전(prev) 으로 갈 수 있는 링크가 제공되야 한다.

- 끝 페이지 번호(endPage)
> 몇 개의 번호를 보여줘야 하는지. 전체 데이터 개수에 영향을 받는다.

- 전체 데이터 개수(totalCount)
> 끝 페이지 번호보다 많은 양의 데이터가 존재하면 뒤로(next) 갈 수 있는 링크가 추가로 붙게 된다.

- 이전 페이지 링크(prev)
> 맨 앞의 페이지 번호가 1이 아니라면 링크를 통해서 이전 페이지를 조회할 수 있어야 한다.

- 이후 페이지 링크(next)
> 맨 뒤의 페이지 이후에 더 많은 데이터가 존재하는 경우 이동이 가능하도록 링크를 제공한다.

ex) 한 번에 10개 데이터 출력 , totalCount가 122 개 일경우

- page(페이지 번호)가 3인 경우
  startPage는 1, endPage는 10, next는 true, prev는 false
- page가 10인 경우
  startPage는 1,endPage는 10, next는  true, prev는 false
- page가 11인 경우
  startPage는 11, endPage는 13, next는 false, prev는 true

### end Page

> startPage 보다 endPage를 먼저 구하는 것이 산술적인 계산이 더 편할 수 있다. 현재 페이지 번호를 기준으로 계산한다.

```java

endPage = (int) (Math.ceil(cri.getPage()) / (double)displayPageNum ) * displayPageNum);
                    (현재 페이지 번호)           (페이지 번호의 수)

```
- 현재 페이지가 3일 때: Math.ceil(3/10) * 10 = 10
- 현재 페이지가 1일 때: Math.ceil(1/10) * 10 = 10
- 현재 페이지가 20일 때: Math.ceil(20/10) * 10 = 20
- 현재 페이지가 21일 때: Math.ceil(21/10) * 10 = 30

### Start Page

> endPage가 구해졌다면 startPage는 단순한 빼기의 문제이다.

```
startPage = (endPage - displayPageNum) + 1;
                      (페이지 번호의 수)
//     1 =     10     -     10         + 1
//    11 =     20     -     10         + 1
```


### TotalCount Endpage

> endPage는 실제 데이터의 개수와 관련이 있기 때문에 다시 한 번 계산할 필요가 있다.

- totalCount : 전체 데이터 개수
- perPageNum : 한 번에 보여지는 데이터 개수


```java
//(10개씨기 보여주는 default의 경우 endPage가 10이 되겠지만, 20개씩 보여주는 경우 endPage가 5가 될 수 있다.)
//이전에 구한 endPage 값과 계산된 결과를 비교해서,계산된 결과가 작은 경우에는 실제 endPage는 최종 게산 결과로 한다.
int tempEndPage = (int) (Math.ceil(totalCount / (double)cri.getPerPageNum()));

if(endPage > tempEndPage){
  endPage = tempEndPage;
}

```

### prev next


#### prev

> prev의 경우 startPage가 1이 아닌지를 검사하는 것으로 충분하다.  삼항 연산자로 처리 1이면 false 아닌경우는 true를 받도록 처리

```
// 결과가 true(1일경우) false 처리한것.
prev = startPage ==1 ? false : true;

```

#### next

> next의 경우 뒤에 더 데이터가 남아 있는지에 대한 처리이므로, endPage * perPageNum이 totalCount보다 작은지를 확인해 줘야 한다.

```
ex)perPageNum이 10이고, endPage가 10인 상황에서 totalCount가 101이라면 next는 true가 되어야 한다.

next = endPage * cri.getPerPageNum() >= totalCount ? false : true;

```
---
### Paging Class
**페이징 처리용 클래스 설계하기**
> 페이징의 계산을 jsp에서 처리할 수 도 있지만, 편리함을 위해서는 별도의 클래스를 설계해서 처리하는 것이 좋다.


**필요한 데이터**

- 외부에서 입력되는 데이터(Criteria에 이미 존재)
  - page: 현재 조회하는 페이지의 번호
  - perPageNum: 한 페이지당 출력하는 데이터의 개수

- DB에서 계산되는 데이터
  - totalCount: SQL의 결과로 나온 데이터의 전체 개수

- 계산을 통해서 만들어지는 데이터
  - startPage
  - endPage
  - prev
  - next

반드시 필요한 데이터는 Criteria와 totalCount.  
이를 통해서 클래스 PageMaker를 만든다.

```java
public class PageMaker{

  private int totalCount;
  private int startPage;
  private int endPage;
  private boolean prev;
  private boolean next;

  private int displayPageNum = 10;

  private Criteria cri;

  public void setCri(Criteria cri){
    this.cri = cri;
  }

//totalCount가 설정 되는 시점에 calcdata()를 실행해서 계산한다.
  public void setTotalCount(int totalCount){
    this.totalCount = totalCount;

    calcData();
  }

  private void calcData(){

    endPage = (int) (Math.ceil(cri.getPage() / (double) displayPageNum) * displayPageNum);

    startPage = (endPage - displayPageNum) + 1;

    int tempEndPage = (int) (Math.ceil(totalCount / (double) cri.getPerPageNum()));

    if (endPage > tempEndPage){
      endPage = tempEndPage;
    }

    prev = startPage == 1 ? false : true;

    next = endPage * cri.getPerPageNum() >= totalCount ? false : true;

  }

  //이하 getter/setter() , toString() 생략

}
```

### boardcontroller view

**listPage.jsp**
```html
<!-- 후에 pageMaker의 makeQuery메소드 생성한후 코드가  약간 바뀐다. -->
<div class="text-center">
  <ul class=pagenation">

    <c:if test="${pageMaker.prev}"> //boolean
      <li><a href="listPage?page=${pageMaker.startPage - 1}">&laquo;</a></li>//'이전 링크 버튼일건데 어떻게 html class코드없이 버튼모양을 식별하는지? laquo 이부분일지도 모르겠다.'
    </c:if>

    <c:forEach begin="${pageMaker.startPage }" //'items 없이 var와 begin, end로 이루어진 forEach.. 생소하다.'
      end="${pageMaker.endPage }" var="idx">
      <li
        <c:out value="${pageMaker.cri.page == idx?' class =active':''}"/>> // '생각해보니.. 이게 시작에서 끝까지 인덱스로 비교후 현재 페이지인지 찾아서 현재 페이지 표시를 하는 부분인것 같다.'(삼항연산자 사용)
      </li>
    </c:forEach>

    <c:if test="${pageMaker.next && pageMaker.endPage > 0}">
      <li><a href="listPage?page=${pageMaker.endPage +1}">&raquo;</a></li>  //'&raquo가 다음페이지 버튼'
    </c:if>
  </ul>
</div>


```

### paging parameter

**페이징 처리의 개선을 위한 Tip**

1.단순히 페이지 번호(page)만 전달 - <a href='listPage?page=3>

2.페이지 번호(page)와 페이지에서 보여지는 데이터의 수(perPageNum) 전달 - <a href='listPage?page=3&perPageNum=20>

2번과 같은 처리를 위해서는
1.JSP에서 직접 수정
2.PageMaker에서 필요한 링크를 생성 *
3.JavaScript를 이용해서 처리하는 방법

### UriComponentsBuilder

스프링 MVC org.springframework.web.util 클래스 중 하나.
URI 작성시 도움이 되는 클래스이다.

```java
//사용예시
@test
public void testURI()throws Exception{

  UriComponents uriComponents = UriComponentsBuilder.newInstance()
  .path("/board/read")
  .queryParam("bno",12)
  .queryParam("perPageNum",20)
  .build();

  logger.info(uriComponents.toString());
}

// 콘솔결과 -  INFO : org.zerock.test.BoardTest - /board/read?bno=12&perPageNum=20
```
```java
//실제사용(PageMaker.class)

public String makeQuery(int page){

  UriComponents uriComponents = UriComponentsBuilder.newInstance()
                                .queryParam("page",page)
                                .queryParam("perPageNum", cri.getPerPageNum())
                                .build();
  return uriComponents.toUriString();

}
```
```html
<!--활용(listPage.jsp)-->
<c:forEach items="${list}" var="boardVO">

  <tr>
    <td>${boardVO.bno}</td>
    <td>
      <a href='/board/readPage${pageMaker.makeQuery(pageMaker.cri.page)}&bno=${boardVO.bno}'>${boardVO.title}</a>
    </td>
    <td>${boardVO.writer}</td>
    <td><fmt:formatDate pattern="yyyy-MM-dd HH:mm" value="${boardVO.regdate}" /></td>
    <td><span class="badge bg-red">${boardVO.viewcnt }</span></td>
  </tr>

</c:forEach>
<!-- 결과: <a href="/board/readPage?page=19&perPageNum=10&bno=123059">-->
```

```html
<div class="text-center">
  <ul class=pagenation">

    <c:if test="${pageMaker.prev}">
      <li><a href="listPage?page=${pageMaker.makeQuery(pageMaker.startPage - 1) }">&laquo;</a></li>
    </c:if>

    <c:forEach begin="${pageMaker.startPage }"
      end="${pageMaker.endPage }" var="idx">
      <li
        <c:out value="${pageMaker.cri.page == idx?' class =active':''}"/>>
        <a href="listPage${pageMaker.makeQuery(idx)}">${idx}></a>
      </li>
    </c:forEach>

    <c:if test="${pageMaker.next && pageMaker.endPage > 0}">
      <li><a href="listPage?page=${pageMaker.makeQuery(pageMaker.endPage +1)}">&raquo;</a></li>
    </c:if>
  </ul>
</div>


```

### javascript use makelink

**JavaScript를 이용하는 링크의 처리**

>링크 처리하는 다른 방법으로 Javascript를 이용한다. 클릭했을떄의 이벤트를 제어하는 방식이다.
>이를 위해 <a> 태그의 href 속성들이 단순히 페이지 번호만을 의미하도록 만들어 주어야 한다.

```html
  <ul class="pagination">
    <li>
      <a href="1">1</a>
    </li>
    <li>
      <a href="2">2</a>
    </li>
    <li>
      <a href="3">3</a>
    </li>
    <li class="active">...</li>
  </ul>

```
>링크에는 단순히 페이지번호만을 넣고, 모든 것은 <form>과 JavaScript를 이용해서 처리한다.

**<form> 태그처리**
```html
<form id="jobForm">
  <input type='hidden' name="page" value=${pageMaker.cri.perPageNum}>
  <input type='hidden' name="perPageNum" value=${pageMaker.cri.perPageNum}>
</form>

```

**javascript처리**
```javascript

$(".pagination li a").on("click", function(event){

  event.preventDefault();

  var targetPage = $(this).attr("href");

  var jobForm = $("#jobForm");
  jobForm.find("[name='page']").val(targetPage);
  jobForm.attr("action","/board/listPage").attr("method","get");
  jobForm.submit();

});

```
> PageMaker나 JavaScript를 사용하여 처리하는 방식은  JSP 내에서 직접 링크를 생성하는 방법보다 복잡하지만, 반복적 개발이 많을때 적은양의
코드로 개발이 가능하다.


### link inform keep

> 목록보기를 통해 기존에 자신이 보던 목록 페이지로 전환 -> history.go(-1)방식 처리도 하기도 하지만, 리스트가 프레임이나 <iframe>내부에 있을 경우는 사용할 수 없기 때문에 직접 처리하는게 좋다.

**조회페이지에서 다시 목록 페이지로 가기 위해 필요한 정보**

- 현재 목록 페이지의 페이지 번호(page)
- 현재 목록 페이지의 페이지당 데이터수(perPageNum)

page와 perPageNum파라미터의 경우 Criteria 타입으로 처리한다.

```html
<!-- readPage.jsp -->

...
<form role="form" action="modifyPage" method="post">
  <input type='hidden' name='bno' value="${boardVO.bno}">
  <input type='hidden' name='page' value="${cri.page}">
  <input type='hidden' name='perPageNum' value ="${cri.perPageNum}">
</form>


```

### OraclePaging

**오라클 페이징 기법-(zerock)**

오라클 페이징은 MySQL과 같이 간단하게 처리되지 못하고 rownum을 활용해주어야 한다.

쿼리 실행시 primary key와 관련된 조건을 주면 이를 활용하는 '실행 계획(Execution Plan)'을 생성하게 된다.

```sql
ex)
select * from tbl_board
where bno > 0
order by bno desc;

```
위를 수행시, 실행계획을 보면 내부적으로 primary key로 지정한 PK_BOARD를 이용해서 TBL_BOARD를 접근한다. -> 이때 DESCENDING이 적용되면서 접근하기 때문에 결과는 최근 데이터가 가장 먼저 나오게 된다.

order by 의 경우 join등을 할 때 내부적으로 최적화된 방법을 찾기 때문에 항상 게시물의 역순으로 데이터를 검색한다는 보장은 없다.
이를 위해서 오라클에서는 개발자가 직접 실행계획을 유도하는 '흰트(Hint)'라는 것을 이용한다.

```sql
select
/*+INDEX_DESC(tbl_board pk_board) */  --참고로 이 pk_board는 pk칼럼명이 아니라 제약조건이름이어야 한다.
*
from tbl_board
where bno > 0;

```

위의 SQL은 오라클의 흰트를 이용해서 강제로 primary key의 역순으로 데이터를 검색하도록 한다.


실행된 결과물을 페이징 처리하기 위해서 필요한 또 다른 개념은 **'ROWNUM'** 이다.

ROWNUM은 쿼리의 처리가 끝나고 최종적으로 출력될 때 각 레코드에 붙는 일종의 일련 번호 이다.

```SQL
select
/*+INDEX_DESC(tbl_board pk_board) */
rownum rn,bno,title,content,writer,regdate,viewcnt
from tbl_board
where bno > 0;
```

위의 쿼리를 실행하면 출력되면서 번호가 붙는 것을 확인 할 수있다.
![paging](https://drive.google.com/uc?export=view&id=1Xis6dHcPgmIx2Unn4mtFNbdEVLxYjCmU)

오라클 페이징은 이렇게 실행 결과의 ROWNUM 값을 이용해서 페이지에 해당하는 데이터를 걸러내는 방식을 이용한다.

주의점은 ROWNUM 조건은 반드시 1이 포함되어야 한다는 점이다.
ROWNUM은 마지막에 데이터가 출력되면서 붙는 번호이기 때문에 where 조건에 1이 포함되지 않으면 매번 새로운 데이터가 1번이 되면서 아무런 데이터가 출력되지 않는문제가 생긴다.

이를 해결하기 위해 'in-line view' 기법을 이용한다.

in-line view는 쉽게 말해서 실행된 SQL문을 하나의 테이블처럼 간주해서 사용하는 방법

만약 10개씩 화면에 내용을 출력하는 경우 2페이지의 데이터는 우선 20개의 레코드를 가져온 후에 다시 rownum으로 생성된 번호를 이용해서 최신 10개의 데이터를 배제하는 방식이다.

```sql

select
*
from
 (
   select
   /*+INDEX_DESC(tbl_board pk_board)*/
   rownum rn, bno, title, content, writer, regdate, viewcnt
   from tbl_board
   where bno > 0
   and rownum <= 20 )   
where rn > 10;

```
쿼리의 실행결과는 아래와 같다.

![img](https://drive.google.com/uc?export=view&id=1pWuB_af0v9Lp57xmby9Hb0_5bQZ43Kpu)

실행 결과는 RN 컬럼이 ROWNUM 번호를 의미하고, bno가 역순으로 정렬된것을 볼 수 있다.

실행계획을 보면 rownum <= 20 조건 실행 -> 실행된 결과 내에서 rn > 10 조건을 이용하므로 2페이지 데이터를 출력하게 된다.

**최종적인SQL**

```sql

<select id="listPage" resultType="org.zerock.domain.BoardVO">
  <![CDATA[
    select
      bno, title, content, writer, viewcont, regdate
    from
      (
        select
          /*+INDEX_DESC(tbl_board, pk_board)*/
          rownum rn, bno, title, content, writer, viewcnt, regdate
        from tbl_board
        where rownum <= #{page} *#{perPageNum} --1. 1~해당페이징의 최대값까지 출력가능한지(rownum특성상 1부터 뽑아야되서 1차적으로 1~필요한 데이터까지)
        and bno > 0
      )
      where rn > (#{page}-1)*#{perPageNum} -- 2. 해당페이징의 최소값보다 큰것까지(1번 데이터중에 필요한 최소값보다 큰것까지 골라옴)
  ]]>
</select>


```
### upload

**<iframe>을 이용한 파일 업로드의 결과 처리**

파일을 필요할 때마다 추가하는 형태로 처리하기위해 , 업로드 된 결과 역시 현재의 창에서 바로 사용할 수 이도록 하는 것이 좋다.

<form> 태그의 경우 기본이 브라우저의 창에서 전송이 일어나기 때문에 화면 전환을 피할 수 없다는 점인데, <form> 태그에 tartget 속성을 주고,

<ifrmae>을 이용하면 화면 전환을 없앨 수 있다.

1.<form>태그의 전송은 화면에 포함된 <iframe>으로 전송
2.결과 페이지는 <iframe> 내에 포함되므로 화면의 변화 없음
3.결과 페이지에서 다시 바깥쪽(parent)의 javascript 함수 호출

```html

//uploadResult.jsp

<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<%@page session="false"%>

<script>
var result = '${savedName}';
parent.addFilePath(result);
</script>

//uploadForm.jsp

<%@ page language="java" contentType="text/html; charset=UTF-8"
	pageEncoding="UTF-8"%>
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN"
"http://www.w3.org/TR/html4/loose.dtd">
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
<title>Insert title here</title>

<style>
iframe {
	width: 0px;
	height: 0px;
	border: 0px
}
</style>
</head>
<body>


	<form id='form1' action="uploadForm" method="post"
		enctype="multipart/form-data" target="zeroFrame">
		<input type='file' name='file'> <input type='submit'>
	</form>

	<iframe name="zeroFrame"></iframe>

	<script>
		function addFilePath(msg) {
			alert(msg);
			document.getElementById("form1").reset();
		}
	</script>


	<!-- 	<form id='form1' action="uploadForm" method="post"
		enctype="multipart/form-data">
		<input type='file' name='file'> <input type='submit'>
	</form>
 -->
</body>
</html>


//uploadAjax.jsp

<%@ page language="java" contentType="text/html; charset=UTF-8"
	pageEncoding="UTF-8"%>
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN"
  "http://www.w3.org/TR/html4/loose.dtd">
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
<title>Insert title here</title>

<style>
.fileDrop {
	width: 100%;
	height: 200px;
	border: 1px dotted blue;
}
small {
	margin-left: 3px;
	font-weight: bold;
	color: gray;
}
</style>
</head>
<body>

	<h3>Ajax File Upload</h3>
	<div class='fileDrop'></div>

	<div class='uploadedList'></div>

	<script src="//code.jquery.com/jquery-1.11.3.min.js"></script>
	<script>
		$(".fileDrop").on("dragenter dragover", function(event) {//dragenter는 드래그해서 대상에 들어오는순간, dragover는 올려놓는동안 계속
			event.preventDefault();//이거 없으면 드래그시 화면이 넘어가버린다.
		});
		$(".fileDrop").on("drop", function(event){
			event.preventDefault();

      //jQuery를 이용하는 경우 순수한 DOM 이벤트가 아니기 때문에, event.originalEvent를 이용해서 순수한 원래의 DOM 이벤트를 가져온다.
      //event.dataTransfer는 이벤트와 같이 전달된 데이터를 의미하고, 그 안에 포함된 파일데이터를 찾기위해 dataTransger.files를 이용한다.
			var files = event.originalEvent.dataTransfer.files;

			var file = files[0];
			//console.log(file);

      //Ajax는 전통적인 form 태그와는 다르기 때문에 과거는 주로 문자열 전송시에만 사용했지만, html5의 FormData 객체를 사용하면
      //form태그로 만든 데이터의 전송 방식과 동일하게 파일 데이터를 전송할 수있다.
			var formData = new FormData();

			formData.append("file", file);


      //Ajax를 POST 방식으로 이용하지만, $.post()를 사용하지않고 굳이 $.ajax()를 이용해서 여러 가지 옵션을 지정한다.
      //jQuery의 $.ajax()를 이용해서 FormData 객체에 있는 파일 데이터를 전송하기 위해서는 위 코드에 나와있는 'processData'와 contentType옵션을
      //반드시 false로 지정해야만 한다. 이 두개옵션은 form을 사용한 파일업로드와 동일하게 해주는 역할을 한다.

			$.ajax({
				  url: '/uploadAjax',
				  data: formData,
				  dataType:'text',
				  processData: false, //데이터를 일반적인 query String 으로 변환할 것인지를 결정. 기본은 true, 다른형식으로 보내기 위할떄는 false
				  contentType: false, //기본값은 application / x-www-form-unlencoded , 파일의 경우 multipart/form-data로 전송하기 위해 false로 지정
				  type: 'POST',
				  success: function(data){

					  var str ="";

					  if(checkImageType(data)){
						  str ="<div><a href=displayFile?fileName="+getImageLink(data)+">"
								  +"<img src='displayFile?fileName="+data+"'/>"
								  +"</a><small data-src="+data+">X</small></div>";
					  }else{
						  str = "<div><a href='displayFile?fileName="+data+"'>"
								  + getOriginalName(data)+"</a>"
								  +"<small data-src="+data+">X</small></div></div>";
					  }

					  $(".uploadedList").append(str);
				  }
				});
		});
		$(".uploadedList").on("click", "small", function(event){

				 var that = $(this);

			   $.ajax({
				   url:"deleteFile",
				   type:"post",
				   data: {fileName:$(this).attr("data-src")},
				   dataType:"text",
				   success:function(result){
					   if(result == 'deleted'){
						   that.parent("div").remove();
					   }
				   }
			   });
		});


/* 		
$(".fileDrop").on("drop", function(event) {
	event.preventDefault();

	var files = event.originalEvent.dataTransfer.files;

	var file = files[0];
	//console.log(file);
	var formData = new FormData();

	formData.append("file", file);

	$.ajax({
		  url: '/uploadAjax',
		  data: formData,
		  dataType:'text',
		  processData: false,
		  contentType: false,
		  type: 'POST',
		  success: function(data){

			  var str ="";

			  console.log(data);
			  console.log(checkImageType(data));

			  if(checkImageType(data)){
				  str ="<div><a href='displayFile?fileName="+getImageLink(data)+"'>"
						  +"<img src='displayFile?fileName="+data+"'/></a>"
						  +data +"</div>";
			  }else{
				  str = "<div><a href='displayFile?fileName="+data+"'>"
						  + getOriginalName(data)+"</a></div>";
			  }

			  $(".uploadedList").append(str);
		  }
		});			
});	 */
function getOriginalName(fileName){
	if(checkImageType(fileName)){
		return;
	}

	var idx = fileName.indexOf("_") + 1 ;
	return fileName.substr(idx);

}
function getImageLink(fileName){

	if(!checkImageType(fileName)){
		return;
	}
	var front = fileName.substr(0,12);
	var end = fileName.substr(14);

	return front + end;

}
/* 		$(".fileDrop").on("drop", function(event) {
			event.preventDefault();

			var files = event.originalEvent.dataTransfer.files;

			var file = files[0];
			//console.log(file);
			var formData = new FormData();

			formData.append("file", file);

			$.ajax({
				  url: '/uploadAjax',
				  data: formData,
				  dataType:'text',
				  processData: false,
				  contentType: false,
				  type: 'POST',
				  success: function(data){

					  alert(data);

				  }
				});

		}); */

	function checkImageType(fileName){

		var pattern = /jpg|gif|png|jpeg/i;

		return fileName.match(pattern);

	}


	</script>

</body>
</html>

```
### uploadcontrollerajax

```java

//UploadController.java의 일부

@ResponseBody
 @RequestMapping(value ="/uploadAjax", method=RequestMethod.POST,
                 produces = "text/plain;charset=UTF-8")// 한국어를 정상적으로 전송하기 위한 간단한 설정.
 public ResponseEntity<String> uploadAjax(MultipartFile file)throws Exception{

   logger.info("originalName: " + file.getOriginalFilename());


   return
     new ResponseEntity<>(
         UploadFileUtils.uploadFile(uploadPath,
               file.getOriginalFilename(),
               file.getBytes()),
         HttpStatus.CREATED);
 }
```
### fileuploadclass

static 기능을 호출해서 파일을 업로드 할 수 있도록 하는 UploadFileUtils 클래스를 설계한다.

파일저장경로(uploadPath)
원본 파일의 이름(originalName)
파일 데이터(byte[])

3개의 데이터를 파라미터로 전송받는 uploadFile()함수를 작성한다.

과정
1.UUID - 고유값생성
2.UUID와 결합한 업로드 파일이름 생성
3.파일이 저장될 '/년/월/일' 정보 생성
4.업로드 기본경로(uploadPath)와 /년/월/일 폴더 생성
5.기본경로 + 폴더경로 + 파일이름으로 파일저장

폴더생성부분의 함수

```java
private static String calcPath(String uploadPath){

   Calendar cal = Calendar.getInstance();
   //  /년도
   String yearPath = File.separator+cal.get(Calendar.YEAR);//File.separator는 윈도우에서는 \ 유닉스계열은 /이 된다.경로 구분자.
 // /년도/몇월
   String monthPath = yearPath +
       File.separator +
       new DecimalFormat("00").format(cal.get(Calendar.MONTH)+1); //달을 두자리수로 포맷
// /몇월/몇일
   String datePath = monthPath +
       File.separator +
       new DecimalFormat("00").format(cal.get(Calendar.DATE));

   makeDir(uploadPath, yearPath,monthPath,datePath);

   logger.info(datePath);

   return datePath;
 }

 private static void makeDir(String uploadPath, String... paths){// ...은 동일타입 다중파라미터받는 ellipsis라는 녀석이다.

   if(new File(paths[paths.length-1]).exists()){ //.exists는 파일이 실제로 존재하는지 여부를 확인한다.
     return;
   }

   for (String path : paths) {

     File dirPath = new File(uploadPath + path);

     if(! dirPath.exists() ){ //존재하면 인듯.
       dirPath.mkdir();//폴더생성
     }
   }
 }
```
### thumnail

```java
//UploadFileUtils.java 의 일부

  private static  String makeThumbnail(
              String uploadPath, //기본경로
              String path, //년/월/일 폴더
              String fileName)throws Exception{ // 업로드된 파일이름

                // BufferdImage는 실제 이미지가 아닌 메모리상의 이미지를 의미하는 객체다.
                // 원본파일을 메모리상으로 로딩하고, 정해진 크기에 맞게 작은 이미지 파일에 원본 이미지를 복사한다.

      //파일읽기      
    BufferedImage sourceImg =
        ImageIO.read(new File(uploadPath + path, fileName));
        //리사이즈
    BufferedImage destImg =
        Scalr.resize(sourceImg,
            Scalr.Method.AUTOMATIC,
            Scalr.Mode.FIT_TO_HEIGHT,100);
            //썸네일 파일이름
    String thumbnailName =
        uploadPath + path + File.separator +"s_"+ fileName;

    File newFile = new File(thumbnailName);
    //.이후에 확장자명 string 자르기(확장자명만 뽑기)
    String formatName =
        fileName.substring(fileName.lastIndexOf(".")+1);

//확장자명을 대문자로 변경하고 쓴다.
    ImageIO.write(destImg, formatName.toUpperCase(), newFile);
    return thumbnailName.substring(
        uploadPath.length()).replace(File.separatorChar, '/'); //치환하는 이유는 브라우저에서 윈도우의 경로로 사ㅛㅇ하는 \문자가 정상적인 경로로 인식되지 않기 때문에 '/'로 치환해준다.
  }


```

```java
//MediaUtils.java

//MediaUtils는 확장자를 가지고 이미지 타입인지를 판단해 주는 역할을 하는데, 별도 클래스로 있는 이유는 다운or이미지보여주기 를 결정하기 위해서ㅓ
public class MediaUtils {

	private static Map<String, MediaType> mediaMap;

	static{

		mediaMap = new HashMap<String, MediaType>();		
		mediaMap.put("JPG", MediaType.IMAGE_JPEG);
		mediaMap.put("GIF", MediaType.IMAGE_GIF);
		mediaMap.put("PNG", MediaType.IMAGE_PNG);
	}

	public static MediaType getMediaType(String type){

		return mediaMap.get(type.toUpperCase());
	}
}

// 최종적으로 파일을 업로드 처리하는 코드
//UploadFileUtils.java 의 일부

public static String uploadFile(String uploadPath,
                             String originalName,
                             byte[] fileData)throws Exception{

   UUID uid = UUID.randomUUID();

   String savedName = uid.toString() +"_"+originalName;

   String savedPath = calcPath(uploadPath);

   File target = new File(uploadPath +savedPath,savedName);

   FileCopyUtils.copy(fileData, target);

   String formatName = originalName.substring(originalName.lastIndexOf(".")+1);

   String uploadedFileName = null;

   if(MediaUtils.getMediaType(formatName) != null){
     uploadedFileName = makeThumbnail(uploadPath, savedPath, savedName);
   }else{
     uploadedFileName = makeIcon(uploadPath, savedPath, savedName);
   }

   return uploadedFileName;

 }

    private static  String makeIcon(String uploadPath,
          String path,
          String fileName)throws Exception{

        String iconName =
            uploadPath + path + File.separator+ fileName;

        return iconName.substring(
            uploadPath.length()).replace(File.separatorChar, '/');
      }


```
---

### uploadcontroller

```java

//UploadController.java 의 일부
//드래그앤 드롭시 호출되어 파일업로드 하고 이름을 리턴하는 메소드
@RequestBody
@RequestMapping(value = "/uploadAjax", method=RequestMethod.POST, produces = "text/plain; charset=UTF-8")
public ResponseEntity<String> uploadAjax(MultipartFile file)throws Exception{
  logger.info("originalName: " + file.getOriginalFilename());

  return
    new ResponseEntity<>(
      UploadFileUtils.uploadFile(uploadPath,file.getOriginalFilename(),
                                            file.getBytes()),
      HttpStatus.CREATED);

}

//
//1.파일이름->태그만들기
//2.파일데이터전송->MIME타입(이미지,일반파일)

@ResponseBody
@Requestmapping("/displayFile") //localhost:8080/displayFile?filaName=/년/월/일/파일명
public ResponseEntity<byte[]> displayFile(String fileName)throws Exception{

  InputStream in = null;
  ResponseEntity<byte[]> entity = null;

  logger.info("FILE NAME : " + fileName);

  try{
    String formatName = fileName.substring(fileName.lastIndexOf(".")+1);

    MediaType mType = MediaUtils.getMediaType(formatName); //JPG,PNG,GIF

    HttpHeaders headers = new HttpHeaders();

    in = new FileInputStream(uploadPath+fileName);

    if(mType != null){ //Image
      headers.setContentType(mType);
    }else{ //Image가 아닐떄
      fileName = fileName.substring(filaName.indexOf("_")+1);
      headers.setContentType(MediaType.APPLICATION_OCTET_STREAM); //이 MIME타입으로 다운로드창을 연다
      headers.add("Content-Disposition", "attachment; filename=\""+ new String(fileName.getBytes("UTF-8"), "ISO-8859-1")+"\"");
    }

      entity = new ResponseEntity<byte[]>(IOUtils.toByteArray(in), //실제로 데이터를 읽는 부분이 IOUTils.toByteArray(in)-> 데이터전송+상태코드+헤더
        headers,
        HttpStatus.CREATED);

  }catch(Exception e){
    e.printStackTrace();
    entity = new ResponseEntity<byte[]>(HttpStatus.BAD_REQUEST);
  }finally{
    in.close();
  }
  return entity;
}


```
---
### jspfileoutput

Ajax로 호출된 결과를 이미지나 파일의 링크로 생성하여 업로드한 파일을 다운로드 하거나 확인한다.

1.Ajax의 결과로 전송된 파일이 이미지 타입의 파일인지 구분
2.이미지 타입인 경우 <img> 태그를 생성한다. 만들어진 <img> 태그를 원하는 요소에 넣어준다.
3.이미지 타입이 아닌 경우 <div> 태그와 <a> 태그를 이용해서 처리한다.

```html
 <!-- uploadAjax.jsp 의 일부  -->

 function checkImageType(fileName){ <!-- 전송받은 문자열이 이미지 파일인지 확인하는 함수 -->

   var pattern = /jpg$|gif$|png$|jpeg$/i; <!-- i의 의미는 대,소문자의 구분없음. -->

   return fileName.match(pattern);

 }


 $(".fileDrop").on("drop", function(event){
			event.preventDefault();

			<!-- var files = event.originalEvent.dataTransfer.files;

			var file = files[0];
			console.log(file);

			var formData = new FormData();

			formData.append("file", file); -->

			$.ajax({
				  url: '/uploadAjax',
				  data: formData,
				  dataType:'text',
				  processData: false,
				  contentType: false,
				  type: 'POST',
				  success: function(data){

					  var str ="";

					  if(checkImageType(data)){
						  str ="<div><a href=displayFile?fileName="+getImageLink(data)+">"
								  +"<img src='displayFile?fileName="+data+"'/>"
								  +"</a><small data-src="+data+">X</small></div>";<!--  삭제버튼(x표시) -->
					  }else{
						  str = "<div><a href='displayFile?fileName="+data+"'>"
								  + getOriginalName(data)+"</a>"
								  +"<small data-src="+data+">X</small></div></div>";
					  }

					  $(".uploadedList").append(str);
				  }
				});
		});

     <!-- 일반 파일이름 길게 출력되는것을 줄이기 위한 기능작성 -->

     function getOriginalName(fileName){
       if(checkImageType(fileName)){
         return;
       }

       var idx = fileName.indexOf("_")+1;
       return fileName.substr(idx);
     }

     <!-- 이미지파일 원본 링크 찾기  
          (이미지인경우 다운로드가 아닌 실제 원본 이미지 데이터를 가져와야만 해서, 현재 보여지는 이미지인 썸네일에서 s_경로를 제거해서 원래의 이미지 파일을 가져온다.)
  -->

   function getImageLink(fileName){

     if(!checkImageType(fileName)){<!-- 이미지 파일이 아니면 return -->
       return;
     }
<!-- 12,13 부분이 s_(썸네일 부부분일것) -->
     var front = fileName.substr(0,12);
     var end = fileName.substr(14);

     return front + end;

   }
```
---

### uploadcontrollerdelete

>이미지 파일이 확인되면 원본 파일을 먼저 삭제하고, 이후에 파일을 삭제하는 방식으로 작성한다.

```java
@ResponseBody
@RequestMapping(value="/deleteFile", method=RequestMethod.POST)
public ResponseEntity<String> deleteFile(STring fileName){

  logger.info("delete file: " + fileName);

  String formatName = fileName.substring(fileName.lastIndexOf(".")+1);
}
```
