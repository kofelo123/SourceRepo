- [페이징](#paging)
  - [Limit](#limit)
  - [DAO 처리를 도와줄 Criteria , 페이지계산](#criteria)
  - [화면 하단의 페이징 처리](#paging-button)
    - [endPage 구하기](#end-page)
    - [startPage 구하기](#start-page)
    - [totalCount와 endPage의 재계산](#totalcount-endpage)
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
