- [Spring-Mybatis]
  - [MyBatis 설정](#mybatisSetting)    


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
  - [UploadController에서 Ajax 처리하기](#uploadcontrollerajax)
  - [파일업로드용 클래스 설계](#fileuploadclass)
  - [썸네일 이미지 생성코드](#thumnail)
  - [UploadController의 재구성](#uploadcontroller)
  - [JSP에서 파일출력하기](#jspfileoutput)
  - [UploadController의 삭제처리](#uploadcontrollerdelete)
  - [JSP에서 첨부파일 삭제처리](#jspdelete)
  - [게시물등록과 파일업로드](#boardfileupload)
  - [첨부파일용 템플릿 추가하기](#template)
  - [조회 페이지와 파일 업로드](#readpagefileupload)
  - [게시물의 수정, 삭제 작업의 파일업로드](#boardupload)
  - [(momstouch)프로젝트경로설정](#projectlocation)
- [인터셉터]
  - [HandlerInterceptor](#handlerinterceptor)

- [sqlSession](#133)
- [Exception처리](#222)
- [RedirectAttributes](#redirectattributes)
- [Uri생성 - UriComponentBuilder](#uricomponentbuilder)
- [검색처리와 동적SQL](#310)
- [동적SQL문의 추가](#327)
- [RestController와 Ajax](#346)
- [댓글처리와 REST](#362)
- [REST 방식의 ReplyController](#377)
- [HiddenMethod의 활용](#385)
- [댓글의 페이징처리](#389)
- [화면에서의 Ajax 호출](#395)
- [JS템플릿-Handlebars](#handlebars)
-

- [properties사용](#properties)


---

- [Upload]
  - [파일업로드 설정](#fileuploadsetting)
  - [이미지축소 - imgScalr](#imgScalr)
  - [FileCopyUtils,UUID](#FileCopyUtils)  
  - [Drag & Drop Ajax 파일업로드](#dropfileupload)
  - [년/월/일 폴더생성](#makedir)
  - [파일다운로드처리](#filedownload)
  - [미디어타입 체크](#checkMediaType)
  - [삭제로직](#deletelogic)
  - [handlebars 템플릿](#handlebarsTemplate)
  - [폼 submit 제어](#formsubmitcontrol)
  - [읽기페이지-첨부파일 가져오기](#readpagefileload)
  - [원본이미지 큰화면 띄우기](#originalimage)
  - [글삭제-업로드파일삭제](#boarduploadfiledelete)

- [Interceptor]
  - [인터셉터 설정](#interceptorsetting)
  - [HandlerMethod](#handlerMethod)
  - [세션쿠키](#sessioncookie)
  - [로그인여부](#login)
  - [기존 URI 세션에 담아두기](#savedest)
  - [자동로그인](#autologin)
  - [자동 로그인 여부에 따른 인터셉터의 인증](#autologinauth)
  - [로그아웃 처리](#logout)


---

# Paging

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
> 시작페이지 번호가 1이 아니라면(11페이지부터) 이전(prev) 으로 갈 수 있는 링크가 제공되야 한다.

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
        <c:out value="${pageMaker.cri.page == idx?' class =active':''}"/>>  <!-- <li class=active> or <li> 이런식으로된다  -->
        <a href="listPage${pageMaker.makeQuery(idx)}">${idx}></a> <!-- 만약 앞에 listpage이런거 안붙이고 ${pageMaker.makeQuery(idx)} 이런식으로 명시할경우 현재 페이지기준으로 파라미터가 붙는다.-->
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
# upload

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
// /년도/몇월/몇일
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

     if(! dirPath.exists() ){ //존재하지 않으면인듯.(폴더)
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

// *uploadFIle(파일업로드) 와 thumnail 부분이 헷갈릴수 있다. 그 과정을 분석해보면
// 1. Drop->파일추출->FileCopyUtils.copy-> 로컬파일저장
// 2. ImageIO.read로 로컬파일을 읽어서 -> ImgScalr 로 이미지 축소-> ImageIO.write(썸네일을 로컬에 저장)

// 최종적으로 파일을 업로드 처리하는 코드
//UploadFileUtils.java 의 일부

public static String uploadFile(String uploadPath,
                             String originalName,
                             byte[] fileData)throws Exception{

   UUID uid = UUID.randomUUID();

   String savedName = uid.toString() +"_"+originalName;

   String savedPath = calcPath(uploadPath);//calcPath() - 폴더 없으면 만들고 , 리턴으로 path값

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
    //업로드하는 메소드호출
      UploadFileUtils.uploadFile(uploadPath,file.getOriginalFilename(),
                                            file.getBytes()),
      HttpStatus.CREATED);

}

//
//1.파일이름->태그만들기
//2.파일데이터전송->MIME타입(이미지,일반파일)

//UploadController의 일부
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
								  +"</a><small data-src="+data+">X</small></div>";<!--  삭제버튼(x표시)  -> jquery로 $(".uploadedList").on("click", "small", function) 해서 ajax로 url - deleteFile, data - {fileName:$(this).attr("data-src") 해서 사용한다}-->
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
한다}
```java
//파일이름 -> 확장자분리 -> 미디어타입검사(이미지인지)
//jquery로 $(".uploadedList").on("click", "small", function) 해서 ajax로 url - deleteFile, data - {fileName:$(this).attr("data-src") 해서 사용
@ResponseBody
@RequestMapping(value="/deleteFile", method=RequestMethod.POST)
public ResponseEntity<String> deleteFile(STring fileName){

  logger.info("delete file: " + fileName);

  String formatName = fileName.substring(fileName.lastIndexOf(".")+1);

  MediaType mType = MediaUtils.getMediaType(formatName);

  if(mType != null){ //이미지 타입 일떄
    String front = filaName.substring(0,12);
    String end = fileName.substring(14);
    new File(uploadPath + (front+end).replace('/', File.separatorChar)).delete();
  }

  new File(uploadPath + fileName.replace('/', File.separatorChar)).delete();

  return new ResponseEntity<String>("deleted", HttpStatus.OK);
}
```

### jspdelete

JSP에서 Ajax를 사용해서 파일의 이름을 컨트롤러에 전달해야 한다.


```js
<!-- uploadAjax.jsp의 일부 -->

$(".uploadedList").on("click", "small" , function(event){

  var that = $(this); //jquery의 $(this)는 이벤트 발생요소를 말한다.

  $.ajax({
    url:"deleteFile",
    type:"post"
    data: {fileName:$(this).attr("data-src")},
    dataType:"text",
    success:function(result){
      if(result == 'deleted'){
        that.parent("div").remove(); //첨부파일의 실제 파일삭제후 화면에서의 div를 삭제
      }
    }
  })

})
```

### boardfileupload

**게시물 등록의 파일 업로드**

```sql
create table tbl_attach(
  fullName varchar(150) not null,
  bno int not null,
  regdate timestamp default now(),
  primary key(fullName)
);

//모든 첨부파일 정보는 특정 게시물과 관련이 있으므로, bno 칼럼을 생성하고 이는 외래키(foregin key)로 참조해서 사용한다.
alter table tbl_attach add constraint fk_board_attach foreign key (bno) references tbl_board (bno);
```

>등록 작업에도 트랜잭션 처리가 필요해진다. 게시물등록시 tbl_board 의 insert와 tbl_attach에도 어떤 첨부파일을 사용하게 되는지를 저장해야하기 때문.

**BoardDAO의 변화**

tbl_attach 테이블에 첨부파일 추가 하는 기능이 추가되는데 **주의점** 은

새롭게 생성된 게시물의 번호가 필요하다는 점이다.

즉 **등록작업은 한 번에 게시물의 테이블인 tbl_board 테이블에 내용이 추가되는것 + tbl_attach 테이블에도 게시물번호를 같이 저장하게 된다.**

문제는 tbl_board 테이블의 설계는 게시물의 번호인 bno 칼럼이 'auto_increment'로 설계되어서 게시물의 등록 시점에 게시물 번호가 생성된다는 점이다.

```
//note
오라클의 경우 시퀀스를 사용하기 때문에 시퀀스명.currval을 이용하면 되지만,
MYSQL의 auto_increment의 경우는 데이터가 등록될 때 칼럼의 값이 생성되므로 처리 방식을 달리해야만 한다. MySQL 의경우 바로 이전의 등록된 번호는
LAST_INSERT_ID()를 이용할 수 있다.
(실제 파일업로드는 드래그앤드롭 즉시 이루어지지만, DB작업은 regist메소드에 트랜잭션으로 게시판작업과 업로드작업이 함께 순서대로 이루어짐)
```

```java
//BoardServiceImpl의 일부
@Transactional
  @Override
  public void regist(BoardVO board) throws Exception{ //BoardVO에 게시글내용 등과 첨부파일이 모두있는상태에서 submit됬을것.

    dao.create(board);

    String[] files = board.getFiles();

    if(files == null) { return; }

    for (String fileName : files){
      dao.addAttach(fileName);
    }
  }
````

### template
**첨부파일용 템플릿 추가하기**

첨부파일을 보여주는 HTML 코드는 조금 복잡하기 때문에 handlebars를 이용해서 템플릿으로 작성한다.(이미지파일,일반파일)

```html
<!-- register.jsp 의 일부-->

<script src="https://cdnjs.cloudflare.com/ajax/libs/handlebars.js/3.0.1/handlebars.js"></script>

<script id="template" type="text/x-handlebars-template">
  <li>
    <span class="mailbox-attachment-icon has-img"><img src="{{imgsrc}}"alt="Attachment"></span>
    <div class="mailbox-attachment info">
      <a href="{{getLink}}" class="mailbox-attachment-name">{{fileName}}</a>
      <a href="{{fullName}}" class="btn btn-default btn-xs pull-right delbtn"><i class="fa fa-fw fa-remove"></i></a>
    </div>
  </li>
</script>

```
>처리과정: Drop-> 서버에 업로드된 파일의 이름 -> getFIleInfo()를 통한 정보추출- JavaScript 객체생성-> handlebars 적용 -> html 생성 -> 화면적용

```js
//register.jsp의 일부

var template = Handlebars.compile($("#template").html()); //template를 뽑아와서 틀을 세팅해놓는것이다. 위의 형식에 맞게.. 그리고 이후에 메소드호출에 파라미터 넘겨서 사용.

$(".fileDrop").on("dragenter dragover", function(event){
	event.preventDefault();
});


$(".fileDrop").on("drop", function(event){
	event.preventDefault();

  //drop시 들어온 이벤트-파일추출-form객체에 파일추가
	var files = event.originalEvent.dataTransfer.files;

	var file = files[0];
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

        //upload.js에 있는 메소드로 템플릿에 필요한 객체를 생성하여 리턴한다.
			  var fileInfo = getFileInfo(data);

			  var html = template(fileInfo);

			  $(".uploadedList").append(html);
		  }
		});
});

//upload.js의 일부
//템플릿에 필요한 객체를 생성하여 리턴한다.
function getFileInfo(fullName){ //data파라미터가 넘어온 file정보일건데 '년/월/일(s_)UUID_파일명.확장자' 일것이다.

  //fileName:파일명.확장자 / imgsrc:보여질썸네일경로(이미지,일반파일다름) / getLink:실제파일경로(원본이미지,실제파일)
	var fileName,imgsrc, getLink;
  //fileLink:UUID_파일명.확장자
	var fileLink;

	if(checkImageType(fullName)){//이미지여부체크
		imgsrc = "/displayFile?fileName="+fullName;
		fileLink = fullName.substr(14);//UUID_파일명.확장자

		var front = fullName.substr(0,12); // /2015/07/01/
		var end = fullName.substr(14);//UUID_파일명.확장자

		getLink = "/displayFile?fileName="+front + end;

	}else{
		imgsrc ="/resources/dist/img/file.png";
		fileLink = fullName.substr(12);//UUID_파일명.확장자
		getLink = "/displayFile?fileName="+fullName;
	}
	fileName = fileLink.substr(fileLink.indexOf("_")+1);//파일명.확장자(파일링크에서 UUID부분을 지움)

	return  {fileName:fileName, imgsrc:imgsrc, getLink:getLink, fullName:fullName};

}

```
**<form> 태그의 submit 처리**

> - [ ] (1.Drop->fileupload) (파일업로드는 게시물과는 별도로 따로 일어남)
> - [x] (2.submit->board register)(참고로 DB에 등록될떄는 게시물의 등록과 파일업로드정보가 함께 일어남(한로직에))

최종적인 submit이 일어나게 되면, 서버에는 사용자가 업로드 한 파일의 정보를 같이 전송해 줘야 한다. 이에 대한 처리는 업로드 된 파일의 이름을
<form> 태그의 내부로 포함시켜서 전송하는 방식을 이용한다.

```js
//register.js의 일부
$("#registerForm").submit(function(event){
  event.preventDefault();

  var that = $(this);

  var str = "";

  $(".uploadedList .delbtn").each(function(index){//조상 자손 선택자 -> uploadedList안에 있는 모든 delbtn요소들
    str += "<input type='hidden' name='files["+index+"]' value='"+$(this).attr("href") +"'>"; //attr(요소):href값을 뽑아옴
  });

  that.append(str);

  that.get(0).submit();

});

```

## readpagefileupload

**조회 페이지와 파일 업로드**

조회페이지 - 삭제관련기능은 없애고, 등록된 파일을 다운로드 하거나 원본 파일을 볼 수 있는 기능이 제공되야.

게시물이 보여진 후 , Ajax를 이용해서 첨부파일의 정보를 가져오고, 화면에 출력하는 형태로 작성.

(자세한건 생략)
```sql
select fullname from tbl_attach where bno = #{bno} order by regdate
```

```java
//SearchBoardController의 일부
//Ajax로 호출되는 특정 게시물의 첨부파일 처리
@RequestMapping("/getAttach/{bno}")
  @ResponseBody
  public List<String> getAttach(@PathVariable("bno")Integer bno)throws Exception{
    return service.getAttach(bno);
  }
```

```js

// readPage.jsp의 일부
var bno = ${boardVO.bno};
var template = Handlebars.compile($("#templateAttach").html());

$.getJSON("/sboard/getAttach/"+bno, function(list)){// 게시판 url과 별개로 비동기식으로 controller 요청해서 데이터를 뽑아온다.
  $(list).each(function(){

    var fileInfo = getFileInfo(this);

    var html = template(fileInfo);

    $(".uploadedList").append(html);

  })
});


//조회페이지의 템플릿은 삭제버튼에 대한 코드가 없다.
<script id="templateAttach" type="text/x-handlebars-template">
<li data-src='{{fullName}}'>
  <span class="mailbox-attachment-icon has-img"><img src="{{imgsrc}}" alt="Attachment"></span>
  <div class="mailbox-attachment-info">
	<a href="{{getLink}}" class="mailbox-attachment-name">{{fileName}}</a>
	</span>
  </div>
</li>                
</script>  


```

**원본 이미지의 조회와 다운로드 처리**

조회 화면은 업로드한 파일을 원본 그대로 확인하거나, 다운로드 할 수 있게 처리를 해야 한다.
일반 파일의 경우 컨트롤러에서 자동으로 MIME 타입을 다운로드 하기 때문에 처리할 것이 없지만,
이미지의 경우 현재 화면이 전환되면서 이미지가 보여지기 떄문에 이벤트 처리가 필요. -> css를 이용해서 화면의 맨 앞쪽으로 보여지게 처리, 화면상에 숨겨져 있는 <div>를 작성하고, 클릭하면 큰 크기로 보여주게 한다.

```html
//readPage.jsp의 일부(이미지 파일일때 처리)

//숨겨져 있는<div>(썸네일 클릭시 원본을 보여줄 div)
<div class='popup back' style="display:none;"></div>
  <div id="popup_front" class='popup front' style="display:none;">
  <img id="popup_img">
</div>

<style type="text/css">
  .popup {position: absolute;}
  .back {background-color: gray; opacity:0.5; width: 100%; height: 300%; overflow:hidden; z-index:1101;}
  .front{z-index:1110; opacity:1; boarder:1px; margin: auto;}
  .show{position:relative; max-width: 1200px; max-height:800px; overflow:auto;}
</style>

//클릭 -> 이벤트의 href추출 -> 이미지여부체크 -> 숨겨진 div에 src추가 -> show 처리
// uploadedList < - / (template처리해서 전달) < - mailbox-attach-info a / #popup_img  숨겨진 div의 이미지 부분
$(".uploadedList").on("click", ".mailbox-attachment-info a", function(event){

    var fileLink = $(this).attr("href");

    if(checkImageType(fileLink)){

      event.preventDefault();

      var imgTag = $("#popup_img");
      imgTag.attr("src", fileLink);

      console.log(imgTag.attr("src"));

      $(".popup").show('slow');
      imgTag.addClass("show");


    }
});
//원본 이미지 클릭시 -> div다시숨김
$("#popup_img").on("click", function(){

  $(".popup").hide('slow');

});

```

## boardupload

게시물 수정화면은 첨부파일을 삭제후 새로운 파일을 업로드 할수있다.
그에 대한 간단한 처리방법으로, 기존의 업로드를 모두 삭제하고, 새롭게 첨부파일과 관련된 정보를 다시넣는다.

```
(dao,mapper 구체적 코드 생략)
delete from tbl_attach where bno = #{bno} //기존 업로드된 첨부파일 모두 삭제

insert into tbl_attach(fullname,bno) values(#{fullName},#{bno}) // 모두삭제된 이후 새로 다시 업로드하는 파일
```

**수정작업에 대한 서비스의 트랜잭션 관리**

첨부파일이 존재할경우 수정은 1.원래게시물 수정+ 2.기존 첨부파일 목록 삭제 + 3.새로운 첨부파일 정보 입력이 함께 이뤄져야 하기 때문에 트랜잭션으로 처리한다.

```java
//BoardServiceImpl 의 일부 (파일삭제후-> 게시물 등록에서의 DB작업인듯)

@Transactional
  @Override
  public void modify(BoardVO board) throws Exception{
    dao.update(board); //1.

    Integer bno = board.getBno();

    dao.deleteAttach(bno);//2.

    String[] files = board.getFiles();

    if(files == null){ return; }

    for(String fileName : files){
      dao.replaceAttach(fileName, bno); //3.
    }
  }

```

**삭제 처리**
삭제 처리는 데이터베이스의 삭제와 업로드 되었던 첨부파일의 삭제 작업이 같이 진행된다.

```java
//UploadController의 일부

//첨부파일의삭제 (이 부분은 수정페이지에서 첨부파일 썸네일의 x버튼(delbtn)누를때 Ajax처리되서 호출되는 부분)

@ResponseBody
  @RequestMapping(value="/deleteAllFiles", method=RequestMethod.POST)
  public ResponseEntity<String> delete(@RequestParam("files[]")String [] files){

    logger.info("delete all files: " + files);

    if(files == null || files.length == 0){ //파일이 없을떄
      return new ResponseEntity<String>("deleted", HttpStatus.OK);
    }

    for (String fileName : files){ //each로 모두삭제
      String formatName = fileName.substring(fileName.lastIndexOf(".")+1);

      MediaType mType = MediaUtils.getMediaType(formatName);

      if(mType != null){ //이미지일때
        String front = fileName.substring(0,12);
        String end = filename.substring(14);
        new File(uploadPath + (front+end).replace('/', File.separatorChar)).delete(); //삭제
      }

      new File(uploadPath+ filename.replace('/', File.separatorChar)).delete();

    }
    return new ResponseEntity<String>("deleted", HttpStatus.OK);
  }

//BoardServiceImpl의 일부

/// 데이터베이스의 삭제 처리

//삭제작업의 경우 tbl_attach가 tbl_board를 참조하기 때문에 반드시 첨부파일과 관련된 정보부터 삭제하고, 게시글을 삭제 한다.
@Transactional
  @Ovveride
  public void remove(integer bno) throws Exception{
    dao.deleteAttach(bno);
    dao.delete(bno);
  }

```

**삭제 화면의 처리**

게시물의 삭제 시 Ajax를 이용해서 첨부파일의 삭제를 지시하고, <form> 방식을 이용해서 삭제를 진행한다.

```js

//readpage.jsp의 일부
$("#removeBtn").on("click", function(){
                    //댓글번호
  var replyCnt = $("#replycntSmall").html().replace(/[^0-9]/g,""); //숫자가 아닌값을 null값으로 대체한다는것(태그같은걸 말하는건지..)

  if(replyCnt > 0){
    alert("댓글이 달린 게시물을 삭제할 수 없습니다.");
    return;
  }

  var arr = [];
  $(".uploadedList li").each(function(index){
    arr.push($(this).attr("data-src")); //data-src를 뽑아와서 arr에 push한다.
  });

  if(arr.length > 0){
    $.post("/deleteAllFiles",{files:arr}, function(){ //post방식으로 ajax수행 - 파일삭제

    });
  }

  formObj.attr("action", "/sboard/removePage");
  formObj.submit();
});

```

String savePath = "product_images";

   ServletContext context = session.getServletContext();
   String uploadFilePath = context.getRealPath(savePath);


## projectlocation
**프로젝트 절대경로 구하기**

```java
@Override
	public String uploadFile(String originalName, byte[] fileData,String uploadPath,HttpServletRequest request) throws IOException {

		// UUID uid = UUID.randomUUID();
    //
		// String savedName = uid.toString() + "_"+originalName;
		// //
		String savePath = "resources/product_images";
		HttpSession session = request.getSession();
		ServletContext context = session.getServletContext();
		String uploadFilePath = context.getRealPath(savePath);//getRealPath()가 어플리케이션의 기본 절대경로를 의미한다.
		System.out.println("테스트:"+uploadFilePath);
    //
		// File target = new File(uploadPath,savedName);
    //
		// FileCopyUtils.copy(fileData, target);

		return savedName;
	}

  //console
  테스트:C:\workspace-sts-3.8.0.RELEASE\MomsTouch\src\main\webapp\resources\product_images
```

---

## HandlerInterceptor

인터셉터는 서블릿의 필터와 유사( 특정 URI 접근에 제어하는 용도로 사용된다.
)하지만, 인터셉터가 spring의 context내부에 접근해있어서 스프링객체와의
접근이 용이하다는 차이점이 있다.

사전,후 처리는 AOP 기능으로 가능하지만, 일반적으로 컨트롤러를 이용할떄는 HandlerInterceptor 를 사용하는경우가
많다고 한다.

HandlerInterceptor의 메소드
- preHandle(request, response, handler)
:컨트롤러 동작의 전처리

- postHandle(request,response,modelAndView)
:컨트롤러 동작의 후처리(Spring MVC의 FrontController인 DispatcherServlet이 화면을 처리하기 전에 동작)

- afterCompletion(request,response,handler,exception)
:MVC의 FrontController인 DispatcherServlet의 화면처리 완료된 상태에서의 처리

handlerInterceptor는 인터페이스로 정의, handlerInterceptorAdaptor는 인터페이스를 구현한 추상 클래스로 설계되어 있다.
일반적으로 Adaptor라는 의미가 인터페이스를 구현하여 사용하기 쉽게 하는 용도가 많다.
```
class SampleIntercepter extends HandlerInterceptorAdapter
```
```xml


<beans:bean id="authInterceptor" class="org.zerock.interceptor.AuthInterceptor"></beans:bean> //register 등 특정 url접근시 로그인 여부 체크->로그인페이지이동

	<beans:bean id="loginInterceptor" class="org.zerock.interceptor.LoginInterceptor"></beans:bean>//loginPost에 맵핑을 가로챈다. session 기존의것 지우고, id/pw 체크후 세션저장한다. 여러 옵션에 따라 ..

//아래 <interceptors> 쓰기위해서는 xml네임스페이스에 spring mvc관련 설정이 되어 있어야
<interceptors>

		<interceptor>
			<mapping path="/user/loginPost" /> //이매핑에 대해서
			<beans:ref bean="loginInterceptor" />
		</interceptor>


 	  <interceptor>  <!-- 로그인해야 접근할수있게   -->
	    <mapping path="/sboard/register/*"/>
	    <mapping path="/sboard/modifyPage/*"/>
	    <mapping path="/sboard/removePage/*"/>
	    <mapping path="/sboard/mail/listmail/*"/>
	    <mapping path="/sboard/readPage/like*"/>
	    <mapping path="/sboard/list/supporter"/>

	    <beans:ref bean="authInterceptor"/>
	  </interceptor>

	</interceptors>
```

```java

//LoginInterceptor - 인터셉터 상속받아 오버라이딩한 메소드 posthandle,prehandle

public class LoginInterceptor extends HandlerInterceptorAdapter {

  private static final String LOGIN = "login";
  private static final Logger logger = LoggerFactory.getLogger(LoginInterceptor.class);

  @Override
  public void postHandle(HttpServletRequest request,
      HttpServletResponse response, Object handler,
      ModelAndView modelAndView) throws Exception {

    HttpSession session = request.getSession();

    ModelMap modelMap = modelAndView.getModelMap();
    Object userVO = modelMap.get("userVO");

    if (userVO != null) {

      logger.info("new login success");
      session.setAttribute(LOGIN, userVO);

      if (request.getParameter("useCookie") != null) {

        logger.info("remember me................");
        Cookie loginCookie = new Cookie("loginCookie", session.getId());
        loginCookie.setPath("/");
        loginCookie.setMaxAge(60 * 60 * 24 * 7);
        response.addCookie(loginCookie);
      }
      // response.sendRedirect("/");
      Object dest = session.getAttribute("dest");

      response.sendRedirect(dest != null ? (String) dest : "/");
    }
  }

  @Override
  public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {

    HttpSession session = request.getSession();

    if (session.getAttribute(LOGIN) != null) {
      logger.info("clear login data before");
      session.removeAttribute(LOGIN);
    }

    return true;
  }
}

```


```java

public class AuthInterceptor extends HandlerInterceptorAdapter {

  private static final Logger logger = LoggerFactory.getLogger(AuthInterceptor.class);

  @Inject
  private UserService service;

  @Override
  public boolean preHandle(HttpServletRequest request,
      HttpServletResponse response, Object handler) throws Exception {

    HttpSession session = request.getSession();   


    if(session.getAttribute("login") == null){

      logger.info("current user is not logined");

      saveDest(request);

      Cookie loginCookie = WebUtils.getCookie(request, "loginCookie");

      if(loginCookie != null) {

        UserVO userVO = service.checkLoginBefore(loginCookie.getValue());

        logger.info("USERVO: " + userVO);

        if(userVO != null){
          session.setAttribute("login", userVO);
          return true;
        }

      }

      response.sendRedirect("/user/login");
      return false;
    }
    return true;
  }  
  //이전의 페이지에 대한 저장 -> loginpost(loginInterceptor)에서 사용.
//AuthInterceptor에서 로그인여부체크->비로그인시 로그인-> 로그인인터셉터 과정에서
//마지막에 sendredirect'/'으로 기본으로 되어있어서 직전페이지로 가지않고 홈으로 가는것에 대해 대안으로 아래 코드가 있다

  private void saveDest(HttpServletRequest req) {

    String uri = req.getRequestURI();

    String query = req.getQueryString();

    if (query == null || query.equals("null")) {
      query = "";
    } else {
      query = "?" + query;
    }

    if (req.getMethod().equals("GET")) {
      logger.info("dest: " + (uri + query));
      req.getSession().setAttribute("dest", uri + query);
    }
  }
}

```


---



###### junitmethod

jUnit 메소드
-

- @Test: 메소드위에 @Test 애노테이션을 추가하면 jUnit은 해당 메소드를 테스트용 코드로 간주

- @Before:모든 테스트 이전에 준비해야할 내용을 작성한 메소드 위해 추가하는 애노테이션

- @After: 테스트 작업이 끝난 후 자동으로 실행되는 메소드

- org.junit.Assert.assertxxx: 테스트 중에 발생되는 값을 확신하는 용도로 사용. (특정 값이나 상태를 예상하고, 체크하는 용도)

---


###### springsettingal

스프링 프로젝트 세팅 Al
-

pom.xml
- jdbc
- jUnit
- Mybatis
- MyBatis-spring
- spring-jdbc
- spring-test
- jackson-databind
- javax.servlet-api 3.1.0
테스트(연결)

root-context.xml - Namespace
-aop
-context
-jdbc
-mybatis-spring

root-context.xml
- DataSource 관련 빈, 프로퍼티 세팅

테스트(DataSource inject해서 연결)

@ MyBatis연결

sqlSessionFactory 빈 생성

mybatis-config.xml 파일추가

root-context.xml의 sqlSessionFactory 빈안에
mybatis-config.xml  속성추가

MyBatis 연결 테스트

root-conetxt.xml sessionFactory 빈안에

각 mapper들 인식할수있게 mapperLoactions 설정

root-context 에서 sqlSession관련 설정

root-context 에 component-scan base-pakage="~.persistence"

pom.xml 파일에 log4jdbc-log4j2 라이브러리 추가

root-context에 driverclassName의 값을 net.sf.log4jdbc.DriverSpy로 변경, url도 log4jdbc추가

resources 폴더에 log4jdbc.log4j2.properties파일, logback.xml 파일 추가

web.xml에 ufc8 인코딩 설정

mybatis-config.xml 에 typeAliases 설정(mybatis mapper에 매번 resultType,parameterType을 패키지까지 포함된 클래스명을 일일이 작성하기 번거로우므로 alias지정 -> ex org.~.momain 생략하고 클래스이름만 ~VO 이렇게 사용가능)



---


###### 133

sqlSession
-

mybatis-spring에서 제공하는 SqlSessionTemplate는 MyBatis의 SqlSession 인터페이스를 구현한 클래스로
트랜잭션관리, 쓰레드 처리의 안정성을 보장,
데이터베이스의 연결과 종료를 책임진다.

SqlSessionTemplate는 SqlSEssionFactory를 생성자로 주입해서 설정한다.

```xml
<bean id="sqlSession" class="org.mybatis.spring.SqlSessionTemplate" destroy-method="clearCache">
	<constructor-arg name="sqlSessionFactory"
	ref="sqlSessionFactory"></constructor-arg>
</bean>
```

각 DAOImple 에서 주입받아서 사용하도록 한다.

@Inject
private SqlSession sqlSession;


SqlSession에서 쓸수있는 기능

- selectOne
- selectList
- selectMap
- insert
- update
- delete


---

RedirectAttribute  - addFlashAttribute()
브라우저까지 전송하지만 uri상에서 보이지않는 숨겨진 데이터의 형태로 전송

<c:forEach items="${list}" var="boardVO">
	<tr>
		<td><fmt:formatDate pattern="yyyy-MM-dd HH:mm" value="${boardVO.regdate}"/></td>
</c:forEach>

충분한양의 데이터넣기

insert into tbl_board(title,content,writer)(select title,content,writer from tbl_board);

---


###### 222

Exception처리
-

Spring MVC - Controller의 Exception처리방법

- @ExceptionHandler
- @ControllerAdvice
- @ResponseStatus를 사용한 Http 상태코드 처리

범용적인 방법이 @ControllerAdvice
:호출되는 메소드의 발생된 모든 Exception을 처리

클래스에 @ControllerAdvice라는 애너테이션 처리
각 메소드에 @ExceptionHandler를 이용해서 적절한 타입의 Exception을 처리


---

###### redirectattributes

RedirectAttributes 사용
-

```java
  @RequestMapping(value = "/removePage/{category}", method = RequestMethod.POST)
	  public String remove(@RequestParam("bno") int bno, SearchCriteria cri, RedirectAttributes rttr,@PathVariable("category")String category) throws Exception {

	    service.remove(bno);

	    rttr.addAttribute("page", cri.getPage());///addFlashAttribute와 달리 URL 파라미터에 붙여주는 방식이다. 그래서 list페이지로 redirect될때 파라미터로 받아낼수있게 cri 넘긴다.
	    rttr.addAttribute("perPageNum", cri.getPerPageNum());
	    rttr.addAttribute("searchType", cri.getSearchType());
	    rttr.addAttribute("keyword", cri.getKeyword());

	    rttr.addFlashAttribute("msg", "SUCCESS");// 파라미터에 표기되지않은채 1회성으로 데이터를 전달할떄 사용

	    return "redirect:/sboard/list/"+category;
	  }

```

 ![](https://drive.google.com/uc?export=view&id=)

 ---


###### uricomponentbuilder

 UriComponentBuilder Uri생성
 -

 ```java
 public String makeQuery(int page){

 UriComponents uriComponents = UriComponentsBuilder.newInstance().queryParam("page",page)
 				.queryParam("perPageNum", cri.getPerPageNum())
 			.build();

 return uriComponents.toUriString();		
 }
 ```

 ```html
 <a href='/board/readPage${pageMaker.makeQuery(pageMaker.cri.page)}&bno=${boardVO.bno}'>
 ```





 ---


 ###### 310

 검색처리와 동적SQL
 -

 ```html
 <select name="searchType">
 	<option value="n"
 		<c:out value="${cri.searchType == null? 'selected' : ''}"/>>
 	---</option>
 	<option value="t"
 		<c:out value="${cri.searchType eq 't'?'selected' : ''}"/>>
 	Title</option>
 	...
 </select>
 ```
 n:검색조건x
 t:제목으로검색
 c:내용으로검색
 w:작성자로검색
 tc:제목이나내용
 cw:제목이나 작성자
 tcw:제목이나 내용 혹은 작성자

 ```java
 //PageMaer.java

 public String makeSearch(int page){

 	UriComponents uriComponents = UriCompoonentsBuilder.newInstance().queryParam("page",page)
 	.queryParam("perPageNum", cri.getPerPageNum())
 	.queryparam("searchType", ((SearchCriteria) cri).getSearchType())
 	.queryParam("keywrod", encoding(((SearchCriteria) cri).getKeyword())).build();

 	return uriComponents.toUriString();
 }

 private String encoding(String keyword){
   if(keyword == null || keyword.trim().length() == 0){
     return "";
   }
   try{
     return URLEncoder.encode(keyword, "UTF-8");
   }catch(UnsupportedEncodingException e){
     return "";
   }
 }
 //(검색키워드가 URL이 인코딩된 형태로 작성된다)
 ```


 ```js

   $(document).ready(function(){

     $('#searchBtn').on("click",function(event){
       self.location = "list"
       + '${pageMaker.makeQuery(1)}'
       + "&searchType="
       + $("select option:selected").val()
       + "&keyword=" + encodeURIComponent($('#keywordInput').val());
     });


   });

 ```

 * MyBatis 동적 SQL

 - if
 - choose(when,otherwise)
 - trim(where,set)
 - foreach

  ![](https://drive.google.com/uc?export=view&id=1i2i1CQS1rBhTA-xPi8Xm30GE3iXIEMR7)



  ---


  ###### properties

  properties사용
  -

  >'properties는 자바코드상에서 변경될수있는 값을 주입해서 사용하려는 목적, 상수로 쓰려는 목적으로 사용되어 왔던것 같다.
  xml로 대체가능한 옛날 방식이라는것 같기도하다. egov에서 xml의 값을 properties에서 주입받는식으로 쓰길래 따라해봤다.'

  기본적으로 new - properties 파일생성이 없어서, 일반파일로 생성후
  확장자명만 바꿔서 사용했더니 , 유니코드로 값을 저장한다
  이것을 한글이나 영문으로 변환해서 보여주는 플러그인이
  "properties editor"

  Help -> Install New Software

  Location: http://propedit.sourceforge.jp/eclipse/updates/

  설치하면된다.

  ```
  #-----------------------------------------------------------------------
  #
  #   setting.properties : 시스템
  #   
  #-----------------------------------------------------------------------
  #   1.  key = value 구조입니다.
  #   2.  key값은 공백문자를 포함불가, value값은 공백문자를 가능
  #   3.  key값으로 한글을 사용불가,   value값은 한글사용이 가능
  #   4.  줄을 바꿀 필요가 있으면 '\'를 라인의 끝에 추가(만약  '\'문자를 사용해야 하는 경우는 '\\'를 사용)
  #   5.  Windows에서의 디렉토리 표시 : '\\' or '/'  ('\' 사용하면 안됨)
  #   6.  Unix에서의 디렉토리 표시 : '/'
  #   7.  주석문 처리는  #사용
  #   8.  value값 뒤에 스페이스가 존재하는 경우 서블릿에서 참조할때는 에러발생할 수 있으므로 trim()하거나 마지막 공백없이 properties 값을 설정할것
  #-----------------------------------------------------------------------



  # ipAddress - local
  Local.IpAddress = localhost

  # ipAddress - server
  Server.IpAddress = jeongwon.me

  //xml에서 로컬작업환경 - 서버환경 변경으로 인한 value값 변경시
  properties 에서 각 local,server 의 key를 변경해주는 방법으로 사용


  //root-context.xml


  <bean id="configProperties" class="org.springframework.beans.factory.config.PropertyPlaceholderConfigurer">
          <property name="locations">
              <list>
              	<value>/WEB-INF/spring/setting.properties</value>

              </list>
          </property>
      </bean>

  <property name="url" value="${Local.Url}"></property>
  			<property name="username" value="${Local.UserName}"></property>
  			<property name="password" value="${Local.Password}"></property>

  ```


  ---


  ###### 327

  동적SQL문의 추가
  -

  if test 는 test 속성에 있는 표현식이 boolean으로 나오는 결과야 한다.
  ```
  <sql id="search">
  		<if test="cri.searchType != null">
  			<if test="cri.searchType == 't'.toString()">
  				and title like CONCAT('%', #{cri.keyword}, '%')
  			</if>
  			<if test="cri.searchType == 'c'.toString()">
  				and content like CONCAT('%', #{cri.keyword}, '%')
  			</if>
  			<if test="cri.searchType == 'w'.toString()">
  				and writer like CONCAT('%', #{cri.keyword}, '%')
  			</if>
  			<if test="cri.searchType == 'tc'.toString()">
  				and ( title like CONCAT('%', #{cri.keyword}, '%') OR content like
  				CONCAT('%', #{cri.keyword}, '%'))
  			</if>
  			<if test="cri.searchType == 'cw'.toString()">
  				and ( content like CONCAT('%', #{cri.keyword}, '%') OR writer like
  				CONCAT('%', #{cri.keyword}, '%'))
  			</if>
  			<if test="cri.searchType == 'tcw'.toString()">
  				and ( title like CONCAT('%', #{cri.keyword}, '%')
  				OR
  				content like CONCAT('%', #{cri.keyword}, '%')
  				OR
  				writer like CONCAT('%', #{cri.keyword}, '%'))
  			</if>
  		</if>
  	</sql>

  ```
  ```
  	<select id="listSearch" resultType="BoardVO">
  <![CDATA[  
    select *
    from tbl_board
    where bno > 0 and category = #{category}
  ]]>

  		<include refid="search"></include>
  ```


###### 346

RestController와 Ajax
-

REST는 특정한 URI는 반드시 그에 상응하는 데이터 자체라는것을 의미하는 방식이다. (/boards/123)

REST API 는 외부에서 URI를 통해서 사용자가 원하는 정보를 제공하는 방식이다.

스프링은 3버전부터 @ResponseBody 어노테이션으로 본격적으로 REST방식의 처리를 지원하기 시작하여
스프링 4부터 @RestController가 본격적으로 소개됨.

@RestController는 기존의 JSP와같은 뷰를 만들어내는게 목적이 아닌 REST 방식의 데이터 처리를 위해서 사용한다.
(모든메소드가 자동으로 @ResponseBody처리됨)

스프링3까지는 @Controller 에 데이터 자체의 서비스하려면
메소드나 리턴타입에 @ResponseBody를 사용.

스프링4부터는 컨트롤러 자체의 용도를 지정한다

(MessageConverter가 데이터를 가공해서 브라우저에 전송,
(과거에는 개발자가 직접 MIME 타입지정, 데이터를 만드는 방식에서 자동화된 처리방식이다.))

데이터 자체를 반환하는데, 주로 사용되는것은 단순 문자열, JSON,XML 등으로 나눈다.

1) 단순문자열일 경우

@RestController에서 문자열 데이터는 기본적으로 브라우저에는 'text/html'타입으로 처리된다.

```
public String sayHello(){
  return "Hello World"; //경로가 아닌 문자열
}
```

2) 객체를 JSON으로 반환하는 경우

별도의 처리가 없어도 객체는 자동적으로 JSON으로 처리 될 수있다.

```java
public class SampleVO{
  ...
}

//Controller
public SampleVO sendVO(){
  SampleVO vo = new SampleVO();
  vo.set~~
  ...
return vo;
}
```

error message: 406 Not Accepetable 상태 에러.
-> 스프링에서 객체 변환에 실패

jackson-databind 라이브러리를 추가하지 않아서 그렇다.

jackson-databind 는 객체를 JSON 타입의 데이터로 변환하거나,반대의 작업을 할 때 사용.

* ResponseEntity 타입

@RestContoller는 별도의 뷰를 제공하지 않는 형태로 서비스를 실행-> 예외적인 상황에서 문제가 발생할 수 있다.

웹의 경우 HTTP의 상태코드가 이러한 정보를 나타낸다.

스프링에서 제공하는  ResponseEntity 타입은 개발자가 직접결과데이터 + HTTP의 상태코드를 직접 제어할수 있따.

즉 상태코드를 데이터와 함께 전송할 수있다.

```
~ ...

return new ResponseEntity<>(list,HttpStatus.NOT_FOUND);
```

@RestController를 이용해서 결과 데이터만서버에서 제공하는 방식은 데이터를 이용하는 클라이언트의 기능이 많아 질 경우 사용된다.
ex) Ajax


---


###### 362

댓글처리와 REST
-


REST 방식을 사용하는 원칙
1. URI가 원하는 리소스를 의미한다.
ex) /baord/123 123번게시물 조회

2. URI에는 식별할 수 있는 데이터를 같이 전달하는 것이 일반적
(HTTP의 전송방식(method)이 실제 작업의 종류를 의미
ex) get(조회), delete(삭제), post(등록), put(수정or등록) ,patch(put 방식대용)

REST는 화면이 없어서 결과를 체크하기 위해 REST 클라이언트 프로그램을 통해 테스트가 가능한데,

Chrome 브루아저의 앱으로 존재하는 Advanced REST Client나 PostMan 등의 프로그램이 있다.

* REST와 Ajax

REST방식이 많이 쓰이는 형태는  Ajax와 결합 될때이다.



---

###### 377

REST 방식의 ReplyController
-

ReplyController는 @RestController를 이용해서 작성한다.


|  URI | 전송방식  | 설명  |
|---|---|---|
| /replies/ + JSON 데이터  | POST  |  새로운댓글등록 |
|  /replies/ + JSON 데이터   |  PUT,PATCH |  댓글 수정 |
| /replies/댓글번호  | DELETE  |  댓글 삭제 |


REST 방식 처리에 사용하는 애노테이션

@PathVariable - URI 경로에서 원하는 데이터를 추출하는 용도

@RequestBody - 전송된 JSON 데이터를 객체로 변환해주는 애노테이션, @ModelAttribute와 유사한 역할을 하지만 JSON에서 사용된다는 점이 차이.

* 등록처리

```
@RestController
@RequestMapping("/replies")
public class ReplyController{

	@Inject
	private ReplyService service;

	@Requestmapping(value = "", method = RequestMethod.POST)
	public ResponseEntity<String> register(@RequestBody ReplyVO vo){

	ResponseEntity<String> entity = null;
	try{
		service.addReply(vo);
		entity = new ResponseEntity<String>("SUCCESS",HttpStatus.OK);
	}catch(Exception e){
		e.printStackTrace();
		entity = new ResponseEntity<String>(e.getMessage(), HttpStatus.BAD_REQUEST);
	}
	return entity;		
	}
}
```

* 특정 게시물의 전체 댓글 목록

```java
@RequestMapping(value = "/all/{bno}", method = RequestMethod.GET)
public ResponseEntity<List<ReplyVO>> list(@PathVariable("bno") Integer bno){

	ResponseEntity<List<ReplyVO>> entity = null;
	try{
			entity = new ResponseEntity<>(service.listReply(bno),HttpStatus.OK);

	}catch(Exception e){
		e.printStackTrace();
		entity = new ResponseEntity<>(HttpStatus.BAD_REQUEST);
	}
	return entity;
}
```

Advanced REST Client로 테스트 했을떄

/replies/all/6127

결과 화면에 JSON 타입으로 데이터가 전송된것을 확인가능하다.


* 수정 처리

REST 방식에서 update 작업은 PUT, PATCH 방식을 이요해서 처리한다.

일반적으로 전체 데이터 수정은 PUT, 일부 데이터 수정은 PATCH를 사용.


```java
@RequestMapping(value = "/{rno}", method = {RequestMethod.PUT, RequestMethod.PATCH})
public ResponseEntity<String> update(@PathVariable("rno") Integer rno, @RequestBody ReplyVO vo){

	ResponseEntity<String> entity = null;
	try{
		vo.setRno(rno);
		service.modifyReply(vo);

		entity = new REsponseEntity<String>("SUCCESS", HttpStatus.OK);
	}catch(Exception e){
		e.printStackTrace();
		entity = new ResponseEntity<String>(e.getMessage(), HttpStatus.BAD_REQUEST);
	}
	return entity;
}
```

---

###### 385

HiddenMethod의 활용
-

브라우저에 따라서 PUT, PATCH, DELETE 방식을 지원하지 않는 경우가 있다.

많은 브라우저가 GET, POST 방식만 지원하기 때문에 REST 방식을 제대로 사용하려면 브라우저가 지원하지 않는 상황에 대해서 고려해야 한다.

대부분의 해결책은 브라우저에서 POST 방식으로 전송하고, 추가적인 정보를 이용해서 PUT, PATCH, DELETE와 같은 정보를 같이 전송하는 것이다.

이를 'OverloadedPOST' 라고 한다.

Ajax를 이용해서 전송하는 경우 추가적인 정보는 아래와 같이 처리한다.

```
$.ajax({
	type:'delete',
	url:'/replies/'+rno
	headers: {
			"Content-Type" : "application/json",
			"X-HTTP-Method-Override": "DELETE" },
	dataType:'text',
	success:function(result){
		console.log("result: " + result);
		if(result == 'SUCCESS'){
			alert("삭제 되었습니다.");
			getPage("/replies/" + bno +"/"+replyPage);
		}
	}
});
```

<form> 태그를 이용해서 데이터를 전송하는 겨웅에는 POST 방식으로 전송하되, ```_method``` 라는 추가적인 정보를 이용한다.

스프링은 이를 위해 HiddenHttpMethodFilter라는 것을 제공한다.

이는 <form> 태그 내에서
```
<input type='hidden' name="_method" value='PUT'>
```
 과 같은 형태로 사용해서 GET/POST 방식만 지원하는 브라우저에서 REST 방식을 사용할 수 있도록 설정할 때 사용한다.
```
- POST + _method value=put' => PUT 방식

- POST +_method value='delete' => DELETE 방식
```

웹 애플리케이션 내에서 Filter로 설정해서 처리한다.

 ![](https://drive.google.com/uc?export=view&id=1-Z6A4kA-A0Dnx6xxNNF-jO-Q38rt9dyO)


---


###### 389

댓글의 페이징처리
-

REST 방식의 경우 전통적인 Model 객체에 데이터를 담지 않고 객체를 처리할 수 있기 때문에 파라미터가 조금 달리지긴 해도 간단히 처리가능하다.

(기존의 Criteria 사용)

컨트롤러의 경우 두 개의 @PathVariable을 이용해서 처리한다.

- '/replies'/게시물번호/페이지번호'

- GET 방식의 처리

 ![](https://drive.google.com/uc?export=view&id=1zeeV_wfwGpuH2t27dypyxAJlHQ1O3dVU)

Ajax로 호출 될것이므로 Model을 사용하지 못한다는 점을 신경써야한다.

전달해야 하는 데이터들을 담기 위해서 Map 타입의 객체를 별도로 생성해야한다.




---


###### 395

화면에서의 Ajax 호출
-

jsp 쪽에 jQuery 라이브러리를 사용할수 있게 처리해야한다.


@RestController의 경우 객체를 JSON 방식으로 전달하기 때문에 jQuery를 이용해서 호출할때는 getJSON()을 이용한다


Ajax 전체 목록에 대한 함수처리
```js
function getAllList(){

	$.getJSON("/replies/all/" + bno, function(data){

		var str ="";
		console.log(data.length);

		$(data).each(
			function(){
				str += "<li data-rno='"+this.rno+"' class='replyLi'>"
						+ this.rno + ":" + this.replytext
						+ "</li>";
			});

		$("#replies").html(str);
	});
}
```

사용자가 ADD REPLY 버튼을 누를때 이벤트 처리

```js

$("#replyAddBtn").on("click", function(){

		var replyer = $("#newReplyWriter").val();
		var replytext = $("#newReplyText").val();

		$.ajax({
			type : 'post',
			url : '/replies',
			headers : {
				"Content-Type" : "application/json",
				"X-HTTP-Method-Override" : "POST"
			},
			dataType : 'text',
			data : JSON.stringify({
				bno : bno,
				replyer : replyer,
				replytext : replytext
			}),
			success : function(result) {

				if (result == 'SUCCESS'){
					alert("등록 되었습니다.");
getAllList();
				}
			}
		});
	});
```

jQuery를 이용하여 $.ajax() 를 통해 서버를 호출한다.
전송하는 데이터는 JSON으로 구성된 문자열을 사용하고, 전송 받은 결과는 단순 문자열이다.

특이한 점은 jQuery가 제공하는 $.post() 등을 사용하지 않고, $.ajax()를 이용해서 다양한 옵션으로 구성된 것을 볼 수있다.

* jQuery의 $.post()를 사용하지 않는 이유

$.post()의 경우 일반적인 데이터 전송 방식에는 적합하다.
예를 들어 위의 호출을 $.post()를 이용하도록 작성하면 아래와 같다.

```js
$("#replyAddBtn").on("click", function(){

	var replyer = $("#newReplyWriter").val();
	var replytext = $("#newReplyText").val();

	$.post("/replies",
		{replyer:replyer, replytext:replytext},
		function(result){
			alert(result);
		}
);

});
```

이 코드를 실행한 결과는 제대로 호출되지 않고 415(지원되지 않는 타입)상태 코드가 전송된다.

이렇게 전송하는 경우 RestController에서는 @RequestBody 애너테이션이 제대로 처리되지 못하게 되는 문제가 생긴다.

@RequestBody는 JSON으로 전송된 데이터를 ReplyVO 타입의 객체를 변환해주는 역할을 하는데, 이때의 데이터는 일반 데이터가 아닌 JSON으로 구성된 문자열 데이터이다.

$.ajax()를 사용한 코드에서

HTTP 헤더정보에 'application/json'이라고 명시한 상태에서 데이터를 전송하고,

전송되는 데이터는 JSON.stringify()를 이용해 JSON 데이터를 구성해서 전송하므로 실제 전송시에는 아래와 같이 문자열이 전송된다.

{"bno":122875, "replyer":"user13","replytext" : "테스트 댓글입니다."}

즉 데이터를 전송할때와 동일하게 직접 문자열로 처리되어서 전송된다.


위의 리스트(getAllList())로 나온 리스트 화면에서

특정 댓글을 선택하면 수정과 삭제가 가능한 화면을 보여줄 필요가 있다.
이를 위해 getAllList() 내부의 각 댓글에 버튼을 추가한다.

```js
$(data).each(function(){
	str+= "<li data-rno='"+this.rno+"' class='replyLi'>"
	+ this.rno+":" + this.replytext+
	"<button>MOD</button></li>";
});
```

각 버튼에 대한 이벤트를 처리하는 작업은 다음과 같이 처리한다.


```js

$("#replies").on("click", ".replyLi button", function(){

	var reply = $(this).parent();

	var rno = reply.attr("data-rno");
	var replytext = reply.text();

	alert(rno + " : " + replytext);
});

```

* div에 댓글 보이기

```js

$("#replies").on("click", ".replyLi button", function(){

  var reply = $(this).parent();

  var rno = reply.attr("data-rno");
  var replytext = reply.text();

  $(".modal-title").html(rno);
  $("#replytext").val(replytext);
  $("#modDiv").show("slow");

});

```
* 삭제 호출하기

```js
$("#replyDelBtn").on("click", function(){

  var rno = $(".modal-title").html();
  var replytext = $("#replytext").val();

  $.ajax({
    type : 'delete',
    url : '/replies/' + rno,
    headers : {
      "Content-Type" : "application/json",
      "X-HTTP-Method-Override" : "DELETE"
    },
    dataType : 'text',
    success : function(result) {
      console.log("result: " + result);
      if(result == 'SUCCESS'){
        alert("삭제 되었습니다.");
        $("#modDiv").hide("slow");
        getAllLIst();
      }
    }
  });
});
```

* 수정 작업 처리하기
```js
  $("#replyModBtn").on("click",function(){

    var rno = $(".modal-title").html();
    var replytext = $("#replytext").val();

    $.ajax({
      type:'put',
      url:'/replies/'+rno,
      headers: {
        "Content-Type": "application/json",
        "X-HTTP-Method-Override": "PUT" },
      data:JSON.stringify({replytext:replytext}),
      dataType:'text',
      success:function(result){
        console.log("result: " + result);
        if(result == 'SUCCESS'){
          alert("수정 되었습니다.");
          $("#modDiv").hide("slow");
          getPageList(replyPage);
        }
      }
      }
    });

  });
```

* 댓글 페이지를 위한 <ul> 처리

```js
  function getPageList(page){

    $.getJSON("/replies/"+bno+"/"+page , function(data){

      console.log(data.list.length);

      var str ="";

      $(data.list).each(function(){
        str+= "<li data-rno='"+this.rno+"' class='replyLi'>"
        +this.rno+":"+ this.replytext+
        "<button>MOD</button></li>";
      });

      $("#replies").html(str);

      printPaging(data.pageMaker);

    });
  }

  function printPaging(pageMaker){
    var str = "";

    if(pageMaker.prev){
      str += "<li><a href= '"+(pageMaker.startpage -1)+"'> << </a></li>";

      for(var i=pageMaker.startPage, len = pageMaker.endPage; i < = len; i++){
        var str
        ...
      }
    }

  }
```
*



---


###### handlebars

JS템플릿-Handlebars
-

```
<script src="https://cdnjs.cloudflare.com/ajax/libs/handlebars.js/3.0.1/handlebars.js"></script>
```
예제

```js
<script>
    var source = "<h1><p>{{name}}</p> <p>{{userid}}</p> <p>{{addr}}</p></h1>";
    var template = Handlebars.compile(source);
    var data = {name:"홍길동", userid:"user00", addr:"조선 한양"};
    $("#displayDiv").html(template(data));


</script>
```
// ex2
```
<script id="template" type="text/x-handlebars-template">
    <span> {{ name }} </span>
    <div>
        <span> {{ userid }}</span>
        <span> {{ addr }}</span>
    </div>
</script>

<script>
    var source = $("#template").html();
    var template = Handlebars.compile(source);
    var data = {name:"홍길동", userid:"user00", addr:"조선 한양"};

    $("#displayDiv").html(template(data));

</script>
```
// ex3
```
<script id="template" type="text/x-handlebars-template">
    {{#each .}}
        <li class="replyLi">
            <div>{{rno}}</div>
            <div>{{replytext}}</div>
            <div>{{replydate}}</div>
        </li>
    {{/each}}
</script>

<script>
    var source = $("#template").html();
    var template = Handlebars.compile(source);
    var data = [
        {rno:1, replytext:'1번 댓글....', replydate:new Date()},
        {rno:2, replytext:'2번 댓글....', replydate:new Date()},
        {rno:3, replytext:'3번 댓글....', replydate:new Date()},
    ];
    $("#replies").html(template(data));
</script>
```

```js
적용
// 하나의 댓글 구성

<script id="template" type="text/x-handlebars-template">
{{#each .}}
<li class="replyLi" data-rno={{rno}}
<i class="fa fa-comments bg-blue"></i>
    <div class="timeline-item">
        <span class="time">
            <i class="fa fa-clock-o"></i>{{prettifyDate regdate}}
        </span>
        <h3 class="timeline-header"><strong>{{rno}}</strong> -{{replyer}}</h3>
        <div class="timeline-body">{{replytext}} </div>
            <div class="timeline-footer">
                <a class="btn tbn-primary btn-xs" data-toggle="modal" data-target="#modifyModal">Modify</a>
            </div>
        </div>
    </li>

{{/each}}
</script>

```




---


###### fileuploadsetting

파일업로드 설정
-

-사용자가 업로드 하는 시간에 따른 자동적인 폴더생성
-업로드 되는 파일의 고유이름 생성
-이미지 파일의 업로드시 썸네일 이미지 생성
-이미지 파일일 경우 서버에서 저장된 파일을 읽어서 적당한 MIME 타입으로 서비스
-일반 파일의 경우 다운로드 타입으로 파일 데이터 서비스


K: 파일업로드에 필요한 commons-fileupload , multipart 지원기능 사용을 위한 multipartResulver(스프링- CommonsMultipartResolver)

```xml
//pom.xml

		<dependency>
			<groupId>commons-fileupload</groupId>
			<artifactId>commons-fileupload</artifactId>
			<version>1.3.2</version>
		</dependency>


//servlet-context.xml

<beans:bean id="multipartResolver" class="org.springframework.web.multipart.commons.CommonsMultipartResolver">
		<beans:property name="maxUploadSize" value="10485760"></beans:property>
	</beans:bean>


<beans:bean id="uploadPath" class="java.lang.String">
		<beans:constructor-arg value="C:\\zzz\\upload">
		</beans:constructor-arg>
	</beans:bean>

//using controller(Resource=name firtst)
//@Resource(name = "uploadPath")
//private String uploadPath;
```


ex)
```html
<form id='form1' action="uploadForm" method="post" enctype="multipart/form-data">
	<input type='file' name='file'> <input type='submit'>
</form>
```
```java
	@RequestMapping(value="/uploadForm" ,method= RequestMethod.POST)
	public void uploadForm(MultipartFile file, Model model)throws Exception{
		
		logger.info("originalName:" + file.getOriginalFilename());
		logger.info("size: " + file.getSize());
		logger.info("contentType: " + file.getContentType());

}
```




---


###### imgScalr

이미지축소 - imgScalr
-


	<dependency>
			<groupId>org.imgscalr</groupId>
			<artifactId>imgscalr-lib</artifactId>
			<version>4.2</version>
		</dependency>


```java
	//썸네일생성
	private static String makeThumbnail(String uploadPath,String path, String fileName)throws Exception{
		//이미지 읽기
		BufferedImage sourceImg = ImageIO.read(new File(uploadPath + path, fileName));
		//썸네일생성
		BufferedImage destImg = Scalr.resize(sourceImg, Scalr.Method.AUTOMATIC, Scalr.Mode.FIT_TO_HEIGHT,100);
		//썸네일이름생성
		String thumbnailName = uploadPath + path + File.separator +"s_"+ fileName;
		//썸네일 이름으로 된 파일객체 생성
		File newFile = new File(thumbnailName);
		//포맷이름
		String formatName = fileName.substring(fileName.lastIndexOf(".")+1);
		//이미지쓰기
		ImageIO.write(destImg,  formatName.toUpperCase(), newFile);
		//이름반환, replace는 윈도우같은 OS 에서쓰는 ('\')에서 -> browser('/')에서 쓰는걸로 교체
		return thumbnailName.substring(uploadPath.length()).replace(File.separatorChar, '/');
	}
```



---


###### FileCopyUtils

FileCopyUtils , UUID
-

org.springframework.util 패키지

파일의 데이터를 파일로 처리, 복사하는 등의 작업

[](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/util/FileCopyUtils.html)


```java

	private String uploadFile(String originalName, byte[] fileData)throws Exception{
		
		UUID uid = UUID.randomUUID();
		
		String savedName = uid.toString() + "_"+ originalName;
		
		File target = new File(uploadPath,savedName);
		
		FileCopyUtils.copy(fileData,  target);
		
		return savedName;
		
	}
```



---


###### dropfileupload

Drag & Drop Ajax 파일업로드
-

> drop - get file - formdata - ajax (sendServer & getData to add htmlcode)

```js

//dragenter dragover 없으면 drop 작동하지 않았다.
$(".fileDrop").on("dragenter dragover", function(event) {
			event.preventDefault();
		});

		$(".fileDrop").on("drop", function(event){
			event.preventDefault();
			
//jQuery는 event가 순수 DOM이벤트가 아니므로 원래 DOM이벤트를 가져온다.
			var files = event.originalEvent.dataTransfer.files;
			
			var file = files[0];

			//console.log(file);
			
			var formData = new FormData();
			
			formData.append("file", file);
			
//옵션을 주기 위해 $.post() 대신 $.ajax() 사용 , processData,contentType을 false로 해야 form방식 파일업로드 
			$.ajax({
				  url: '/uploadAjax',
				  data: formData,
				  dataType:'text',
				  processData: false, 
				  contentType: false,
				  type: 'POST',
				  success: function(data){
					  
//... 이하 이미지/일반파일 여부에 따라 업로드 목록에  html코드 만들어 넣어주는 로직

}
```



---


###### makedir

년/월/일 폴더생성
-

```java

    //년 월 일 생성
    private static void calcPath(String uploadpath){
        Calendar cal = Calendar.getInstance();

        String yearPath = File.separator() + cal.get(Calendar.YEAR);
        String monthPath = yearPath + File.separator() + new DecimalFormat("00").format(cal.get(Calendar.MONTH)+1);
        String datePath = monthPath + File.separator() + new DecimalFormat("00").format(cal.get(Calendar.DATE));
        
        mkdir(uploadPath,yearPath+monthPath,datePath);
    }        

//폴더생성
    private static void mkdir(String uploadPath, String... paths){

        if(new File(uploadPath+paths[paths.length-1]).exists())
            return;

        for(String path : paths){

            File dirPath = new File(uploadPath+path);

            if(! dirpath.exists()){
                dirPath.mkdir();
            }
        }
    }
        
```


---


###### filedownload

파일다운로드처리
-

```java

  @ResponseBody
  @RequestMapping("/displayFile")
  public ResponseEntity<byte[]>  displayFile(String fileName)throws Exception{
    
    InputStream in = null; 
    ResponseEntity<byte[]> entity = null;
    
    logger.info("FILE NAME: " + fileName);
    
    try{

      String formatName = fileName.substring(fileName.lastIndexOf(".")+1);
  	//포맷이름으로 미디어타입추출      
      MediaType mType = MediaUtils.getMediaType(formatName);
      //헤더생성
      HttpHeaders headers = new HttpHeaders();
      //파일입력스트림생성
      in = new FileInputStream(uploadPath+fileName);
      
      //이미지면
      if(mType != null){
        headers.setContentType(mType);
      }else{//일반파일이면
        //(UUID_파일이름 - 파일이름 추출)
        fileName = fileName.substring(fileName.indexOf("_")+1);       
        headers.setContentType(MediaType.APPLICATION_OCTET_STREAM); //다운로드 미디어타입
        headers.add("Content-Disposition", "attachment; filename=\""+ 
          new String(fileName.getBytes("UTF-8"), "ISO-8859-1")+"\"");
      }

        entity = new ResponseEntity<byte[]>(IOUtils.toByteArray(in), 
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


###### checkMediaType

미디어타입 체크
-

```java
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

```


```js
<script>
    function checkImageType(fileName){
        var pattern = /jpg$|gif$|png$|jpeg$/i;

        return fileName.match(pattern);
    }
</script>
```



---


###### deletelogic

삭제로직
-

```js


// 업로드 목록- 파일옆 x클릭 -> 삭제 
		$(".uploadedList").on("click", "small", function(event){
			
				 var that = $(this);
				console.log($(this).attr("data-src"));
				
			   $.ajax({
				   url:"deleteFile",
				   type:"post",
				   data: {fileName:$(this).attr("data-src")}, // data-src에 들어있는 파일이름  값뽑아옴
				   dataType:"text",
				   success:function(result){
					   if(result == 'deleted'){ //성공시 서버에서 ResponseEntity에서 deleted를 반환하도록 했음
						   that.parent("div").remove(); //부모인 div를 코드를 삭제
					   }
				   }
			   });
		});
		
```

```java

    //삭제로직 (업로드목록-삭제버튼시 ajax로 호출됨)
  @ResponseBody
  @RequestMapping(value="/deleteFile", method=RequestMethod.POST)
  public ResponseEntity<String> deleteFile(String fileName){
    
    logger.info("delete file: "+ fileName);
    
    String formatName = fileName.substring(fileName.lastIndexOf(".")+1);
    //포맷이름으로 이미지 미디어타입이 있으면 뽑아옴
    MediaType mType = MediaUtils.getMediaType(formatName);
    
    //이미지파일이면 썸네일도삭제
    if(mType != null){      
      
      String front = fileName.substring(0,12);
      String end = fileName.substring(14);
      new File(uploadPath + (front+end).replace('/', File.separatorChar)).delete();
    }
    //이미지,일반파일의 삭제
    new File(uploadPath + fileName.replace('/', File.separatorChar)).delete();
    
    
    return new ResponseEntity<String>("deleted", HttpStatus.OK);
  }  
  
```





---


###### handlebarsTemplate

handlebars 템플릿
-


```js
<script src="https://cdnjs.cloudflare.com/ajax/libs/handlebars.js/3.0.1/handlebars.js"></script>

//템플릿 틀이 되는 부분
<script id="template" type="text/x-handlebars-template">
<li>
  <span class="mailbox-attachment-icon has-img"><img src="{{imgsrc}}" alt="Attachment"></span>
  <div class="mailbox-attachment-info">
	<a href="{{getLink}}" class="mailbox-attachment-name">{{fileName}}</a>
	<a href="{{fullName}}" 
     class="btn btn-default btn-xs pull-right delbtn"><i class="fa fa-fw fa-remove"></i></a>
	</span>
  </div>
</li>                
</script>    

```

```js
//틀을 setting 해놓는다.
var template = Handlebars.compile($("#template").html());


$(".fileDrop").on("dragenter dragover", function(event){
	event.preventDefault();
});


$(".fileDrop").on("drop", function(event){
	event.preventDefault();
	
	var files = event.originalEvent.dataTransfer.files;
	
	var file = files[0];

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
				//서버로부터 받은 data를 가지고 템플릿에 넣을 데이터를 가공해서 가져온다			  
			  var fileInfo = getFileInfo(data);
			  //가공한 데이터를 템플릿에 넣어 html 코드생성
			  var html = template(fileInfo);			  
			  $(".uploadedList").append(html);
		  }
		});	
});


...

function getFileInfo(fullName){
		
	var fileName,imgsrc, getLink;
	
	var fileLink;
	
	if(checkImageType(fullName)){
		imgsrc = "/displayFile?fileName="+fullName;
		fileLink = fullName.substr(14);
		
		var front = fullName.substr(0,12); // /2015/07/01/ 
		var end = fullName.substr(14);
		
		getLink = "/displayFile?fileName="+front + end;
		
	}else{
		imgsrc ="/resources/dist/img/file.png";
		fileLink = fullName.substr(12);
		getLink = "/displayFile?fileName="+fullName;
	}
	fileName = fileLink.substr(fileLink.indexOf("_")+1);
	
	//fileName:파일명.확장자 / imgsrc:썸네일 이미지표시 / getLink:실제 이미지링크 , fullName: 날짜(디렉터리)+UUID+_+파일명.확장자(fileName)
	return  {fileName:fileName, imgsrc:imgsrc, getLink:getLink, fullName:fullName};
	
}

}

```



---


###### formsubmitcontrol

폼 submit 제어
-

```js
// 폼 submit - 파일 업로드를 위한 file 코드처리
$("#registerForm").submit(function(event){
	event.preventDefault();
	
	var that = $(this);
	
	var str ="";
	
	//업로드목록하위 모든 delbtn요소들로 부터 href 속성값을 추출해 hidden타입으로 file html코드들을 만들어준다.
	$(".uploadedList .delbtn").each(function(index){
		 str += "<input type='hidden' name='files["+index+"]' value='"+$(this).attr("href") +"'> ";
	});
	
	that.append(str);

	// jQuery의 get(0)는 순수한 DOM객체를 얻어내기 위해 하용
	that.get(0).submit();
});
```



---


###### readpagefileload

읽기페이지-첨부파일 가져오기
-

```js
	// 읽기페이지-첨부파일 가져오기	
	// 서버로 부터 업로드된 파일리스트를 가져옴
	$.getJSON("/sboard/getAttach/"+bno,function(list){ 
		$(list).each(function(){
			//파일이름으로부터 템플릿에 필요한 데이터들 가공해서 가져옴
			var fileInfo = getFileInfo(this);
			//템플릿처리- html코드생성
			var html = template(fileInfo);
			
			 $(".uploadedList").append(html);
			
		});
	});
	
	
		
```



---


###### originalimage

원본이미지 큰화면 띄우기
-

```css
 <style type="text/css">
    .popup {position: absolute;}
    .back { background-color: gray; opacity:0.5; width: 100%; height: 300%; overflow:hidden;  z-index:1101;}
    .front { 
       z-index:1110; opacity:1; boarder:1px; margin: auto; 
      }
     .show{
       position:relative;
       max-width: 1200px; 
       max-height: 800px; 
       overflow: auto;       
     } 
  	
    </style>
```

```html
    <div class='popup back' style="display:none;"></div>
    <div id="popup_front" class='popup front' style="display:none;">
     <img id="popup_img">
    </div>
```

```js
	//업로드  클릭
	$(".uploadedList").on("click", ".mailbox-attachment-info a", function(event){
		
		//href 값을 가져옴
		var fileLink = $(this).attr("href");
		
		//이미지여부 검사
		if(checkImageType(fileLink)){
			
			event.preventDefault();
					
			//팝업 div에 src속성으로 href값 넣어줌
			var imgTag = $("#popup_img");
			imgTag.attr("src", fileLink);
			
			console.log(imgTag.attr("src"));
			
			//팝업 div를 show, 팝업 div에 css에 정의된 show가 적용되도록 addclass
			$(".popup").show('slow');
			imgTag.addClass("show");		
		}	
	});
	
	//show되어있는 팝업창 클릭시 숨김
	$("#popup_img").on("click", function(){
		
		$(".popup").hide('slow');
			
	});	  
```	



---


###### boarduploadfiledelete

글삭제-업로드파일삭제
-

```java
	//게시글삭제 - 업로드 파일삭제
	$("#removeBtn").on("click", function(){
		//댓글번호 , 숫자가 아닌값은 null값으로 대체함(태그를 말하는듯.)
		var replyCnt =  $("#replycntSmall").html().replace(/[^0-9]/g,"");
		
		if(replyCnt > 0 ){
			alert("댓글이 달린 게시물을 삭제할 수 없습니다.");
			return;
		}	
		
		var arr = [];
		//data-src를 뽑아서 arr에 push
		$(".uploadedList li").each(function(index){
			 arr.push($(this).attr("data-src"));
		});
		//ajax post방식으로 데이터 담아서 전송 - 업로드파일삭제
		if(arr.length > 0){
			$.post("/deleteAllFiles",{files:arr}, function(){
				
			});
		}
		//게시글의 삭제
		formObj.attr("action", "/sboard/removePage");
		formObj.submit();
	});	
```


---


###### interceptorsetting

인터셉터 설정
-

인터셉터와 필터는 특정 URI 접근을 제어하는 공통점이 있음.
필터는 웹 어플리케이션 영역내에서 존재하지만,
인터셉터는 스프링 컨텍스트에 존재하므로 spring내의 모든 객체에 접근이 가능하다.

HandlerInterceptor 인터페이스를 구현한 추상클래스인
HandlerInterceptorAdaptor를 상속해서 사용한다.

```java

public class SampleInterceptor extends HandlerInterceptorAdapter{

	
	@Override
	public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler,
			ModelAndView modelAndView) throws Exception {
		
		System.out.println("post handle...");
	
	}
	
	@Override
	public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler)
			throws Exception {
		
		System.out.println("pre handle...");
		
		return true;
	}
}
```

```xml

<beans:bean id="sampleInterceptor" 
class="com.jeongwon.interceptor.SampleInterceptor"></beans:bean>

	
	<interceptors>
		<interceptor>
			<mapping path="/doA"/>
			<mapping path="/doB"/>
			<beans:ref bean="sampleInterceptor"/>
		</interceptor>
	</interceptors>
```




---


###### handlerMethod

handlerMethod
-

인터셉터에서 메소드와 빈을 알 수있도록 하는 역할을 한다.

// handler는 HandlerMethod 타입으로 캐스팅 한후 원래 메소드와 객체(빈)를 확인 할 수있다.

```java
	@Override
	public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler)
			throws Exception {
		
		System.out.println("pre handle...");
		
		HandlerMethod method = (HandlerMethod) handler;
		Method methodObj = method.getMethod();
		
		System.out.println("Bean:" + method.getBean());
		System.out.println("Method: " + methodObj);
		
		return true;
	}
```





---


###### sessioncookie

세션쿠키
-

자물쇠 : HttpSession

열쇠 : Session Cookie

자물쇠들이 있는 보관함 : Session Repository

세션과다 -> 서버 성능 영향 -> 세션 자동 정리 기능
(web.xml에서 HttpSession의 timeout을 지정가능)

public class LoginInterceptor extends HandlerInterceptorAdapter{
	
	private static final String LOGIN = "login";
	private static final Logger logger = LoggerFactory.getLogger(LoginInterceptor.class);
	
	//로그인 전처리
	@Override
	public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler)
			throws Exception {
		//세션을 가져와서
		HttpSession session = request.getSession();
		//세션이 있으면 세션을 지워준다
		if(session.getAttribute(LOGIN) != null) {
			logger.info("clear login data before");
			session.removeAttribute(LOGIN);
		}
		return true;
	}
	//로그인 후처리
	@Override
	public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler,
			ModelAndView modelAndView) throws Exception {
		//세션을 가져와서 , userVO 객체를 가져오는 과정을 거친다
		HttpSession session = request.getSession();
		ModelMap modelMap = modelAndView.getModelMap();
		Object userVO = modelMap.get("userVO");
		
		//userVO가 있으면 세션에 넣어주고 리다이렉트한다.
		if(userVO != null) {
			logger.info("new login success");
			session.setAttribute(LOGIN, userVO);
			
			// 기존 URI 저장된게 있으면 redirect
			Object dest = session.getAttribute("dest");
			response.sendRedirect(dest != null ? (String)dest:"/");
		}
	}
}

```xml
//servlet-context.xml

	<beans:bean id="loginInterceptor" class="com.jeongwon.interceptor.LoginInterceptor"></beans:bean>
	
	<interceptors>
		<interceptor>
			<mapping path="/user/loginPost"/>
			<beans:ref bean="loginInterceptor"/>
		</interceptor>
	</interceptors>
	

```

// JSP에서 사용되는 EL의 경우 자동으로 HttpSession에 있는 'login'을 찾아서 사용하기때문에

${login.uid} 와 같은형태로 사용할 수있다.

<c:if test="${login.uid == boardVO.writer}">
/
<c:if test="${not empty login}">

---


###### login

로그인여부
-

로그인여부

```java
public class AuthInterceptor extends HandlerInterceptorAdapter {
	
	private static final Logger logger = LoggerFactory.getLogger(AuthInterceptor.class);
	
	// 로그인여부검사(인증)  
	@Override
	public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler)
			throws Exception {

		HttpSession session = request.getSession();
		//세션의 로그인 여부검사 - 비로그인시 로그인페이지로 리다이렉트
		if(session.getAttribute("login") == null) {
			
			logger.info("current user is not logined");
			
			//기존 URI 세션에 저장하기 (돌아가기위한)
			saveDest(request);

			response.sendRedirect("/user/login");
			return false;
		}
		return true;
	}
}
```

```xml
	
	<beans:bean id="authIntercepter" class="com.jeongwon.interceptor.AuthInterceptor"></beans:bean>

	<interceptors>
		<interceptor>
			<mapping path="/sboard/register" />
			<beans:ref bean="authInterceptor"/>
		</interceptor>
	</interceptors>

```


---


###### savedest

기존 URI 세션에 담아두기
-

```java
//AutoInterceptor.java

	//기존 URI 세션에 저장하기
	private void saveDest(HttpServletRequest req) {
		//uri와 쿼리스트링을 가져와서
		String uri = req.getRequestURI();
		String query = req.getQueryString();
		//쿼리스트링이 null이 아닐때 ?을 붙여준다
		if (query == null || query == "null") {
			query = "";
		}else {
			query = "?" + query;
		}
		//get메소드일때 세션에 담아준다(uri+query)
		if(req.getMethod().equals("GET")) {
		logger.info("dest : " + (uri + query));
		req.getSession().setAttribute("dest", uri + query);
		}
	}
```



---


###### autologin

자동로그인
-

자동로그인 - 특정쿠키전송, HttpSession에서 사용되는 세션쿠키와 다름 , 
개발자가 만들어내는 쿠키 -> 만료기한을 정할 수 있어 오래보관가능.

세션 = 서버 내부에서만 사용 / 쿠키는 브라우저-서버 주고받아 보안취약, 길이제한,문자열만 보관

모바일에서 매번 로그인 번거로움->
로그인 정보 오래 유지할수있는 쿠키가 다시 유행


```java
// postHandle 메소드(로그인 후처리)

...

//userVO가 있으면
		if(userVO != null) {
			
			//로그인에 성공, 세션에 담아준다
			logger.info("new login success!");
			session.setAttribute(LOGIN, userVO);
			
			// 자동로그인 기능을 사용하면
			if(request.getParameter("useCookie") != null) {
				//쿠키생성과 속성추가, 생성한 쿠키추가해주기
				logger.info("remember me....");
				Cookie loginCookie = new Cookie("loginCookie",session.getId());
				loginCookie.setPath("/");
				loginCookie.setMaxAge(60 * 60 * 24 * 7);
				response.addCookie(loginCookie);
			}
		
...

```

쿠키는 개발자도구 - Application 에서 전달됨을 확인할수 있다.
JSESSIONID는 톰캣에서 발행된 세션쿠기이고, loginCookie는 인터셉터에 의해 만들어진 쿠키이다.

브라우저 종료 세션끊김-> JSESSIONID인 세션쿠키가 없어도 loginCookie는 값을 유지한다.


   //로그인
    @Requestmapping(value="loginPost", method=RequestMethod.POST)
    public void loginPost(Model model,LoginDTO dto , HTtpSession session){
        
        UserVO vo = service.login(dto);
        //로그인 실패시 종료
        if(vo == null){return;}
        //자동로그인 활성화시에 시간을 지정하고 넘김
        if(dto.isUseCookie()){
            int amount = 60 * 60 * 24 * 7;
            Date sessionLimit = new Date(System.currentTimemillis() + (amount*1000));
            service.keepLogin(dto.uid, sessionLimit, session.getId());
        }
    }




---


###### autologinauth

자동 로그인 여부에 따른 인터셉터의 인증
-

```java
 //자동로그인 여부에 따른 인터셉터의 인증

    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler){
        HttpSession session = request.getSession();
        //세션에서 가져온 로그인값이 없을떄
        if(session.getAttribute("login") == null){
            //되돌아가기 위한 목적지를 담아놓고
            saveDest(request);
            Cookie loginCookie = Webkit.getCookie(request,"loginCookie");
            //쿠키를 가져와서 로그인 쿠키가 있으면
            if(cookie != null){
                //쿠키의 값을 DB에 넘겨 체크해서 있으면 사용자 객체를 가져온다.
                UserVO vo = service.checkLogincookieSession(loginCookie.getValue());
                //그 객체가 있으면 세션에 로그인으로 담는다+ 리턴으로 true해준다.
                if(vo != null){
                    
                    session.setAttribute("login", vo);
                    return true;
                }
            
            }
            //로그인쿠키가 없으면 로그인 페이지로 리다이렉트 시키고 리턴으로 false처리한다
            response.sendRedirect("/user/login");
            return false;
        }
        // 로그인이 되어있으면 바로 리턴으로 true시킨다.
        return true;
    }

```java



---


###### logout

로그아웃 처리
-


    //로그아웃 처리
    @RequestMapping(value="logout", method=RequestMethod.POST)
    public String logout(HttpServletRequest request, HttpServletResponse response, HttpSession session)throws Exception{
        //로그인 세션가져온다
        Object obj = session.getAttribute("login");

        if(obj != null){
              // 있으면사용자객체에 담고
            UserVO vo = (UserVO)obj;
            // 세션을 지운다
            session.removeAttribute("login");
             // 세션을 무효화 시킨다.
            session.invalidate();
            // 로그인 쿠키를 가져와서
            Cookie loginCookie = WebUtils.getCookie(request,"loginCookie");
             
            if(loginCookie != null){// 있으면 path, 기간을 설정하고 쿠키를 담아준다 / db에 넣어준다
                loginCookie.setPath("/");
                loginCookie.setMaxAge(0);
                response.addCookie(loginCookie);
                service.keepLogin(vo.getUid(),session.getId(),new Date());
            }
        }
        return "user/logout";
    }




---


###### mybatisSetting

MyBatis 설정
-

```xml
	<!-- https://mvnrepository.com/artifact/org.mybatis/mybatis -->
		<dependency>
			<groupId>org.mybatis</groupId>
			<artifactId>mybatis</artifactId>
			<version>3.4.1</version>
		</dependency>

	<!-- https://mvnrepository.com/artifact/org.mybatis/mybatis-spring -->
		<dependency>
			<groupId>org.mybatis</groupId>
			<artifactId>mybatis-spring</artifactId>
			<version>1.3.0</version>
		</dependency>

	<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-jdbc</artifactId>
			<version>${org.springframework-version}</version>
		</dependency>

		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-test</artifactId>
			<version>${org.springframework-version}</version>
		</dependency>

```

