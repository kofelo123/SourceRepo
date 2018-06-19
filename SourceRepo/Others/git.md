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

log를 사용할때 graph를 지저하면 선으로 된 그림 형태로 이력 흐름이 표시된다. online을 선택하면 한줄로 커밋 정보가 표시된다.

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

저장소 처음 생성시, 바로 'master' 라는 이름의 브랜츠를 git이 만들어둔다.

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

커밋x 변경내용 or 새 추가파일이 인덱스와 작업트리에 남은책 다른브랜치로 전환(checkout)
=> 그 변경 내용은 전환된 브랜치에서 커밋 할 수있다.


단, 커밋 가능 변경 내용 중 전환된 브랜치에서도 변경 있는 경우 체크아웃 실패 할수있음.

=> 이 경우, 이전 브랜치에서 커밋하지 않은 변경 내용을 커밋하거나, stash를 이용해 일시적으로 변경 내용을 다른 곳에 저장하여 충돌을 피하게 한 후 체크아웃을 해야함

stash = 파일의 변경 내용을 일시적으로 기록해두는 영역

작업 트리와 인덱스 내에서 아직 커밋하지 않은 변경을 일시적으로 저장 가능.
나중에 다시 불러와 원래의 브랜치나 다른 브랜치에 커밋가능.

 ![](https://drive.google.com/uc?export=view&id=1B4AXPEfc-j51MF3fTBsrMm0t6w8Mjm95)


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
