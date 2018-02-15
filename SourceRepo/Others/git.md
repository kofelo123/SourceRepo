- [pull request](#pull-request)
- [깃허브 검색](#githubsearch)
- [에러](#error)
  -[add - 줄바꿈관련에러](#adderror)

### Pull request
> 깃허브의 아주 강력한 협업기능이다.
> 머지 전 리뷰를 통한 공동학습가능.

1.Fork한다.
2.Clone한다.
3.BRANCH - 작업은 매번 Branch를 만들어서 Branch 단위로 한다.
4.Make Pull Request - 깃허브에서 새로운 pull requset를 만들 수 있다.
5.Code Review & Merge Pull Request - Pull Request에 대해 댓글로 토론할 수 있다.
라인단위로 리뷰가 가능하며, 리뷰 완료시 Merge 한다.
6.PULL - Pull Requset 가 프로젝트에 Merge 되었다면, 로컬 master 로 반영 한다.

---

## githubsearch

[깃허브 서칭API](https://github.com/search/advanced)

```
user:kofelo123  pagemaker

user:kofelo123 filename:PageMaker(대소문자 구별해줘야)

repo:kofelo123/SourceRepo

mapper in:file user:kofelo123 //코드 내용


```



---
# error

## adderror
git add .
warning: LF will be replaced by CRLF in target/classes/.gitignore.
The file will have its original line endings in your working directory.
줄바꿈 관련해서 os별 크로스 문제가 있어서 이걸 세팅으로 해결하는것에 대한 글을 글이 있던데, 나는 그냥 git init으로 초기화 시키고 진행했다.
