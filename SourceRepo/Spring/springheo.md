- [pom.xml수정시 발생에러](#pomxmlerror)
- [로깅](# logging)
- [Dependency Injection](#ch2)
- [스프링 컨텍스트](#2.1.3)
- [생성자 의존주입](#2.1.4)
- [p네임스페이스](#59)
- [컬렉션 타입 의존성 설정](#60)
- [프로토타입과 싱글턴](#67)
- [빈설정](#70)
- [의존성주입 어노테이션 종류](#diannotation)
- [스프링 설정파일 나누기](#78)
- [AOP와 횡단 관점](#81)
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
}
}
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

 스프링의 의존성 주입은 XML을 이용한 방법과
 애너테이션을 이용한 방법이있다.

 @ xml을 이용한 의존성 주입

 생성자를 이용한 의존성주입 , setter 메서드를 이용한 의존성 주입방법 이있다.

 설정파일추가 new -> Spring Bean Configuration FIle


 스프링에는 빈이 스프링 IOC 컨테이너에 의해 관리된다.

 스프링은 설정파일을 통해 인스턴스화하고 관리할수 있다.

 스프링을 IoC 컨테이너라고 부르는 이유가 여기에 있다.

 스프링은 프레임워크 이면서 동시에 컨테이너역할을 한다.

 빈을 생성하고 소멸하기까지 생명주기를 관리하기 위해

 빈을 컨테이너에 로드하여 관리한다.

 <bean> 태그를 통해 xml 기반의 스프링 빈 설정으로 인스턴스를 관리할 수 있다.

  ![](https://drive.google.com/uc?export=view&id=1ofsw_-sOLAGTtNgMGo2-mI-OAfG0QjEO)



  ---


  ###### 2.1.3

  스프링 컨텍스트
  _

  스프링의 컨텍스트 클래스들은 객체를 생성하고 관리하는 컨테이너의 기능을 합니다. 컨테이너로부
  터 빈을 찾기 위해서는 컨텍스트가 있어야 합니다. 스프링의 컨텍스트는 BeanFactory 인터페이스를
  구현한 클래스들과 이를 상속한 ApplicationContext 인터페이스를 구현한 클래스들로 나뉩니다.
  BeanFactory인터페이스를구현한클래스들은 Lazy Loading방식을사용하기때문에클라이언트의
  요청 시 빈이 생성되고 설정파일에 등록된 빈을 생성하고 관리하는 기본적인 컨테이너 기능만 제공
  합니다. ApplicationContext는 Pre Loading 방식을 사용하며, 빈을 생성하고 관리하는 기능 외에
  트랜잭션 관리, 국제화 처리 등 많은 기능을 제공합니다.


   ![](https://drive.google.com/uc?export=view&id=1nwcakskxtEP8A4dhbSc_EzU_xExT_ILZ)

  FileSystemXmlApplicationContext는 파일시스템 경로에 있는 XML 설정파일을 로딩하는 컨테이너
  이며, ClassPathXmlApplicationContext는 클래스패스 경로에 있는 XML 설정파일을 로딩하는 컨
  테이너입니다. GenericXmlApplicationContext는 파일시스템경로에 있는 XML 설정파일뿐만 아니
  라 클래스패스 경로에 있는 XML 파일을 로딩시킬 수 있습니다. 클래스파일 경로에 있는 XML 문서
  를 지정할때에는 classpath: 접두어를 파일명 앞에 붙이면 됩니다. XmlWebApplicationContext는
  웹 애플리케이션 개발에 사용되지만 직접 생성하지는 않는 컨텍스트입니다.


  다음은 위의 클래스들 중에서 GenericXmlApplicationContext를 이용하여 컨텍스트를 생성하는 코
  드입니다.
  ```java
  HelloMain.java
  1 package com.coderby.myapp.hello;
  2
  3 import org.springframework.context.support.AbstractApplicationContext;
  4 import org.springframework.context.support.GenericXmlApplicationContext;
  5
  6 public class HelloMain {
  7
  8 public static void main(String[] args) {
  9 AbstractApplicationContext context =
  10 new GenericXmlApplicationContext("application-config.xml");
  11
  12 context.close();
  13 }
  14
  15 }
  ```

  ---


  ###### 2.1.4

  컨트롤러 클래스에게 서비스 클래스의 인스턴스를 전달해주기 위해컨트롤러 클래스에 생성자를 추
  가합니다. 그러면 스프링은 설정파일에서 생성자를 이용한 의존성 주입을 할 수 있습니다.
  다음 코드는 컨트롤러 클래스에 생성자를 추가한 코드입니다.

  ```java
  HelloController.java
  1 package com.coderby.myapp.hello;
  2
  3 import com.coderby.myapp.hello.service.IHelloService;
  4
  5 public class HelloController {
  6
  7 IHelloService helloService;
  8
  9 public HelloController(IHelloService helloService) {
  10 this.helloService = helloService;
  11 }
  12
  13 public void hello(String name) {
  14 System.out.println("실행 결과 : " + helloService.sayHello(name));
  15 }
  16 }
  ```
  스프링 설정파일에 HelloController 클래스의 빈을 정의할 때 <constructor-arg>태그를 이용하여
  의존성을 주입시킬 수 있습니다.


  ```xml
  application-config.xml
  1 <?xml version="1.0" encoding="UTF-8"?>
  2 <beans xmlns="http://www.springframework.org/schema/beans"
  3 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  4 xsi:schemaLocation="http://www.springframework.org/schema/beans
  http://www.springframework.org/schema/beans/spring-beans.xsd">
  5
  6 <bean id="helloService" class="com.coderby.myapp.hello.service.HelloService" />
  7
  8 <bean id="helloController" class="com.coderby.myapp.hello.controller.HelloController">
  9 <constructor-arg ref="helloService" />
  10 </bean>
  11 </beans>
  ```
  <constructor-arg> 태그의 ref 속성은 의존성 주입할 다른 빈의 아이디를 지정합니다. 위 코드에서
  는 helloController빈은 helloService빈을의존성주입하도록설정해야합니다.위코드에서 ref속
  성의 값인 helloService인 것은 빈의 아이디가 helloService로 정의되어 있는 빈을 찾도록 합니다.

  위 코드를 테스트하기 위한 메인 클래스는 다음과 같습니다.


  ```java
  HelloMain.java
  1 package com.coderby.myapp.hello;
  2
  3 import org.springframework.context.support.AbstractApplicationContext;
  4 import org.springframework.context.support.GenericXmlApplicationContext;
  5
  6 import com.coderby.myapp.hello.controller.HelloController;
  7
  8 public class HelloMain {
  9
  10 public static void main(String[] args) {
  11 AbstractApplicationContext context =
  new GenericXmlApplicationContext("application-config.xml");
  12 HelloController controller =
  context.getBean("helloController", HelloController.class);
  13 controller.hello("JinKyoung");
  14 context.close();
  15 }
  16 }
  ```

  main() 메서드에서 컨트롤러 빈을 찾기 위해 getBean() 메서드를 이용했습니다. getBean() 메서드
  는 컨텍스트로부터 해당 빈을 찾아 반환합니다. 위 코드에서 10 라인은 다음과 같이 작성할 수 있습
  니다. getBean(Class<T> requiredType) : T 메서드를 이용하면 메서드 인자로 지정한 유형의 객체
  가 컨텍스트에 하나만 있을 경우 해당 빈을 리턴해 줍니다.

  ```java
  10 HelloController controller = context.getBean(HelloController.class);
  ```


  생성자의 인자로 전달하는 타입이 문자열(String)이거나 기본 데이터 타입일경우 value속성을 이용한다.

   ![](https://drive.google.com/uc?export=view&id=1IEeF8CVv5872wenIutVW-tdCQiFtZ1Uq)

  <constructor-arg> 태그의 속성으로 name 속성과 type 속성을 사용할 수 있다. name 속성은 생성자 파라미터 변수의 정확한 이름을 지정한다. 두 파라미터 변수가 같은 타입일 경우 모호성을 피할때 사용한다.

  type속성은 단일인자를 갖는 생성자가 모두 String 타입으로 변환되는 모호함을 해결하기 위해 사용한다.
  (아마 위의 두속성을 사용하는경우가 극히 드물다.)


  의존성을 주입할 객체가 많을 경우 클래스에 생성자를 중복 선언하는것은 부담스러운 일이다.
  클래스에 set 메서드를 선언하고 설정파일에서 <property>태그로 의존객체를 주입할 수 있다.

  set 메서드를 사용할경우 기본생성자외에 다른 생성자를 정의해서는 안된다.

   ![](https://drive.google.com/uc?export=view&id=1E-OHJn2M7AXfVzQv56vTYZvJEjnF3gFD)

  <property> 태그의 속성은 name, ref, value가 있다.
  name: 주입할 빈의 고유이름
  ref : 주입할 객체의 이름
  value : 값지정

   ![](https://drive.google.com/uc?export=view&id=1zb5ya-8DgBdYx1BYyAABHy6Gc3MFTzL2)

   ![](https://drive.google.com/uc?export=view&id=153A6gjkc_Rx84pkGBALz6d1ptoqJOUWK)

  ---


  ######59
  p네임스페이스
  -

  setter 의존성 주입하는 경우 p 네임스페이스를 이용해서 bean 태그 속성으로 의존성 할 수있다.
  Namespace 탭에서 p 네임스페이스를 체크해줘야한다.

   ![](https://drive.google.com/uc?export=view&id=1L67SzHcLcLLuzFFvepBWMAZyDEgmSEUW)


  ---


  ######60
  컬렉션 타입 의존성 설정
  -

  의존성 주입해야할 멤버변수 타입이 배열,컬렉션일 경우도 의존성 주입 가능.


   ![](https://drive.google.com/uc?export=view&id=1gghemb_0jYwv-2Rd_fkd_ocdYWOj4G_R)

   ![](https://drive.google.com/uc?export=view&id=1t-6iKxBVCx7_VGC93f4PNyBzwfF8_k13)



  * List

  배열 또는 List 타입 속성들을 의존성 주입할경우 <list> 태그를 사용.

  <list>는 <value>,<ref>,<bean> 태그를 자식태그로 가질수 있다.

  <value>태그는 List가 가져야할 데이터의 타입이 String이거나 기본데이터타입일때 사용.
  <ref>태그는 List 가 이미 정의되어 있는 다른빈을 가져야 할때 사용,
  <bean> 태그는 미리정의되어있는 빈이 아닌 새로운 빈을 직접 정의해 사용할 수 있다.

   ![](https://drive.google.com/uc?export=view&id=1BVojSsS3chkepKG82Jo_uRUjK8I-6niV)


  * <set>

  Set 타입 속성을 의존주입할떄 사용
  <value>
  <ref>
  <bean>이 있다.(List와 사용법 이하 같음)

  * <map>

  Map은 Key 와 Value를 가져야 하므로 쌍을 함께 지정해줘야하는데 그때 <entry> 태그를 사용.
  <entry>태그는 key 속성으로 Map 데이터의 Key를 지정한다.
  이하 <value>,<ref>,<bean> 태그를 역시 사용할수있다.

   ![](https://drive.google.com/uc?export=view&id=1JhIZHuNfqFGeFKzL_D0823T4XmomuKHj)

   ![](https://drive.google.com/uc?export=view&id=1YZOwypK6QUKh9h84JbIouQnLn2d33pqS)

   ![](https://drive.google.com/uc?export=view&id=1elULLncibe8dRDMmk3z-rC9QG8cJDSPm)

   ![](https://drive.google.com/uc?export=view&id=1getK93ZqD7XLwZ_N7PlL-z4peciNCjRo)

   ![](https://drive.google.com/uc?export=view&id=1glgsgMJyzhM295ixUnj5H_kpEC8wFngv)


 ---

 ###### 67

 프로토타입과 싱글턴
 -

 스프링 컨테이너는 빈생성시 클래스당 한개 인스턴스를 생성한다. 싱글턴 레지스트리를 이용해서 일반 클래스도 싱글턴처럼 관리해주는 방식을 제공한다.

 이것은 bean의 옵션으로 선택할수도 있다.

  ![](https://drive.google.com/uc?export=view&id=1_qH4WuBltM4FFREjurEW5cpwV3PnMFAy)

 ---

 ###### 70

 빈 설정
 -

 빈 설정을 위한 어노테이션은 네지가 있다.

 1. @Component : 일반적인 컴포넌트 등록을 위한 클래스에 사용

 2. @Controller : 컨트롤러 클래스에 사용
 3. @Service : 서비스 클래스에 사용
 4. @Repository : DAO 클래스 또는 리포지토리 클래스

 빈의 이름은 클래스의 첫글자를 소문자로 한 이름이다.



 ---


 ###### diannotation

 의존성주입 어노테이션 종류
 -

 *  @Autowired를 이용한 의존성 주입

 @Autowired는 타입을 기준으로 의존성을 주입해준다.

 ```java

 @Autowired
 IHelloService helloService;

 ```
 * @Qualifier를 이용한 의존객체 설정

 HelloService 클래스 외에 IHelloService 인터페이스를 구현한 클래스가 하나 더 추가될수있을때

 HelloService 빈이 아닌 NiceService 빈을 전달하고 싶을경우 @Qualifier 어노테이션을 써서 빈의 이름을 지정한다.

 @Autowried
 @Qualifier("niceService")
 IHelloService helloService;

 >'빈을 타입으로 받지만 같은 타입내 여러개 빈이 있는경우를 말하는듯'


 * @Resource를 이용한 의존성 주입

 @Resource 는 @Autowired와 @Qualifier를 같이 사용하는 것과 같다. name 속성으로 이름을 지정할 수 있다.
 ```java
 @Resource(name="niceService")
 IHelloService helloService
 ```

 * @Inject 어노테이션을 이용한 의존성 주입

 @Inject 는 @Autowired를 사용하는것과 같다.

 이 둘은 같은 타입 빈이 두개 이상 있을경우 변수의 이름과 같은 이름을 갖는 빈을 찾는다.
 찾지 못하면 에러가 발생.

 Error Massage : Error creating bean with name ~ : Injection of autowired dependencies failed

 * XML 사용, 어노테이션 사용의 빈생성 및 의존성 설정의 비교

  ![](https://drive.google.com/uc?export=view&id=1EuU954vEvidjFjXaFqr5W-n19rdX-R8L)


 ---

 ###### 78

 스프링 설정파일 나누기
 -

 기능별로 여러 xml 파일로 나누어 작성하여 관리 할 수있다.


  ![](https://drive.google.com/uc?export=view&id=1bzhu86UkHYxYhq5en-oRcyCysTh0-Apt)

 * <import> 태그를 이용한 다른 빈설정 파일 포함

 한 파일에 작성하는 것같은 효과를 줄수있다.

  ![](https://drive.google.com/uc?export=view&id=1rHNCL9yFak29jCA6BG2jYmE4IpxLH-Xu)

 * 여러개 설정파일 지정

 여러 설정파일로 나누었을 경우 설정파일 지정을위해 * 문자를 사용할 수 있다.

 (ApplicationContext)이용한경우

  ![](https://drive.google.com/uc?export=view&id=1hVlnWvx9SNIQVzRbdNVGdI9pbEua5hJ6)

 웹 애플리케이션에서 설정파일을 지정할 때는 web.xml 파일을 이용해야한다. 이 경우에도 * 를 이용해서 여러개 설정파일을 지정 가능하다.

![](https://drive.google.com/uc?export=view&id=1B5TMR4tYjezg8u6LZwhE6BoToYk0Vlmd)

 * 문자를 이용하여 설정파일을 지정하지 않고 직접 모든 설정파일을 하나하나 지정할 수있다.

  ![](https://drive.google.com/uc?export=view&id=1dIcTkWH-bdU_P9RpV5lKoRjqz8TrzsH2)


 ---


 ###### 81

 AOP와 횡단 관점
 -

 OOP에서는 횡단관점 분리를 위해 공통기능을 하나의 클래스 단위로 모으고 그것들을 모듈로부터 분리하여 재사용성과 보수성을 높인다.

 각 모듈로부터 공통기능을 분리 하더라도 호출하는 코드는 분리하지 못하게 되는데
 그래서 AOP가 필요하게 된다.

 AOP는 핵심 로직을 구현한 코드에서 공통기능을 직접적으로 호출하지 않는다.

 그 호출까지도 관점으로 다룬다.

 AOP는 각 모듈로 부터 관점에 관한 코드를 완전히 제거하는것을 목표로 한다.

 * 프락시 클래스를 이용한 AOP 구현

 IHelloService 인터페이스를 구현한 HelloService 클래스 의 sayHello 메소드를 삭제하고

 HelloServiceProxy 클래스가 HelloService 를 상속받도록하여 로그를 출력하는 메소드(공통코드) 실행후 super 클래스의 메소드를(핵심코드) 실행

 Controller는 기존의 HelloService 빈을 참조하여 실행하지않고 HelloServiceProxy 빈을 참조하여 실행하면 분리하여 작성했지만 핵심코드가 실행될때 공통코드로 실행되도록 할수있다.

 위의 프락시클래스를 이용한 AOP구현은 스프링 AOP 없이 관점지향 을 구현한 예이다.

 핵심코드가 실행될때 공통코드가 실행되도록 핵심코드에 공통코드를 삽입하는 것을 위빙이라고 한다.

 매번 관점지향 프로그래밍을 위해 프락시 클래스를 만들어야한다면 부담스럽기 때문에, AOP 프레임워크는 부가적인 코드를 작성하지 않도록 해준다.

~85
