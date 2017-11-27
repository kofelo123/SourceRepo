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

  이하 getter/setter , toString () 생략

}
```

### boardcontroller view
