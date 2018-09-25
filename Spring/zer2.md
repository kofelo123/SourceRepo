- [ch1](#180910_4)
    - [1장 정리](#180911_2)
    - [sqlSession](#180911_17)
    - [테스트 - 자바설정](#180911_3)
    - [묵시적 자동주입](#180911_4)
    - [log4j 없을떄 log메소드 에러](#180911_7)
    - [커넥션 풀 - 히카리](#180911_8)
    - [빈에러 빈생성 못할떄](#180911_10)
    - [Mapper 인터페이스](#180911_12)
    - [XML 매퍼와 같이쓰기](#180911_13)
    - [log4jdbc-log4j2 설정](#180912_3)
    - [log4j 설정파일 없어서 나는 에러](#180911_1)
    - [Invalid bound statement (not found)](#180912_2)
    - [Error creating bean with name 'dataSource' ](#180912_7)
    
- [2장 스프링mvc](#180912_4)
    - [javaConfig 기본환경설정](#180912_5)
    - [Web Deployment Assembly](#180912_6)
    - [@InitBinder](#180913_4)
    - [@DateTimeFormat](#180913_5)
    - [@ModelAttrbute](#180913_6)

- [ch3 (정리)](#180914_99)
    - [Incompatible types. Found ](#180914_3)
    - [파일 업로드 처리](#180914_1)
    - [Controller의 Exception 처리](#180914_2)
    - [driverClassName 프로퍼티 에러](#180914_6)
    - [Incompatible types. Found](#180914_3)
    - [Mapper 인터페이스 빈 의존주입못받을떄](#180914_5)
    - [SelectKey](#180917_2)
    - [@AllArgsConstructor](#180917_3)
    - [Controller을 쓰는 테스트](#180917_4)
    - [JavaConfig-기존 web.xml의 UTF-8 인코딩처리](#180917_6)
    - [오라클 DB의 페이징처리](#180917_7)


- [MyBatis와 스프링에서 페이징처리](#180917_8)
- [페이징처리](#180918_4)
- [ch4 REST 와 Ajax를 사용한 댓글처리](#180918_8)
- [ch5. AOP](#180918_11)
- [ch6. 파일업로드 방식](#180918_12)
- [ch7. 스프링 웹 시큐리티](#180918_13)
- [log 사용안될떄](#180918_14)
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

*gradle설치

https://gradle.org/install -> Install manually - Binary-only

Spring 5x의 경우 Tomcat 9.0 버전 이용권장. JDK1.8

*프로젝트 생성

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

최근 유행하는 HikariCP

https://github.com/brettwooldridge/HikariCP 를 사용하도록 한다. 스프링 부트에서 사용되고있다.

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

pom.xml 하단부에 ```<plugins>``` 내에 아래 설정 추가

```xml
 <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-war-plugin</artifactId>
            <version>3.2.0</version>
            <configuration>
                <failOnMissiingWebXml>false</failOnMissiingWebXml> //이부분이 에러나는데 이유를 모르겠음..+ 이 전체 plugin 없어도 돌아는 간다.
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






-----------------------------------------

###### 180911_1

log4j 설정파일 없어서 나는 에러
-

메시지 WARN No appenders could be found for logger ..

log4j:WARN Please initialize the log4j system properly.


log4j.xml 파일(로그설정파일)이 없어서 나는 상황.

클래스패스에 넣어준다(src/main/resources)

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE log4j:configuration PUBLIC "-//APACHE//DTD LOG4J 1.2//EN" "log4j.dtd">
<log4j:configuration
	xmlns:log4j="http://jakarta.apache.org/log4j/">

	<!-- Appenders -->
	<appender name="console"
		class="org.apache.log4j.ConsoleAppender">
		<param name="Target" value="System.out" />
		<layout class="org.apache.log4j.PatternLayout">
			<param name="ConversionPattern" value="%-5p: %c - %m%n" />
		</layout>
	</appender>

	<!-- Application Loggers -->
	<logger name="org.zerock.controller">
		<level value="info" />
	</logger>

	<!-- 3rdparty Loggers -->
	<logger name="org.springframework.core">
		<level value="info" />
	</logger>

	<logger name="org.springframework.beans">
		<level value="info" />
	</logger>

	<logger name="org.springframework.context">
		<level value="info" />
	</logger>

	<logger name="org.springframework.web">
		<level value="info" />
	</logger>

	<logger name="jdbc.audit">
		<level value="warn" />
	</logger>

	<logger name="jdbc.resultset">
		<level value="warn" />
	</logger>
	<logger name="jdbc.connection">
		<level value="warn" />
	</logger>


	<!-- Root Logger -->
	<root>
		<priority value="info" />
		<appender-ref ref="console" />
	</root>

</log4j:configuration>

```




-----------------------------------------

###### 180911_2

1장 정리
-

[@Setter]는 3가지 속성 부여가능

- value : 접근제한속성 (기본값: lombok.AccessLevel.PUBLIC)

- onMethod : setter 메서스 생성시 메서드에 추가할 어노테이션 지정

- onParam : setter 메서드의 파라미터에 어노테이션을 사용하는 경우에 적용

[@Data]는 Lombok에서 가장 자주 사용되는 어노테이션. 

@ToString , @EqualsAndHashCode, @Getter/@Setter, @RequiredArgsContructor를 모두 결합한 형태로 한번에 자주사용되는 메서드를 생성.

@Log4j : 로그 객체 생성





#trycatch 이런식으로도 가능
```java
@Test
    public void testMyBatis(){

        try(SqlSession session = sqlSessionFactory.openSession();
            Connection con     =  session.getConnection();
            ){
            log.info(session);
            log.info(con);
        }catch(Exception e){
            fail(e.getMessage());
        }


    }

```



-----------------------------------------

###### 180911_17

sqlSession
-


SQLSession은 Mybatis에서 핵심적인 존재이다.

SQLSessionFactory에서 만들어낸다.

Connection을 생성하거나 SQL을 전달하고 결과를 리턴받는 구조.

mybatis-spring 라이브러리의 클래스

```xml
 <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
        <property name="dataSource" ref="dataSource"></property>
    </bean>
```


JavaConfig
-

```java
    @Bean
    public SqlSessionFactory sqlSessionFactory() throws Exception{
        SqlSessionFactoryBean sqlSessionFactory = new SqlSessionFactoryBean();

        sqlSessionFactory.setDataSource(dataSource());
        return (SqlSessionFactory) sqlSessionFactory.getObject();
    }
```



-----------------------------------------

###### 180911_3

테스트 - 자바설정
-

```
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(classes ={RootConfig.class}) // 자바설정에서는 이런식으로 사용
@Log4j
public class SampleTests {
```



-----------------------------------------

###### 180911_4

묵시적 자동주입
-

스프링 4.3 이후 생성자에서의 의존주입에 @Autowired를 붙이지 않아도 묵시적으로 생성자주입이 가능하다고 한다.

```
@Component
@ToString
@Getter
public class SampleHotel{
	
	public SampleHotel(Chef chef){
		this.chef = chef;
	}

}
```
즉 위 같은 방법으로도 생성자주입이 가능.

이는 Lombok과 결합하면 아래와 같이 변경가능

```java
@Component
@ToString
@Getter
@AllArgsConstructor
public class SampleHotel{
	private Chef chef;
}

```
[@AllArgsConstructor] 는 인스턴스 변수로 선언된 모든 것을 파라미터로 받는 생성자를 작성.

만일 여러개의 인스턴스 변수중 특정한 변수에 대해서만 생성자 작성하고 싶다면

@NonNull과 @RequiredArgsConstructor 어노테이션을 이용가능.

```java
@Component
@ToString
@Getter
@RequiredArtgsConstructor
public class SampleHotel{
	@NonNull
	private Chef chef;
}

```

[@RequiredArtgsConstructor] 는 @NonNull 이나 final이 붙은 인스턴스 변수에 대한 생성자를 만들어낸다.


-----------------------------------------

###### 180911_7

log4j 없을떄 log메소드 에러
-

Error:(14, 1) java: package org.apache.log4j does not exist

log4j 없을떄 log.info() 같은 메소드 쓰면 에러난다. dependency가 없어서 그러니 추가해주면된다.

```xml
   <dependency>
        <groupId>log4j</groupId>
        <artifactId>log4j</artifactId>
        <version>1.2.17</version>
    </dependency>
```

-----------------------------------------

###### 180911_8

커넥션 풀 - 히카리
-

자바에서는 DataSoure 인터페이스를 통해 커넥션 풀을 사용. 이를 통해 매번 DB와 연결이 아닌 미리 연결을 맺어주고 반환하는 구조로 성능향상을 추구한다.

커넥션풀 종류는 여러있고, spring-jdbc 라이브러리를 이용하는 방식도 있으나 , 	
최근에 유행하는 HikariCP를 사용한다.
(스프링 부트 2.0에도 사용되고있음)

```xml
<dependency>
        <groupId>com.zaxxer</groupId>
        <artifactId>HikariCP</artifactId>
        <version>2.7.4</version>
    </dependency>
```


```xml
	<bean id="hikariConfig" class="com.zaxxer.hikari.HikariConfig">
		<property name="driverClassName"
                  value="oracle.jdbc.driver.OracleDriver"></property>
        <property name="jdbcUrl"
                  value="jdbc:oracle:thin:@localhost:1521:XE"></property>
        <property name="username"
                  value="zer2"></property>
        <property name="password"
                  value="zer2"></property>
    </bean>
    <bean id="dataSource" class="com.zaxxer.hikari.HikariDataSource"
            destroy-method="close">
        <constructor-arg ref="hikariConfig" />
    </bean>
```

JavaConfig
---

```java
@Configuration
@ComponentScan(basePackages= {"org.zerock.sample"})
public class RootConfig {

    @Bean
    public DataSource dataSource(){
        HikariConfig hikariConfig = new HikariConfig();
        hikariConfig.setDriverClassName("oracle.jdbc.driver.OracleDriver");
        hikariConfig.setJdbcUrl("jdbc:oracle:thin:@localhost:1521:XE");
        hikariConfig.setUsername("zer2");
        hikariConfig.setPassword("zer2");

        HikariDataSource dataSource = new HikariDataSource(hikariConfig);

        return dataSource;
    }
}
```
-----------------------------------------

###### 180911_10

빈에러 빈생성 못할떄
-

java.lang.IllegalStateException: Failed to load ApplicationContext

BeanCreationException: Error creating bean with name

Property 'driverClassName' threw exception; nested exception is java.lang.RuntimeException: Failed to load driver class oracle.jdbc.driver.OracleDriver in either of HikariConfig class loader or Thread context classloader

빈을 생성하지 못한다는 에러다.

jar가 lib에 없어서 그렇다. 

Project Settings -> Libraries 에 확인


-----------------------------------------

###### 180911_12

Mapper 인터페이스
-

Mapper를 작성하는 작업은 XML을 이용할 수도 있지만, Mapper 인터페이스로 코드를 최소화하여 작성가능하다.

MyBatis의 어노테이션을 이용해서 SQL을 메서드에 추가한다.

```java
public interface TimeMapper {

    @Select("SELECT sysdate FROM dual")
    public String getTime();

}

```

[Mapper설정]

Mybatis가 동작시에 Mapper를 인식할 수 있게 root-context.xml에 추가적 설정 필요.

```<mybatis:scan>```이라는 태그이용

(mybatis-spring 네임스페이스 추가해야함)
```xml
xmlns:mybatis-spring="http://mybatis.org/schema/mybatis-spring"

(스키마 로케이션)
http://mybatis.org/schema/mybatis-spring
http://mybatis.org/schema/mybatis-spring-1.2.xsd



<mybatis-spring:scan base-package="org.zerock.mapper" />
```

JavaConfig
---

RootConfig 설정 클래스 위에 어노테이션을 붙여준다.

```java
@MapperScan(basePackages= {"org.zerock.mapper"})
```




-----------------------------------------

###### 180911_13

XML 매퍼와 같이쓰기
-

SQL이 복잡하거나길어지는 경우는 어노테이션보다 XML 사용을 더 선호하게 되는데

MyBatis-Spring은 Mapper인터페이스와 XML을 동시에 사용가능하다.

XML파일 위치의 경우 Mapper 인터페이스가 있는 곳에 같이 작성하거나 resources 구조에 xml을 저장할 폴더를 생성할 수 있다. 

가능한 Mapper 인터페이스와 같은 이름을 사용하는게 가독성면에서 권장된다.


xml을 만든후 기존의 TimeMapper 인터페이스에 추가적인 메서드 getTime2()를 선언한다.

```java
public interface TimeMapper {
    
    @Select("SELECT sysdate FROM dual")
    public String getTime();

    public String getTime2(); //이부분

}

```

메소드위에 어노테이션이 없는채로 되어있고 이를 처리하는 xml은 아래와 같다.

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="org.zerock.mapper.Timemapper">

    <select id="getTime2" resultType="string">
        SELECT sysdate FROM dual
    </select>

</mapper>
```

주의점은 ```<mapper>```의 namespace속성값이다.

MyBatis는 Mapper 인터페이스와 XML을 인터페이스 이름과 namespace 속성값을 가지고 판단.

동일 이름시 이를 병합하여 처리.

```<select>``` 태그의 id속성값은 메서드의 이름과 동일하게 맞춰야한다.
resultType 역시 메서드의 리턴타입과 동일하게 해야한다.







-----------------------------------------

###### 180912_2

Invalid bound statement (not found)
-

 Invalid bound statement (not found)

- Mapper Interface와 mapping되는 xml파일에 오타가 있는 경우 : 예를 들면 id와 Interface에 메소드명이 일치하지 않는 경우

- classpath에 경로가 잘못된 경우

- xml이 저장될 경로는 잘못 생성한 경우

http://blog.woniper.net/247


Mapper.java가 있는위치에 xml을 놓아도 에러가 생긴다

 src/main/java폴더는 java source만 컴파일을 하고 xml파일은 빌드를 하지 않기 때문이다. 해결 방법은 src/main/java 폴더의 xml파일도 빌드에 포함 될 수 있도록 아래와 같이 resource를 추가한다..(위 url참고 생략.)



명심할것은 위의 문제에서 

Mapper 인터페이스의 경로와 mapper.xml의 경로를 일치시켜줘야하는것.

그러나 그 경로에있어 resources에 있어야 하고, 인터페이스와 달리 패키지 접근이 아니라 디렉터리 접근이라는 점..

Mapper 인터페이스의 위치가 org.zerock.mapper 패키지에 있다면

resouces에서 mapper.xml 역시 org.zerock.mapper 경로에 있어야 하나

다시한번 강조하지만 resouces에서는 패키지 개념이 없다는것, 디렉터리를

org.zerock.mapper 한번에 이름을 만들면 패키지 만든것과 IDE에서 겉으로 보기에는 차이가 없어 보이지만, 패키지는 . 을 찍어 생성하면 path개념으로 생성된다(. -> \) 그러나 디렉터리에서는 '.' 을 찍는것은 그냥 '.'일뿐이다  그러므로 둘은 분명 다르다

resources에서 동일 패키지와 동일 경로를 만들떄 반드시 디렉터리를 org 한번, zerock한번 mapper한번 총 3번에 걸쳐서 따로 만들어주어야한다.


-----------------------------------------

###### 180912_3

log4jdbc-log4j2 설정
-

MyBatis -> Jdbc의 PreparedStatement -> SQL 처리

=> 전달파라미터 = ? 치환처리 => 값 확인이 어려움. => SQL 로그를 좀더 쉽게 보기위해 log4jdbc-log4j2 라이브러리 사용.


```xml
<dependency>
        <groupId>org.bgee.log4jdbc-log4j2</groupId>
        <artifactId>log4jdbc-log4j2-jdbc4</artifactId>
        <version>1.16</version>
    </dependency>
```

이후 해야할것

1.로그설정 파일 추가 

resources경로에 log4jdbc.log4j2.properties 추가

```
log4jdbc.spylogdelegator.name=net.sf.log4jdbc.log.slf4j.Slf4jSpyLogDelegator
```

2. jdbc 연결정보 수정

```xml
//root-context.xml

   <bean id="hikariConfig" class="com.zaxxer.hikari.HikariConfig">
        <!--
                <property name="driverClassName"
                          value="oracle.jdbc.driver.OracleDriver"></property>
                <property name="jdbcUrl"
                          value="jdbc:oracle:thin:@localhost:1521:XE"></property>
        -->
        <property name="driverClassName"
                  value="net.sf.log4jdbc.sql.jdbcapi.DriverSpy"></property>
        <property name="jdbcUrl"
                  value="jdbc:log4jdbc:oracle:thin:@localhost:1521:XE"></property>
        
        <property name="username"
                  value="zer2"></property>
        <property name="password"
                  value="zer2"></property>
    </bean>
```


Javaconfig
---
```java
@Bean
    public DataSource dataSource(){
        HikariConfig hikariConfig = new HikariConfig();
//        hikariConfig.setDriverClassName("oracle.jdbc.driver.OracleDriver");
//        hikariConfig.setJdbcUrl("jdbc:oracle:thin:@localhost:1521:XE");
        hikariConfig.setDriverClassName("net.sf.log4jdbc.sql.jdbcapi.DriverSpy");
        hikariConfig.setJdbcUrl("jdbc:log4jdbc:oracle:thin:@localhost:1521:XE");
        hikariConfig.setUsername("zer2");
        hikariConfig.setPassword("zer2");

        HikariDataSource dataSource = new HikariDataSource(hikariConfig);

        return dataSource;
    }
```

로그레벨 지정은 log4j.xml의 logger관련 설정으로 한다.

-----------------------------------------

###### 180912_4

2장 스프링mvc
-

스프링 MVC는 스프링의 서브 프로젝트중 하나이다.

스프링은 '코어'라고 할수있는 프레임워크에 여러 서브 프로젝트를 결합해서 다양한 상황에 대처할 수 있도록 개발되었다.

 ![](https://drive.google.com/uc?export=view&id=1i-1RNXPHnzXSpiWKWcnSJlog0JoJOnuP)

JavaConfig 등을 이용하려면 서블릿 3.0이상의 dependency를 사용하는것이 좋다.

프로젝트 구동 시 관여하는 xml

web.xml : 톰캣

sevlet-context.xml , root-context.xml : 스프링 관련설정

HandlerMapping은 Request의 처리를 담당하는 컨트롤러를 찾기 위해 존재

이 인터페이스를 구현한 여러 객체중 RequestMappingHandlerMapping 같은 경우 개발자가 @RequestMapping 이 적용된 것을 기준으로 판단.

적절한 컨트롤러가 찾아졌다면 handlerAdapter를 이용해서 해당 컨트롤러를 동작시킴


ViewResolver는 컨트롤러가 반환하는 결과를 어떤veiw를 통해서 처리하는지 해석.

servlet-context.xml에 정의된 InternalResourceViewResolver을 가장 흔하게 사용.


view는 실제로 응답보낼 데이터를 jsp등을 이용해서 생성하는 역할을 한다. 만들어진 응답은 DispatcherServlet을 통해 전송

모든 Request는 DispatcherServlet을 통하도록 설계 -> Front-Controller 패턴



[GetMapping][PostMapping]

스프링 4.3 부터 @RequestMapping의 축약형으로 사용

@RequestMapping의 경우 get,post 모두 지원해야할 경우 배열로 처리가능하다는 장점이 있다.

@GetMapping은 편하긴 하지만 기능에 대한 제한은 많은편이다 (최근에 PUT, DELETE 방식 등도 점점 많이 사용되고 있으므로)



[@Data]

VO, DTO의 경우 @Data를 클래스 위에 붙여서 변수만 적는 간단한형태로 작성 가능

@Data -> getter/setter , equals(), toString() 등이 자동생성


[동일 이름의 여러 파라미터가 여러개 전달되는 경우]

ArrayList<> 을 사용해서 처리가능하다

```java
    @GetMapping("/ex02List")
    public String ex02List(@RequestParam("ids")ArrayList<String> ids){

        log.info("ids: " + ids);

        return "ex02List";
    }
```

ArrayList의 경우 @RequestParam 생략 못한다.


배열도 가능 
```
@ReuqestPram("ids")String[] ids ~

log.info("ids:" + Arrays.toString(ids));
```


*객체 리스트

전달하는 데이터가 DTO같은 객체이고 여러개 처리시 한번에 처리가능

SampleDTO를 여러개 받아서 처리하고 싶으면 SampleDTO의 리스트를 포함하는 SampleDTOList 클래스를 설계한다.

```java
@Data
public class SampleDTOList {

    private List<SampleDTO> list;

    public SampleDTOList(){
        list = new ArrayList<>();
    }
}
```

http://localhost:8080/sample/ex02Bean?list[0].name=aaa&list[2].name=bbb

-> 
```
INFO : org.zerock.controller.SampleController - list dtos: SampleDTOList(list=[SampleDTO(name=aaa, age=0), SampleDTO(name=null, age=0), SampleDTO(name=bbb, age=0)])
```

톰캣은 버전에 따라 문자열에서 '[ ]' 문자를 허용하지 않을 수 있다.

js를 사용할 경우 encodeURIComponent()와 같은 방법으로 해결가능하나

'[' 는 '%5B'로 ']' 는 '%5D"로 변경하는 방법도 있다.


[ResponseEntity 타입으로 헤더다루기]

스프링mvc는 HTTP 프로토콜의 헤더를 다룰때 HttpServletRequest나 HttpServletResponse를 직접 핸들링 하지않아도 이런 작업이 가능하도록 작성되었다.

ResponseEntity에서 원하는 헤더정보나 데이터를 전달 가능하다.

```java

@GetMapping("/ex07")
    public ResponseEntity<String> ex07(){
        log.info("/ex07........");

        // {"name": "홍길동"}
        String msg = "{\"name\": \"홍길동\"}";

        HttpHeaders header = new HttpHeaders();
        header.add("content-Type","application/json;charset=UTF-8");

        return new ResponseEntity<>(msg, header, HttpStatus.OK);
    }
```




-----------------------------------------

###### 180912_5

javaConfig 기본환경설정
-

web.xml , root-context.xml ,  servlet-context.xml 제거

web.xml이 없다는 신호를 주는 설정을 추가해야함.

pom.xml 하단부에 ```<plugins>``` 내에 아래 설정 추가

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

[RootConfig] 클래스 생성

```java
@Configuration
public class RootConfig {


}

```


[ServletConfig] 클래스 생성

@EnableWebMvc 와 WebMvcConfigurer 인터페이스를 구현하는 방식    
(과거에는 WebMvcConfigurerAdapter 추상클래스 사용했으나, 스프링 5.0버전부터
Deprecated(사용x) 되었으므로 주의)

@Configuration과 WebMvcConfigurationSupport 클래스를 상속하는 방식 - 일반 @Configuration 우선순위가 구분되지 않는 경우에 사용

책에서는 @EnableWebMvc 사용. 기존의 servlet-context.xml을 ServletConfig에서 구현

```java
@EnableWebMvc
@ComponentScan(basePackages= {"org.zerock.controller"})
public class ServletConfig implements WebMvcConfigurer {

    @Override
    public void configureViewResolvers(ViewResolverRegistry registry){

        InternalResourceViewResolver bean = new InternalResourceViewResolver();
            bean.setViewClass(JstlView.class);
            bean.setPrefix("/WEB-INF/Views/");
            bean.setSuffix(".jsp");
            registry.viewResolver(bean);

    }

    @Override
    public void addResourceHandlers(ResourceHandlerRegistry registry){

        registry.addResourceHandler("/resources/**").addResourceLocations("/resources/");
    }

    

    @Override
    public void addArgumentResolvers(List<HandlerMethodArgumentResolver> list) {

    }

    @Override
    public void addReturnValueHandlers(List<HandlerMethodReturnValueHandler> list) {

    }

    @Override
    public void configureMessageConverters(List<HttpMessageConverter<?>> list) {

    }

    @Override
    public void extendMessageConverters(List<HttpMessageConverter<?>> list) {

    }

    @Override
    public void configureHandlerExceptionResolvers(List<HandlerExceptionResolver> list) {

    }

    @Override
    public void extendHandlerExceptionResolvers(List<HandlerExceptionResolver> list) {

    }

    @Override
    public Validator getValidator() {
        return null;
    }

    @Override
    public MessageCodesResolver getMessageCodesResolver() {
        return null;
    }

    @Override
    public void configurePathMatch(PathMatchConfigurer pathMatchConfigurer) {

    }

    @Override
    public void configureContentNegotiation(ContentNegotiationConfigurer contentNegotiationConfigurer) {

    }

    @Override
    public void configureAsyncSupport(AsyncSupportConfigurer asyncSupportConfigurer) {

    }

    @Override
    public void configureDefaultServletHandling(DefaultServletHandlerConfigurer defaultServletHandlerConfigurer) {

    }

    @Override
    public void addFormatters(FormatterRegistry formatterRegistry) {

    }

    @Override
    public void addInterceptors(InterceptorRegistry interceptorRegistry) {

    }


    @Override
    public void addCorsMappings(CorsRegistry corsRegistry) {

    }

    @Override
    public void addViewControllers(ViewControllerRegistry viewControllerRegistry) {

    }

}

```

[WebConfig] 클래스 생성

```java
public class WebConfig extends AbstractAnnotationConfigDispatcherServletInitializer {


    
    @Override
    protected Class<?>[] getRootConfigClasses() {
        return new Class[] {RootConfig.class};
    }

    @Override
    protected Class<?>[] getServletConfigClasses() {
        return new Class[] {ServletConfig.class};
    }

    @Override
    protected String[] getServletMappings() {
        return new String[] { "/"};
    }
    
}
```


-----------------------------------------

###### 180912_6

Web Deployment Assembly
-

이클립스는 멀티 프로젝트를 고려하여 Deployment Assembly 메뉴를 제공하고, 톰캣 was 가동시 특정 디렉터리 영역에 소스를 배포한다

(기본 .metadata ~ org.eclipse.wst.server.core\tmp1)

예를 들어 어떤 서비스를 개발하는데 그 서비스에는 관리자 페이지와 사용자 페이지 두 개의 사이트를 제공해야 한다고 하자.

프로젝트 설계 시 대게 아래와 같이 두 개의 프로젝트(관리자, 사용자)를 생성할 것이고, 각 프로젝트에서 공통으로 참조해야할 프로젝트를 추가적으로 생성할 것이다.

ADMIN project (관리자)

USER project (사용자)

COMMON project (공통)



즉, ADMIN project + COMMON project 소스를 한 곳으로 배포해서 tomcat을 구동해야 할 배포 디렉토리가 필요한데 그곳이 .metadata\.plugins\org.eclipse.wst.server.core 디렉토리 하위인 것이다.



이처럼 Deployment Assembly는 멀티 프로젝트를 이용해서 개발 진행 시 각 프로젝트의 폴더를 조립하는 기능을 담당한다.



아마 메이븐 프로젝트를 생성하면 다음과 같이 Deployment Assembly가 설정되어 있는 것을 확인할 수 있는데 자세히 보면 왼쪽의 Source 디렉토리에 있는 패스들이 오른쪽의 Deploy Path에 맵핑되어 있는 것을 볼 수 있을 것이다.

결국 배포 디렉토리 하위에 WEB-INF/classes 디렉토리와 같은 식으로 배포가 된다.



결론은 tomcat 플러그인 웹 프로젝트 가상 배포 장소에 배포하게 될 디렉토리 매핑 정보를 관리하는 메뉴인 것이다.

http://lng1982.tistory.com/115


-----------------------------------------

###### 180912_7

Error creating bean with name 'dataSource' 
-

Error creating bean with name 'dataSource' defined in class path resource [spring/root-context.xml]: Bean instantiation via constructor failed; nested exception is org.springframework.beans.BeanInstantiationException: Failed to instantiate [com.zaxxer.hikari.HikariDataSource]: Constructor threw exception; nested exception is java.lang.RuntimeException: Driver net.sf.log4jdbc.sql.jdbcapi.DriverSpy claims to not accept jdbcUrl

db관련 JUnit 테스트는 잘되었는데, 톰캣 구동시 에러가났다.

dataSource 빈을 생성못하는 상황에대해 오타인지, 의존의 문제인지 히카리 관련 문제인지 기타 검색도 하고 하다가

ojdbc jar파일 문제일 가능성이 높다고 생각되었다.

인텔리제이 사용이 익숙치 않아서 ojdbc jar파일을 잘못 넣었는지, ojdbc jar 버전문제인지 싶어서 STS에서도 테스트했다.

그 과정에서 같은 에러메시지를 보았고, 인텔리제이를 쓰느라 책의 이클립스 설정에서 간과했던 부분인 'Web Deployment Assembly'에 대한 설정을 시도하게되었다

해결되는것을 보면서 이 'Web Deployment Assembly'가 뭔지 좀 알아보게되었다.

프로젝트의 코드를 톰캣구동시 배포되는 환경으로 어떤부분을 배포할지에 대한것인데, 외부 jar파일에 대해서는 따로 여기서 추가해줘야 하는것같다.
(그게 아니면 pom.xml에서 수동으로 dependency 설정하고 수동으로 ojdbc를 메이븐 로컬저장소에 넣어도 되지 않을까 싶긴하다)

원인을 알고나니 인텔리제이에서는 그냥 간단하게 Project Structure의 Artifacts에서 Available Elements의 ojdbc jar파일을 이동시켜주기만 배포시에 알아서 된다.


-----------------------------------------

###### 180913_4

@InitBinder
-

파라미터수집시 변환 가능한 데이터는 자동 변환되지만, 경우에 따라 변환해서 처리해야하는 경우가 있다.

'2018-01-01' 문자열을 java.util.Date 타입으로 변환하는 작업도 그렇다.

```java
@Data
public class TodoDTO {

    private String title;
    private Date dueDate;
}

```
```java
//Controller 

    @InitBinder
    public void initBinder(WebDataBinder binder){
        SimpleDateFormat dateFormat = new SimpleDateFormat("yyyy-MM-dd");
        binder.registerCustomEditor(java.util.Date.class, new CustomDateEditor(dateFormat, false));
    }

    @GetMapping("/ex03")
    public String ex03(TodoDTO todo){
        log.info("todo: " + todo);
        return "ex03";
    }
```

호출  http://localhost:8080/sample/ex03?title=test&dueDate=2018-01-01

로그

INFO : org.zerock.controller.SampleController - todo: TodoDTO(title=test, dueDate=Mon Jan 01 00:00:00 KST 2018)

@InitBinder 처리가 되지 않는다면 브라우저는 400에러 발생.

-----------------------------------------

###### 180913_5

@DateTimeFormat
-

@InitBinder를 이용해서 날짜를 변환 할 수있지만

파라미터로 사용되는 인스턴스 변수에 @DateTimeFormat을 적용해도 변환가능

이를 사용할때는 @InitBinder는 필요 x

```java
@Data
public class TodoDTO {

    private String title;
    
    @DateTimeFormat(pattern = "yyyy/MM/dd")
    private Date dueDate;
}
```

요청

http://localhost:8080/sample/ex03?title=test&dueDate=2018/01/01

로그

INFO : org.zerock.controller.SampleController - todo: TodoDTO(title=test, dueDate=Mon Jan 01 00:00:00 KST 2018)


-----------------------------------------

###### 180913_6

@ModelAttrbute
-

스프링 mvc는 기본적으로 JavaBeans 규칙에 맞는 객체는 다시 화면으로 전달한다.

좁은 의미에서 javaBeans규칙은 단순히 생성자가 없거나 빈 생성자를 가져야 하며, getter/setter를 가진 클래스 객체를 의미

전달될때는 클래스명의 앞글자는 소문자로 처리.


반면에 기본자료형의 경우는 파라미터로 선언되더라도 기본적으로 화면까지 전달되지는 않는다.


```java
@GetMapping("/ex04")
public String ex04(SampleDTO dto, int page){

}...
//위에서 dto만 화면에 전달, page는 전달x
```

@ModelAttribute는 전달받은 파라미터를 강제로 Model에 담아서 전달하도록 할때 필요한 어노테이션.

파라미터 타입에 관계없이 무조건 Model로 담아서 전달된다.

파라미터로 전달된 데이터를 다시 화면에서 사용해야할때 유용하게 사용됨.

주의) 기본자료형에 @ModelAttribute 적용할경우

반드시 @ModelAttriibute("page")와 같이 값을 지정해야한다.

>'나는 이것이 뷰에 전달될 이름때문에만 사용하는줄 알았는데 기본자료형의 경우 저렇게 하지않으면 500 에러가났다.

``` 
.NoSuchMethodException: int.<init>()


org.springframework.beans.BeanInstantiationException: Failed to instantiate [int]: No default constructor found; nested exception is java.lang.NoSuchMethodException: int.<init>()
```

디폴트 생성자가 없는 경우 에러가 날수있는것같다.




-----------------------------------------

###### 180914_99

ch3 (정리) 
-

xml의 dtd같은 (xmlx:xsi=http:// wer~"
		xsi:schemaLocation ~)

것들도 dependecy가 없으면 못가져 온다.


Mapper 인터페이스로 sql작성시 ';' 이 없도록 작성해야함.

sql에서 where bno > 0 과 같은 조건은 테이블 검색시 bno라는 컬럼조건을 주어서 Primary Key를 사용하도록 유도하는 조건이다.


자동으로 pk값이 정해지는 경우
-


2가지 방식으로 처리가능

- insert만 처리 -> 생성된  pk를 알 필요없는경우

- insert문이 실행되고 생성된 pk값을 알아야하는경우



delete의 리턴값을 int로 주면 삭제후 반환값에 의해 판단가능. 삭제되면 1이상값을, 해당게시물이 없으면 0이 출력됨

sqldeveloper에서 
```
insert into tbl_board(bno,title,content, writer)values(seq_board.nextval,'이름','내용','작성자');
```
double quote "" 사용하면에러난다.

SQL 오류: ORA-00984: column not allowed here
00984. 00000 -  "column not allowed here"


Oracle의 delete문
-

MySQL과 달리 Oracle은 delete절에 FROM 생략가능..

```
delete tbl_board
where bno =10;
```


assertNotnull 사용안될떄
-

import static org.junit.Assert.assertNotNull;
추가

---

```
    @Override
    public boolean remove(Long bno) {

        log.info("remove....." + bno);

        return mapper.delete(bno) == 1;
    }
```

정상적으로 수정이나 삭제가 이루어지면 1이라는 값이 반환되기 때문에 return == 을 통해서 true/false를 boolean으로 리턴할 수 있다.

INFO : org.zerock.service.BoardServiceTests - REMOVE RESULT: true

리턴활용

      if(service.modify(board)){
            rttr.addFlashAttribute("result", "success");
        }


경우에 따라서 Controller에 대한 테스트 코드를 작성하는 것에대해 거부감을 가지는 경우도 많다.

대부분은 일정에 여유가 없다는 이유로 테스트를 작성하지 않는 경우가 많은데, 프로젝트를 진행하는 멤버들의 경험치가 낮을수록 테스트를 먼저 진행하는 습관을 가지는 것이 좋다. 

반복적으로 입력과 수정,WAS의 재시작 시간을 고려해보면Controller에 대한 테스트를 진행하는 선택이 더 빠른 개발의 결과를 낳는경우가 많다.

<%@ page contentType="text/html;charset=UTF-8" language="java" pageEncoding="UTF-8" %>

이런 페이지 인코딩 관련 태그는 include/header에만 있으면 안되고 페이지 마다 있어야 하는것으로 보인다.


한글문제관련
-
1. 브라우저에서 한글이 깨져서 전송되는지
-> 개발자도구 네트워크에서 헤더확인


2.스프링 MVC쪽에서 한글처리 필터가 등록안되었는지
-> 로그등으로 Controller에 전달될때 이미 한글이 깨진상태로 처리된상황


RedirectAttribute를 활용한 일회성처리
-
```js
    $(function(){
        var result = '<c:out value="${result}"/>';
    })
```

이렇게 해두고 RedirectAttribute의 addFlashAttribute처리 하면 글등록 직후일때 표기되는 그런 일회성 메시지를 자연스럽게 사용가능.


추가로 모달을 활용해서 아래와같이

```js
 $(function(){
        var result = '<c:out value="${result}"/>';

        checkModal(result);

        function checkModal(result){

            if(result == ''){
                return;
            }
            if(parseInt(result) > 0 ){
                $(".modal-body").html("게시글 " + parseInt(result) + " 번이 등록되었습니다.");
            }
            $("#myModal").modal("show");
        }
    });
```


뒤로가기에 대해
-

의외로 꽤 많은 웹사이트들에서 뒤로가기에 대해 제대로 처리되지않아서 , 다운로드를 다시 시도하거나 경고창이 뜨는경우가 있다.


참고로 브라우저에서 앞,뒤로 가기는 서버를 다시호출하는 개념이 아니라 과거의 자신이 가진 모든 데이터를 활용한다.

등록 -> 완료모달 -> 목록 -> 조회

여기서 조회에서 뒤로가기를 했을경우, 목록이 아니라 완료 모달이 뜨는것..


window의 history 객체를 이용해서 모달창을 띄울 필요가 없음을 표시해두는 방식을 이용해야한다.

window의 history객체는 스택구조로 동작한다.

```js
$(function(){
        var result = '<c:out value="${result}"/>';

        checkModal(result);
	//추가된부분
        history.replaceState({}, null, null); 

        function checkModal(result){
		//추가된부분
            if( result === '' || history.state){
                return;
            }
            if(parseInt(result) > 0 ){
                $(".modal-body").html("게시글 " + parseInt(result) + " 번이 등록되었습니다.");
            }
            $("#myModal").modal("show");
        }

        $("#regBtn").on("click",function(){
            self.location = "/board/register";
        });


    });
```

GetMapping 다중 url
-
```java
  @GetMapping({"/get","/modify"})
    public void get(@RequestParam("bno") Long bno, Model model){
        log.info("/get or /modify");
        model.addAttribute("board", service.get(bno));
    }
```

처리하는 내용이 같을경우 리턴 void로 주면 알아서 해당 .jsp를 찾아가는것 같다.

CRUD 뷰단 설계순서
-



register.jsp  -> readPage.jsp(폼삭제 + readOnly+ BoardVO)
-> modify.jsp(폼 생성+제목,내용의 수정위한 readonly삭제)


버튼의 식별에 있어서 아이디로 해도되지만 책에서는 data-oper라는걸 사용했다.

```
 <button type="submit" data-oper="modify" class="btn btn-default">Modify Button</button>

                    <button type="submit" data-oper='remove' class="btn btn-danger">List</button>

                    <button type="submit" data-oper='list' class="btn btn-info">List</button>



//

var operation = $(this).data("oper");

            console.log(operation);

            if(opration === 'remove'){
                formObj.attr("action", "/board/remove");
            }else if(operation === 'list'){
                //move to list
                self.location= "/board/list";
                return;
            }
            formObj.submit();
```


페이징처리는 크게 

1.번호를 사용한 페이징

2.계속 보기형태

계쏙 보기의 경우 Ajax와 앱이 등장한 이후에 무한스크롤이나 더 보기 와 같은 형태로 구현된다.


-----------------------------------------

###### 180914_1

파일 업로드 처리
-

Servlet 3.0전까지는 common의 파일업로드를 사용하거나 cos.jar 등을 이용해서 처리했다.

servlet3.0 이후(톰캣7.0) 기본적으로 업로드되는 파일을 처리할 수 있는 기능이 추가되어서 라이브러리가 따로 필요없다.

그러나 Spring Legacy Project로 생성되는 프로젝트는 Sevlet 2.5기준이므로 3.0이후 지원설정을 사용하기 어렵다.

그러므로 일반적으로 많이 사용하는 commons-fileupload를 사용한다.

```xml
<dependency>
        <groupId>commons-fileupload</groupId>
        <artifactId>commons-fileupload</artifactId>
        <version>1.3.3</version>
    </dependency>
```
```xml
//servlet-context.xml


<beans:bean id="multipartResolver" class="org.springframework.web.multipart.commons.CommonsMultipartResolver">
		<beans:property name="defaultEncoding" value="utf-8"></beans:property>
		//1024 * 1024 * 10 bytes = 10MB , 한번의 Request로 전달될 수있는 최대크기
		<beans:property name="maxUploadSize" value="104785760"></beans:property>
		// 2014 * 2014 * 2 bytes = 2MB , 하나의 파일 최대크기
		<beans:property name="maxUploadSizePerFile" value="2097152"></beans:property>
		//절대 경로를 사용하기 위해 URI 형태로 제공해야하므로 'file:/'로 시작하도록 함
		<beans:property name="uploadTempDir" value="file:/C:/upload/tmp"></beans:property>
		//메모리상에서 유지하는 최대의 크기 , 이 이상의 크기의 데이터는  uploadTempDir에 임시파일의 형태로 보관됨.
		<beans:property name="maxInMemorySize" value="10485756"></beans:property>
	</beans:bean>
```

```html
<form action="/sample/exUploadPost" method="post"
      enctype="multipart/form-data">

    <div>
        <input type='file' name='files'>
    </div>
    <div>
        <input type='file' name='files'>
    </div>
    <div>
        <input type='file' name='files'>
    </div>
    <div>
        <input type='file' name='files'>
    </div>
    <div>
        <input type='file' name='files'>
    </div>
    <div>
        <input type='submit'>
    </div>
</form>
```

```java
  @GetMapping("/exUpload")
    public void exUpload(){
        log.info("exUpload...........");
    }

    @PostMapping("/exUploadPost")
    public void exUploadPost(ArrayList<MultipartFile> files){

        files.forEach(file -> {
            log.info("----------------------");
            log.info("name: " + file.getOriginalFilename());
            log.info("size:" + file.getSize());
        });
    }
```

스프링 mvc는 전달되는 파라미터가 동일한 이름으로 여러개 -> 배열로 처리가능 -> MultipartFIle의 배열타입으로 작성

이 위 코드는 최종업로드를 위해 byte[] 처리해야하는데 아직 처리하지않은 상태.


JavaConfig
---

```java
  @Bean(name = "multipartResolver")
    public CommonsMultipartResolver getResolver() throws IOException{

        CommonsMultipartResolver resolver = new CommonsMultipartResolver();

        //10MB
        resolver.setMaxUploadSize(1024 * 1024 * 10);

        //2MB
        resolver.setMaxUploadSizePerFile(1024 * 1024 * 2);

        //1MB
        resolver.setMaxInMemorySize(1024 * 1024);

        //temp upload
        resolver.setUploadTempDir(new FileSystemResource("C:\\upload\\tmp"));

        resolver.setDefaultEncoding("UTF-8");

        return resolver;
    }
```


-----------------------------------------

###### 180914_2

Controller의 Exception 처리
-

스프링 MVC에서의 처리

- @ExceptionHandler , @ControllerAdvice 이용한 처리

- @ResponseEntity를 이용하는 예외 메시지 구성


[@ControllerAdvice]

AOP를 이용한 방식이다

```java
@ControllerAdvice //스프링컨트롤러에서 발생한 예외를 처리하는 존재임을 명시
@Log4j
public class CommonExceptionAdvice {

    @ExceptionHandler(Exception.class) //해당 메서드가 파라미터에 명시된 예외타입을 처리한다는 의미
    public String except(Exception ex, Model model){

        log.error("Exception ......" + ex.getMessage());
        model.addAttribute("exception", ex);
        log.error(model);
        return "error_page";

    }
}
```

```xml
//servlet-context.xml

<context:component-scan base-package="org.zerock.exception" />
```

```html
<body>

    <h4><c:out value="${exception.getMessage()}"></c:out></h4>

    <ul>
        <c:forEach items="${exception.getStackTrace() }" var="stack">
            <li><c:out value="${stack}"></c:out></li>
        </c:forEach>

    </ul>
</body>
```

[404에러페이지]

404와 500 에러코드가 가장 흔한에러일것이다.

500에러는 Internal Server Error이므로 @Exceptionhandler를 이용해서 처리되지만

잘못된 URL으로 인한 404에러는 좀 다르게 처리하는게 좋다.

서블릿,jsp 당시 개발은 web.xml으로 별도의에러페이지를 지정할 수 있으나, 에러 발생시 추가작업을 하기 어렵기때문에 스프링을 이용해서 404와 같이 WAS 내부에서 발생하는 에러를 처리하는 방식을 알아두면 좋다.


스프링 mvc의  모든 요청은 DispatcherServlet을 이용해서 처리되므로 404에러도 같이 처리할 수 있또록 web.xml을 수정한다.

```xml
<servlet>
        <servlet-name>appServlet</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath:spring/servlet-context.xml</param-value>
        </init-param>
        <!-- 추가된 부분 -->
        <init-param>
            <param-name>throwExceptionIfNoHandlerFound</param-name>
            <param-value>true</param-value>
        </init-param>
        <load-on-startup>1</load-on-startup>
    </servlet>
```

```java
@ControllerAdvice //스프링컨트롤러에서 발생한 예외를 처리하는 존재임을 명시
@Log4j
public class CommonExceptionAdvice {

    @ExceptionHandler(Exception.class) //해당 메서드가 파라미터에 명시된 예외타입을 처리한다는 의미
    public String except(Exception ex, Model model){

        log.error("Exception ......" + ex.getMessage());
        model.addAttribute("exception", ex);
        log.error(model);
        return "error_page";

    }

    //추가된 부분
    @ExceptionHandler(NoHandlerFoundException.class)
    @ResponseStatus(HttpStatus.NOT_FOUND)
    public String handle404(NoHandlerFoundException ex){

        return "custom404";
    }


}
```

JavaConfig
---

web.xml에 설정했던 throwExceptionIfNoHandleFound를 설정하기 위해서 서블릿 3.0이상을 이용해야만 하고 WebConfig 클래스를 아래와 같이 수정해야한다.


```xml
//(기본적으로 있던 servlet-api는 3.0 알파버전이 lastest버전인데 안되는걸로 보이고(예전모델) , 최근까지 버전이 나오고있는 javax.servlet-api의 3.1.0버전 사용.

<dependency>
        <groupId>javax.servlet</groupId>
        <artifactId>javax.servlet-api</artifactId>
        <version>3.1.0</version>
        <scope>provided</scope>
    </dependency>
```


```java

public class WebConfig extends AbstractAnnotationConfigDispatcherServletInitializer {


    @Override
    protected Class<?>[] getRootConfigClasses() {
        return new Class[] {RootConfig.class};
    }

    @Override
    protected Class<?>[] getServletConfigClasses() {
        return new Class[] {ServletConfig.class};
    }

    @Override
    protected String[] getServletMappings() {
        return new String[] { "/"};
    }

	//추가된부분(servlet 3.0밑으로는 지원안됨)
    @Override
    protected void customizeRegistration(ServletRegistration.Dynamic registration){

        registration.setInitParameter("throwExceptionIfNoHandlerFound","true");
        
    }
}
```


-----------------------------------------

###### 180914_5

Mapper 인터페이스 빈 의존주입못받을떄
-

org.springframework.beans.factory.UnsatisfiedDependencyException: Error creating bean with name 'org.zerock.mapper.BoardMapperTests': Unsatisfied dependency expressed through method 'setMapper' parameter 0; nested exception is org.springframework.beans.factory.NoSuchBeanDefinitionException: No qualifying bean of type 'org.zerock.mapper.BoardMapper' available: expected at least 1 bean which qualifies as autowire candidate. Dependency annotations: {}

Mapper를 주입 못받을때 주의사항으로

mapper 인터페이스 는 component-scan이 아닌 mybatis-scan이 되어야 한다는점..

```xml
<mybatis-spring:scan base-package="org.zerock.mapper"></mybatis-spring:scan>
```


- [Mapper 인터페이스 빈 의존주입못받을떄](#180914_5)

-----------------------------------------

###### 180914_6

driverClassName 프로퍼티 에러
-

 Error creating bean with name 'hikariConfig' defined in ServletContext resource [/WEB-INF/spring/root-context.xml]: Error setting property values; nested exception is org.springframework.beans.PropertyBatchUpdateException; nested PropertyAccessExceptions (1) are:
PropertyAccessException 1: org.springframework.beans.MethodInvocationException: Property 'driverClassName' threw exception; nested exception is java.lang.ExceptionInInitializerError

```xml
<property name="driverClassName"
				  value="net.sf.log4jdbc.sql.jdbcapi.DriverSpy"></property>
```

log4jdbc를 쓸떄 빈을 생성할수없고 driverClassName 에 대한 에러가있을떄

단순히 root-context에 driverClassName의 value가 오타가 아니라면

log4j를 위한 설정인

log4jdbc.log4j2.properties의 내용관련 오타일 문제가 크다.

// log4jdbc.log4j2.properties
```
log4jdbc.spylogdelegator.name=net.sf.log4jdbc.log.slf4j.Slf4jSpyLogDelegator
```


-----------------------------------------

###### 180914_3

Incompatible types. Found 
-

```
Incompatible types. Found: 'java.lang.Class<org.springframework.web.servlet.NoHandlerFoundException>', required: 'java.lang.Class<? extends java.lang.Throwable>[]'
```

타입관련 에러가 났는데,

404에러 관련 	
@ExceptionHandler(NoHandlerFoundException.class)	

위코드에 대한 에러인데 

Servlet-api에 대한 dependency가 잘못되었다.

이유는 모르겠으나 이 프로젝트만  servlet-api의 버전이 변경되어있었다.

javax.servlet » servlet-api

3.1.0 버전 -> 2.5 버전으로 내렸다.

servlet-api가 작동이 안되서 에러가 난것..

STS에서는 pom.xml이 바로 에러가 났는데
(Missing artifact javax.servlet:servlet-api:jar:3.1.0)

intellJ에서 그런 에러가 안뜬게좀 아쉽다.

실제로 해당경로에 들어가면 디렉터리는 있지만 안에 jar가 없었다.

아마 jar를 못불러 오는걸봐서 해당버전이 현재 mvn repository에서 지원안되고 있는 상황인가 하는생각이 든다.

-----------------------------------------

###### 180914_5

Mapper 인터페이스 빈 의존주입못받을떄
-

org.springframework.beans.factory.UnsatisfiedDependencyException: Error creating bean with name 'org.zerock.mapper.BoardMapperTests': Unsatisfied dependency expressed through method 'setMapper' parameter 0; nested exception is org.springframework.beans.factory.NoSuchBeanDefinitionException: No qualifying bean of type 'org.zerock.mapper.BoardMapper' available: expected at least 1 bean which qualifies as autowire candidate. Dependency annotations: {}

Mapper를 주입 못받을때 주의사항으로

mapper 인터페이스 는 component-scan이 아닌 mybatis-scan이 되어야 한다는점..

```xml
<mybatis-spring:scan base-package="org.zerock.mapper"></mybatis-spring:scan>
```







-----------------------------------------

###### 180917_2

SelectKey 
-

MyBatis에서 SelectKey는 주로 값을 미리 SQL을 통해서 처리해두고 특정한 이름으로 결과를 보관하는방식.

@Insert할떄 SQL문을 보면 #{bno}와 같이 미리 처리된 결과를 이용하는것을 알 수있다.

```java
public void insertSelectKey(BoardVO board);
```

```xml
 <insert id="insertSelectKey">

        <selectKey keyProperty="bno" order="BEFORE"
                   resultType="long">
            SELECT seq_board.nextval
            FROM DUAL
        </selectKey>

        INSERT INTO tbl_board
                    (bno
                    ,title
                    ,content
                    ,writer)
        VALUES (#{bno}
              , #{title}
              , #{content}
              , #{writer})
    </insert>
```

VO에서 log.info(board) 이런식으로 로그를 찍으려고 하면 lombok에 의해서 자동 toString()될건데

이 로그가 찍힐떄 SelectKey를 활용하지 않으면 bno가 null이 찍히고 SelecKey를 활용하면 bno값을 로그로 바로 확인가능하다.

-----------------------------------------

###### 180917_3

@AllArgsConstructor
-
스프링 4.3 부터 단일 파라미터를 받는 생성자의 경우 필요한 파라미터를 자동으로 주입할 수있다.

@AllArgsConstructor는 모든 파라미터를 이용하는 생성자를 만들기 때문에 실제코드는 BoardMapper를 주입받는 생성자가 만들어진다.



```java
@Log4j
@Service
@AllArgsConstructor //option1
public class BoardServiceImpl implements  BoardService {


    @Setter(onMethod_ = @Autowired) //option2
    private BoardMapper mapper;

...
```

즉 @Setter에 autowired를 하는방법이 있고, 그 대신 @AllArgsConstructor하는방법이 있다.

-----------------------------------------

###### 180917_4

Controller을 쓰는 테스트
-

```java
package org.zerock.controller;

import lombok.Setter;
import lombok.extern.log4j.Log4j;
import org.junit.Before;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;
import org.springframework.test.context.web.WebAppConfiguration;
import org.springframework.test.web.servlet.MockMvc;
import org.springframework.test.web.servlet.request.MockMvcRequestBuilders;
import org.springframework.test.web.servlet.setup.MockMvcBuilders;
import org.springframework.web.context.WebApplicationContext;

/**
 * @author 허정원
 * since 2018-09-17
 * <pre>
 * << 개정이력(Modification Information) >>
 *
 *    수정일         수정자                수정내용
 *  -----------    --------    ---------------------------
 *   2018-09-17
 *
 * </pre>
 */

@RunWith(SpringJUnit4ClassRunner.class)
//Test for Controller
@WebAppConfiguration
@ContextConfiguration({
        "classpath:spring/root-context.xml"
       ,"classpath:spring/servlet-context.xml"})
@Log4j
public class BoardControllerTests {

    @Setter(onMethod_ = {@Autowired})
    private WebApplicationContext ctx;

    private MockMvc mockMvc;


    @Before
    public void setup(){
        this.mockMvc = MockMvcBuilders.webAppContextSetup(ctx).build();
    }

    @Test
    public void testList() throws Exception{

        log.info(
                mockMvc.perform(MockMvcRequestBuilders.get("/board/list"))
               .andReturn()
               .getModelAndView()
               .getModelMap());
        
    }
}

```

@WebAppConfiguration은 Servlet의 ServletContext를 이용하기 위해서인데 WebApplicationContext라는 존재를 이용하기 위해서이다.

MockMvc는 가짜 mvc라고 생각하면된다.

마치 브라우저에서 사용하는것처럼 url,파라미터등을 만들어서 Controller에서실행가능.

MockMvcRequestBuilders라는 존재를 통해 get방식의 호출을 할 수있다.


-----------------------------------------

###### 180917_6

JavaConfig - 기존 web.xml의 UTF-8 인코딩처리
-

```java
//WebConfig의 일부

    @Override
    protected Filter[] getServletFilters(){
        CharacterEncodingFilter characterEncodingFilter =
                                new CharacterEncodingFilter();
        characterEncodingFilter.setEncoding("UTF-8");
        characterEncodingFilter.setForceEncoding(true);

        return new Filter[] { characterEncodingFilter };
    }
```

-----------------------------------------

###### 180917_7

오라클 DB의 페이징처리
-

데이터양이 많을수록 정렬이라는 작업은 리소스를 많이 소모한다.

DB사용시 웹에서 가장 신경쓰는 부분은

1.속도 2.필요한만큼만 데이터를 가져오는것

데이터가 많아지면 매번 정렬하는 결과를 기다리
는 시간을 소모해야하고, 웹에서 동시에 여러명의 
사용자가 정렬이 필요한경우

시스템은 많은 부하가 걸리며 연결 가능한 커넥션의 개수가 점점 줄어서 서비스가 멈추는 상황을 초래한다.

빠르게 동작하는 SQL을 위해 order by를 가능한 하지말아야 한다.

orderby는 1.데이터가 적은경우, 2.정렬을 빠르게 할 수있는 방법이 있는 경우

가 아니면 order by 는 주의해야한다.



## 실행계획과 order by


실행 계획은 SQL을 DB에 어떻게 처리할것인가 에 대한것이다.

SQL이 DB에 전달되면 여러단계에 걸쳐서 이에 대한 처리계획을 다음과 같이 세우게 된다.




1.SQL 파싱 	
: SQL구문의 오류가 있는지, 실행 대상객체(테이블,제약조건,권한 등)가 존재하는지 검사

2.SQL 최적화 => 	
: SQL이 실행되는데 필요한 비용(cose)를 계싼.
이 계산값을 기초로 어떤 방식으로 처리할지 실행계획을 세움.

3.SQL 실행	
: 세워진 실행 계획을 통해 메모리상에서 데이터를 읽거나 물리적인 공간에서 데이터를 로딩한느 등의 작업을 하게 됨.

개발자는 도구나 SQL PLUS 등으로 특정한 SQL에 대한 실행 계획을 알아볼 수있다.

흔히 *SQL 튜닝이라고 하는 작업은 이를 보고 어떤방식이 더 효과적인지 판단하게 수정하게 된다.


*실행계획을 보는 간단한 방법은 '안쪽에서 바깥쪽으로, 위에서 아래로 ' 봐주면된다.

*'FULL'이라는 의미는 테이블의 모든 데이터를 스캔했다는 의미.

실행 계획을 세우는것은 DB에서 하는역할이므로 여러 상황에 따라 DB는 실행계획을 다르게 작성한다.


(참고 - 반복으로 실행하면 DB가 메모리상에 보관하는 데이터를 가져오는 상황이 되어 속도가 빠를수있다).

```
SELECT * 
FROM tbl_board 
ORDER BY bno + 1 desc;
```
위의 sql은 고의로 bno 칼럼에 숫자1을 연산하게끔 하고 역순정렬 -> 테이블 풀스캔->SORT-> 시간많이소모

```
SELECT * 
FROM tbl_board 
ORDER BY bno desc;
```

위는 테이블 전체를 스캔하긴했지만, PK_BOARD라는 것을 이용해서 접근, 위와 달리 SORT과정을 가지지 않는다.
이는 인덱스를 가지기 때문

## order by 보다는 인덱스


데이터 많은 상태의 정렬이 문제가 되고 그 일반적인 해결책은 '인덱스'를 이용하여 정렬을 생략하는 방법이 있다.

인덱스라는 존재가 이미 정렬된 구조이므로 이를 이요해서 별도의 정렬을 하지않는 방법이다.

```sql
SELECT
  /*+ INDEX_DESC(tbl_board pk_board) */
  *
FROM
  tbl_board
WHERE bno > 0;
```

위의 SQL을 실행한 결과는 실행시간의 엄청난 차이가 나게 된다.

실행계획을 보면 확연히 다른모습을 보여준다.

1. SORT를 하지않으며

2. TBL_BOARD를 바로접근하지 않고 PK_BOARD를 이용해서 접근한점

3. RANGE SCAN DESCENDING, BY INDEX ROWID로 접근했다는 점이다.


### PK_BOARD라는 인덱스


테이블 생성시 제약조건으로 PK를 pk_board로 지정했는데, DB에서 PK는 중요한 의미를 가진다.

1. 식별자의 의미 , 2. 인덱스의 의미를 가진다.

### 인덱스는 말 그대로 색인이다.

도서뒤쪽에 정리되어있는 색인처럼. 원하는 내용이 책의 어디에 있는지 알 수있다.

DB의 테이블을 하나의 책이라고 생각하면 어떻게 데이터를 찾거나 정렬하는지 이해가 쉽다.

색인은 알파벳 순서나 한글 순서로 정렬한다. 이를통해 원하는 내요을 위에서 부터 혹은 아래서 부터 반대로 찾거나 하는데 이를 #스캔(scan)이라고 표현한다.


테이블생성함녀서 PK부여하면 인덱스가 만들어지는데 식별의 의미가 있지만, 구조상으로는 인덱스라는 존재가 만들어지는 것을 의미한다.

tbl_board테이블은 bno 라는 칼럼을 기준으로 인덱스를 생성하게 된다.

인덱스와 실제 테이블을 연결하는 고리는 #ROWID라는 존재이다.

ROWID는 데이터 베이스 내의 주소에 해당하며 모든 데이터는 자신만의 주소르 가진다.



## 인덱스를 이용하는 정렬


인덱스는 이미 정렬이 되어있는 상태이므로 이들을 SORT하는 과정을 생략가능

bno의 역순으로 정렬한 결과를 원한다면 이미 정렬된 인덱스를 이용해서 뒤에서 찾아 올라가는 방식을 이용할 수있다.

이때의 개념이 'DESCENDING이다.

실행계획에 RANGE DESCENDING이라고 되어있다.


## 인덱스와 오라클 힌트(hint)



보통 게시판의 목록은 시간의 역순이다.

이때 개발자의 입장에서 정렬을 안하는쪽으로 select문을 실행하고 싶어진다.

오라클은 select문을 전달할 때 #힌트(hint)라는것을 사용할 수있다.

힌트는 지금 내가 전달한 select문을 이렇게 실행해주면 좋겟다는 것.

어떻게 처리하는지에 대한 얘기일 뿐이라 힌트 구문에 에러가 나도 SQL실행에 지장을 주지는 않는다.

그러므로 항상 실행계획으로 잘 수행이 되었는지 확인해야함.

order by bno desc 같은 구문이 때로는 DB상황에 따라 테이블의 모든 데이터를 정렬하는 식으로 동작할 수있어서

힌트로 명시하는것이 좀더 강제성이 부여되는 방식이다.

```sql
SELECT * 
FROM tbl_board
ORDER BY bno DESC;

SELECT /*+INDEX_DESC (tbl_board pk_board) */*
FROM tbl_board;
```

위 두가지 SQL의 결과는 같지만 두번째는 order by없이도 동일한 결과를 낸다.

힌트내용이 tbl_board 테이블에 pk_board인덱스를 역순으로 이용해줄것이라고 되어있어서 

실행계획에서 이를 활용한다.

(힌트에는 여러 종류가있다.)

### 힌트 사용문법

```SQL
SELECT
	/*+ Hint name ( param...) */ column name, ....
FROM
	table name

```


## FULL 힌트

SELECT 문을 실행할 때 테이블 전체를 스캔할 것으로 명시하는 힌트가 FULL 힌트

데이터가 많을때는 상당히 느리게 실행된다.

```sql
SELECT /*+ FULL(tbl_board) */ * FROM tbl_board ORDER BY bno DESC;
```

실행계획을 보면 TBL_BOARD를 FULL로 접근, 다시 SORT를 적용.


## INDEX_ASC, INDEX_DESC 힌트

목록 페이지에서 많이 쓰이는 힌트가 인덱스 관련된 'INDEX_ASC, INDEX_DESC'힌트이다.

주로 order by 를 위해서 사용한다고 생각하면 된다.

인덱스 자체가 정렬을 해둔 상태이므로 이를 통해서 SORT과정을 생략 하기위한 용도

테이블이름과 인덱스 이름을 같이 파라미터로 사용한다.

```sql
SELECT /*+ INDEX_ASC(tbl_board pk_board) */ * FROM tbl_board 
WHERE bno > 0;
```



## ROWNUM과 인라인뷰

역순으로 조회하는 방법을 안 이후에는 필요한만큼의 데이터를 가져오는 방법에대해 생각해봐야한다.

ROWNUM은 쉽게 생각해서 SQL이 실행된 결과에 넘버링을 해준다고생각하면 된다.

ROWNUM을 통해서 해당 데이터가 몇 번째로 나오는지 알아낼 수있따.

ROWNUM은 실제 데이터가 아닌 테이블에서 데이터를 추출한 후에 처리되는 변수이므로 상황에 따라 값이 매번 달라질 수 있다.

만약 FULL스캔을 하는 과정에서 bno가 218번인 데이터는 ROWNUM으로 보면 3번이지만 데이터정렬로 보면 8번째 라던지 그렇게 될수있다.

즉, ROWNU은 데이터를 가져올떄 적용되는것이고, 그 이후에 정렬되는 과정에서 ROWNUM이 변경되지 않는다는것. 다르게 생각하면 정렬은 나중에 처리된다는 의미이기도 하다.

## 인덱스를 이용한 접근시 ROWNUM

ROWNUM은 데이터를 가져오면서 붙는 번호이므로 어떤 순서로 접근하느냐에 따라 ROWNUM값이 달라질수 있다.

FULL로 접근하는게 아니라 PK_BOARD 인덱스로 접근하면 다음과 같은 경로로 접근한다.

1. PK_BOARD  인덱스를 통해서 테이블에 접근

2. 접근한 데이터에 ROWNUM 부여


1의 과정에서 이미 정렬이 되었으므로 ROWNUM값은 FULL로 접근하는것과 전혀 다른값을 가진다

```sql
SELECT /*+ INDEX_ASC(tbl_board pk_board) */
	rownum rn, bno ,title, content
FROM tbl_board;
```

ROWNUM은 데이터에 접근하는 순서이기 때문에 가장 먼저 접근하는 데이터가 1번이 된다.

이를 이용하면 테이블을 bno의 역순으로 접근해서 bno값이 가장 큰 데이터가 ROWNUM 값이 1이 되도록 작성할 수 있다.

```sql
SELECT
/*+ INDEX_DESC(tbl_board pk_board) */
rownum rn, bno, title, content
FROM tbl_board
WHERE bno > 0;
```

위는 인덱스의 역으로 접근하여 bno가 가장높은 값이 가장 먼저 오게되고 rownum인 rn이 1부터 정렬되므로 페이징처리에 해당되는 구조라고 볼수있다.



## 페이지번호 1,2의 데이터

한페이지당 10개의 데이터를 출력할시

```sql
SELECT /*+INDEX_DESC(tbl_board pk_board) */
	rownum rn, bno, title, content
FROM
	tbl_board
WHERE rownum <= 10;
```

2페이지를 출력하고자 할떄 아래와 같이 하면 안된다.

```sql
SELECT /*+INDEX_DESC(tbl_board pk_board) */
	rownum rn, bno, title, content
FROM
	tbl_board
WHERE rownum > 10 and rownum <= 20;
```
위는 아무데이터가 안나온다.

ROWNUM > 10 을 찾으려고 해도 ROWNUM 특성상 1부터 시작하기 때문에 무효화된다

이후 다른데이터를 가지고와도 마찬가지이므로 ROWNUM은 이같은 과정이 반복되면 값을 항상 1로 만들고 없어지는 과정이 반복되는것이다.

따라서 이러한 이유로 SQL작성시 ROWNUM 조건명시할떄는 반드시 1이 포함되어야 한다.

```sql
//ROWNUM은 반드시 1이 포함되도록 해야한다.

SELECT /*+INDEX_DESC(tbl_board pk_board) */
	fownum rn, bno, title, content
FROM
	tbl_board
WHERE rownum <= 20;

```

위는 rownum조건이 1을 포함하도록 하고,  역순으로 20개를 가져옴.


## 인라인뷰(in-line-view) 처리

위의 경우 2페이지 데이터는 가져왔으나 20개를 가지고왔으므로 의도한것이 아니다.


이를 위해 인라인뷰를 이용한다.( SELECT문 안쪽  FROM에 다시 SELECT문)

논리적으로 인라인뷰는 어떤 결과를 구하는 SELECT문이 있고, 그 결과를 다시대상으로 삼아서 SELECT를 하는것.

뷰는 일종의 창문같은 개념으로 복잡한 SELECT처리를 하나의 뷰로 생성하고 사용자들은 뷰를 통해서 복잡하게 만드러진 결과를 마치 하나의 테이블처럼 쉽게 조회한다는 개념이다.

인라인뷰는 이러한 뷰의 작성을 별도로 하지 않고 말 그대로 FROM 구문안에 바로 작성하는 형태.


```sql
SELECT 
	bno, title, content
FROM 
	(
	SELECT /*+INDEX_DESC(tbl_board pk_board) */
	FROM
		tbl_board
	WHERE rownum <= 20
	)
WHERE rn > 10;
```


요약하면

- 필요한 순서로 정렬된 데이터에 ROWNUM붙임 

- ROWNUM <=30 과같이 처음부터 필요한페이지 까지 데이터를 구함

- 구해 놓은 데이터를 하나의 테이블처럼 간주하고 인라인뷰로 처리

- 인라인뷰에서 필요한 데이터만을 남긴다.









-----------------------------------------

###### 180917_8

MyBatis와 스프링에서 페이징처리
-

페이징 - SQL처리 이해 필요한 파라미터가 있다.

1.페이지번호(pageNum)

2.한 페이지당 몇개의 데이터인지(amount)


BoardMapper는 인터페이스+어노테이션이므로 페이징 처럼 경우에 따라 SQL 구문처리가 필요한 상황에서 복잡하게 작성된다

저자는 개인적으로 복잡할떄는 xml로 처리하는것이 알아보기쉽고 관리가 편하다고 생각한다고 한다.


-----------------------------------------

###### 180918_4

페이징처리
-

### 페이징 끝 번호(endPage)계산

` this.endPage = (int)(Math.ceil(페이지번호 /10.0)) * 10;'

### 페이징 시작번호(startPage)계산

`this.startPage = this.endPage - 9; `

### total을 통한 endpage의 재계산

```java
realEnd = (int) (Math.ceil((total * 1.0 / amount ) );

if(realEnd < this.endPage){
	this.endPage = realEnd;
}
```

### 이전(prev)계산

`this.prev = this.startPage > 1;`


### 다음(next)계산

`this.next = this.endPage < realEnd;`


페이징 관련 jsp단 처리

```

                        <div class='pull-right'>
                            <ul class="pagination">

                                <c:if test="${pageMaker.prev}">
                                    <li class="paginate_button previous"><a
                                            href="${pageMaker.startPage -1}">Previous</a></li>
                                </c:if>

                                <c:forEach var="num" begin="${pageMaker.startPage}"
                                           end="${pageMaker.endPage}">
                                    <li class="paginate_button  ${pageMaker.cri.pageNum == num ? "active":""} ">
                                        <a href="${num}">${num}</a>
                                    </li>
                                </c:forEach>

                                <c:if test="${pageMaker.next}">
                                    <li class="paginate_button next"><a
                                            href="${pageMaker.endPage +1 }">Next</a></li>
                                </c:if>


                            </ul>
                        </div>
                        <!--  end Pagination -->
```

위에서 a태그는 `<a href="3">3</a>` 같이 처리된다.(존재x url호출)

위에서 a태그가 원래 동작을 못하도록 js에서 처리한다.

실제 페이지를 클릭하면 동작을 하는 부분은 별돌의 <form> 태그를 이용해서 처리하도록 한다.

```jsp
<form id='actionForm' action="/board/list" method='get'>
    <input type='hidden' name='pageNum' value='${pageMaker.cri.pageNum}'>
    <input type='hidden' name='amount' value='${pageMaker.cri.amount}'>

    <input type='hidden' name='type'
           value='<c:out value="${ pageMaker.cri.type }"/>'>
    <input type='hidden' name='keyword'
        value='<c:out value="${ pageMaker.cri.keyword }"/>'>
</form>
```


## jquery의 clone사용

else if(operation === 'list'){
                //move to list
                formObj.attr("action", "/board/list").attr("method","get");

                var pageNumTag = $("input[name='pageNum']").clone();
                var amountTag = $("input[name='amount']").clone();
                var keywordTag = $("input[name='keyword']").clone();
                var typeTag = $("input[name='type']").clone();

                formObj.empty();

                formObj.append(pageNumTag);
                formObj.append(amountTag);
                formObj.append(keywordTag);
                formObj.append(typeTag);
            }

복사한후 폼을 비우고 필요한 태그를 넣는방식을 clone() 활용


## 다중항목검색에서

```sql
...
where like '%Test%' or content like '%Test%'
and rownum <= 20
)
where rn > 10;
```
위 쿼리는 우리가 기대하는 10건의 데이터가 아닌 더 많은 데이터를 뽑게된다.

이유는 AND 연산자가 OR연산자보다 우선순위가 높기 때문에

ROWNUM이 20보다 작거나 같으면서(AND) 내용에 'Test'라는 문자열이 있거나(OR) 제목에 'Test'
라는 문자열이 있는 게시물을 모두 검색하게된다.

이를 방지하기 위해 우선순위 연산자인 '()'를 or조건들에 처리해줘야한다.

```sql
...
where l(ike '%Test%' or content like '%Test%')
and rownum <= 20
)
where rn > 10;
```

## 동적 SQL중..

### `<trim>,<where>,<set>`

단독으로 사용되지않고 `<if> ,<choose>`와 같은 태그들을 내포하여 SQL들을 연결해주고 앞뒤에 필요한 구문들(AND, OR, WHERE 등)을 추가하거나생략하는 역할을 한다.

SQL을 작성하다보면 상황에 따라서 WHERE나 AND,OR등이 문제가 되는 상황이 발생할 수 있다.

에를들어 'WHERE ROWNUM <= 20'은 문제가 없지만 검색조건이 들어가면 문제가 될 수 있다.

`<where>`의 경우 태그안쪽에서 SQL이 생성될때 WHERE 구문이 붙고 그렇지않으면 생성되지않는다.

```sql
select * from tbl_board
	<where>
		<if test="bno != null">
			bno = #{bno}
		</if>
	</where>
```

즉 , bno 값이 존재 -> select * from tbl_board where bno = 33

bno가 null인 경우 => select * from tbl_board

이렇게 되는것.


`<trim>`은 태그의 내용을 앞의 내용과 관련지어 원하는 접두/접미를 처리 할 수있다.

```sql
select * from tbl_board
	<where>
		<if test="bno != null">
			bno = #{bno}
		</if>
		<trim prefixOverrides = "and">
			rownum = 1
		</trim>
	</where>
```

trim은 prefix, suffix, prefixOverrides, suffixOverrides 속성이 있다.

bno값이 존재 =>select * from tbl_board where bno = 33 and rownum =1

bno값이 존재x경우 => select * from tbl_board where rownum = 1


## <foreach>

foreach는 List, 배열, 맵등을 이용해서 루프처리할 수있다.

주로 IN조건에 많이 사용되나 경우에 따라서 복잡한 where 조건을 만들때도 사용할 수 있다.

예를 들어 제목't' 는 'tttt' 내용'c'는 'cccc'라는 값을 이용한다면

```java
Map<String, String> map = new HashMap<>();
map.put("T", "TTTT");
map.put("C", "CCCC");
```

작성된 Map을 파라미터로 전달하고 foreach를 이용하면 다음과 같은 형식이 가능하다.


```sql
select * from tbl_board

        <trim prefix="where (" suffix= ")" frefixOverrides="OR">
                <foreach item="val" index="key" collection="map">

                        <trim prefix="OR">
                                <if test="key == 'C'.toString()">
                                        content = #{val}
                                </if>
                                <if test="key == 'T'.toString()">
                                        title = #{title}
                                </if>
                                <if test="key == 'W'.toString()">
                                        writer = #{val}
                                </if>
                        </trim>
                </foreach>
        </trim>
```


foreach를 배열이나 List를 이용하는 경우는 item속성만 이용하면 되고, Map의 형태로 key와 value를 이용해야할 때는 index와 item 속성을 둘 다 이용한다.
전달된 값에따라서 다음과 같이 처리된다.

```sql
select * from tbl_board
	where ( content = ?
		OR title = ? )

INFO : jdbc.sqlonly - select * from tbl_board where (content = 'CCCC' OR title = 'TTTT')
```

* p334 -> getTypeArr :검색조건에따른 배열로처리

페이징 결과코드



```sql

<select id="getListWithPaging"
            resultType="org.zerock.domain.BoardVO">
        <![CDATA[
          select
            bno, title, content, writer, regdate, updatedate
          from
              (
              select /*+INDEX_DESC(tbl_board pk_board) */
                rownum rn, bno, title, content, writer, regdate, updatedate
              from
                tbl_board
              where
          ]]>
        <trim prefix="(" suffix=") AND " prefixOverrides="OR">
            <foreach item='type' collection="typeArr">
                <trim prefix="OR">
                    <choose>
                        <when test="type == 'T'.toString()">
                            title like '%'||#{keyword}||'%'
                        </when>
                        <when test="type == 'C'.toString()">
                            content like '%'||#{keyword}||'%'
                        </when>
                        <when test="type == 'W'.toString()">
                            writer like '%'||#{keyword}||'%'
                        </when>
                    </choose>
                </trim>
            </foreach>
        </trim>

        <![CDATA[
      rownum <= #{pageNum} * #{amount}
      )
  where rn > (#{pageNum} -1) * #{amount}
  ]]>
    </select>

```

`<sql><include>`를 적용한 처리

위의 코드에서 동적 처리를 하는 부분이 getListWithpaging뿐만 아니라 getTotalCount에서도 쓰이므로 sql의 일부를 별도로 보관하고 include형태로 재활용하는 방식으로 코드를 변경

```sql
	<sql id="criteria">
		<trim prefix="(" suffix=") AND " prefixOverrides="OR">
			<foreach item='type' collection="typeArr">
				<trim prefix="OR">
					<choose>
						<when test="type == 'T'.toString()">
							title like '%'||#{keyword}||'%'
						</when>
						<when test="type == 'C'.toString()">
							content like '%'||#{keyword}||'%'
						</when>
						<when test="type == 'W'.toString()">
							writer like '%'||#{keyword}||'%'
						</when>
					</choose>
				</trim>
			</foreach>
		</trim>
	</sql>

//

	<select id="getListWithPaging"
		resultType="org.zerock.domain.BoardVO">
  <![CDATA[
  select 
    bno, title, content, writer, regdate, updatedate
  from 
      (
      select /*+INDEX_DESC(tbl_board pk_board) */
        rownum rn, bno, title, content, writer, regdate, updatedate 
      from 
        tbl_board
      where 
  ]]>

<include refid="criteria"></include>
      
  <![CDATA[    
      rownum <= #{pageNum} * #{amount}
      )
  where rn > (#{pageNum} -1) * #{amount}   
  ]]>
	</select>



<select id="getTotalCount" resultType="int">
		select count(*) from tbl_board where 
		
		<include refid="criteria"></include> 
		
		bno > 0
		
	</select>


```


(참고 UriComponentBuilder로 쿼리만들어서 전송하는 방법의 경우 URL 인코딩 결과로 만들어지며 가장 편리한점은 한글처리에 신경쓰지 않아도 된다는점)

컨트롤러에서

`return "~/list" + cri.getListLink();` 이런식으로 호출



-----------------------------------------

###### 180918_8

ch4 REST 와 Ajax를 사용한 댓글처리
-

URL은 URI의 하위개념이라고 볼 수있다.

URL: 이곳에 가면 당신이 원하는것을 찾을수있다.

URI: 당신이 원하는 곳의 주소는 여기다. 와같은 좀더 현실적인 의미
DB의 PK같은 의미로 사용된다.


REST관련 어노테이션중 생소한것

@CrossOrigin : Ajax의 크로스 도메인 문제를 해결해주는 어노테이션

```xml
<dependency>
        <groupId>com.fasterxml.jackson.core</groupId>
        <artifactId>jackson-databind</artifactId>
        <version>2.9.6</version>
    </dependency>

    <!-- https://mvnrepository.com/artifact/com.fasterxml.jackson.dataformat/jackson-dataformat-xml -->
    <!-- xml의 처리 -->
    <dependency>
        <groupId>com.fasterxml.jackson.dataformat</groupId>
        <artifactId>jackson-dataformat-xml</artifactId>
        <version>2.9.6</version>
    </dependency>
    <!--테스트할때 직접 Java 인스턴스를 JSON 타입의 문자열로 변환해야 하는 일이 있어서 gson 라이브러리 추가-->
    <dependency>
        <groupId>com.google.code.gson</groupId>
        <artifactId>gson</artifactId>
        <version>2.2.4</version>
    </dependency>
```


### produces속성

```java
    @GetMapping(value = "/getText", produces = "text/plain; charset=UTF-8")
    public String getText() {

        log.info("MIME TYPE: " + MediaType.TEXT_PLAIN_VALUE);

        return "안녕하세요";

    }
```

produces 속성은 해당 메서드가 생산하는 MIME 타입을 의미한다.

위와같이 문자열로 직접 지정할 수 있고, 메서드 내의 MediaType이라는 클래스를 이용할 수 도 있다.
( //참고
```java
    @GetMapping(value = "/getSample",
            produces = { MediaType.APPLICATION_JSON_UTF8_VALUE,
                    MediaType.APPLICATION_XML_VALUE })
```

위코드는 xml로 로드되며

getSample.xml , getSample.json 이렇게 url에 따라 타입을 다르게 화면에 띄워진다.

produces를 꼭 명시해야 하는것은 아니다

```java
@GetMapping(value = "/getSample")
    public SampleVO getSample() {

        return new SampleVO(112, "스타", "로드");

    }
```
위코드는 기본적으로 text지만 /getSample.json , /getSample.xml  이런식으로url에 따라 다르게 반환한다.

)
(개발자도구의 Response Headers의 Content-type에서 타입 확인가능)


### @NoArgsConstructor
```java
@Data
@AllArgsConstructor //모든 속성 사용한 생성자 생성
@NoArgsConstructor //비어있는 생성자 생성
public class SampleVO {
```
위코드를 통해서 빈 생성자, 모든속성 들어간 생성자 둘다 생성가능.


---

### ReponseEntity

```java
 @GetMapping(value = "/check", params = { "height", "weight" })
    public ResponseEntity<SampleVO> check(Double height, Double weight) {

        SampleVO vo = new SampleVO(000, "" + height, "" + weight);

        ResponseEntity<SampleVO> result = null;

        if (height < 150) {
            result = ResponseEntity.status(HttpStatus.BAD_GATEWAY).body(vo);
        } else {
            result = ResponseEntity.status(HttpStatus.OK).body(vo);
        }

        return result;
    }
```

### @PathVariable 

```java

	@GetMapping("/product/{cat}/{pid}")
	public String[] getPath(@PathVariable("cat") String cat, @PathVariable("pid") Integer pid) {

		return new String[] { "category: " + cat, "productid: " + pid };
	}
```


( @RequestBody는 내부적으로 HttpMessageConverter 타입의 객체들을 이용해서 다양한 포맷의 입력 데이터를 변환가능 , 대부분 jSON데이터를 서버에 보내서 원하는 타입의 객체로 변환하는 용도로 사용되나, 경우에 따라 JSON외에도 원하는 포맷의 데이터를 보내고, 이를 해석해서 원하는 타입으로 사용하기도 함)


## RestContoller의 테스트

REST Controller를 테스트 할수 있는 쉬운방법은

REST 방식의 데이터를 전송하는 툴을 이용하거나, JUnit과 spring-test를 이용해서 테스트 하는 방식을 고려할 수 있다.

```java
 @Test
    public void testConvert() throws Exception {

        Ticket ticket = new Ticket();
        ticket.setTno(123);
        ticket.setOwner("Admin");
        ticket.setGrade("AAA");

        String jsonStr = new Gson().toJson(ticket);

        log.info(jsonStr);

        mockMvc.perform(post("/sample/ticket")
                .contentType(MediaType.APPLICATION_JSON)
                .content(jsonStr))
                .andExpect(status().is(200));
    }
```

Gson의 라이브러리는 자바객체를 json 문자열로 변환하기 위해 사용


---

# Ajax 댓글처리


## @Param어노테이션과 댓글 목록

MyBatis는 두 개 이상의 데이터를 파라미터로 전달하기 위해서 

1. 별도의 객체로 구성

2. Map을 이용

3. @Param을 이용해서 이름을 사용


가장 간단한 방식이 @Param이다.

@Param 의 속성값은 #{}으로 MyBatis에서 사용가능


```java
//BoardMapper.java

public List<ReplyVO> getListWithPaging(@Param("cri") Criteria cri, @Param("bno") Long bno);
```

boardMapper.xml에서 #{bno} 이런식으로 자연스럽게 사용하면 된다.


REST 방식의 URL 설계시에는 PK기준으로 작성하는게 좋다.

PK만으로 조회,수정,삭제가 가능. 다만 댓글 목록은 PK를 사용할 수 없기떄문에 파라미터로 필요한 게시물의 번호, 페이지번호 정보들을 URL에서 표현하는 방식을 사용



REST 방식처리시 주의점은 서버 호출시 데이터의 포맷과 서버에서 보내주는 데이터 타입을 정확히 설게해야한다.

예를 들어 , 댓글경우 브라우저에서는 JSON을 서버에, 서버는 그 결과를 문자열로 브라우저에 전달.

```java
	@PostMapping(value = "/new", consumes = "application/json", produces = { MediaType.TEXT_PLAIN_VALUE })
	public ResponseEntity<String> create(@RequestBody ReplyVO vo) {

		log.info("ReplyVO: " + vo);

		int insertCount = service.register(vo);

		log.info("Reply INSERT COUNT: " + insertCount);

		return insertCount == 1  
				?  new ResponseEntity<>("success", HttpStatus.OK)
				: new ResponseEntity<>(HttpStatus.INTERNAL_SERVER_ERROR);
	}
```

consumes와 produces를 이용해서 JSON 방식의 데이터만 처리되도록 하고, 문자열을 반환하도록 한다.

반환에 있어서는 삼항 연산자를 썼다.




## javascript의 모듈화

javaScript에서 가장 많이 사용하는 패턴중 하나는 모듈 패턴이다.

쉽게 말해 관련있는 함수들을 하나의 모듈처럼 묶음으로 구성하는것을 의미한다.

모듈패턴은 Java의 클래스처럼 JS를 이용해서 메서드를 가지는 객체를 구성한다.

모듈패턴은 JS의 즉시 실행함수와 '{}'을 이용해서 객체를 구성.

즉시 실행하는 함수 내부에서 필요한 메서드를 구성해서 객체를 구성하는 방식이다.

```js
var replyService = (function(){
    function add(reply, callback){
        console.log("reply...........");
    }
    return {add:add};
})();
```


외부에서는 replyService.add(객체,콜백)를 전달하는 형태로 호출할 수있는데, Ajax 호출은 감춰져 있기 떄문에 코드를 좀 더 깔끔하게 작성할 수있다.

reply.js 내에 add 함수는 Ajax를 이용해서 POST 방식으로 호출하는 코드를 작성한다.

```js
var replyService = (function() {

    function add(reply, callback, error) {
        console.log("add reply...............");

        $.ajax({
            type: 'post',
            url: '/replies/new',
            data: JSON.stringify(reply),
            contentType: "application/json; charset=utf-8",
            success: function (result, status, xhr) {
                if (callback) {
                    callback(result);
                }
            },
            error: function (xhr, status, er) {
                if (error) {
                    error(er);
                }
            }
        })
    }
    return{
        add : add
    }
})();
```
(위에서 하위의 ();) 안붙여주면 메소드 못찾아서 인식못함 -> TypeError: replyService.add is not a function 이런 에러메시지.

return을 안적어주면 Uncaught TypeError: Cannot read property 'add' of undefined 이런 에러메시지가 뜬다)



js는 특이하게도 함수의 파라미터 개수를 일치시킬 필요가없기 때문에 callback이나 error와 같은 파라미터는 필요에 따라서 작성할 수 있다.

---

## 댓글의 페이징 처리 

### 데이터 베이스의 인덱스 설계

댓글에 있어서 우선 고려대상은, tbl_reply 접근시 댓글의 번호(rno)가 아닌 게시물번호(bno)중심이 된다는것.

`tbl_reply where bno = 200 order by rno asc' 같이..

만약 100번인 게시물의 댓글이 여러개가 있으면

PK_REPLY를 이용해서 검색을 하다보면, 중간에 있는 다른게시물의 번호들은 건너뛰어가면서 댓글을 찾아야한다.	

(TBL_REPLY -> TBL_REPLY)

이는 데이터가 많아지면 성능에 문제가 생긴다.

효율을 높이려면 게시물의 번호에 맞게 댓글들을 모아서 빠르게 찾을 수 있는 구조로 만드는 것이 좋다.

bno 별로 댓글을 모아두어 특정 게시물의 댓글을 찾을 떄 모여있는 부분만을 찾을 수 있게 할수있다.

(IDX_REPLY -> TBL_REPLY)

`create index idx_reply on tbl_reply (bno desc, rno asc);'

이러한 구조를 이용하면 'bno=200 order by rno asc'와 같이 쿼리를 실행하면 IDX_REPLY에서 200에 해당하는 범위만 찾아서 사용하게 된다(range scan)

이러한 구졸르 생성하는 것을 '인덱스를 생성한다'고 표현한다.




### 인덱스를 이용한 페이징 쿼리

인덱스를 사용하는 이유중 하나는 정렬을 피할수 있기 때문이다.

특정 게시물의 rno를 순번대로  데이터를 조회하고 싶다면 다음과 같은 쿼리를 작성

```sql
SELECT /*+INDEX(tbl_reply idx_reply) */
  rownum rn, bno, rno, reply ,replyer, replyDate, updatedate
FROM tbl_reply
WHERE bno = 3145745
AND rno > 0
```

실행계획으로 결과를 보면 IDX_REPLY를 이용해서 테이블에 접근하게 된다.

실제 페이징에 적용.

```sql
<select id="getListWithPaging" resultType="org.zerock.domain.ReplyVO">

  <![CDATA[
 select  rno, bno, reply, replyer, replydate, updatedate
 from 
   (
    select /*+INDEX(tbl_reply idx_reply) */ 
      rownum rn,  rno, bno, reply, replyer, replyDate, updatedate
    from tbl_reply
    where bno =  #{bno}
    and rno > 0
    and rownum <= #{cri.pageNum} * #{cri.amount}
   ) where rn > (#{cri.pageNum} -1) * #{cri.amount}
]]>

</select>

```






-----------------------------------------

###### 180918_11

ch5. AOP
-

스프링은 AOP 처리가 된 객체를 생성할떄 AspectJ Weaver 라이브러리의 도움을 받는다.

```xml
 <dependency>
        <groupId>org.aspectj</groupId>
        <artifactId>aspectjrt</artifactId>
        <version>${org.aspectj-version}</version>
    </dependency>
    <dependency>
        <groupId>org.aspectj</groupId>
        <artifactId>aspectjweaver</artifactId>
        <version>${org.aspectj-version}</version>
    </dependency>
```

네임 스페이스에 aop, context 추가해야함

root-context.xml에 아래와 같은낸용 추가

```
	xmlns:aop="http://www.springframework.org/schema/aop"
	xmlns:context="http://www.springframework.org/schema/context"


//schemalocation
http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-4.3.xsd
		http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop-4.3.xsd
```

```
//root-context.xml

<context:annotation-config></context:annotation-config>

	<context:component-scan
		base-package="org.zerock.service"></context:component-scan>
	<context:component-scan
		base-package="org.zerock.aop"></context:component-scan>


// Advice의 @Before등이 동작한다.
<aop:aspectj-autoproxy></aop:aspectj-autoproxy>

```

### JavaConfig

```java
@Configuration
@ComponentScan(basePackages= {"org.zerock.service"})
@ComponentScan(basePackages="org.zerock.aop")
@EnableAspectJAutoProxy

@MapperScan(basePackages= {"org.zerock.mapper"})
public class RootConfig {
```



### args를 이용한 파라미터 추적

```java
  @Before("execution(* org.zerock.service.SampleService*.doAdd(String, String)) && args(str1, str2)")
  public void logBeforeWithParam(String str1, String str2) {

    log.info("str1: " + str1);
    log.info("str2: " + str2);
  }  

```

args를 통해 전달되는 파라미터를 간단히 구할 수있다.

그러나 간단할떄에만 쓴다. 복잡하고 파라미터가 다른 여러 종류의 메서드에 적용할때는 @Around와 ProceedingJoinPoint를 이용해서해결


## DB 설계와 트랜잭션

DB 자료의 효율적 관리를 위해 '정규화'작업을 한다.

그 기본은 중복된 데이터를 제거해서 데이터 저장의 효율을 올리자는것.

이는 

1.테이블은 늘어나고, 2. 각 테이블의 데이터 양은 줄어드는것이 일반적

정규화 진행시 원칙적으로 칼럼으로 처리되지 않는 데이터는 다음과 같다.

- 시간이 흐르면 벼경되는 데이터를 칼럼으로 기록x => 대표적으로 사용자의 생년월일은 칼럼이지만, 현재 나이는 칼럼으로 유지x

- 계산이 가능한 데이터를 칼럼으로 기록 x => 주문과 주문 상세가 별도의 테이블로 분리되어있다면 사용자가 한번에 몇개의 상품을 주문했는지 등은 칼럼으로 기록x

- 누구에게나 정해진 값을 이용하는 경우 DB에서 취급x => 2018년 1월 1일은 월요일이며 이것은 모든 사람들에게 통용되므로 기록x 


정규화가 잘되었거나, 위같은 규칙들이 잘 설계된경우 '트랜잭션'이 많이 일어나지는 않는다.

정규화가 진행될수록 테이블은 더 순수한 형태가 되어간다. 이는 트랜잭션 처리의 대상과 멀어진다.

정규화를 진행할수록 테이블은 더욱 간결해지나, 반대로 쿼리 등을 이용해서 필요한 데이터를 가져오는 입장에서는 점점 불편해진다.

현 상황을 알기위해 단순 조회가 아닌, 직접 조인이나 서브쿼리를 이용해서 처리해야하기 때문.


조인,서브쿼리 이용시 다시 성느으이 이슈가 발생할 수 있다.매번 계산이 발생되는 쿼리는 성능 저하를 초래하므로 많은 양의 데이터 처리시 바람직 하지않을수 있다. =>이럴떄는 흔히 '반정규화'를 하게된다.

중복되는 값을 데이터베이스에 보관하고, 대신 조인이나 서브쿼리의 사용을 줄이는 방식이다.


정규화 규칙을 따르면 게시물,댓글 테이블을 따로두나 목록페이지를 생각하면 리스트에서 댓글의 갯수를 보통 카운트해서 보여주는게 있다.

그때 조인이나 서브쿼리를 이용해서 처리한다.

이러한 상황은 흔히 tbl_board 테이블에 댓글의 숫자를 직접 칼럼으로 처리하는 경우가 많다.

이로인해 게시물 목록 가져올경우에 댓글 테이블을 이용할 필요가 없어지면 그만큼 성능상 이득을 볼 수있게된다.


반정규화는 이처럼 중복이나 계산의 결과를 미리 보관해서 좀더 빠른결과를 얻기 위한 노력이다.

이로인해 쿼리가 단순해지고 성능상 이득이 있지만, 대신 댓글을 추가할떄 댓글 테이블에 insert하고 댓글 숫자는 tbl_board테이블에 update하는 작업이 필요해지고, 이는 하나의 트랜잭션으로 관리되어야하는 작업이다.


---

### 트랜잭션 환경

```xml
	<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-tx</artifactId>
			<version>${org.springframework-version}</version>
		</dependency>
```

root-context에 tx 네임스페이스 설정해야함.

```xml
//root-context.xml

xmlns:tx="http://www.springframework.org/schema/tx"

//schemalocation
http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx-4.3.xsd"


//


<tx:annotation-driven />


<aop:aspectj-autoproxy></aop:aspectj-autoproxy>


```

### JavaConfig의 트랜잭션 설정

```java
@Configuration
@ComponentScan(basePackages= {"org.zerock.service"})
@ComponentScan(basePackages="org.zerock.aop")
@EnableAspectJAutoProxy

//추가된부분
@EnableTransactionManagement

@MapperScan(basePackages= {"org.zerock.mapper"})
public class RootConfig {


...

@Bean
  public DataSourceTransactionManager txManager() {
      return new DataSourceTransactionManager(dataSource());
  }
...
```

@EnableTransactionManagement 설정은 'aspectj-autoproxy'에 대한 설정이 되고 txManager()는 <bean>설정을 대신하게 된다.


---

ServiceImpl에 해당 트랜잭션 로직이 있는 메소드에 @Transactional 처리해줘야한다.

```java

  @Setter(onMethod_ = { @Autowired })
  private Sample1Mapper mapper1;
  
  @Setter(onMethod_ = { @Autowired })
  private Sample2Mapper mapper2;
  
  @Transactional
  @Override
  public void addData(String value) {
    
    log.info("mapper1....................");
    mapper1.insertCol1(value);
    
    log.info("mapper2.....................");
    mapper2.insertCol2(value);
    
    log.info("end..........................");
    
  }


```

### @Transactional 어노테이션 우선순위

1.메서드 > 2. 클래스 > 3. 인터페이스


### @Param

mapper 전달에서 사용하는 다중 파라미터 방법중 하나이다.

`public void updateReplyCnt(@Param("bno") Long bno, @Param("amount") int amount);`



```xml
 <update id="updateReplyCnt">
    update tbl_board set replycnt = replycnt + #{amount} where bno = #{bno}
  </update>  
```


-----------------------------------------

###### 180918_12

ch6. 파일업로드 방식
-

파일업로드를 크게 나누면

1. `<form>` 태그를 이용하는방식: 브라우저의 제한이 없어야 하는경우에 사용.	
- 일반적으로 페이지 이동과 동시에 첨부파일을 업로드 하는방식	
- `<iframe>`을 이용해서 화면의 이동없이 첨부파일을 처리하는 방식

2. Ajax를 이용하는 방식: 첨부파일을 별도로 처리하는 방식.	
- '<input type="file">'을 이용하고 Ajax로 처리하는 방식	
- HTML5의 Drag And Drop 기능이다 jQuery 라이브러리를 이용해서 처리하는 방식.

### 첨부파일에 사용되는 API

1. cos.jar: 2002년이후 개발종료. 비권장

2. commons-fileupload: 가장 일반적많이활용, 서블릿 스펙 3.0 이전에도 사용가능

3. 서블릿 3.0이상 - 3.0 이상부터는 자체적인 파일 업로드 처리가 API상에서 지원


책에서 는 3번쨰 방법을 사용한다.

```xml
//web.xml 수정전(legacy)

<web-app version="2.5" xmlns="http://java.sun.com/xml/ns/javaee"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://java.sun.com/xml/ns/javaee http://java.sun.com/xml/ns/javaee/web-app_2_5.xsd">
```


```xml
// web.xml수정후 
<web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns="http://xmlns.jcp.org/xml/ns/javaee"
	xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_3_1.xsd"
	id="WebApp_ID" version="3.1">
```


`<servlet>` 태그안에 `<multipart-config>`태그를 추가한다.

```xml
//web.xml

	<servlet>
		<servlet-name>appServlet</servlet-name>
		<servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
		<init-param>
			<param-name>contextConfigLocation</param-name>
			<param-value>/WEB-INF/spring/appServlet/servlet-context.xml</param-value>
		</init-param>
		<load-on-startup>1</load-on-startup>

		<multipart-config>
			<location>C:\\upload\\temp</location>
			<max-file-size>20971520</max-file-size> <!--1MB * 20 -->
			<max-request-size>41943040</max-request-size><!-- 40MB -->
			<file-size-threshold>20971520</file-size-threshold> <!-- 20MB -->
		</multipart-config>

	</servlet>

```

```
//servlet-context.xml


	<beans:bean id="multipartResolver"
		class="org.springframework.web.multipart.support.StandardServletMultipartResolver">

	</beans:bean>

```

## JavaConfig

`<multipart-config>' 는 WebConfig 클래스에서 javax.servlet.MultipartConfigElement 클래스를 이용

```java
//WebConfig.java

@Override
	protected void customizeRegistration(ServletRegistration.Dynamic registration) {

		registration.setInitParameter("throwExceptionIfNoHandlerFound", "true");

		MultipartConfigElement multipartConfig = 
				new MultipartConfigElement("C:\\upload\\temp", 
						20971520, 
						41943040,
						20971520);
		
		registration.setMultipartConfig(multipartConfig);

	}
```


```java
//ServletConfig.java

  @Bean
  public MultipartResolver multipartResolver() {
    StandardServletMultipartResolver resolver 
              = new StandardServletMultipartResolver();
    return resolver;
  }
```


	<input type='file' name='uploadFile' multiple>

최근 브라우저에서 지원되는속성인데

하나의 `<input>` 태그로 한꺼번에 여러개의 파일을 업로드할 수있다.



## 파일의 확장자나 크기의 사전 처리

최근 포털에서도 특정한 확장자를 제외한 파일들의 업로드를 제한하는 경우가 많은데, 이는 첨부파일을 이용하는 웹 공격을 막기 위해서 행해지는 조치이다.

exe,sh,zip등의경우 업로드를 제한하는 처리를 해본다.

이를 위해 파일확장자를 정규표현식을 이용해서 검사할 수 있다.


```js
var regex = new RegExp("(.*?)\.(exe|sh|zip|alz)$");
		var maxSize = 5242880; //5MB

		function checkExtension(fileName, fileSize) {

			if (fileSize >= maxSize) {
				alert("파일 사이즈 초과");
				return false;
			}

			if (regex.test(fileName)) {
				alert("해당 종류의 파일은 업로드할 수 없습니다.");
				return false;
			}
			return true;
		}
```

```js
$("#uploadBtn").on("click", function(e) {

			var formData = new FormData();

			var formData = new FormData();

			var inputFile = $("input[name='uploadFile']");

			var files = inputFile[0].files;

			//console.log(files);

			for (var i = 0; i < files.length; i++) {

				if (!checkExtension(files[i].name, files[i].size)) {
					return false;
				}

				formData.append("uploadFile", files[i]);

			}

```

### 중복된 이름의 첨부파일 처리

1. 현재시간을 밀리세컨드(천분의1초)까지 구분해서 파일이름을 생성한다.

2. UUID를 이용한 문자열 생성처리



### 섬네일 이미지 생성

만일 용량이 큰 파일을 섬네일 처리하지 않으면 모바일 같은 환경에서 많은 데이터를 소비해야만 한다.

그 방법에는 여러가지가 있는데

- JDK 1.4 부터 제공하는 ImageIO를 이용

- ImgScalr 를 사용

JDK에 포함된 API보다 별도의 라이브러리를 사용하는 경우가 많은데, 이는 이미지를 축소했을 때의 크기나 해상도를 직접 조절하는 작업을 줄이기 위해서이다.

책에서는 'Thumbnailator' 라입르러리 사용

```xml
<!-- https://mvnrepository.com/artifact/net.coobird/thumbnailator -->
		<dependency>
			<groupId>net.coobird</groupId>
			<artifactId>thumbnailator</artifactId>
			<version>0.4.8</version>
		</dependency>
```

#### 이미지 파일의 판단

```java

private boolean checkImageType(File file) {

		try {
			String contentType = Files.probeContentType(file.toPath());

			return contentType.startsWith("image");

		} catch (IOException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}

		return false;
	}
```

//

byte[]로 이미지 파일의 전송시 브라우저에 보내주는 MIME 타입이 파일에 종류에 따라 달라지는점이 신경쓰이는 점이다.

이 부분을 해결하기 위해 probeContentType()을 이요해서 적절한 MIME 타입 데이터를 Http 헤더 메시지에 포함시킬수 있도록 처리한다.

```java

		try {
			HttpHeaders header = new HttpHeaders();

			header.add("Content-Type", Files.probeContentType(file.toPath()));
			result = new ResponseEntity<>(FileCopyUtils.copyToByteArray(file), header, HttpStatus.OK);
		} catch (IOException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}
		return result;
	}

```

이로인해 파일의 확장자에 맞게 MIME타입이 변경된다.


### 첨부파일의 이름에 포함된 공백문자나 한글이름 문제

이를 해결하기 위해 encodeURIComponent()를 이용해서 URI 호출에 적합한 문자열로 인코딩 처리해야한다.

```js
function showUploadedFile(uploadResultArr){
			    
		    var str = "";
		    
		    $(uploadResultArr).each(function(i, obj){
		        
		        if(!obj.image){
		          
		          var fileCallPath =  encodeURIComponent( obj.uploadPath+"/"+ obj.uuid +"_"+obj.fileName);
		          
		          str += "<li><a href='/download?fileName="+fileCallPath+"'>" 
		        		  +"<img src='/resources/img/attach.png'>"+obj.fileName+"</a></li>"
		        }else{
		          
		          var fileCallPath =  encodeURIComponent( obj.uploadPath+ "/s_"+obj.uuid +"_"+obj.fileName);
		          
		          str += "<li><img src='/display?fileName="+fileCallPath+"'><li>";
		        }
		    });
		    
		    uploadResult.append(str);
		  
		  }
```


## 첨부파일의 원본 보여주기

섬네일의 경우 클릭시에 화면에 크게 원본파일을 보여주는 형태로 처리되야하는데

이 경우 브라우저에서 새로운 `<div>` 등을 생성해서 처리하는 방식을 이용하는데

흔히 ### 'light-box' 라고 한다

직접구현하거나, 여러 라이브러리에서 사용가능


## 게시물의 조회와 첨부파일


게시물 조회에서 게시물과 첨부파일에 대해

화면에서 두 테이블에 있는 정보를 사용하기 위해서 다음과 같은 방법을 고려

1. BoardVO 객체를 가져올때 join을 처리해서 한꺼번에 게시물과 첨부파일의 정보를 같이 처리하는 방식. 	
=>DB를 한번만 호출해서 효율적이나 MyBatis쪽에서 처리할게 많아짐

2. JSP에서 첨부파일의 정보를 Ajax를 이용해서 처리하는 방식.	
=>다시 쿼리를 처리해야 하는 불편함이 있으나, 난이도가 낮고 , 화면에서 처리는 JS 처리가 복잡해진다.

전통적 방식은 1번 방식. 쿼리를 1회-> DB부하를 줄여줄 수있다.



## 파일삭제관련

DB 삭제를 먼저 호출 -> 파일 삭제

- 해당 게시물의 첨부파일 정보를 미리 준비

- DB 상에서 해당 게시물과 첨부파일 데이터 삭제

- 첨부파일 목록을 이용해서 해당폴더에서 섬네일 이미지와 일반 파일을 삭제




## 잘못 업로드된 파일 삭제

Ajax 업로드는 게시물 등록하거나 수정하기 전에 미리 업로드시킨 파일들을 볼 수 있다는 장점이 있지만, 다음과 같은 문제가 있다.

- 첨부파일만을 등록하고 게시물 등록x시에 문제		
-> 파일은 이미 서버에 업로드되었으나, 게시물을 등록하지 않았으므로 의미없이 파일들만 서버에 업로드된 상황

- 게시물을 수정할 때 파일을 삭제했지만 실제로 폴더에서 기존파일은 삭제 되지 않은 문제 -> DB에는 기존파일이 삭제되었지만, 실제 폴더에 남는문제

위 상황의 공통적 문제는 Ajax 작업후 비정상적 브라우저를 종료하거나 페이지를 빠져나가는 문제.


이 문제 해결의 핵심은 정상적으로 사용자의 게시물에 첨부된 파일인지 , 사용자가 게시물을 수정할때 업로드했지만 최종적으로 사용되는 파일인지 아닌지 파악 해야함.


로직은 게시물이 최종 submit 되지않으면 첨부파일을 등록되어 업로드되었어도, DB에는 변화가 없다.


정상적으로 첨부파일이 게시물에 사용되면 db의 tbl_attach 테이블에 기록되어있으므로, 비교하는 작업을 해서 업로드된 파일의 목록을 찾아야 한다.

이떄 반드시 오늘날짜가 아닌 파일들을 대상으로 한다. 오늘 날짜를 대상으로 하면 현재 게시물을 작성하거나 수정중인 파일을 삭제할 가능성이 있음.

첨부파일 목록에서 업로드 되었으나 DB에는 존재하지 않는 파일을 수동으로 삭제한다


이 작업은 주기적으로 동작해야 하므로 스케줄링을 할 수있는 Spring-Batch나 Quartz 라이브러리를 이용한다.


## Quartz 라이브러리 설정

Quartz 라이브러리는 일반적으로 스케줄러를 구성하기 위해서 사용한다.

서버를 운영하기 위해 간혹 주기적으로 매일, 매주, 매월 등 주기적으로 특정한 프로그램을 실행할 필요가 있다.

이 작업은 운영체제의 기능을 이용해서 작업을 할 수도 있지만, 스프링과 Quartz 라이브러리로 간단히 처리가능

(Spring Batch의 경우 많은 양의 데이터를 주기적으로 읽고 쓰는 작업에는 유용하지만 설정은 더 복잡함.)



```xml

	<!-- https://mvnrepository.com/artifact/org.quartz-scheduler/quartz -->
	<dependency>
		<groupId>org.quartz-scheduler</groupId>
		<artifactId>quartz</artifactId>
		<version>2.3.0</version>
	</dependency>


	<!-- https://mvnrepository.com/artifact/org.quartz-scheduler/quartz-jobs -->
	<dependency>
		<groupId>org.quartz-scheduler</groupId>
		<artifactId>quartz-jobs</artifactId>
		<version>2.3.0</version>
	</dependency>
```

Quartz에 대한 설정은 xml과 어노테이션 활용가능

root-context.xml 의 task 네임스페이스를 추가해야함

```
//root-context.xml

xmlns:task="http://www.springframework.org/schema/task"

//schemaLocation

http://www.springframework.org/schema/task http://www.springframework.org/schema/task/spring-task-4.3.xsd


//

<context:component-scan
    base-package="org.zerock.task"></context:component-scan>


<task:annotation-driven />
```


### JavaConfig

```java
@Configuration
@ComponentScan(basePackages= {"org.zerock.service"})
@ComponentScan(basePackages="org.zerock.aop")
@ComponentScan(basePackages="org.zerock.task") 

@EnableAspectJAutoProxy
@EnableScheduling
@EnableTransactionManagement

@MapperScan(basePackages= {"org.zerock.mapper"})
public class RootConfig {
```
---

task 패키지를 만들고 FileCheckTask 클래스생성

```java
package org.zerock.task;

import java.io.File;
import java.nio.file.Path;
import java.nio.file.Paths;
import java.text.SimpleDateFormat;
import java.util.Calendar;
import java.util.Date;
import java.util.List;
import java.util.stream.Collectors;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.scheduling.annotation.Scheduled;
import org.springframework.stereotype.Component;
import org.zerock.domain.BoardAttachVO;
import org.zerock.mapper.BoardAttachMapper;

import lombok.Setter;
import lombok.extern.log4j.Log4j;

@Log4j
@Component
public class FileCheckTask {

	@Setter(onMethod_ = { @Autowired })
	private BoardAttachMapper attachMapper;

	private String getFolderYesterDay() {

		SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd");

		Calendar cal = Calendar.getInstance();

		cal.add(Calendar.DATE, -1);

		String str = sdf.format(cal.getTime());

		return str.replace("-", File.separator);
	}

	@Scheduled(cron = "0 0 2 * * *")
	public void checkFiles() throws Exception {

		log.warn("File Check Task run.................");
		log.warn(new Date());
		// file list in database
		List<BoardAttachVO> fileList = attachMapper.getOldFiles();

		// ready for check file in directory with database file list
		List<Path> fileListPaths = fileList.stream()
				.map(vo -> Paths.get("C:\\upload", vo.getUploadPath(), vo.getUuid() + "_" + vo.getFileName()))
				.collect(Collectors.toList());

		// image file has thumnail file
		fileList.stream().filter(vo -> vo.isFileType() == true)
				.map(vo -> Paths.get("C:\\upload", vo.getUploadPath(), "s_" + vo.getUuid() + "_" + vo.getFileName()))
				.forEach(p -> fileListPaths.add(p));

		log.warn("===========================================");

		fileListPaths.forEach(p -> log.warn(p));

		// files in yesterday directory
		File targetDir = Paths.get("C:\\upload", getFolderYesterDay()).toFile();

		File[] removeFiles = targetDir.listFiles(file -> fileListPaths.contains(file.toPath()) == false);

		log.warn("-----------------------------------------");
		for (File file : removeFiles) {

			log.warn(file.getAbsolutePath());

			file.delete();

		}
	}
}

```


@Scheduled 내에 cron 이라는 속성을 부여해서 주기를 제어한다.

로그 확인을 위해 log.warn() 레벨로  실행중에 확인할 수있도록 한다.


DB에 등록된 어제날짜의 파일의 목록이 필요하므로 BoardAttachMapper에 첨부파일 목록을 가져오는 메서드 작성

```java
//BoardAttachMapper.java

public List<BoardAttachVO> getOldFiles();
```


어제 날짜의 파일목록

```xml
//BoardAttachMapper.xml

<select id="getOldFiles"
		resultType="org.zerock.domain.BoardAttachVO">

		select * from tbl_attach where uploadpath = to_char(sysdate -1 ,'yyyy\mm\dd')

	</select>
```

### Cron 설정과 삭제처리

Cron은 원래 유닉스 계열의 스케줄러 프로그램 이름이지만, 많이 사용되다 보니 언어나 기술에 맞는 라이브러리 형태로 만힝 사용됨.

@Scheduled(cron="0 * * * * ")같은 표현식이 있는데

그 의미는 '매분 0초가 될때마다 실행한다' 이다.

 ![](https://drive.google.com/uc?export=view&id=1qdkoNxPiqBAZC88Y9k6z50IHrDef644v)



작업순서

1. DB에서 어제 사용된 파일목록 얻어오기

2. 해당 폴더의 파일목록에서 DB에 없는 파일 찾기

3. 이후 DB에 없는 파일을 삭제


위의 코드는 매일 새벽 2시에 동작한다. attachMapper에서 어제 날짜로 보관되는 모든 첨부파일의 목록을 가져온다. 

DB에서 가져온 파일 목록은 BoardAttachVO 타입의 객체이므로 , 나중에 비교를 위해서 java.nio.Paths 의 목록으로 변환한다.

이때 이미지 파일의 경우에는 섬네일 파일도 목록에 필요하기 때문에 별도로 처리해서 해당 일의 예상 파일 목록을 완성한다.

코드에서는 fileListPaths 라는 이름의 변수로 처리한다.

DB에 있는 파일들의 준비가 끝나면 실제 폴더에 있는 파일들의 목록에서 DB에 없는 파일들을 찾아서 목록으로 준비한다.

이 결과는 removeFiles 변수에 담아서 처리한다. 최종적으로는 삭제 대상이 되는 파일들을 삭제한다.


-----------------------------------------

###### 180918_13

ch7. 스프링 웹 시큐리티
-

웹에서 사용자 권한, 등급에 기반을 두는 로그인 체크를 이용한다. 

웹에서 기본적으로 쿠키를 이용하거나 세션을 이용하는 방식이 일반적이다. 

스프링 시큐리티를 이용하여 다음 같은 작업을 간편히 처리할 수 있다

- 로그인 처리와 CSRF 토큰 처리

- 암호화처리

- 자동로그인

- JSP에서의 로그인 처리



시큐리티의 기본동작 방식은 서블릿의 여러 종류의 필터와 인터셉터를 이용해서 처리된다.

필터는 서블릿에서 말하는 단순한 필터를 의미하고, 인터셉터는 스프링에서 필터와 유사한 역할을 한다.


차이는 필터는 스프링과 무관하게 서블릿 자원이며, 인터셉터는 스프링의 빈으로 관리되면서 스프링의 컨텍스트 내에 속한다.

그로인해 인터셉터는 스프링 컨텍스트 내에 있는 모든 자원을 활용할수 있다.

스프링 시큐리티는 위와같이 인터셉터,필터를 이용하면서 별도의 컨텍스트를 생성해서 처리한다.

 ![](https://drive.google.com/uc?export=view&id=1c1S1KZonX2oUbZHnRZ_6rjr8uaUGWFN2)


```xml

<dependency>
    <groupId>org.springframework.security</groupId>
    <artifactId>spring-security-web</artifactId>
    <version>5.0.7.RELEASE</version>
</dependency>

<dependency>
    <groupId>org.springframework.security</groupId>
    <artifactId>spring-security-config</artifactId>
    <version>5.0.7.RELEASE</version>
</dependency>

<dependency>
    <groupId>org.springframework.security</groupId>
    <artifactId>spring-security-core</artifactId>
    <version>5.0.7.RELEASE</version>
</dependency>

<!-- https://mvnrepository.com/artifact/org.springframework.security/spring-security-taglibs -->
<dependency>
    <groupId>org.springframework.security</groupId>
    <artifactId>spring-security-taglibs</artifactId>
    <version>5.0.7.RELEASE</version>
</dependency>


```

security-context.xml을 생성한다.

security 네임스페이스를 설정.

```xml
//sequrity-context.xml

xmlns:security="http://www.springframework.org/schema/security"

// schemaLocation

"http://www.springframework.org/schema/security http://www.springframework.org/schema/security/spring-security.xsd

```
스프링 시큐리티 5버전에서 한가지 주의사항은

 5.0의  버전의 경우 네임스페이스에서 문제가 발생한다. 그래서 위와같이 5.0 을 사용하지 않아야함(schema location의 spring-security-5.0.xsd 사용x)


그리고 시큐리티가 스프링 mvc에서 사용되기 위해서 필터를 이용해서 스프링 동작에 관여하도록 설정한다.

```xml
//web.xml

	<filter>
		<filter-name>springSecurityFilterChain</filter-name>
		<filter-class>org.springframework.web.filter.DelegatingFilterProxy</filter-class>
	</filter>

	<filter-mapping>
		<filter-name>springSecurityFilterChain</filter-name>
		<url-pattern>/*</url-pattern>
	</filter-mapping>
```

여기까지 하고 실행하면 
No bean named 'springSecurityFilterChain' 에러가 나는데

이유는 시큐리티 컨텍스트 파일을 아직 로드하지 못했기 때문 -> web.xml설정

```xml
web.xml

	<context-param>
		<param-name>contextConfigLocation</param-name>
		<param-value>/WEB-INF/spring/root-context.xml
    /WEB-INF/spring/security-context.xml
    </param-value>
	</context-param>

```

```xml
//security-context.xml

<security:http>
			<security:form-login />
	</security:http>
	
	<security:authentication-manager>

</security:authentication-manager>

```


## 시큐리티가 필요한 URI 설계

시큐리티에 의해 제어가 필요한 URI를 설계하고 적용한다.

- /sample/all -> 로그인을 하지 않은 사용자도 접근 가능한 URI

- /sampel/member -> 로그인 한 사용자들만이 접근할 수 있는 URI

- /sample/admin -> 로그인 한 사용자들 중에서 관리자 권한을 가진 사용자만이 접근할 수 있는  URI


## 인증(Authentication)과 권한부여(Authorization - 인가)

인증(Authentication)은 스스로 자신을 증명하는것

권한부여(Authorization)은 남에 의해서 자격이 부여된다.


시큐리티에서 가장 중요한 역할을 하는 존재가 인증을 담당하는 AuthenticationManager(인증 매니저)라는 존재이다.

이는 다양한 방식의 인증을 처리할 수 있도록 아래 와 같은 구조로 설계되어있다.

 ![](https://drive.google.com/uc?export=view&id=1jBN2XLYED7HF5ede14vxaRDb-Zr-RmSY)


ProviderManager는 인증에 대한 처리를 AuthenticationProvider 타입의 객체를 이용해서 처리를 위임한다

 ![](https://drive.google.com/uc?export=view&id=1kXRp5juDaZwwYyI-wjesPuSFNUJBu5s0)


AuthenticationProvider(인증 제공자)는 실제 인증 작업을 진행한다.

이떄 인증된 정보에는 권한에 대한 정보를 같이 전달하게 되는데 이 처리는 UserDetailsService라는 존재와 관련이 있다.

UserDetailsService 인터페이스의 구현체는 실제로 사용자의 정보와 사용자가 가진 권한의 정보를 처리해서 반환한다.

 ![](https://drive.google.com/uc?export=view&id=1bZogoPKzzIQPX6Xulr4PxKHMgjlAkNM_)



### 개발자가 스프링 시큐리티를 커스터마이징 하는 방식

1. AuthenticationProvider를 직접 구현하는 방식

2. 실제 처리를 담당하는 UserDetailsService를 구현하는 방식

대부분 1번의 방법을 사용하느것으로 충분하지만, 새로운 프로토콜이나 인증 구현 방식을 직접 구현하는 경우에는 AutenticationProvider 인터페이스를 직접 구현해서 사용한다.



## 로그인과 로그아웃 처리

```xml
//security-context.xml
<security:http>

		<security:intercept-url pattern="/sample/all" access="permitAll"/>

		<security:intercept-url pattern="/sample/member"
								access="hasRole('ROLE_MEMBER')"/>

		<security:form-login />
		
	</security:http>
```

특정한 URI에 접근할때 인터셉터를 이용해서 접근을 제한하는 설정은 <security:inertcept-url>을 이용한다. 

이는 pattern이라는 속성과 access라는 속성을 지정해야만 한다.

access의 속성값으로 사용되는 문자열은 1. 표현식 2. 권한명 을 의미하는 문자열을 이용할 수 있다.

<security:http> 는 기본설정이 표현식을 이용하는 것이다.

만일 단순한 문자열만 이용하고 싶은 경우에는 use-expressions="false"를 지정한다.

(그러나 표현식을 사용하는 방식이 권장된다)


흔히 생각하는 시스템의 userid개념은 스프링 시큐리티에서의 username에 해당된다.

스프링 시큐리티의 User는 인증 정보와 권한을 가진 객체이다.



인증과 권한에 대한 실제 처리는 UserDetailsService라는 것을 이용해서 처리하는데, XML에서는 다음과 같이 지정할 수있다.

```
//security-context.xml

<security:http>

		<security:intercept-url pattern="/sample/all" access="permitAll"/>

		<security:intercept-url pattern="/sample/member"
								access="hasRole('ROLE_MEMBER')"/>

		<security:form-login />

	</security:http>
//추가된부분

	<security:authentication-manager>

		<security:authentication-provider>

			<security:user-service>

				<security:user name="member" password="member" authorities="ROLE_MEMBER"/>


			</security:user-service>

		</security:authentication-provider>

	</security:authentication-manager>

```

위를 통해 로그인을 시도하면 예상과 달리 에러가 난다.

500에러 -> There is no PasswordEncoder mapped for the id "null"

실행결과에서 에러는 "PasswordEncoder"라는 존재가 없기 때문에 발생


스프링 시큐리티 5버전부터 반드시 PasswordEncoder라는 존재를 이용하도록 변경되었다.

임시방편으로 시큐리티 5버전은 포맷팅 처리를 지정해서 패스워드 인코딩 방식을 지정할 수있다.

만일 패스워드의 인코딩 처리없이 사용하고 싶다면 패스워드 앞에 '{noop}' 문자열을 추가한다.

```xml
<security:user name="member" password="{noop}member" authorities="ROLE_MEMBER" />
```

## 로그아웃 확인

시큐리티에서 로그아웃을 하는 확실한 방법은

브라우저에서 유지하고 있는 세션과 관련된 정보를 삭제한다.
(개발자도구 Application - Cookies에서볼수있음)


(JSESSIONID는 Tomcat에서 발행하는 쿠키의 이름 , WAS마다 다른 이름을 사용한다고 함)




## 접근제한 메시지처리

로그인은 했으나 권한이 없는 경우 403 Forbidden 메시지를 보게되는데

시큐리티에서는 이 접근 제한에 대해서 AccessDeniedHandler 를 직접구현하거나 특정한 URI를 지정할 수 있다.


```xml
	<security:http>

		<security:intercept-url pattern="/sample/all" access="permitAll"/>

		<security:intercept-url pattern="/sample/member"
								access="hasRole('ROLE_MEMBER')"/>

		<security:intercept-url pattern="/sample/admin"
								access="hasRole('ROLE_ADMIN')" />

		<security:form-login />
		//추가된부분
		<security:access-denied-handler  error-page="/accessError" />
		
	</security:http>
```


이후 에러매핑을 받는 CommonController생성
```java
	@GetMapping("/accessError")
	public void accessDenied(Authentication auth, Model model) {

		log.info("access Denied : " + auth);

		model.addAttribute("msg", "Access Denied");
	}
```

그리고 해당 에러 뷰파일생성

```jsp
//accessError.jsp

<body>
<h1>Access Denied Page</h1>


<h2><c:out value="${SPRING_SECURITY_403_EXCEPTION.getMessage()}"/></h2>

<h2><c:out value="${msg}"/></h2>


</body>
```

### AccessDeniedHandler 인터페이스를 구현하는 경우

<security:access-denied-handler error-page="/accessError" />와 같이 error-page만 제공하는 경우 사용자가 접근했던 URI 자체의 변화는 없다. 

접근제한된 경우 다양한 처리를 하고싶으면 직접 AccessDeniedHandler 인터페이스를 구현하는 편이 좋다.

접근 제한시 쿠키나 세션에 특정한 작업을 하거나 HttpServletResponse에 특정한 헤더 정보를 추가하는 등의 행위를 할 경우에는 직접 구현하는 방식이 더 권장된다.

직접 구현하는 클래스 생성

security 패키지 생성후 CustomAccessDeniedHandler 클래스 추가

```java
@Log4j
public class CustomAccessDeniedHandler implements AccessDeniedHandler {

  @Override
  public void handle(HttpServletRequest request, 
      HttpServletResponse response, AccessDeniedException accessException)
      throws IOException, ServletException {

    log.error("Access Denied Handler");

    log.error("Redirect....");

    response.sendRedirect("/accessError");

  }

}
```
CustomAccessDeniedHandler 클래스는 AccessDeniedHandler  인터페이스를 직접 구현한다. 

인터페이스의 메서드는 handle()뿐이기 때문이고 HttpServletRequest, HttpservletResponse를 파라미터로 사용하기 때문에 직접적으로 서블릿 API를 이용하는 처리가 가능하다.

위 코드에서 접근 제한에 걸릴때 리다이렉트 하는 방식으로 동작하도록 되어있다.

security-context.xml 에서는 error-page 속성대신 
CustomAccessDeninedHandler 를 빈으로 등록

```xml
//security-context.xml

<bean id="customAccessDenied"
		class="org.zerock.security.CustomAccessDeniedHandler"></bean>

  ...

	<!--<security:access-denied-handler  error-page="/accessError" />-->
		<security:access-denied-handler  ref="customAccessDenied" />

	</security:http>

```

`<security:access-denied-handler >` 는 error-page 속성과 ref 속성 둘 중 하나만을 사용.

## 커스텀 로그인 페이지

시큐리티의 기본 로그인 페이지가 있으나, 별도의 URI를 이용해 로그인 페이지를 제작해서 사용하는게 일반적이다.

접근 제한 페이지와 유사하게 직접 특정한 URI를 지정할 수 있다.


```xml
//security-context.xml

<security:http>
	 //추가된부분
		<security:intercept-url pattern="/sample/all"
								access="permitAll" />

...


<!--<security:form-login />-->
	//변경된 부분
		<security:form-login login-page="/customLogin" />

```

login-page  속성의 URI 는 반드시 GET 방식으로 접근한는 URI 를 지정


controller 패키지의 CommonController에 '/customLogin'에 해당하는 메서드를 추가

```java
	@GetMapping("/customLogin")
	public void loginInput(String error, String logout, Model model) {

		log.info("error: " + error);
		log.info("logout: " + logout);

		if (error != null) {
			model.addAttribute("error", "Login Error Check Your Account");
		}

		if (logout != null) {
			model.addAttribute("logout", "Logout!!");
		}
	}
```

customLogin.jsp 를 추가한다.

```jsp
<%@ page language="java" contentType="text/html; charset=UTF-8"
    pageEncoding="UTF-8"%>
<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c" %>
    
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
<title>Insert title here</title>
</head>
<body>
  
  <h1>Custom Login Page</h1>
  <h2><c:out value="${error}"/></h2>
  <h2><c:out value="${logout}"/></h2>
  
  <form method='post' action="/login">
  
  <div>
    <input type='text' name='username' value='admin'>
  </div>
  <div>
    <input type='password' name='password' value='admin'>
  </div>
  <div>
<%--  <div>
    <input type='checkbox' name='remember-me'> Remember Me
  </div>--%>

  <div>
    <input type='submit'>
  </div>
    <input type="hidden" name="${_csrf.parameterName}"
    value="${_csrf.token}" />
  
  </form>
  
</body>
</html>

```

특이점으로 /login 경로로 post 로 데이터를 전송해야하며

input 태그의 name 속성은 기본적으로는 username 과 password 를 이용하며

hidden 으로 _csrf.parametername 처리가 되어있다.

만일 사용자가 패스워드 등을 잘못 입력하는 경우에는 자동으롣 ㅏ시 로그인 페이지로 이동하게 된다.

## CSRF(Corss-site request forgery)공격과 토큰

시큐리티에서 POST 방식을 이용하는 경우 기본적으로 CSRF 토큰이라는 것을 이용하게 된다.

별도의 설정이 없다면 시큐리티가 적용된 사이트의 모든 POST방식에는 CSRF 토큰이 사용되는데 '사이트간 위조 방지'를 목적으로 특정한 값의 토큰을 사용하는 방식이다.


CSRF 공격은 '사이트간 요청 위조'라고 변역될 수있다. 서버에서 받아들이는 정보가 특별히 사전 조건을 검증하지 않는다는 단점을 이용하는 공격방식

(실제로 이러한 기법으로 관리자 계정을 탈취해 개인정보가 유출되는 사례가 있었다)

CSRF를 이용해서 단순히 게시물의 조회수를 늘리는 등의 조작부터 피해자의 계정을 이용하는 다양한 공격이 가능하다.

...

## CSRF 토큰

CSRF 토큰은 사용자가 임의로 변하는 특정한 토큰값을을 서버에서 체크하는 방식이다.

POST 방식으로 특정 작업시 브라우저에 전송된 CSRF 토큰값과 서버가 보관하고 있는 토큰의 값을 비교한다. CSRF 토큰의 값이 다르다면 작업을 처리 하지 않는다.

## 시큐리티의 CSRF 설정

세션을 통해 보관되고, 브라우저에서 전송된 CSRF 토큰값을 검사하는 방식으로 처리

시큐리티에서 CSRF 토큰 생성을 비활성화 하거나 CSRF 토큰을 쿠키를 이용해서 처리하는 등의 설정을 지원한다.

	<security:csrf disabled="true"/>

## 로그인 성공과 AuthenticationSuccessHandler

로그인 성공 이후 특정 동작(페이지이동)을 하도록 제어하고 싶을때, 혹은 별도의 쿠키등을 생성해서 처리하고 싶은경우를 생각할 수있다.


AuthenticationSuccessHandler 인터페이스를 구현해서 설정 할 수 있다.

```java
@Log4j
public class CustomLoginSuccessHandler implements AuthenticationSuccessHandler {

	@Override
	public void onAuthenticationSuccess(HttpServletRequest request, HttpServletResponse response, Authentication auth)
			throws IOException, ServletException {

		log.warn("Login Success");

		List<String> roleNames = new ArrayList<>();

		auth.getAuthorities().forEach(authority -> {

			roleNames.add(authority.getAuthority());

		});

		log.warn("ROLE NAMES: " + roleNames);

		if (roleNames.contains("ROLE_ADMIN")) {

			response.sendRedirect("/sample/admin");
			return;
		}

		if (roleNames.contains("ROLE_MEMBER")) {

			response.sendRedirect("/sample/member");
			return;
		}

		response.sendRedirect("/");
	}
}



```

security-context.xml 에서 빈을 추가해주고 로그인 성고후 처리를 담당하는 핸들러로 지정한다.

```xml
	<bean id="customLoginSuccess"
		class="org.zerock.security.CustomLoginSuccessHandler"></bean>


		<!--<security:form-login login-page="/customLogin" />-->
		<security:form-login login-page="/customLogin"
							 authentication-success-handler-ref="customLoginSuccess" />



-----------------------------------------

###### 180918_14

log 사용안될떄
-

	Cannot resolve method 'info

log 가 안될때

```xml

        <dependency>
            <groupId>log4j</groupId>
            <artifactId>log4j</artifactId>
            <version>1.2.17</version>
        </dependency>

```






