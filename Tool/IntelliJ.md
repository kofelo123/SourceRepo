- [메이븐 프로젝트 생성](#mavenproject)
- [인텔리제이 - 깃허브연동](#intellijgithub)
- [톰캣 디플로이 미지정에러](#tomcatnotdeploy)
- [dispatcher-servlet.xml, applicationContext.xml](#intellijcontext)
- [properties 한글인코딩](#propertiesencoding)
- [빌드 버전에러](#buildsversionerror)
- [Maven 강제 업데이트](#mavenupdate)
- [exploded에 대해](#180730_4)
- [Maven Update, Reimport](#180730_5)



- [오토 퍼블리싱 세팅](#180730_6)
- [인텔리제이 전체화면 키변경](#180806_29)
- [분할창생성](#180806_30)
- [sysout - Live Templates 수정](#180807_1)
- [javadoc 관련](#180807_3)
- [콘솔에서 검색 (find - replace)](#180808_1)
- [서버실행시 Run - Redeploy](#180808_2)
- [message tool 관련설정](#180823_4)
- [인텔리제이 "" (Double quotes) 자동 안되게 설정](#180826_8)
- [double quotes ""  xml에서  자동으로 안되도록 수정](#180911_9)
- [maven dependency 추가](#180910_5)
- [beans graph 보는방법](#180910_6)
- [외부 jar 파일 넣기](#180911_6)
- [maven dependency 추가검색관련](#180911_11)
- [테스트실행 단축키](#180913_1)
- [인텔리제이 xml등에서 노란색 영역의 hightling 끄는방법](#180918_6)
- [인텔리제이 sql코드정리](#181120_5)
- [터미널에서git-bash 띄우기](#181120_6)
- [플러그인 - Keypromoter](#181213_2)


- [인텔리제이 디버깅](#181213_1)


-[에러](#)
  - [잘못된 컨텍스트 지정으로 인한 에러](#wrongcontext)
  - [프로젝트 경로 변경시 스프링컨텍스트의 스키마 에러](#pathmdifyerror)
  - [could not autowired no beans of](#couldnotautowired)

---


###### intellijgithub

인텔리제이 - 깃허브연동
-

* Add

우클릭 - git - add

* Commit

우클릭 - git - Commit Directory

* Add 및 Commit시 변경된 파일의 Font-Color의 의미

Red - Workspace Files

Green - Staged Files

Blue - Modified Files

White - Commited Files

Gray - Deleted Files

* Push

우클릭 - Git - Repository - Push

* Clone

VCS -> Checkout from Version Control - Git


* 환경설정

1. File - Setting - Path지정 (\Git\bin\git.exe)

2. Git Repository 설정 (생성이 필요할떄)

 VCS - Import into Version Control - Create Git Repository

3. GItHub 계정등록 : File - Setting - VersionControl - GitHub

[참고블로그](http://secuinfo.tistory.com/entry/Intellij-Github-Link)

---


###### tomcatnotdeploy

톰캣 디플로이 미지정에러
-

인텔리제이에서 톰캣 스타트버튼을 누르면 아래처럼 리스타트,리빌드 등의 옵션이 있으나, 그냥 옵션없이 진행되는 경우는
톰캣에 디플로이먼트를 지정해주지 않아서 그렇다.

그결과 빈 프로젝트니 당연히 페이지를 찾을수없을 페이지만 웹에 띄우게 된다(404도 아님)

 ![](https://drive.google.com/uc?export=view&id=1OEVhPsokBqkOOUX2r60S53vcpBJbqM2E)


 ---


 ###### mavenproject

 메이븐 프로젝트 생성
 -

 1. New Project - Maven - (Create from archetype(해제))

 2. 프로젝트 우클릭- Add Framework Support - Spring - Spring MVC 체크하기

 3. Project Structure - Facts 에서 Spring - Spring Application Context의 위치를 조정(인텔리제이의 기본세팅은 WEB-INF등 위치가 이상해서 내가 원하는 위치에 폴더생성하고 Spring Context옮긴후 세팅에서 위치 지정) -> (기본적으로 이설정은 auto detected 된다. 그러나 시도해보니 가끔 스프링 설정파일이 기본적으로 프로젝트 생성시 같이 생성되지 않은 경우가 있는데 이때는 root-context.xml servlet-context.xml 이런것을 원하는 디렉터리에 생성해주면 인텔리제이에서 auto detected된다.)))

 Facts의 Web의 경우 상단의 Deployment Descriptors에 web.xml path를 지정
 (변경 - 프로젝트 \src\main\webapp\WEB-INF\web.xml))
 하단 - 웹 리소스 디렉터리 지정
 (변경 - 프로젝트\src\main\webapp)

4. Project Structure - Artifacts - 좌측 상단 프로젝트:war exploded 
우측 Avaliable Elements 의 lib와 facet resouce~모두 좌측으로 넘김


(web.xml 이클립스 기본)
```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app version="2.5" xmlns="http://java.sun.com/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://java.sun.com/xml/ns/javaee http://java.sun.com/xml/ns/javaee/web-app_2_5.xsd">

    <!-- The definition of the Root Spring Container shared by all Servlets and Filters -->
    <context-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>classpath:spring/root-context.xml</param-value>
    </context-param>

    <!-- Creates the Spring Container shared by all Servlets and Filters -->
    <listener>
        <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
    </listener>

    <!-- Processes application requests -->
    <servlet>
        <servlet-name>appServlet</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath:spring/servlet-context.xml</param-value>
        </init-param>
        <load-on-startup>1</load-on-startup>
    </servlet>

    <servlet-mapping>
        <servlet-name>appServlet</servlet-name>
        <url-pattern>/</url-pattern>
    </servlet-mapping>

</web-app>

```

servlet-context.xml 기본
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans:beans xmlns="http://www.springframework.org/schema/mvc"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns:beans="http://www.springframework.org/schema/beans"
	xmlns:context="http://www.springframework.org/schema/context"
	xsi:schemaLocation="http://www.springframework.org/schema/mvc http://www.springframework.org/schema/mvc/spring-mvc.xsd
		http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
		http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd">

	<!-- DispatcherServlet Context: defines this servlet's request-processing infrastructure -->
	
	<!-- Enables the Spring MVC @Controller programming model -->
	<annotation-driven />

	<!-- Handles HTTP GET requests for /resources/** by efficiently serving up static resources in the ${webappRoot}/resources directory -->
	<resources mapping="/resources/**" location="/resources/" />

	<!-- Resolves views selected for rendering by @Controllers to .jsp resources in the /WEB-INF/views directory -->
	<beans:bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
		<beans:property name="prefix" value="/WEB-INF/views/" />
		<beans:property name="suffix" value=".jsp" />
	</beans:bean>
	
	<context:component-scan base-package="com.test.jeongwon" />
	
	
	
</beans:beans>

```

초기 pom.xml
```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
	<modelVersion>4.0.0</modelVersion>
		<groupId>com.package</groupId>
	<artifactId>web</artifactId>
	<name>Name</name>
	<packaging>war</packaging>
	<version>1.0.0-BUILD-SNAPSHOT</version>
	<properties>
		<java-version>1.8</java-version>
		<org.springframework-version>4.3.8.RELEASE</org.springframework-version>
		<org.aspectj-version>1.6.10</org.aspectj-version>
		<org.slf4j-version>1.6.6</org.slf4j-version>
	</properties>
	<dependencies>
		<!-- Spring -->
		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-context</artifactId>
			<version>${org.springframework-version}</version>
			<exclusions>
				<!-- Exclude Commons Logging in favor of SLF4j -->
				<exclusion>
					<groupId>commons-logging</groupId>
					<artifactId>commons-logging</artifactId>
				 </exclusion>
			</exclusions>
		</dependency>
		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-webmvc</artifactId>
			<version>${org.springframework-version}</version>
		</dependency>
				
		<!-- AspectJ -->
		<dependency>
			<groupId>org.aspectj</groupId>
			<artifactId>aspectjrt</artifactId>
			<version>${org.aspectj-version}</version>
		</dependency>	
		
		<!-- Logging -->
		<dependency>
			<groupId>org.slf4j</groupId>
			<artifactId>slf4j-api</artifactId>
			<version>${org.slf4j-version}</version>
		</dependency>
		<dependency>
			<groupId>org.slf4j</groupId>
			<artifactId>jcl-over-slf4j</artifactId>
			<version>${org.slf4j-version}</version>
			<scope>runtime</scope>
		</dependency>
		<dependency>
			<groupId>org.slf4j</groupId>
			<artifactId>slf4j-log4j12</artifactId>
			<version>${org.slf4j-version}</version>
			<scope>runtime</scope>
		</dependency>
		<dependency>
			<groupId>log4j</groupId>
			<artifactId>log4j</artifactId>
			<version>1.2.15</version>
			<exclusions>
				<exclusion>
					<groupId>javax.mail</groupId>
					<artifactId>mail</artifactId>
				</exclusion>
				<exclusion>
					<groupId>javax.jms</groupId>
					<artifactId>jms</artifactId>
				</exclusion>
				<exclusion>
					<groupId>com.sun.jdmk</groupId>
					<artifactId>jmxtools</artifactId>
				</exclusion>
				<exclusion>
					<groupId>com.sun.jmx</groupId>
					<artifactId>jmxri</artifactId>
				</exclusion>
			</exclusions>
			<scope>runtime</scope>
		</dependency>

		<!-- @Inject -->
		<dependency>
			<groupId>javax.inject</groupId>
			<artifactId>javax.inject</artifactId>
			<version>1</version>
		</dependency>
				
		<!-- Servlet -->
		<dependency>
			<groupId>javax.servlet</groupId>
			<artifactId>servlet-api</artifactId>
			<version>2.5</version>
			<scope>provided</scope>
		</dependency>
		<dependency>
			<groupId>javax.servlet.jsp</groupId>
			<artifactId>jsp-api</artifactId>
			<version>2.1</version>
			<scope>provided</scope>
		</dependency>
		<dependency>
			<groupId>javax.servlet</groupId>
			<artifactId>jstl</artifactId>
			<version>1.2</version>
		</dependency>
	
		<!-- Test -->
		<dependency>
			<groupId>junit</groupId>
			<artifactId>junit</artifactId>
			<version>4.7</version>
			<scope>test</scope>
		</dependency>        
	</dependencies>
    <build>
        <plugins>
            <plugin>
                <artifactId>maven-eclipse-plugin</artifactId>
                <version>2.9</version>
                <configuration>
                    <additionalProjectnatures>
                        <projectnature>org.springframework.ide.eclipse.core.springnature</projectnature>
                    </additionalProjectnatures>
                    <additionalBuildcommands>
                        <buildcommand>org.springframework.ide.eclipse.core.springbuilder</buildcommand>
                    </additionalBuildcommands>
                    <downloadSources>true</downloadSources>
                    <downloadJavadocs>true</downloadJavadocs>
                </configuration>
            </plugin>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>2.5.1</version>
                <configuration>
                     <source>${java-version}</source>
                    <target>${java-version}</target>
                    <compilerArgument>-Xlint:all</compilerArgument>
                    <showWarnings>true</showWarnings>
                    <showDeprecation>true</showDeprecation>
                </configuration>
            </plugin>
            <plugin>
                <groupId>org.codehaus.mojo</groupId>
                <artifactId>exec-maven-plugin</artifactId>
                <version>1.2.1</version>
                <configuration>
                    <mainClass>org.test.int1.Main</mainClass>
                </configuration>
            </plugin>
        </plugins>
    </build>
</project>

```


 6. views 디렉토리 파일 만들기 & index.jsp 이동시키기

 7. Run Edit Configuration으로 Tomcat Server 추가하기

 Run - Edit Configuration

---

###### intellijcontext

dispatcher-servlet.xml, applicationContext.xml
-

인텔리제이와 STS는 기본적으로 스프링 MVC의 구조가 약간 다른부분도있고

스프링 컨텍스트 네임부터 달랐다.

STS의 controller 쪽의 처리와 view resolver 등이 존재하는 servlet-context는 (DispatcherServlet의 설정)
인텔리제이의 dispatcher-servlet.xml이 되고

STS의 비즈니스 계층, DAO 계층 등을 구성하는 root-context는  (ContextLoaderListener 라고부르는것같다.)
인텔리제이의 applicationContext.xml이 담당한다.

```xml
//인텔리제이 Deployment Descriptor ( web.xml)
<context-param>
    <param-name>contextConfigLocation</param-name>
    <param-value>/WEB-INF/applicationContext.xml</param-value>
</context-param>

<listener>
    <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
</listener>
<servlet>
    <servlet-name>dispatcher</servlet-name>
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
    <load-on-startup>1</load-on-startup>
</servlet>
<servlet-mapping>
    <servlet-name>dispatcher</servlet-name>
    <url-pattern>/</url-pattern>
</servlet-mapping>

```

기본적으로 <servlet-name>의 이름이 dispatcher면 /WEB-INF/dispatcher-servlet.xml 파일을 로딩한다
그러나 STS처럼 이름이 다르거나, 여러개 지정 할경우 아래처럼 initParam해서 적어준다.

```xml
// STS web.xml
<context-param>
   <param-name>contextConfigLocation</param-name>
   <param-value>/WEB-INF/spring/root-context.xml</param-value>
 </context-param>

 <listener>
   <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
 </listener>
 <servlet>
   <servlet-name>appServlet</servlet-name>
   <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
   <init-param>
     <param-name>contextConfigLocation</param-name>
     <param-value>/WEB-INF/spring/appServlet/servlet-context.xml</param-value>
   </init-param>
   <load-on-startup>1</load-on-startup>
 </servlet>
 <servlet-mapping>
   <servlet-name>appServlet</servlet-name>
   <url-pattern>/</url-pattern>
 </servlet-mapping>

```

---


###### propertiesencoding

properties 한글 인코딩
-

다국어 또는 정적 설정 등의 이유로 아직 * .properties 파일을 사용하는데 이 파일에서 한글을 보려면 Eclipse의 경우엔 Properties 플러그인을 설치해야 볼 수 있다. IntelliJ IDEA에서는 간단한 설정을 통해 한글을 볼 수 있는데 방법은 다음과 같다.

Preferences -> Editor -> File Encodings 에서 'Transparent native-to-ascii conversion'를 체크하면 된다.

 ![](https://drive.google.com/uc?export=view&id=1xI81L3mZTmTZ4HlfCOsyKWNKMEoruR-o)


 ---


###### buildsversionerror

 빌드 버전에러
 -

 error message : source release 8 requires target release 1.8

 pom.xml에 명시된  빌드의 버전이 1.6으로 되어있거나 해서 자바8 이나 jdk 1.8로 지정된 프로젝트와 맞지않을경우 수정해줘야함


 -> pom.xml에 build 관련 정의가없으면 매번 5로 변하거나, 매번 arifact에 lib등록해줘야 하는걸로 보인다.

---



###### mavenupdate

Maven 강제 업데이트
-

file-> settings - Build,Execution,Deployment - Build Tools ->Maven -> Repositories - update


---


###### wrongcontext

잘못된 컨텍스트 지정으로 인한 에러
-

Unable to locate Spring NamespaceHandler for XML schema namespace [http://xmlns.jcp.org/xml/ns/javaee]


@ContextConfiguration( locations ={"file:web/WEB-INF/** .xml"})

두개의 컨텍스트를 동시에 쓰려고 이런식으로 쓰다가
web.xml까지 읽어서 생긴에러

---


###### pathmdifyerror

프로젝트 경로 변경시 스프링컨텍스트의 에러
-

프로젝트를 통쨰로 옮기고 난뒤 에러가 났다.

applicationContext와 dispatcher-servlet에서

xmlns:context="http://www.springframework.org/schema/context"

이부분에 에러가 났는데,

uri is not registered (settings languages & frameworks schemas and dtds

안되서 다양한 관점으로 시도해보다가

에러난 코드의 Alt - Enter -> Fetch external resource 로 해결함


---


###### couldnotautowired
-

에러: could not autowired no beans of

전혀 문제가 없는 코드인데 (실행도 잘됨) 인텔리제이 컴파일 에러가 표기되고 있어서 Alt+Enter에서 권장되는 방법중 하나였던 방법이(Edit inspections profile setting) 스택오버플로우에도 해결법으로 나와있어서 해결했다.

 Settings>Inspections>Spring Core>Code

 ![](https://drive.google.com/uc?export=view&id=1SwnlVNpjKN-e8czckPPj8l9Ne5WFmTPJ)

error-> warning으로..
(이걸로 안되면 스프링 모델도 waning)



-----------------------------------------

###### 180730_4

exploded에 대해
-


war:exploded를 톰캣에서 써야 restart등 옵션이 생긴다.

이에관해서 검색하면 다음과 같은 설명이 있따.
```
웹어플리케이션을 배포하기 위한 패키징 유형을 3가지로 나눌 수 있습니다.

1. package(archive) : 아카이브(war,ear) 파일로 배포
- 아카이브는 결국 WAS에 의해 압축이 풀림
- 파일이 많을 경우 압축해제 시간 오래걸릴 수 있음
- 리모트 서버에 배포시 한개의 파일만 전송하면 됨
- WAS에서 제공하는 업로드를 통한 배포기능 활용가능
2. exploded(expanded) : 아카이브를 압축해제한 디렉토리 형태 구조
- 압축 및 해제 과정이 불필요
- 별도의 디렉토리에 원본 소스를 복사하여 만듬
- 파일이 많은 경우 복사 시간 오래걸릴 수 있음
- 원본 소스를 건드리지 않고 배포를 원하는 경우 적합
- 리모트 서버에 배포시 파일이 많은 경우 전송 시간이 오래걸릴 수 있음.(rsync는 빠르다?)

3. in-place : 소스 디렉토리(전체 또는 일부)를 그대로 배포
- 추가적인 복사 과정 불필요
- 로컬 서버에 배포하는 경우에 적합
- WAS가 런타임시 생성하는 파일이 소스와 섞일 수 있음



출처: http://ecogeo.tistory.com/tag/war%3Aexploded [아키텍트를 꿈꾸며 - 에코지오]
```


그리고 exploded시에 target 폴더에 저장된느것같다.

maven clean하면지워지는 작업용 폴더라고 한다.
커밋에서 제외되어야 한다.

지워도 maven build하면 다시 생긴다.

maven으로 빌드하면 생기는 jar파일 저장이 주용도이다.

프로젝트 결과시 배포되어 실제 서버에 반영될떄는 target 밑에있는 jar나 war를 배포하게된다.


"intellij에서 서버돌리면 target폴더에 war를 가지고 deploy하는 것 같던데요!

그래서 개발할 때 문제가 있으면 , 소스코드 내용이 target 폴더에 잘 반영되었는지 확인하는 습관이 있습니다.. 

컴파일이 제대로 안되있을때가있더라고요 여러가지 이유에 의해서,, "


-----------------------------------------

###### 180730_5

Maven Update, Reimport
-

eclipse에서 잘 되던 maven project를 intellij로 import 해서 일단 실행

잘안될때

해당 프로젝트에서 

Maven -> Generate Sources and Update Folders 

Maven -> Reimport



출처: http://jjeong.tistory.com/1018 [jjeong]


-----------------------------------------

###### 180730_6

오토 퍼블리싱 세팅
-

인텔리제이 설정중

이클립스의 Auto publishing이 인텔리제이에서는 기본적으로 수동적이거나 따로 설정을 해줘야 한다.

다음과 같이 설정

Run/Debug Configurations 에서

기본탭인 Server탭의  'On frame deactivation' 항목을 'Update classess and resources'를 설정해 놓으면, 애플리케이션이 잠시 대기하고 있는 동안에 변경사항을 확인하여 변경된 클래스와 리소스를 갱신시켜준다. 


혹은 좌측하단 서버창의 항목중에 'update resources on frame deaction을 클릭해서 활성화 시켜도 된다.

[참고](http://java.ihoney.pe.kr/277)


-----------------------------------------

###### 180806_2

인텔리제이 전체화면 키변경
-

이클립스와 일치하게 변경

Hide All Tool Windows - 
기본:Ctrl + Shift  + F12 => Ctrl+M으로 변경


-----------------------------------------

###### 180806_3


-

Split Vertically -> Ctrl + Shift + S

분할창 생성한다. 단지 Move right와 조금 다르다.

같은 파일을 분할창으로 생성해서 두개의 창으로 하나의 파일을 비교하는것도 가능.



-----------------------------------------

###### 180807_1

sysout - Live Templates 수정
-

인텔리제이 System.out.println()

sout -> syso 로 수정

Settings -> Live Templates -> output -> 
기존에 sout을 syso로 설정,

그리고, sysout추가


-----------------------------------------

###### 180807_3

javadoc 관련
-

클래스 생성시 javadoc 관련 생성되도록 설정했다.

Settings ->Editor -> File and Code Templates
Files -> Class 





-----------------------------------------

###### 180808_1

콘솔에서 검색 (find - replace)
-

콘솔에서 검색이 되지않아서 

구글에 검색을 키워드를 search로 검색했는데 보통 뭔가 코드 찾을때 find라고 키워드를 쓰는것같다.

find로 keymap을 보니 단축키 지정이 안되어있었다.
(기본적으로 Ctrl+F로 사용하는 인텔리제이 기능은 replace기능)

그래서 콘솔은 수정하는게 아니니 replace 안먹히는데 find기능에 Ctrl+Shift+F로  단축키 지정하고

콘솔에 사용하니 잘된다.





-----------------------------------------

###### 180808_2

서버실행시 Run - Redeploy
-

restart server 보다 redeploy가 더빨라서 (2배가량)

Ctril + R 을 redeploy로 수정.

그리고 이는 서버탭이 선택되었을때만 실행되어

run tab키(기본Alt+4-> Ctrl+R)로 수정

Ctrl + R 두번 누르면 redeploy 된다

Run 은 Ctrl + Alt + R



-----------------------------------------

###### 180823_4

message tool 관련설정
-

인텔리제이에서 컴파일에 에러나 경고 등을 띄워주는 메세지툴이 있는데

수시로 띄워지고, 코드를 다 작성하기전에도 띄워지고 하는 번거로움때문에 메세지 툴의 
설정아이콘을 눌러서

(Setting - Build,Excution,Deployment -> Compilier )에서 Automatically show first_error in editor체크를 해제했다.




-----------------------------------------

###### 180826_8

인텔리제이 "" (Double quotes) 자동 안되게 설정
-

편의를 위해 = 붙이면 자동으로 "" 큰따옴표 처리를 인텔리제이에서 지원해주는데

다른 에디터와 달라서 자꾸 헷갈린다

File - Settings- Editor -Code Style-HTML -Other tab -> Generated quote marks to None

처리하면된다.



-----------------------------------------

###### 180910_5

maven dependency 추가
-

우클릭 -> generate
(keymap 추가 -> Ctrl + G)



-----------------------------------------

###### 180910_6

beans graph 보는방법
-

Show UML Popup 기능
(기본키 Ctrl+Alt+U -> 키맵 추가 Ctrl->U )


-----------------------------------------

###### 180911_6

외부 jar 파일 넣기
-

ojdbc 같은경우 11버전까지 메이븐 지원안하므로 직접 넣어줘야..

인텔리제이에서 Project Structure -> Libraries 의 좌측창의 + 로 add해준다.

추가) 외부 jar 넣을때 그 로컬의 환경이 달라졌거나 로컬의 jar파일이 사라졌을때 문제가 생긴다

-> 프로젝트 내부의 lib폴더에 jar파일 넣고 그 경로로 위의 설정을 지정하는 방법을 사용하는게 맞지않을까 생각한다.



-----------------------------------------

###### 180911_9

double quotes "" xml에서 자동으로 안되도록 수정
-

Editor -> smart keys -> xml/html -> add quotes ... 체크해제

html의 경우 code style에 해당 설정있었으나 
xml은 code style에 없어서 검색해보고 찾음

-----------------------------------------

###### 180911_11

maven dependency 추가검색관련
-

인텔리제이에서 


```xml
  <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-tx</artifactId>
        <version>5.0.7.RELEASE</version>
    </dependency>

```
와 같은 의존을 검색하면

org.springframework:spring-tx:5.0.7.RELEASE

이렇게 등록되어있는걸 검색해야하는데

검색시 spring-tx , *spring-tx 이런식으로 검색해도 잘안된다.

org.springframework:spring-tx 이렇게 길게 검색해야하는 상황이 생기는데

* spring-tx 이렇게 애스터리크 이후 공백하나를 주고 검색하니 검색이 된다.

추가) dependency에서 검색할때(인텔리제이) spring-web이면 spring web으로 '-' 빼야 검색이 잘됨



-----------------------------------------

###### 180913_1

테스트실행 단축키
-

keymap - Run context configuration -> 기본 Ctrl+Shift+F10 => Ctrl + T 수정

-----------------------------------------

###### 180918_6

인텔리제이 xml등에서 노란색 영역의 hightling 끄는방법
-

sql영역에서 노란색으로 하이라이트 되어있는데 오히려 불편하고 끄는방법을 잘 몰라서 검색했다.

Alt + Enter -> Change dialect to .. => Edit inspection profile setting 

여기서 바꿔주면된다.


settings -> editor -> inspections -> sql dialect detection



--------

###### 181120_5

인텔리제이 sql코드정리
-

ctrl +shift + enter => sql코드정리



-----------------------------------------

###### 181120_6

터미널에서git-bash 띄우기
-

기본 터미널 -> c\windows\System32\cmd.exe

터미널에서 git bash 띄우려면

c\programfiles\bin\sh.exe 

(git-bash.exe를 설정하면 새 창에서 git-bash를 실행하여 실행됨(git-bash실행하는것과 같음))

-----------------------------------------

###### 181213_1

인텔리제이 디버깅
-
 
습관적으로 run을 실행하지만 로컬에서 왠만하면 debug로 실행하는 습관이 좋다.

구동중에 BreakPoint를 사용하려면 debug
여야만 한다.

(나중에 할일있으면 참고)
https://jojoldu.tistory.com/149



-----------------------------------------

###### 181213_2

플러그인 - Keypromoter
-

플러그인에서 - Keypromoter 설치하면
인텔리제이 내의 버튼들을 눌렀을떄 단축키를 알려줘서 , 단축키 찾기 좋도록 해준다.

