
- [Tern Eclipse IDE - 자동완성 플러그인](#8704_125)
- [깃허브 프로젝트 import](#8705_121)
- [로컬에 있는 프로젝트 import 안될때(project파일 없을시)](#notworkingimport)
- [Archive for required library - maven에러](#8706_100)
- [classpath와 웹루트path](#8726_99)
- [서버 relaunch 설정수정](#8727_5)
- [eclipse - todo](#180827_4)

---


###### 8704_125

Tern Eclipse IDE - 자동완성 플러그인
-


jquery 플러그인

Tern Eclipse IDE 

(이클립스 마켓 플레이스에서 설치)


적용: 프로젝트 우클릭 -> Configure -> conver to tern Project 

해서 modules에서  자동완성으로 사용할 라이브러리를 추가해준다(jquery)

(자동완성 기능만 제공하지 실제 라이브러리를 추가하는건 아닌거같다)



---


###### 8705_121

깃허브 프로젝트 import
-

1. Import existing Eclipse projects : 현재 존재하는 이클립스 프로젝트에 원격 저장소를 불러온다.

2. Import using the new Project wizard : 새로운 프로젝트 마법사를 이용해 프로젝트를 만들고 원격 저장소를 불러온다.

3. Import as general project :  이클립스에 생성된 프로제그가 아닌 프로젝트를 불러올 때 선택한다.


1번째 옵션인 Import existing Eclipse projects 의 경우 .classpath , .project 파일이 가져오는 git에서 없을경우 에러가 난다.
(임의로 만들어주던지, git에 포함되어있든지)  

-> 그러나 이 두파일은 git ignore되어있는게 맞다.   
-> 여러 구성원이 한팀으로 있을때 PC마다 환경이 달라서 classpath가 달라질수있어서 그때마다 수정해야하는 경우가 발생한다.

2번째옵션인 Import using the new Project wizard 로 하니 

error message: Connecting Git team provider failed. See log for details.
Error connecting project TheArc, no Git repositories found

이하 문제가 생기면서 git term이 작용안되서

그냥 Import as general project 로 받은후 properties -> configure -> convert to maven project했다.
(3번방법이 가장 문제없이 잘 되었다.)

(깃에서 가져올떄 Import as general project -> .project파일생성됨 / convert to maven project -> classpath파일 생성됨)



---

---

## notworkingimport

errormesage: No projects are found to import

이클립스를 import할떄 안될때는

.project, .classpath 없이 소스있는 경우인데

워크스페이스 아닌곳에 프로젝트를 둔후

new - spring regacy project해서

프로젝트 불러오기 지정해서 생성시켜서

.project 만들면된다.


###### 8706_100

Archive for required library - maven에러
-

Archive for required library (경로)
in project (프로젝트명) cannot be read or is not a valid ZIP file

해당 경로로 가서 폴더 삭제후(이클립스 종료해야 삭제됨) -> project clean -> maven update project



---


###### 8726_99

classpath와 웹루트path
-

기본 루트 href="/resource 이런것들 web관련된것은 webapp이 기본 루트패스

여기에 path 설정이 있으면 /thearc/resource 이런식으로..

그 상위의 자바단은 classpath:로 하는게아닐까..

-----------------------------------------

###### 8727_5

서버 relaunch 설정수정
-

매번 서버 재로딩을 마우스로 버튼 눌러야 하는 불편함 때문에

이클립스 Preferences - keys 에서 

Relaunch Application에 단축키 바인딩된게 없었는데 
Ctrl+ Alt+ R 로 지정함(in Windows)




-----------------------------------------

###### 180827_4

eclipse - todo
-

//TODO 해놓으면 tasks 탭에서 볼수있다.

![](https://drive.google.com/uc?export=view&id=1OZ3dHtPGManZgqkEDfINBj46XjV2x19p)




