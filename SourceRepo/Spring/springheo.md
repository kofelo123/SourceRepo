- [pom.xml수정시 발생에러](#pomxmlerror)
- [로깅](# logging)
- [Dependency Injection](#ch2)
---

# 스프링 프레임워크 원리부터 실전까지 - 허진경

## pomxmlerror

pom.xml 수정후 소스코드에 아무 문제가 없어도 에러표시가 날 경우
이클립스 종료 -> 내문서- m2 폴더를 삭제하고 이클립스 재실행
-> 프로젝트 컨텍스트에서 mavenn>update Project를 실행시켜준다.
이러한 현상이 발생하는 이유는 Maven 도구에 의해 다운로드 되는 라이브러리파일(jar)이 다운로드 도중 인터넷 연결 지연 등의 문제로 정상 다운로드 되지 않아서 발생하는 문제
---

## logging

@ 자바 로깅 프레임워크

로깅은 Logger, Formatter 그리고 Handler(Appender)의 세 가지 주요 부분으로 나뉜다.

Logger는 기록 할 메세지를 캡처하여 이를 로깅 프레임 워크로 전달한다.

메세지를 받은후 프레임워크는 메시지 와 함께  Formatter를 호출하고

Fomatter는 출력을 위해 형식을 지정한다. 그런 다음 프레임 워크는

형식화 된 메시지를 적절한 Appender에 전달하여 처리한다.

로깅 프레임워크는 여러가지가 있다(Log4J, JavaLoggingAPI,Logging,SLF4J 등..)

스프링 프로젝트 생성시에 디폴트로 지정되는것이 SLF4J이다.

@ SLF4J

스프링 프레임워크는 JCL(Java Commons Logging)을 이용해서 로그를 남긴다.

SLF4J(Simple Logging Facade for java)가 나온 뒤로 JCL대신 SLF4j를 사용하는

오픈소스 프로젝트가 증가했다.

스프링 프레임워크 프로젝트 생성시 자동으로 pom.xml에 등록된다.

@ 로그 남기기

소스코드에서 로그를 남기기 위해 LoggerFactory의 getLogger() 메서드를 이용해 Logger 객체를 얻어야 한다. LoggerFactory와 Logger는 org.slf4j 패키지를 import해야한다.

```java
notation.RequestMethod;
13
14 @Controller
15 public class HomeController {
16
17 private static final Logger logger = LoggerFactory.getLogger(HomeController.class);
18
19 @RequestMapping(value = "/", method = RequestMethod.GET)
20 public String home(Locale locale, Model model) {
21 logger.info("Welcome home! The client locale is {}.", locale);
...
```


@ 로그 레벨

개발자는 Logger 클래스의 trace(),debug(),info(), warn(), error() 메서드를 이용하여 로그를 남길 수 있다.

낮은 수준의 로그 레벨을 지정하면 해당 레벨과 그보다 심각한 수준의 로그 레

벨이 출력된다.

getLogger() 메서드의 인자로 현재 클래스를 지정해 준다. 그러면 로그 메시지에 클래스의 이름이
함께 표시된다.


SLF4J는  log4j의  6  가지  로깅  레벨  중  5  가지가  사용됩니다(ERROR,
WARN,  INFO,  DEBUG,  TRACE).  FATAL은  로깅  프레임  워크가  응용  프로그램이  종료되어야  하는
시점을  결정할  장소가  아니므로  로그  작성자의  관점에서  ERROR와  FATAL  사이에는  차이가  없다는
것을  기반으로  삭제되었습니다.

출력되는  로그  레벨의  변경은  설정파일을  통해  가능합니다.  SLF4J  설정파일은  src/main/resources
폴더  아래에  log4j.xml  파일을  이용합니다.  다음  코드는  로깅  설정파일입니다.  여러분이  애플리케이
션의  로그  레벨을  바꾸고  싶다면  <!--  Application  Loggers  -->  주석  아래의  <logger>  태그의
name  속성의  값이  com.coderby.myapp인  로그  레벨을  변경시키세요.  아래의  예처럼  로그  레벨을
error로  한다면  warn,  debug,  info,  trace  레벨의  로그는  출력되지  않을  것입니다

```xml
<?xml version="1.0" encoding="UTF-8"?>
2 <!DOCTYPE log4j:configuration PUBLIC "-//APACHE//DTD LOG4J 1.2//EN" "log4j.dtd">
3 <log4j:configuration xmlns:log4j="http://jakarta.apache.org/log4j/">
4
5 <!-- Appenders -->
6 <appender name="console" class="org.apache.log4j.ConsoleAppender">
7 <param name="Target" value="System.out" />
8 <layout class="org.apache.log4j.PatternLayout">
9 <param name="ConversionPattern" value="%-5p: %c - %m%n" />
10 </layout>
11 </appender>
12
13 <!-- Application Loggers -->
14 <logger name="com.coderby.myapp">
15 <level value="error" />
    </logger>
17
18 <!-- 3rdparty Loggers -->
19 <logger name="org.springframework.core">
20 <level value="info" />
21 </logger>
22
23 <logger name="org.springframework.beans">
24 <level value="info" />
25 </logger>
26
27 <logger name="org.springframework.context">
28 <level value="info" />
29 </logger>
30
31 <logger name="org.springframework.web">
32 <level value="info" />
33 </logger>
34
35 <!-- Root Logger -->
36 <root>
37 <priority value="warn" />
38 <appender-ref ref="console" />
39 </root>
40
41 </log4j:configuration>
```

@ Appender 패턴

Appender 패턴을 이용하여 원하는 형식으로 로그를 출력가능

```xml
다음  코드는  log4j.xml  파일의  일부입니다.
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE log4j:configuration PUBLIC "-//APACHE//DTD LOG4J 1.2//EN" "log4j.dtd">
<log4j:configuration xmlns:log4j="http://jakarta.apache.org/log4j/">
<!-- Appenders -->
<appender name="console" class="org.apache.log4j.ConsoleAppender">
<param name="Target" value="System.out" />
<layout class="org.apache.log4j.PatternLayout">
<param name="ConversionPattern" value="%-5p: %c - %m%n" />
</layout>
</appender>
```

 ![](https://drive.google.com/uc?export=view&id=1_eNLCIYunDlBpN0bLT6VquJX55Piacqy)

 ---


 ## ch2

 Dependency Injection
 -

 스프링 프레임워크의 가장 중요한 특징은 객체의 생명주기를
 스프링 컨테이너가 관리한다는 것.

 그래서 IoC 컨테이너라고 부르기도 한다.

 스프링 컨테이너는 빈을 관리하기도 하지만 간단한 설정을 통해
 객체 의존성 주입을 지원한다.

 의존성 주입은 빈을 필요로 하는 곳에 해당하는 빈을 만들어 전달 해 주는것을 의미.
