- [젠킨스 설치 및 세팅](#installsetting)
- [에러](#error)
- [히카리풀-db설정 안맞으면 빌드배포시 에러](#181123_15)
- [젠킨스 war방식->설치방식](#181223_3)

######installsetting

젠킨스 설치 및 세팅
-



jenkins.war를 톰캣 webapps 에 넣어주고

톰캣을 수동으로 시작시켜준다.

젠킨스 세팅관련:

* Global Tool Configuration


 ![](https://drive.google.com/uc?export=view&id=19e-X-LIwrtzVmAjichVwvQgachpjqgcG)

Maven은 3.5.3으로 자동설치 시켰다.

* Plugin Manager

Deploy to container Plugin 설치 (빌드후 톰캣에 배포하는 역할)

(필요하면 SSH관련 플러그인도 설치하면 될듯)


* 프로젝트 내 세팅

 ![](https://drive.google.com/uc?export=view&id=1Cfb1V1JQXrY8ccJD2zLT6J_PGocDWwMY)

 ![](https://drive.google.com/uc?export=view&id=1-593QJQgeYIbABLmOmZtqe4Mq-pqY_q6)

(원격서버도 http://jeongwon.me 이런식으로 도메인으로 세팅하면된다.)


* Tomcat user 설정

젠킨스의 배포를 담당하는 tomcat을 사용하기 위해 tomcat manager를 접근 할 수 있도록 tomcat-users.xml을 수정한다
(aws나 원격 서버에 (deploy하려는서버) 세팅해주고 리스타트 해야 한다.)


tomcat-users.xml을 열고
```
  <role rolename="manager-gui"/>
  <role rolename="manager-script"/>
  <role rolename="manager-jmx"/>
  <role rolename="manager-status"/>
  <role rolename="admin-gui"/>
  <role rolename="admin-script"/>
  <user username="admin" password="admin" roles="manager-gui,manager-script,manager-jmx,manager-status,admin-gui,admin-script"/>
```
위 문구를 추가 하여 admin에게 권한을 부여 한다

여기서의 username 의 "admin" 과 password의 "admin"을 위의 프로젝트세팅의 Cridentials항목 (인증관련)에 기입한다.

* war파일 이름변경

war가 기본적으로 스냅샷 버전명으로 파일이름이 생성되어서 일일이 고치기 불편할때(ROOT.war등으로)

pom.xml의 <build> 태그안, <plugins>태그밖에 태그로 <finalName>war파일이름</finalName>
이렇게 지정해주면된다.

```
<build>
		<finalName>smartit</finalName> //smartit.war로 deploy됨.
		<plugins>
			<plugin>
```

###### error

에러
-

* massage: unmappable character for encoding MS949

플랫폼의 기본파일 encoding이 다를경우 UTF-8로 개발된 소스를 maven 빌드할 경우이다.

이 경우 프로젝트의 pom.xml의 maven-compiler-plugin 부분의 encoding 타입을 UTF-8로 지정해주면된다.

```
//pom.xml

    <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>2.5.1</version>
                <configuration>
                    <source>1.8</source>
                    <target>1.8</target>
                    <compilerArgument>-Xlint:all</compilerArgument>
                    <showWarnings>true</showWarnings>
                   <showDeprecation>true</showDeprecation>
                   <encoding>UTF-8</encoding>
                </configuration>
      </plugin>

```

* massage: class path not set in conjunction with -source 1.6
jenkins use -source 7 or higher to enable diamond operator

1.6이하 버전에서는 <> 제너릭코드 쓸수없다는 것

젠킨스 세팅의 문제는 아니고 프로젝트 소스의 pom.xml에
maven의 java 버전을 지정하는 부분이 있다.

위의 UTF-8 인코딩 관련 세팅과 중복되는 부분이다.

<source> , <target> 관련 태그를 참조하면된다.


* massage: Failed to connect to repository : Error performing command: git.exe ls-remote -h
error cloning remote repo 'origin'

git.exe관련 설정이 잘못됬을때.. git.exe 경로를 제대로 설정해주면된다(jenkins설정)


* massage: ContainerException: Failed to redeploy
ConnectException: Connection refused: connect

톰캣을 구동하지 않았을때 발생한다 또는 톰캣의 tomcat-users.xml에 admin 접근 세팅을 안했을때도 발생.

[참고1](http://hjw1456.tistory.com/21)
[참고2](https://dukeom.wordpress.com/2017/03/20/jenkinsgithubmaven-%EC%9C%BC%EB%A1%9C-%EB%B9%8C%EB%93%9C%EB%B0%B0%ED%8F%AC%ED%95%98%EA%B8%B0-34/)


-----------------------------------------

###### 181123_15

히카리풀-db설정 안맞으면 빌드배포시 에러
-

에러 중.. The Tomcat Manager responded "FAIL - Deployed application at context path

Deploy 이후 에러가 발생했는데, 에러메시지가 되게 추상적이라 감이 안잡히는데 deploy도 되긴했고 문제는 딱히 없는데 

젠킨스 빌드에러로 표시되니 이 문제를 해결하고자 하루종일 검색하고, 여러 경우의수로 추리하고 했으나, 뚜렷한 힌트가 될만한게 없었다.

젠킨스 , 톰캣 설정관련 문제일거라고 확신하고 있었는데

시큐리티 혹은 히카리풀로 변경하면서 인지 모르겠으나, 기존에는 db관련 config를 Local에 두고 성공적 빌드배포한후 

따로 Server로 수동적으로 우분투접속후 xml수정후 톰캣 재시작 했는데 Server로 미리 바꿔놓고 깃허브에 push한후 젠킨스 빌드배포하니 에러가 안났다..

서버 톰캣로그를 볼수있도록 해놓은다음 다시 테스트해보니, 히카리풀의 문제였다. 히카리는 기존의 내가썻던 스프링의 DriverManagerDataSource 와 달리 톰캣구동 과정에서 

Local의 database와 Server의 database명이 다르니까(thearc,thearc2) 바로 에러를 뱉어낸것이다.



-----------------------------------------

###### 181223_3

젠킨스 war방식->설치방식
-

1. 기존방식: jenkins.war 파일만 가지고 톰캣 webapps에 두고 startup.bat 구동시킴.  or CMD 에서 해당경로 이동후 
java -jar jenkins.war
환경및 워크스페이스: User/사용자계정/.jenkins

2.설치방식: 서비스에서 시작시키면 바로구동
환경및 워크스페이스: C\ProgramFile\jenkins


젠킨스의 사용자계정및 데이터들은 파일로 저장되는 구조기 때문에 해당 경로에서 설정, 아이디나 프로젝트들이 있게된다.


기존의 워크스페이스에 내가 쓰던 계정 프로젝트들이 있어서 그것을 설치방식인 서비스 시작방법으로 젠킨스 구동하기위해 

서비스로 구동되는 이 디렉터리의 설정파일에서 (jenkins.xml) JENKINS_HOME의 value를 레거시하게 사용했던워크스페이스로돌리니 기존의 계정과 프로젝트를 그대로 쓰게되었다.




