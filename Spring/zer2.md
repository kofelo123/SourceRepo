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
- [2장 스프링mvc](#180912_4)
    - [javaConfig 기본환경설정](#180912_5)
    - [Web Deployment Assembly](#180912_6)
    - [@InitBinder](#180913_4)
    - [@DateTimeFormat](#180913_5)
    - [@ModelAttrbute](#180913_6)


- [log4j 설정파일 없어서 나는 에러](#180911_1)
- [Invalid bound statement (not found)](#180912_2)
- [Error creating bean with name 'dataSource' ](#180912_7)
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

ch3 (정리)
-

xml의 dtd같은 (xmlx:xsi=http:// wer~"
		xsi:schemaLocation ~)

것들도 dependecy가 없으면 못가져 온다.


Mapper 인터페이스로 sql작성시 ';' 이 없도록 작성해야함.

sql에서 where bno > 0 과 같은 조건은 테이블 검색시 bno라는 컬럼조건을 주어서 Primary Key를 사용하도록 유도하는 조건이다.


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









- [ch3 (정리)](#180914_3)
- [파일 업로드 처리](#180914_1)
- [Controller의 Exception 처리](#180914_2)
- [driverClassName 프로퍼티 에러](#180914_6)
- [Incompatible types. Found](#180914_3)
- [Mapper 인터페이스 빈 의존주입못받을떄](#180914_5)