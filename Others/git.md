- [pull request](#pull-request)
- [깃허브 검색](#githubsearch)
- [깃허브 과정](#githubprocess)
- [깃허브 서칭API](https://github.com/search/advanced)
- [git bash에서 사용자변경](#userchange)
- [원하는것만 push](#selectpush)
- [git add -p](#gitaddp)
- [git fetch,pull / add,clone](#fetchpulladdclone)
- [git ignore](#gitignore)

- [깃허브 기본](#gitbasic)
- [merge - 변경 이력 병합](#8061805)
- [브랜치](#8061861)
  - [브랜치 전환](#branchtrans)
  - [브랜치 통합](#branchintintegrated)
  - [토픽 브랜치와 통합 브랜치에서의 작업흐름](#8062254)
  - [성공적인 git 브랜칭 모델](#8062260)
  - [브랜치 병합 및 삭제](#8627_101)
  - [동시에 여러 작업하기](#8627_103)
  - [병합할 때 발생하는 충돌 해결](#8628_102)
  - [rebase로 병합하기](#8628_115)
  - [pull, 원격 저장소 데이터를 로컬 저장소에 가져와 병합하기](#8628_160)
  - [fetch - 원격 저장소의 데이터를 로컬에 가져오기만 하기](#8628_628)
  - [push - 로컬 저장소 데이터를 원격 저장소로 밀어넣기](#8628_214)
- [태그](#8628_233)
  - [태그 추가](#8629_62)
  - [주석 달린 태그 추가](#8629_64)
  - [태그 삭제](#8629_65)

- [커밋]
  - [ --amend - 이전에 작성한 커밋 수정하기](#8629_710)
  - [revert - 이전에 작성한 커밋 지우기](#8629_72)
  - [reset - 커밋을 버리고 특정 버전으로 다시 되돌아가기](#8629_73)
  - [chheery-pick - 다른 브랜치로부터 특정 커밋을 가져와서 내 브랜치에 넣기](#8629_750)
  - [rebase -i  - 커밋 이력 편집하기](#8629_82)
  - [브랜치상의 커밋을 하나로 모아 병합](#8629_91)  

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







###### gitbasic

깃허브 기본
-


인덱스에 등록되지 않은 파일은 커밋이 안된다.

'인덱스'에 파일 상태를 기록하는것을 stage-스테이징 이라고 표현한다.




<사용자 설정관련>

git 설정내역은 사용자 home 폴더의 .gitconfig 파일에서 직접 설정하고 기록될수있지만, config 명령어로도 설정할수있다.
```
$ git config --global user.name "<사용자명>"
$ git config --global user.email "<메일 주소>"
```

git add가 스테이징 하는역할

git add 파일1 파일2 파일3 이런식으로 가능

<원격 저장소 추가>

원격 저장소의 주소는 이름으로 기록해 둘 수 있다.

기록 해두면 push 할 때마다 긴 원격 저장소의 주소를 입력 할 필요가 없다.

git remote add <name> <url>

" 콘솔일 경우 push 나 pull 은 실행 시에 원격 저장소명을 생략하면, origin이라는 이름의 원격 저장소를 사용합니다. 그 때문에 원격 저장소에는 origin이라는 이름을 붙이는 것이 일반적입니다."

<push>

```
 git push <repository> <refspec>...
```
 <repository>는 push 경로의 주소, <refspec>은 push 할 브랜치를 지정

행 옵션에서 한번 -u를 지정하면, 이후에는 그 브랜치명 지정을 생략할 수 있다.

단, 비어있는 원격 저장소에 최초로 push했을 때는 원격 저장소명과 브랜치명을 생략할 수 없다.


<복제(clone)>

```
git clone <repository> <directory>
```

복제한 저장소에서 push 할 때는 파라미터 origin master를 생략할 수 있습니다.

```
git push
```

<pull>

저장소명 생략시, origin으로 등록된 저장소 밑에 pull 수행함.
```
git pull <repository> <refspec>
```

//git log 명령어로 이력을 확인할 수 있다.
```
git log
```


---

###### 8061805

merge - 변경 이력 병합
-

끌어온 저장소가 최신 버전이 아닌 경우=> 
즉 내가 pull 을 실행한 후 다른 사람이 push 를 하여 원격 저장소를 업데이트 해버린 경우->
내 push 요청이 거부되어 버린다.

이때 병합(merge)라는 작업을 진행 -> 다른 사람의 업데이트 이력을 내 저장소에도 갱신.

병합기능 -> 현재 브런치에 알아서 통합 -> 그러나 자동으로 통합 안되는 경우도 있다 -> 수동으로해야

<자동으로 병합할 수 없는 경우>

원격 저장소 - 로컬 저장소 양쪽에서 파일의 동일한 부분을 변경한 경우

이 경우 두 변경 내용중 어느 쪽을 저장할 것인지 자동으로 판단 할 수 없기 때문에 충돌이 발생

 ![](https://drive.google.com/uc?export=view&id=1NO6m66ApO23o3a9qXbOdUFhuQjyEYToa)

===== 로 구분된 윗 부분이 로컬 저장소, 
아랫 부분이 원격 저장소의 변경 내용

Git이 자동으로 병합되지 않은 부분을 변경 시키는데 파일에서

이 부분을 직접 수정해서 커밋해야함.

log를 사용할때 graph를 지정하면 선으로 된 그림 형태로 이력 흐름이 표시된다. oneline을 선택하면 한줄로 커밋 정보가 표시된다.

```
 git log --graph --oneline
*   d845b81 병합
|\
| * 4c01823 pull 설명을 추가
* | 95f15c9 commit의 설명 추가
|/
* 3da09c1 add 설명을 추가
* ac56e47 first commit
```


---


###### 8061861

브랜치
-

여러 개발자들이 동시에 다양한 작업을 할수있게함

각자 독립된 작업 영역(저장소)안에서 마음대로 소스코드 변경.

분리된 영역에서 변경된 내용은 나중에 원래의 버전과 비교해서 하나의 새로운 버전으로 만들어 낸다.


브랜치는 다른 브랜치와 병합(merge)함으로써, 새로운 하나의 브랜치로 모을 수 있다.



여러명이서 동시에 작업 -> 다른사람의 작업에 영향 주거나 받지않도록, 

먼저, 메인 브랜치에서 자신의 작업 전용 브랜치 생성 => 각자 작업을 진행한후 => 작업이 끝난 사람은 메인브랜치에서 자신의 브랜치의 변경 사항을 적용 => 다른사람의 작업에 영향 받지않고 독립적 작업수행후 그 결과를 하나로 모으게 된다.

'작업 단위' 즉 브랜치로 작업 기록을 중간중간 남기게 되므로 문제 발생시 원인 작업을 찾아낼 수 있다.

 ![](https://drive.google.com/uc?export=view&id=19yApcFKtKA8-RZiM8pl96Qdhy14X0Y94)


<master 브랜치>

저장소 처음 생성시, 바로 'master' 라는 이름의 브랜치를 git이 만들어둔다.

새로운 브랜치를 만들어서 사용한다고 선언(체크아웃,checkout)하지 않는 이상, 모든 작업은 'master' 브랜치에서 이루어진다.

<브랜치 만들기>

브랜치의 종류{

- 통합 브랜치(Integration Branch)

: 언제든지 배포할 수있는 버전을 만들 수 있어야 하는 브랜치.
=> 늘 안정적인 상태 유지 중요.(모든 기능이 정상적으로 동작하는 상태)

문제 발생하여, 그 문제(버그) 수정을 하던지 새로운 기능을 추가해야하면 토픽브랜치를 만들수있다.

처음은 보통 통합 브랜치에서 토픽 브랜치를 만든다. 저장소 처음 만들때 생기는 'master'브랜치를 통합 브랜치로 사용한다.


- 토픽 브랜치(Topic Branch)

: 기능 추가나 버그 수정과 같은 단위 작업을 위한 브랜치.여러 개의 작업을 동시에 진행시, 그 수만큼 생성가능.

통합 브랜치로 부터 만들어 내며, 토픽브랜치 특정작업이 완료시, 다시 통합 브랜치에 병합한다.

토픽 브랜치를 '피처 브랜치(Feature branch)' 라고도 한다.

}

 ![](https://drive.google.com/uc?export=view&id=1OFj2nJnDHKSfQkVDOyI6c9K2Hi4tGAap)

---

###### branchtrans

브랜치 전환
-

<브랜치 전환>

git 설치시 'master' 브랜치가 기본선택

다른 브랜치에서 작업하고 싶으면 -> 체크아웃(checkout)명령어로 원하는 브랜치로 전환

체크아웃시=> 브랜치 안의 마지막 커밋내용이 작업트리에 펼쳐짐.-> 브랜치가 전환되었으므로 이후의 커밋은 전환한 브랜치가 추가됨.

<HEAD>
현재 사용중인 브랜치의 선두부분을 뜻한다.
기본은 'master'의 선두 부분이다. 'HEAD'를 이동하면, 사용하는 브랜치가 변경됨.

커밋 지정시 ~(틸드,물결기호)와 ^(캐럿,삽입기호)를 이용해서 현재 커밋으로 부터 특정 커밋 위치를 가리킴.

이를 'HEAD' 뒤에 붙여 몇 세대 앞의 커밋을 가리킴.

^ 은 브랜치 병합에서 원본이 여럿 있을때 몇번째 원본인지 지정

 ![](https://drive.google.com/uc?export=view&id=1HuFphblNalxwtGQ9Yn3Hvv3y6V3xgV5D)


<stash>

커밋x - 변경내용 or 새 추가파일이 인덱스와 작업트리에 남은채로 다른브랜치로 전환(checkout)
=> 그 변경 내용은 전환된 브랜치에서 커밋 할 수있다.


단, 커밋 가능 변경 내용 중 전환된 브랜치에서도 변경 있는 경우 체크아웃 실패 할수있음.

=> 이 경우, 이전 브랜치에서 커밋하지 않은 변경 내용을 커밋하거나, stash를 이용해 일시적으로 변경 내용을 다른 곳에 저장하여 충돌을 피하게 한 후 체크아웃을 해야함

stash = 파일의 변경 내용을 일시적으로 기록해두는 영역

작업 트리와 인덱스 내에서 아직 커밋하지 않은 변경을 일시적으로 저장 가능.
나중에 다시 불러와 원래의 브랜치나 다른 브랜치에 커밋가능.

 ![](https://drive.google.com/uc?export=view&id=1B4AXPEfc-j51MF3fTBsrMm0t6w8Mjm95)

---


###### branchintintegrated

브랜치 통합
-

<브랜치 통합>

작업 완료 토픽 브랜치는 최종적으로 통합 브랜치에 병합됨.

브랜치 통합에는 merge , rebase 두가지 종류가 있따.

선택에 따라 통합 후의 브랜치의 이력이 달라진다.

- merge

: merge를 사용하면, 여러 개의 브랜치를 하나로 모음

 'master' 브랜치에서 분기하는 'bugfix'라는 브랜치가 있다고 가정

 ![](https://drive.google.com/uc?export=view&id=1YROklsD-7WpFpR1dHqAGySA6KF1iE_gZ)

 'bugfix' 브랜치를 'master' 브랜치로 병합할 때,

master 브랜치 상태가 이전부터 변경되지 않아 있으면 쉽게 병합된다.

bugfix브랜치의 이력은 master브랜치의 이력을 모두 포함하기 때문에, master 브랜치는 단순히 이동하기만 해도 bugfix 브랜치의 내용을 적용할 수있다.

이 같은 병합을 'fast-forward(빨리감기) 병합'이라고 부른다.

 ![](https://drive.google.com/uc?export=view&id=14djeruoUEDquVvH__zobwxDgt6s0VCre)

!> bugfix 브랜치 분기 이후에 master 브랜치에 여러 변경사항이 적용되는 경우, master 브랜치 내의 변경내용과 bugfix 브랜치 내의 변경내용을 하나로 통합 해야한다.

 ![](https://drive.google.com/uc?export=view&id=1BTS_80cH7QijpC-VMR-WtB1hkazfQO71)

~> 양쪽의 변경을 가져온 merge commig(병합커밋)을 실행하게 된다. 병합 완료후, 통합브랜치인 master 브랜치로 통합된 이력이 생긴다.

 ![](https://drive.google.com/uc?export=view&id=1RpiqeiGsMPAVNKja9jwPx0qlu7eoXmrQ)


(참고)

병합시 fast-forward 병합이 가능한 경우도 non fast-forward 병합옵션을 지정하여 아래와 같이 만들수 있다.

 ![](https://drive.google.com/uc?export=view&id=1VVP2phMQEMYW88uQDdGchsGbIcQmLrTf)

non fast-forward 병합시 브랜치가 그대로 남아 브랜치 실행한 작업확인 및 관리에서 유용할수있다.


rebase

: 'master' 브랜치에서 분기하는 'bugfix' 브랜치가 있다고 가정

 ![](https://drive.google.com/uc?export=view&id=1Kn2p-O86hPiYL09TgxoJW7azmSodzOyW)

bugfix 브랜치를 master 브랜치에 rebase 하면
master 브랜치 뒤로 이동하면서 하나의 줄기로 이어진다.

이때 커밋 X와 커밋 Y내에 내용이 master 커밋과 충돌하는 부분이 생길수있는데, 이때는 각 커밋에서 발생한 충돌 내용을 수정할 필요가 있음.

 ![](https://drive.google.com/uc?export=view&id=1EOCAcOEswzG8K6fiwHlw5jvS91jV5xOK)

rebase 하면 아래와 같이 master 의 위치는 그대로 유지된다. master 브랜치의 위치를 변경하기 위해서는 master 브랜치에서 bugfix 브랜치를 fast-foward(빨리감기) 병합하면 된다.

 ![](https://drive.google.com/uc?export=view&id=1guVm0mq5JhEZ7BEHSN0_i0YJVGQpe6jV)

(참고)

merge 와 rebase 는 통합 브랜치에 토픽브랜치를 토합하는 목적은 같으나, 특징이 다르다.

merge: 변경 내용의 이력이 모두 남아 이력이 복잡해짐

rebase : 이력은 단순, !> 원래 커밋이 변경됨
정확한 이력을 남겨야할때 사용 X


토픽 브랜치에 통합 브랜치의 최신 코드를 적용할때 rebase를 사용,
통합 브랜치에 토픽 브랜치를 불러올 경우에는 우선 rebase를 한 후 merge



---

###### 8062254

토픽 브랜치와 통합 브랜치에서의 작업흐름
-

상황 : 토픽 브랜치 2개 - 새로운 기능 추가작업, 버그수정작업

완료후 통합브랜치와 버그수정용 토픽 브랜츠를 병합하여 수정된 버전을 만들어낼 수있다.

 ![](https://drive.google.com/uc?export=view&id=1ALHWKHQGOKloi6K0njkmGrmcJF0xsO9W)

원래의 브랜치로 돌아와 새로운 기능추가 작업을 계속 진행하려할때,

커밋 X의 버그가 수정된 코드가 지금의 커밋 O에 적용해야 할떄

1.직접 merge 2. 커밋 X를 적용한 통합 브랜치에 rebase 

2번 방법을 이용해본다.

rebase를 이용하여 커밋 X의 내용을 적용한 상태로 새로운 기능을 추가하기 위해 O' 버전으로 만들어내는 방법을 이용하면된다.

 ![](https://drive.google.com/uc?export=view&id=1vdgItf5zVdamAADtOghZoVWGVOBVS4ML)


---

###### 8062260

성공적인 git 브랜칭 모델
-

4개의 종류의 브랜치를 이용해서 개발한다.

- 메인 브랜치(Main branch)

- 피처 브랜치(Feature branch)또는 토픽 브랜치(Topic branch)

- 릴리스 브랜치(Release branch)

- 핫픽스 브랜치(Hotfix branch)

 ![](https://drive.google.com/uc?export=view&id=1QyAiLJmonJ4iuWOAgQ2CeuVJqLQvlVWT)

<메인 브랜치(Main branch)>

'master' , 'develop' 브랜치, 이 두종류를 보통 메인 브랜치로 사용한다.

- master : 배포 가능한 상태만 관리, 커밋할때는 태그를 사용하여 배포 번호를 기록

- develop : 통합 브랜치의 역할, 평소에는 이 브랜치를 기반으로 개발 진행

<피처 브랜치(Feature branch)>

토픽 브랜치 역할을 담당

새로운 기능 개발 빛 버그수정 필요시 'develop' 브랜치로 부터 분기한다.

기본적으로 공유할 필요가없기 때문에, 원격 관리 x, 개발 완료시 'develop'브랜치로 병합하여 다른 사람들과 공유

<릴리즈 브랜치(Release branch)>

버그를 수정하거나 새로운기능을 포함한 상태로 모든 기능이 정상적으로 동작하는지 확인.

관례적으로 이름앞에 'release-'를 붙임.
다음 번 릴리즈를 위한 개발작업은 'develop' 브랜치에서 계속 진행

릴리즈를 위한 최종적인 버그 수정등의 개발을 수행한다. 준비마치고 배포 가능상태가 되면, 'master'브랜치로 병합, 병합한 커밋에 릴리즈 번호 태그를 추가

릴리즈 브랜치에서 발견한 버그 수정사항은 'develop'브랜치에도 적용해줘야함. ~> 그러므로 배포 완료후 'develop' 브랜치에 대해서도 병합작업을 수행

<핫 픽스 브랜치>

배포한 버전에서 긴급 수정 필요성 -> 'master'에서 분기되는 브랜치. 관레적으로 이름앞에 'hotfiix-' 를 붙임.

'develop' 브랜치개발중 이전 배포 소스코드에 아주 큰 버그 발생시

문제 부분을 빠르게 수정후 배포 필요
-> 'develop' 브랜치에서는 시간 소요, 안정성 보장x  ~>(그러므로) 배포가 가능한 'master'브랜치에서 직접 브랜치를 만들어 필요한 부분만 수정한 후 'master'브랜치에 병합하여 이를 배포

이때 만든 핫픽스 브랜치 변경사항은 'develop' 브랜치에도 병합하여 문제가 되는 부분을 처리해준다.


---

###### 8627_101

브랜치 병합 및 삭제
-

 ![](https://drive.google.com/uc?export=view&id=1luxAFOjirb1PksURff1kvSYzvQAs2AXT)


브랜치병합 - merge 명령어로 실행, 병합할 커밋 이름을 넣어 실행하면 지정한 커밋내용이 'HEAD'가 가리키고 있는 브랜치에 넣어진다.

'HEAD'는 현재 사용중이 브랜치에 위치하게 된다.

```
git merge <commit>
```

master 브랜치에 issue1을 넣기위해 우선
'master'브랜치에 'HEAD'가 위치하게 한다(checkout)

```
git checkout master
```

(issue1 브랜치에서 파일편집했기때문에 아직 master 브랜치에서는 파일내용이 변경되지 않아야한다.)

이후 병합을 한다.
```
git merge issue1

===
Updating 1257027..b2b23c4
Fast-forward
 myfile.txt |    1 +
 1 files changed, 1 insertions(+), 0 deletions(-)

```
이런 방식의 병합을 'fast-forward(빨리감기)병합'이라 한다.



<브랜치 삭제>

issue1 브랜치 내용이 모두 master에 통합되어 더이상 필요가 없다.

삭제하려면 -d 옵션을 지정하여 실행

```
git branch -d <브랜치명>
```


---

###### 8627_103

동시에 여러 작업하기
-

두 개의 브랜치 생성 - 동시 여러 작업 처리 상황만든다.

```
git branch issue2
git branch issue3

git checkout issue2
```

 ![](https://drive.google.com/uc?export=view&id=16hRMnAr0MSPPTLxh1irDyg4uxq--tfoJ)

myfile.txt.에 수정한다.

```
add: 변경 사항을 만들어서 인덱스에 등록해보기
commit: 인덱스 상태를 기록하기 
```

```
git add myfile.txt
git commit -m "commit의 설명 추가"
```

 ![](https://drive.google.com/uc?export=view&id=1G-h8Ly7VmxOlS_F9daugCGP3UzYkITbW)

```
git checkout issue3
```
issue3에는 commit에 대한 수정설명이 적용되지 않은상태( issue2에서 commit했기떄문에)

issue3에는 pull 명령어에대한 설명으로 채운후 변경사항 커밋한다.

```
add: 변경 사항을 만들어서 인덱스에 등록해보기
pull: 원격 저장소의 내용을 가져오기 
```

```
git add myfile.txt
git commit -m "pull 설명추가"
```

 ![](https://drive.google.com/uc?export=view&id=1ifnIIzCGhREMS2Udwwl3QbxQzQsxE9Q7)


---

###### 8628_102

병합할 때 발생하는 충돌 해결
-

```
git checkout master

git merge issue2
```
fast-forward(빨리감기)병합 됨.

 ![](https://drive.google.com/uc?export=view&id=1e_4bo-FV4jR2VR7gxVCx-ubrOi9nE0Hp)

여기서 브랜치 issue3 병합시도

```
git merge issue3

===

CONFLICT (content): Merge conflict in myfile.txt
Automatic merge failed; fix conflicts and then commit the result.


```

충돌남, 같은 행에 다른 내용이 있어서

해당파일에 자동으로 기록되어있음.
```

add: 변경 사항을 만들어서 인덱스에 등록해보기
<<<<<<< HEAD
commit: 인덱스의 상태를 기록하기
=======
pull: 원격 저장소의 내용을 가져오기
>>>>>>> issue3
```

충돌이 난부분은 일일이 파일에서 수정해줘야한다.

수정후 다시 커밋한다

```
git add myfile.txt
git commit -m "issue3 브랜치 병합"
```
충돌부분 수정했으므로 그 변화를 기록하는 병합 커밋이 새로 생성되었다.
그리고 'master'브랜치의 시작('HEAD')이 그 위치로 이동해있게 된다. 이를'non fast-forward 병합'이라 한다.

 ![](https://drive.google.com/uc?export=view&id=1xGaurTRPley55UNiyxcEuzopHbHaXQkv)



============================================
 ![](https://drive.google.com/uc?export=view&id=)



---

###### 8628_115

rebase로 병합하기
-

이전의 방법(merge)은 master 브랜치로 모두 병합하여
두개의 줄기로 브랜치 분기되었다가 하나로 합쳐진다.

issue3브랜치 병합시 rebase를 먼저실행후 병합을 시도한다면 그 이력을 하나의 줄기로 만들 수 있다.


이전의 마지막으로 진행했던 병합 명령 취소하기
```
git reset --hard HEAD~
```

 ![](https://drive.google.com/uc?export=view&id=1kRmKxFdN4YpBzjhigbptuHIscEjHqKeL)


이제 issue3 브랜치 전환 -> master 브랜치에 rebase 실행

```
git checkout issue3

git rebase master

.. conflict in myfile.txt(충돌 메시지)

```
이전의 merge 때와 마찬가지로 충돌난 파일내용을 수정해준다.

rebase의 경우 충돌부분 수정후 commit이 아니라 rebase 명령에 --continue옵션을 지정하여 실행해야.

```
git add myfile.txt
git rebase --continue
```

만약 rebase 자체를 취소하려면 --obort 옵션을 지정하면 된다.

 ![](https://drive.google.com/uc?export=view&id=1Xa0ydhF0WtmHWfM5ztq7CBZq5QSH_hvp)

rebase만 실행한 경우 위 그림처럼 issue3 브랜치가 두 브랜치의 앞쪽으로 위치가 옮겨졌을뿐 master 브랜치는 아직 issue3의 변경사항이 적용되지 못한상태로 뒤에 남겨져 있으므로, 
master 브랜치로 전환하여 issue3 브랜치의 변경사항을 모두 병합해야한다.

``` 
git checkout master

git merge issue3

====

Updating 8f7aa27..96a0ff0
Fast-forward
 myfile.txt |    1 +
 1 files changed, 1 insertions(+), 0 deletions(-)
```

myfile.txt의 최종 내용은 merge했을 경우와 동일하지만 그 이력은 아래처럼 달라진다.

 ![](https://drive.google.com/uc?export=view&id=1mlzH-3XDg7gbQumKMWQDZ25KULhRHZcY)



---


###### 8628_160

pull, 원격 저장소 데이터를 로컬 저장소에 가져와 병합하기
-

로컬 저장소의 모든 변경 사항이 반영되어 있는 상태 -> 새 변경 사항이 있는 원격 저장소의 커밋 Y 를 로컬로 가져오는 경우

 ![](https://drive.google.com/uc?export=view&id=1CC9mdgzf_B_OA1Z4lm9q--l1n0zncj8N)

이런 경우, 단순히 'fast-forward 병합'이 됨. 그림 속의 'master'는 로컬 저장소의 'master' 브랜치, 'origin/master'는 원격 저장소 'origin'의 'master' 브랜치를 나타냄.

 ![](https://drive.google.com/uc?export=view&id=1aFHkleJTTxBeYdUAKNyGu0Z6fyzKgSbU)


그러나 로컬 저장소의 'master' 브랜치에서도 변경 사항이 생긴 경우, 양 쪽의 변경을 통합할 필요

 ![](https://drive.google.com/uc?export=view&id=1f2efXEION4n0kBZTKfE3l5wdrEqTA3zD)

이 때 pull 을 실행하여 소스를 병합할 수 있다. 충돌하는 변경이 없을 경우 자동적으로 병합 커밋이 만들어 지지만, 충돌이 있을 경우에는 충돌난 부분을 수동으로 해결한 다음 직접 commit 을 해 주어야 함.

 ![](https://drive.google.com/uc?export=view&id=1TA902hDzy3PCBgtip5wPggs2iGKrdjlj)


---

###### 8628_628

fetch - 원격 저장소의 데이터를 로컬에 가져오기만 하기
-

pull - 원격저장소 내용 가져옴 -> 자동병합

fetch - 원격저장소 내용을 확인만 하고 병합x

fetch실행시 원격저장소 최신이력 확인가능.
가져온 최신 커밋이력은 이름없는 브랜치로 로컬에 가져오게 된다.

이 브랜치는'FETCH_HEAD'의 이름으로 체크아웃 가능.

(예를들어, 로컬저장서와 원격 저장소에서 B에서 진행된 커밋이 있는 상태에서 fetch를 수행하면 아래와 같이 이력이 남아짐)

 ![](https://drive.google.com/uc?export=view&id=1-cueyyYcAYHXRj5W6IuVakesjN__bJqB)

이 상태에서 원격 저장소의 내용을 로컬 저장소의 'master'에 통합하고 싶은 경우는, 'FETCH_HEAD'브랜치를 merge 하거나 다시 pull을 실행하면 된다.


이렇게 fetch 후 merge 를 수행하면, pull 명령을 실행했을 때와 같은 이력이 만들어 진다. 사실 pull 이라는 것은 내부적으로 보면 fetch + merge 이기 때문




---

###### 8628_214

push - 로컬 저장소 데이터를 원격 저장소로 밀어넣기
-


push할떄에 push한 브랜치가 'fast-forward 병합' 방식으로 처리되도록 지정해 둘 필요가 있다.

push하지 않는 한 원격저장소에 영향을 주지않고, 자신만의 브랜치에서 자유롭게 작업 할 수있다.

x> 로컬저장소에서 작성한 브랜치를 공유하려면 명시적으로 원격 저장소로 push 해야한다.

주의) 원격 저장소에서 모두가 공유하고 있는 커밋은 기본적으로 덮어쓰거나 임의로 변경해서는 안된다. 그 경우, 원격 저장소와 동기화 되어 있는 다른 저장소들의 이력에 모두 영향을 준다.



---

###### 8628_233

태그
-

커밋을 참조하기 쉽도록 알기 쉬운 이름을 붙인것

한 번 붙인 태그는 브랜치처럼 위치가 이동하지 않고 고정된다.

일반적으로 이름 정보만 갖는 태그(Lightweight tag)와 상세한 정보를 포함하는 주석태그(Annotated tag) 두 가지 태그를 사용가능.

- 일반 태그
: 이름만 붙일 수 있음

- 주석태그  
:1.이름을 붙일수있음  
2.태그에 대한 설명도 포함가능  
3.서명 넣을 수 있음  
4.이 태그를 만든 사람의 이름, 이메일과 태그를 만든 날짜 정보 포함 가능


보통 '릴리즈 브랜치' 에서는 주석 태그로 설명이나 서명을 넣은 보다 상세한 정보를 포함하는 태그사용.

로컬에서 사용하는 '토픽 브랜치'에서는 이름만 만들어 붙이는 태그 사용.

 ![](https://drive.google.com/uc?export=view&id=1eGEs_CrGjR_34OXkjKHHvPNYk7LaofY4)


태그 이름을 지정 -> checkout or reset 함으로써 간단하게 과거의 특정 상태로 되돌릴 수 있다.
  



---

###### 8629_62

태그 추가
-

apple이라는 태그 달기
```
git tag apple
```

파라미터 없이 tag 명령어 -> 태그목록 조회

```
git tag
```

log 명령어에 --decorate 옵션 -> 태그 정보를 포함한 이력 확인가능

```
git log --decorate

===
commit 702afcfc8551257526cc34409412bfd589e8bbe1 (HEAD -> master, tag: apple, issue3)
Author: kofelo123 <hlw123@naver.com>
Date:   Wed Jun 27 10:57:27 2018 +0900

    pull 설명 추가

```

 ![](https://drive.google.com/uc?export=view&id=1NmWsfS6VYAjK5rMDQSUgJDueA9dZ85ev)




---

###### 8629_64

주석 달린 태그 추가
-

주석 달린 태그 => tag 명령어에 -a 옵션 지정
엔터키를 눌러 실행한  후 태그에 대한 주석 내용을 바로 입력. -m 옵션을 지정하여 명령어를 실행 할 때 바로 내용을 입력 할 수 도 있다.


```
git tag -a <tagname>

```
현재 'HEAD'가 가리키고 있는 커밋에 'banana'라는 주석이 달린 태그를 달려면 아래와같이
```
git tag -am "누구나 쉽게 이해할 수 있는 Git 입문" banana
```

-n 옵션 지정 -> tag 명령이 실행시 태그목록과 주석 내용 조회

```
git tag -n

===
apple           pull 설명 추가
banana          누구나 쉽게 이해할 수 있는 Git 입문

```

 ![](https://drive.google.com/uc?export=view&id=1F9viVgKW0LwqsXmUbiwpwerzlWdewM1w)


---

###### 8629_65

태그 삭제
-

tag 명령어에 -d 옵션  -> 태그삭제

```
git tag -d <tagname>
```
 ![](https://drive.google.com/uc?export=view&id=1SZmk5DshP7GipDPH7mWr9ngwqjvWoQkv)



---

###### 8629_710

 --amend - 이전에 작성한 커밋 수정하기
-

(누락된 파일 새로 추가, 기존의 파일을 업데이트, 이전의 커밋 설명을 변경하고 싶을떄..)

--amend 옵션을 지정 - 커밋을 수행하면, 같은 브랜치 상에서 이저에 커밋했던 내용에 새로운 내용을 추가하거나 설명을 수정가능.


(실습)

```
git log

git add sample.txt
git commit --amend
```
열려진 편집기에서 최근 커밋한 내용이 포함되어있다. 내용을 원하는 내용으로 수정후 저장하고 빠져나온다.

 ![](https://drive.google.com/uc?export=view&id=1KEpMJ5P38a4hDb4Y-EwjJuNclyydv-C_)



---

###### 8629_72

revert - 이전에 작성한 커밋 지우기
-

(이전에 작성한 커밋을 안전하게 지우고 싶을때)

revert 명령어로 특정 커밋의 내용을 삭제 할 수있다.

rebase -i 명령어나 reset 명령어를 통해 커밋을 삭제 할 수도 있지만, 해당 커밋이 이미 공개된 상태일 경우 삭제작업을 함부러 하기가 어렵다.

이런 경우 revert 명령어를 통해 특정 커밋의 내용을 지우는 새로운 커밋(B')을 만들어 안전하게 처리가능.

revert 명령어를 사용 - 커밋 B의 내용을 뒤집어 원래의 커밋 A의 상태로 돌아갈 수있는 새로운 커밋 B'를 만들 수 있음을 알 수있다.

 ![](https://drive.google.com/uc?export=view&id=1yYtj9DtsEkujjHVjkYq-HzH4-OtTcwwM)


(실습)  

revert를 이용하여 "pull의 설명을 추가"(commit내용) 를 지워본다.

현재의 이력

 ![](https://drive.google.com/uc?export=view&id=1KCdU_lApT2APCqVIlSrPmZe5cDppJfKw)

```
git log
```

```
git revert HEAD
```

sample.txt 를 열어  지워졌는지 확인

 ![](https://drive.google.com/uc?export=view&id=177xAtxDqA84YUTBQ7NrV35G3aWIyHqhO)

```
git log
```

---

###### 8629_73

reset - 커밋을 버리고 특정 버전으로 다시 되돌아가기
-

reset 명령어를 이용해 더 이상 필요없어진 커밋을 버릴 수 있다. (커밋뿐만 아니라 스테이징(add)도 취소됨)
명령어 실행 시 어떤 모드로 실행할 지 지정하여 'HEAD' 위치와 인덱스, 작업트리 내용을 함께 되돌릴지 여부를 선택


 ![](https://drive.google.com/uc?export=view&id=1CLKo5F1vvRXR1Yg-oYXevRSkNsODqra8)


모드는 기본적으로 'mixed'가 지정되며, 아래의 표와 같이 'soft'와 'hard' 중에서 선택 가능


 ![](https://drive.google.com/uc?export=view&id=1Wqp30wJ5zI5DBh_SNz7cEHCgV8HSu1Yg)

( 커밋을 되돌리고 싶을떄(soft)
 변경한 인덱스의 상태를 원래대로 돌리고 싶을떄(mixed)
최근의 커밋을 완전히 버리고 이전의 상태로 되돌리고 싶을떄(hard) )

(실습)  


reset을 사용하여 'master' 브랜치 앞의 두 개의 커밋을 삭제

현재의 이력

 ![](https://drive.google.com/uc?export=view&id=1vmb7QrrufaD0nQArMdloETXsxOr_isOv)

reset 을 사용하여 커밋 삭제

 ![](https://drive.google.com/uc?export=view&id=1na4XUat2Bf67vRRiF30Rd14xntzlNitw)


```
git reset --hard HEAD~~
HEAD is now at 326fc9f add의 설명을 추가
```

sample.txt에 내용이 삭제된지 확인

참고) reset 전의 커밋은 'ORIG_HEAD'라는 이름으로 참조가능.
실수로 reset을 한 경우는 'ORIG_HEAD'로 reset하여 reset실행 전의 상태로 되돌릴 수 있다.

```
git reset --hard ORIG_HEAD
```

(추가 참고)
```
$ git reset HEAD^ : 최종 커밋 취소. 그러나 변경된 파일은 남아있다.
$ git reset --hard HEAD^ : 최종 커밋 취소하고 파일 까지 복구한다. 
$ git reset HEAD~n : 마지막 n개의 커밋을 취소 한다. 그러나 변경된 파일은 남아 있다. ( n : 숫자 )
$ git reset --hard HEAD~n : 마지막 n개의 커밋을 취소. 파일 또한 복구됨.
```

---

###### 8629_750

chheery-pick - 다른 브랜치로부터 특정 커밋을 가져와서 내 브랜치에 넣기
-

( 특정 브랜치에 잘못 추가된 커밋을 올바른 브랜치로 옮기려고 할때
다른 브랜치의 커밋을 현재의 브랜치에도 추가하고 싶을 때)

cherry-pick을 이용하면 다른 브랜치에서 지정한 커밋을 복사하여 현재 브런치로 가져올 수 있다.

 ![](https://drive.google.com/uc?export=view&id=1yY4rEoKg803ZMlKRpvjThIwrHZjITo8_)

(실습)


현재의 이력

 ![](https://drive.google.com/uc?export=view&id=1GT4up8JhqQMdKIdT60Z8okrbfgb3ZkgE)

다른 브랜치에서 수행한 "commit의 설명추가" 커밋 내용을 'master' 브랜치로 가져오는것을 시도해본다.

cherry-pick사용, 
```
git checkout master

//아래 99daed2는 git log를 했을떄 나오는 commit 코드같은건데 매우긴 풀네임을 써도되고 아래처럼 잘라서 해도 되는것같다. 

git cherry-pick 99daed2
```

충돌 발생 -> sample.txt. 열고 충돌 수정후 커밋

```
git add sample.txt
git commit
```

---

###### 8629_82

rebase -i  - 커밋 이력 편집하기
-

( push 하기 전에 이전의 커밋 내용을 정리하고자 할때
그룹으로 묶을 수 있는 커밋들을 알기 쉽게 하나로 통합하려고 할때
이전 커밋에 누락된 파일들을 나중에 추가하고자 할 때)



rebase 명령어에 'i' 옵션을 지정 -> 커밋을 다시 쓰거나 다른커밋과 바꿔 넣을 수 있고, 특정 위치의 커밋을 삭제하거나 여러 커밋을 하나로 통합하는 작업을 할 수 있다.

 ![](https://drive.google.com/uc?export=view&id=1W2jxV5f0MM_3QpcXNj8YlxiuuG8Q3gTo)

 ![](https://drive.google.com/uc?export=view&id=136UJawvjxi8OK2KivIQ50H7BbV57y2BR)


(실습)


rebase -i 로 커밋 모두 통합하기
-

이력은 다음과 같다.

 ![](https://drive.google.com/uc?export=view&id=1QjMUXYaQfs-2uv2P1WpnLpP4WQ1NJatb)

"commit의 설명을 추가" , "pull의 설명을 추가" 이 두커밋을 하나의 커밋으로 통합한다.

```
git rebase -i HEAD~~
```

텍스트 에디터가 열리고 'HEAD'에서 'HEAD~~' 까지의 커밋이 다음과 같이 표시된다.

```

pick 9a54fd4 commit의 설명 추가
pick 0d4a808 pull의 설명을 추가

# Rebase 326fc9f..0d4a808 onto d286baa
#
# Commands:
#  p, pick = use commit
#  r, reword = use commit, but edit the commit message
#  e, edit = use commit, but stop for amending
#  s, squash = use commit, but meld into previous commit
#  f, fixup = like "squash", but discard this commit's log message
#  x, exec = run command (the rest of the line) using shell
#
# If you remove a line here THAT COMMIT WILL BE LOST.
# However, if you remove everything, the rebase will be aborted.
#
```

두번째 줄의 pick 문자를 squash로 변경하고 저장 종료.

이로써 두개의 커밋이 하나의 커밋으로 통합.

 ![](https://drive.google.com/uc?export=view&id=13Fr-WYtWIRw2oybCD9e8jxZpqDsn7mW7)


(실습2)


 ![](https://drive.google.com/uc?export=view&id=1713WcBHerGgkNHoKkeyg5v36ojKly4DP)

현재이력, "commit의 설명을 추가"한 커밋을 수정해본다.

```
git rebase -i HEAD~~
```
-> 텍스트 에디터가 열리고 'HEAD'에서 'HEAD~~'까지의 커밋이 다음과 같이 표시된다.
```
pick 9a54fd4 commit의 설명 추가
pick 0d4a808 pull 설명을 추가

# Rebase 326fc9f..0d4a808 onto d286baa
#
# Commands:
#  p, pick = use commit
#  r, reword = use commit, but edit the commit message
#  e, edit = use commit, but stop for amending
#  s, squash = use commit, but meld into previous commit
#  f, fixup = like "squash", but discard this commit's log message
#  x, exec = run command (the rest of the line) using shell
#
# If you remove a line here THAT COMMIT WILL BE LOST.
# However, if you remove everything, the rebase will be aborted.
#
```

첫번쨰 줄의 pick 을 edit로 변경하고 저장 종료한다.
 그러면 다음과 같은 문장이 출력되고 수정할 커밋이 체크아웃 상태가 된다.

```
Stopped at d286baa... commit의 설명 추가
You can amend the commit now, with

        git commit --amend

Once you are satisfied with your changes, run

        git rebase --continue
```

sample.txt 를 열어, commit 의 설명 부분을 적당히 변경

commit --amend를 실행-> 번경된 내용저장

```
git add sample.txt
git commit --amend
```

commit != rebase작업의 끝 => 이 커밋 작업이 종료했다는걸 알리려면, --continue 옵션 지정

```
git rebase --continue
```

이떄 다른 커밋에서 충돌이 발생할 수 있다. 그럴 때에는 충돌 부분을 수정한 후 add 와 rebase --continue를 실행하면 됨
이 때, 커밋은 필요 없으므로 실행하지 않는다.
만약 도중에 rebase 작업을 중지하고자 하는 경우에는 rebase에 --abort 옵션을 지정하여 실행

=> 커밋 수정 완료

rebase 전의 커밋은 'ORIG_HEAD'라는 이름으로 남아져 있다. 만약 rebase 한 후 원래대로 되돌리고자 하는 경우에는 'git reset --hard ORIG_HEAD'을 실행하여 rebase 전의 상태로 되돌릴 수 있다.


---


###### 8629_91

브랜치상의 커밋을 하나로 모아 병합
-

(토픽 브랜치 안의 커밋을 한꺼번에 모아서 통합 브랜치에 병합하고자 할떄)

병합(merge)시 사용할 수있는 --squash 기능이 있다.

이로 브랜치를 병합하면 해당 브랜치의 커밋 전체를 통합한 커밋이 추가된다.

 ![](https://drive.google.com/uc?export=view&id=1chB2sg_5PHVSnyt5AU2bqdYMlxOgzxxa)


(실습)

이력은 

 ![](https://drive.google.com/uc?export=view&id=1XTK_gceax1s87ILxL0sW5GW7AT0xNTKF)

'issue1'브랜치의 모든 커밋을 하나의 커밋으로 병합하여 'master'브랜치로 가져와봄

master 브랜치로 이동후 --squash옵션을 지정하여 merge를 실행
```
 git checkout master
 git merge --squash issue1

===

Auto-merging sample.txt
CONFLICT (content): Merge conflict in sample.txt
Squash commit -- not updating HEAD
Automatic merge failed; fix conflicts and then commit the result

```

충돌 발생 -> samtple.txt 열어 충돌부분 수정후 커밋

```
git add sample.txt
git commit
```

이로써 issue1 브랜치 상의 모든 커밋을 하나로 병합한 내용이 'master'브랜치에 추가되었다.

