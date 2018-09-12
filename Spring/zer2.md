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
- [스프링mvc](#180912_4)
- [javaConfig 기본환경설정](#180912_5)
- [Web Deployment Assembly](#180912_6)



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

스프링mvc
-

스프링 MVC는 스프링의 서브 프로젝트중 하나이다.

스프링은 '코어'라고 할수있는 프레임워크에 여러 서브 프로젝트를 결합해서 다양한 상황에 대처할 수 있도록 개발되었다.

 ![](https://drive.google.com/uc?export=view&id=1i-1RNXPHnzXSpiWKWcnSJlog0JoJOnuP)

JavaConfig 등을 이용하려면 서블릿 3.0이상의 dependency를 사용하는것이 좋다.

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
                <failOnMissiingWebXml>false</failOnMissiingWebXml> //이부분이 에러나는데 이유를 모르겠음..+ 이 전체 plugin 없어도 돌아는 간다.
            </configuration>
        </plugin>
```

Root


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


