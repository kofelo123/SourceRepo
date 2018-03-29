- [젠킨스 설치 및 세팅](#installsetting)

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

* Tomcat user 설정

젠킨스의 배포를 담당하는 tomcat을 사용하기 위해 tomcat manager를 접근 할 수 있도록 tomcat-users.xml을 수정한다

tomcat-users.xml을 열고

  <role rolename="manager-gui"/>
  <role rolename="manager-script"/>
  <role rolename="manager-jmx"/>
  <role rolename="manager-status"/>
  <role rolename="admin-gui"/>
  <role rolename="admin-script"/>
  <user username="admin" password="admin" roles="manager-gui,manager-script,manager-jmx,manager-status,admin-gui,admin-script"/>

위 문구를 추가 하여 admin에게 권한을 부여 한다

여기서의 username 의 "admin" 과 password의 "admin"을 위의 프로젝트세팅의 Cridentials항목 (인증관련)에 기입한다.



에러

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

톰캣을 구동하지 않았을때 발생한다.

[참고1](http://hjw1456.tistory.com/21)
[참고2](https://dukeom.wordpress.com/2017/03/20/jenkinsgithubmaven-%EC%9C%BC%EB%A1%9C-%EB%B9%8C%EB%93%9C%EB%B0%B0%ED%8F%AC%ED%95%98%EA%B8%B0-34/)
