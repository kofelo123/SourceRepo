- [pull request](#pull-request)
- [깃허브 검색](#githubsearch)
- [깃허브 과정](#githubprocess)
- [깃허브 서칭API](https://github.com/search/advanced)
- [git bash에서 사용자변경](#userchange)
- [원하는것만 push](#selectpush)
- [git add -p](#gitaddp)
- [git fetch,pull / add,clone](#fetchpulladdclone)
- [git ignore](#gitignore)
- [에러](#error)
  - [add - 줄바꿈관련에러](#adderror)
  - [두개의 히스토리 가진 프로젝트병합](#refusingtomerge)

---

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



```
user:kofelo123  pagemaker

user:kofelo123 filename:PageMaker(대소문자 구별해줘야)

repo:kofelo123/SourceRepo

mapper in:file user:kofelo123 //코드 내용


```

## githubprocess

```
git init

git remote add origin (url)

git pull origin master(There is no tracking information for the current branch.-> origin master 해줘야댐)

git add .

git commit -m

git push origin master


순서로

(remote: origin / branch: master)

(ReadMe 만드는거 필수다)
```

---
# error

## adderror
git add .
warning: LF will be replaced by CRLF in target/classes/.gitignore.
The file will have its original line endings in your working directory.
줄바꿈 관련해서 os별 크로스 문제가 있어서 이걸 세팅으로 해결하는것에 대한 글을 글이 있던데, 나는 그냥 git init으로 초기화 시키고 진행했다.


## refusingtomerge

//ErrorMessage
fatal: refusing to merge unrelated histories

(두개의 히스토리를 가진 프로젝트 병합)

```
git pull origin master --allow-unrelated-histories
```

---


## userchange

깃 bash에서 사용자변경
```
git config --global user.name "이름"
git config --global user.email "이메일"
```
프로젝트 하나에서만 변경하고 싶으시면
```
git config --local user.name "이름"
git config --local user.email "이메일"
```
---


###### selectpush

원하는것만 스테이징해서 push할때
-

변경된 모든사항이 아니라 원하는것만 선택해서 commit 하고 push할때..

git status -> 변경된 모든 파일들 출력

그중에 변경할 파일만 add 변경할 파일

```
git add *AdminServiceImpl.java  (*는 앞의 경로를 생략하는용도)
```

---

###### gitaddp

git add -p
-

위의 방법보다 한단계 고차원 방법으로 스테이징하고 커밋하는 방법이다.

add . 의 경우 의도하지 않은 파일을 스테이징 할 수있어서
add 선택파일 해서 스테이징을 하나 , 좀더 괜찮은 방법은

스테이징 하는 변경사항을 확인하면서 작업하는것이다.

git add -p를 하면 modified 된 파일의 수정 부분을 단위별로 나누어서 추가할지 안 할지를 물어본다. 여기서 보이는 변경사항의 하나의 단위를 hunk라고 부른다. 이 hunk 단위로 추가할지 말지를 정할 수 있다. ?를 입력하면 다음과 같은 각 명령어를 볼 수 있다.
```
y - stage this hunk
n - do not stage this hunk
q - quit; do not stage this hunk or any of the remaining ones
a - stage this hunk and all later hunks in the file
d - do not stage this hunk or any of the later hunks in the file
g - select a hunk to go to
/ - search for a hunk matching the given regex
j - leave this hunk undecided, see next undecided hunk
J - leave this hunk undecided, see next hunk
k - leave this hunk undecided, see previous undecided hunk
K - leave this hunk undecided, see previous hunk
s - split the current hunk into smaller hunks
e - manually edit the current hunk
? - print help
```

보통 쓰는 것은 y를 누르면 해당 hunk를 스테이징에 추가하고 n을 누르면 추가하지 않고 다음 hunk를 보여준다.. q를 입력하면 add과정을 종료한다.

git이 알아서 hunk의 단위를 나누어 주지만 원하는 것보다 그 단위가 크다면(hunk에 스테이징에 추가할 내용과 아닌 내용이 섞여 있는 경우) s를 입력하면 hunk를 더 잘게 쪼개준다.

git add -p를 습관들이면 다음과 같은 장점이 있다.

스테이징에 추가하기 전에 추가되는 변경사항을 눈으로 확인할 수 있다. 이 과정에서 원하지 않은 디버깅 코드나 커밋과 관련되어 있지 않지만, 수정사항에 들어가 있는 부분을 제거할 수 있다.(이런 실수는 실제로 많이 한다.)
코드를 수정할 때 커밋단위로 나누어서 작업하지 않고 여러 작업이 섞이는 경우가 있는데(예를 들면 A 함수를 추가하다 보니 테스트를 하기 위해서 B 함수를 잠시 주석처리를 한다거나 A 함수를 추가하다 보니 B 함수에서 버그를 발견해서 버그도 수정했다거나..) 작업을 완료한 후에 관련된 부분만 나누어서 스테이징에 추가할 수 있다.
커밋을 훨씬 논리적인 단위로 나눌 수 있다. 파일 단위로 add를 하면 이런 부분이 어렵다.
untracked 파일은 -p를 할 때 나오지 않는다. 그래서 새로운 파일을 추가하는 행위를 의식적으로 할 수 있어서 실수를 줄여준다.
현재는 스테이징에 추가할 때 git add -p만 사용해서 커밋할 코드의 변경사항을 내 눈으로 다시 한 번 확인하고 있다. 머릿속에 변경사항이 다 있다고 생각했음에도 이 과정에서 실수나 빠뜨린 부분을 발견할 때가 꽤 있다.


=================================================

---


###### fetchpulladdclone

git  add,clone
-

git clone 은 사실 다른 명령어를 몇 개 실행한다. 디렉토리를 만들고 디렉토리로 들어가고 나서 git init 명령으로 빈 Git 저장소를 만든다. 그다음 입력한 URL을 origin 이라는(기본값) 이름의 리모트로 추가하고(git remote add) git fetch 명령으로 리모트 저장소에서 데이터를 가져온다. 마지막으로 최종 커밋을 워킹 디렉토리에 Checkout 한다(git checkout).

git pull,fetch
-

pull 을 실행하면, 원격 저장소의 내용을 가져와 자동으로 병합 작업을 실행하게 됩니다. 그러나 단순히 원격 저장소의 내용을 확인만 하고 로컬 데이터와 병합은 하고 싶지 않은 경우에는 fetch 명령어를 사용할 수 있습니다.

 fetch 후 merge 를 수행하면, pull 명령을 실행했을 때와 같은 이력이 만들어집니다. 사실 pull 이라는 것은 내부적으로 보면 fetch + merge 이기 때문이죠!

---

## gitignore

```
# : comments

# no .a files
*.a

# but do track lib.a, even though you're ignoring .a files above
!lib.a

# only ignore the TODO file in the current directory, not subdir/TODO
/TODO

# ignore all files in the build/ directory
build/

# ignore doc/notes.txt, but not doc/server/arch.txt
doc/*.txt

# ignore all .pdf files in the doc/ directory
doc/**/*.pdf

```


[](https://www.gitignore.io/) 에 들어가서 ide, os , 언어 등에 따라 기본 ignore 세팅된 것을 가져올수있다.

gitignore 파일 생성 안될때 .gitignore. 으로 생성하면 만들어진다(윈도우)

gitignore에 푸시된 내용이 삭제되지 않는 문제는 아래와 같이 해결할 수있다.

```
$ git rm -r --cached .
$ git add .
$ git commit -m "fixed untracked files”
$ 푸시
```

git rm => 원격 저장소와 로컬 저장소에 있는 파일을 삭제한다.

git rm --cached => 원격 저장소에 있는 파일을 삭제한다. 로컬 저장소에 있는 파일은 삭제하지 않는다.






