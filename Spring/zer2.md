- [ch1](#180910_4)


---


- [settings](#settings)
- [lombok 라이브러리](#lombok)
---


###### settings

settings
-


spring 5.x / inteliJ Gradle

최근의 스프링 예제등이나 부트는 XML< Java Config 추세

Security로 로그인 처리

Lombok 활용 - Getter/Setter 코드줄임

MyBatis의 Mapper 인터페이스 + XML
-> 기존의 DAO 개발이 아닌 인터페이스와 SQL을 활용하는 형태의 개발방식 사용

* gradle설치

https://gradle.org/install -> Install manually - Binary-only

Spring 5x의 경우 Tomcat 9.0 버전 이용권장. JDK1.8

* 프로젝트 생성

인텔리제이의 스프링 프로젝트 생성 방식은
1. 처음부터 스프링 프로젝트 지정하고생성
2. Maven or Gradle 프로젝트 생성후 프레임워크 추가 하는 방식, 직접 프레임워크 라이브러리를 추가하는 방식


여기서는 Gradle을 이용해서 최신 스프링을 직접 다운로드 받아서 사용하는 방식을 이용 - 번거롭지만 원하는 프레임워크의 버전을 가장 쉽게 사용할 수 있고 필요시 Gradle에 라이브러리 관리 기능을 이용해서 직접 다운로드 가능

프로젝트 생성시 add Framework Support를 사용하는 방법은 편하지만
스프링 버전을 변경할떄 오히려 더 복잡한 경우가 많다
Gradle 을 이용해서 처리하는 방법이 있다.

build.gradle 파일을 편집

스프링 공식 홈페이지에서 dependency를 가져와서 dependencies에 넣어준다.

```
dependencies {
    testCompile group: 'junit', name: 'junit', version: '4.11'
    testCompile group: 'junit', name: 'junit', version: '4.12'
    compile 'org.springframework:spring-context:5.0.5.RELEASE'
    compile 'org.springframework:spring-test:5.0.3.RELEASE'
}
```



---


###### lombok

lombok 라이브러리
-


```
//(gradle)
compile group: 'org.projectlombok' , name: 'lombok' , version: '1.16.20'
```

인텔리제이에서 온전한 사용위해
관련 Plugin 설치필요

Settings - Plugin

하단의 Browse repositories - lombok검색 -> 설치후재시작

컴파일시 정상동작위해 컴파일 관련 옵션을 조정해야함

Settings - Build - Compile - Annotation Processors

에서 Enable annotation processing 체크



~17


---


sql developer 의 사용자생성

system 접속 - 다른사용자 - 사용자생성

기본테이블스페이스: USERS
임시 테이블 스페이스:TEMP

부여된롤 - DBA,CONNECTION 의 권한부여됨, 기본값 체크


JDBC 드라이버 설정

Oracle DB의 JDBC Driver 는 공식적으로 Maven,Gradle지원 x

여러방법중 직접 폴더생성, 해당폴더를 Gradle에서 이용하는 방식을 사용해본다.

sqldeveloper의 jdbc-> lib 경로의 ojdbc6.jar이용
(홈페이지에서 ojdbc8이 있고 jdk버전에 맞게하는게 좋다고한다. 예제에서는 ojdbc6)

lib 디렉터리생성 - jar넣어준다.
```
//build.gradle의 dependencies
compile fileTree(dir: 'lib', include: ['*.jar'])
```

다른방법으로 project structure의 libraries-java
에서 추가해주는방법도있다.

인텔리제이는 추가한 뒤에 프로젝트 갱신시에 필요하지 않는 라이브러리로 간주되어 에러를 발생 할 수있다.(직접사용되지 않을때 자동으로 제외시키는 기능이있다고한다. -> fix처리하는등 이런게 번거롭다.)


다중사용자 - 커넥션풀 - 자바에서는 DataSource 인터페이스로 커넥션풀 사용.

커넥션풀 종류가 많은데, spring-jdbc lib를 사용하는 방식도 있지만,

최근 유행하는 HikariCP[](https://github.com/brettwooldridge/HikariCP) 를 사용하도록 한다. 스프링 부트에서 사용되고있다.

```
  compile group: 'org.slf4j' , name: 'slf4j-api' , version: '1.7.25'

    testCompile group: 'org.slf4j' , name: 'slf4j-log4j12' , version: '1.7.25'

    compile group: 'com.zaxxer' , name: 'HikariCP' , version: '2.7.6'

```

SQLSessionFactory 타입자체는 MyBatis에 속하고
SqlSessionFactoryBean은 MyBatis-spring 모듈에서 가져다가 사용한다.

SQL이 내부적으로 어떻게 실행되는지 제대로 보기 위해 log4jdbc-log4j2 라이브러리 사용

```
compile 'org.bgee.log4jdbc-log4j2:log4jdbc-log4j2-jdbc4.1:1.16'
```
log4jdbc.log4j2.properties 생성
```
log4jdbc.spylogdelegator.name=net.sf.log4jdbc.log.slf4j.Slf4jSpyLogDelegator
```

DataSource의 DriverClassname() , JdbcUrl 부분 수정

<로그의 레벨설정>

많은양 로그 - 불편할떄

log4j.propeties 수정하여 좀더 높은 레벨의 로그로 조절



-----------------------------------------

###### 180910_3




- [환경구축 관련](#180910_3)

-----------------------------------------

###### 180910_4

ch1
-

환경구축 관련
-

#실행환경편집

이클립스가 기본적으로 JDK가 아닌 JRE를 이용해서 실행되므로 이후에 설치하는 Lombok 등의 사용에 지장이 있을수 있따. 이러한 문제를 미리 막기 위해 이클립스 폴더 내의 sts.ini 를수정함.

```
-vm
C:\Program Files\Java\jdk1.8.0_91\bin\javaw.exe
```



Java Configuration
-

web.xml 파일 삭제 및 스프링 관련 설정 xml 파일삭제

pom.xml 하단부에 <plugins> 내에 아래 설정 추가

```xml
 <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-war-plugin</artifactId>
            <version>3.2.0</version>
            <configuration>
                <failOnMissiingWebXml>false</failOnMissiingWebXml>
            </configuration>
        </plugin>
```

#@Configuration

해당 클래스가 설정파일이라는 어노테이션

RootConfig 클래스를 생성하고 @Configuration을 붙인다.


#WebConfig 클래스생성

web.xml역할을 대신한다.

AbstractAnnotationConfigDispatcherServletInitializer 클래스를 상속받아서 사용

3개의 추상 메소드를 오버라이드 하도록 되어있다.

- getRootConfigClasses()

- getServletConfigClasses()

- getServletMappings()


getRootConfigClasses()메소드는 이름처럼 root-context.xml을 대신한다. 	
```java
@Override
    protected Class<?>[] getRootConfigClasses() {
        return new Class[0];
    }
```
책에서 RootConfig 클래스를 사용하므로 아래와 같이 내용을 변경

```java
@Override
    protected Class<?>[] getRootConfigClasses() {
        return new Class[] {RootConfig.class};
    }
```

#ComponentSacn

```java
@Configuration
@ComponentScan(basePackages= {"org.zerock.sample"})
public class RootConfig {

}
```



의존성 주입 테스트
-

#lombok관련

롬복의 @Data -> setter, 생성자, toString() 자동생성

```
    @Setter(onMethod_ = @Autowired)
    private Chef chef;
```

@Setter는 자동으로 setChef()를 컴파일시 생성

onMethod 속성은 생성되는 setChef()에 @Autowired 어노테이션을 추가하도록 한다.






