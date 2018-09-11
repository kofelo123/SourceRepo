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



- [log4j 설정파일 없어서 나는 에러](#180911_1)
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

<select> 태그의 id속성값은 메서드의 이름과 동일하게 맞춰야한다.
resultType 역시 메서드의 리턴타입과 동일하게 해야한다.







