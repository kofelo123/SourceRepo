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
- [AOP용어와 Spring의 AOP](#86)
- [Annotation을 이용한 AOP](#102)
- [스프링 JDBC](#114)
- [Spring JDBC 설정](#118)
- [트랜잭션 처리-XML](#143)
- [트랜잭션 처리 - 어노테이션](#150)
- [JDBC 연결 정보 메세지 암호화](#155)

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


---


###### 86

AOP용어와 Spring의 AOP
-

![](https://drive.google.com/uc?export=view&id=13aojeLoy2fcanG71xdSy-7nC7Yvjf9ZD)


* 위빙방법

1.컴파일시 위빙

2.클래스 로딩시 위빙

3.런타임시 위빙
:Spring AOP의 방법으로, 프록시를 이용하여 런타임시에 위빙하는 방법으로 메서드 호출시만 AOP를 적용할 수있다.

* Spring에서 AOP

스프링은 설정정보를 이용하여 런타임에 대상 객체에 대한 프록시 객체를 생성한다. 그리고 대상 객체를 프록시를 통한 간접접근을 한다.

스프링에서는 3가지 방식으로 AOP를 구현한다.

1.XML 기반의 POJO 클래스를 이용한 AOP 구현
2. AspectJ에서 정의한 @Aspect 어노테이션 기반의 AOP 구현
(3. 스프링 API를 이용한 AOP구현(권장x))

* XML을 이용한 AOP

![](https://drive.google.com/uc?export=view&id=1p3m87fWBB4dS9c0fiYTMNdK9tEmwUBlU)


* aop 태그설명

1. <aop:config>

최상위 태그이다.

<aop:pointcut>, <aop:aspect>,<aop:advisor> 태그를 포함시킬수있다.

2. <aop:pointcut>

포인트컷을 지정하기 위해 사용한다.

<aop:config>태그안 이나, <aop:aspect> 태그안에 정의 가능.

<aop:pointcut>태그는 id 속성과 expression 속성을 갖는다.

id 속성: 각 포인트컷의 고유 아이디값을 가져야함

expression: 포인트컷 표현식을 지정

```
<aop:config>
	<aop:pointcut
		id="hello"
		expressoin="excution(* com.coderby..HelloService.sayHello(..))/>

```

3.<aop:aspect>

공통코드 객체를 지정하기 위한 태그, 어드바이스와 포인트컷을 연결한다.

id, order , ref 속성을 가짐

id속성은 고유아이디
order: 특정 포인트컷에 여러 개 어드바이스가 실행될 때 aspect의 순서를 제어함
ref: 포인트컷의 아이디를 지정

<aop:aspect>태그안에 <aop:pointcut> 태그와 <aop:after>,
<aop:after-returning>,<aop:after-throwing>,<aop:around>,<aop:before>등어드바이스 태그를 가질 수 있다.

![](https://drive.google.com/uc?export=view&id=1ez8r69Vt5bZvINoIgEns6qXJXMPEg64e)


* 어드바이스

어드바이스의 종류는 before, after, after-returning, after-throwing, around가 있다.
각 어드바이스들은 <aop:before>,<aop:after>,<aop:after-returning>,<aop:after-throwing> 그리고 <aop:around> 태그를 이용해서 설정한다.

method, pointcut, pointcut-ref, arg-names 속성을 가진다.

1. <aop:before>

before 어드바이스 설정하기 위해 사용. 핵심코드가 실행되기 전에 공통코드가 실행되도록 함.
```
<aop:before pointcut-ref="hello" method="log" />
```

2.<aop:after>

after 어드바이스를 설정하기 위해 사용.
핵심코드가 실행된 후 콩통코드가 실행되도록 함.

```
<aop:after pointcut-ref="hello" method="log" />
```

3.<aop:after-returning>

after-returning 어드바이스 설정위해 사용
핵심코드 메서드가 리턴한다음 공통코드가 실행되도록 한다.

returning 속성을 추가로 가진다.( 리턴 값이 전달될 공통코드의 메서드 파라미터 이름을 지정)

```
public void resultLog(Object resultObj){...}


<aop:after-returning pointcut-ref="hello" method="resultLog" returning="resultObj" />
```
4.<aop:after-throwing>
핵심코드에서 예외가 발생할 경우 공통코드가 실행

throwing 속성을 추가로 가진다.

```
<aop:after-throwing pointcut-ref="xxx" method="exceptionLog" throwing="ex" />


```
5.<aop:around>

핵심코드가 실행되는 동안 공통코드가 실행되도록 한다.

before, after, after-returning, after-throwing 어드바이스를 around 어드바이스 하나로 해결할수있다.
around 어드바이스에 지정할 공통코드 메서드는 반드시 ProceedingJoinPoint 변수를 파라미터로 선언해야한다.

ProceedingJoinPoint 객체의 getSignature() 메서드는 타겟(핵심코드) 객체의 메서드 원형을 리턴한다.



* <aop:advisor>

어드바이스와 포인트컷을 지정하는 역할을 한다.

주로 트랜잭션 처리를 위한 어드바이스(<tx:advice>)와 트랜잭션 처리대상을 포인트컷으로 지정후 이를 묶는역할을 한다.

<aop:advisor>태그를 이용하여 어드바이스와 포인트컷을 묶는
예.
```
<aop:config>
<aop:pointcut id="txPointcut" expression="execution(*
com.coderby.myapp..*Service.*(..))" />
<aop:advisor advice-ref="txAdvice" pointcut-ref="txPointcut" />
</aop:config>
```


* Pointcut 표현식

조인포인트의 부분집합이다. 어드바이스가 적용되는 조인포인트들을 의미한다.

스프링에서는 정규 표현식이나 AspectJ 문법을 이용하여 포인트컷을 정의할 수있다.

excution,within, bean 표현식이사용가능

bean: 빈의 이름으로 포인트컷 지정
within: 지정판 패키지 또는 클래스내의 모든 메서드를 포인트컷으로 지정
excution: 일반적인 포인트컷 표현식으로 메서드 시그니처별로 포인트컷을 지정가능.

1. excution

![](https://drive.google.com/uc?export=view&id=1zerukbKpqF6x_7n1CI8eAODprJhxyCD7)

2. within

특정 타입에 속하는 메서드를 포인트컷으로 설정할때 사용.
지정한 패키지 또는 클래스 내의 모든 메서드들을 포인트컷으로 지정

![](https://drive.google.com/uc?export=view&id=1IL_UuPIUGBC1oDFhyDJfIAh1Hffvcnbm)

3. bean

![](https://drive.google.com/uc?export=view&id=1EKM82cWQjyy1t5hx_FSdQDKhWt2FWfIV)


---

###### 102

Annotation을 이용한 AOP
-

//pom.xml
 ![](https://drive.google.com/uc?export=view&id=1hvsN8g7Aab7FSrQ6SQaWdZsYRNt7_nyx)


AOP 설정위해 XML 설정파일에서 <aop:aspectj-autoproxy> 태그를 추가해야한다.

* AOP 어노테이션

1) @Aspect

: 공통 코드를 정의한 클래스에 사용. 빈으로 등록되야 하기 떄문에 @Aspect 어노테이션과 @Component 어노테이션을 같이사용

```
@Component
@Aspect
public class LogAspect{

}
```

2) @Pointcut

: 포인트컷 지정시 사용.

```
@Pointcut(value="excution(* com.coderby..*.sayHello(..))")
private void helloPointcut() {}

```

* 어드바이스

사용하는 어노테이션은  @Before, @After , @AfterReturning, @AfterThrowing, @Around가 있다.

파라미터로 JoinPoint 타입 변수를 선언할 수 있다.
JoinPoint 객체의 getSignature()메서드를 이용해서 호출하는 핵심메서드의 원형을 알 수 있다.

1) @Before

핵심코드가 실행되기 전에 실행할 메서드 정의

```java
@Before("helloPointcut()")
public void beforeLog(JoinPoint joinPoint){
	Signature signature = joinPoint.getSignature();
	System.out.println("[Log : Before] : " + signature.getName());
}
```

2) @After

핵심코드 실행 후 실행 (메서드 정상 종료 뿐만아니라 예외때도 실행)

```java
@After("helloPointcut()")
public void afterLog(JoinPoint joinPoint){
	Signature signature = joinPoint.getSignature();
	System.out.println("[Log : After ] : " +signature.getName());
}
```

3) @AfterReturning

핵심코드 실행후 실행, 메서드 정상 실행 후에만 실행.(예외는 실행x)
파라미터로 결과를 받을 타입의 변수 선언후 returning 속성에 변수이름을 지정하면 어드바이스 호출될때 핵심코드에 실행한 결과값이 메서드 파라미터로 전달됨.

```java
@AfterReturning(pointcut="helloPointcut()", returning="message")
public Object afterReturningLog(JoinPoint joinPoint, String message){
	Signature signature = joinPoint.getSignature();
	System.out.println("[Log: AfterReturning] : " + signagure.getName());
	System.out.println("[Log: AfterReturning] 리턴값 : " + mesage);
	return message;
}
```

4) @AfterThrowing

예외발생시 실행

```
@AfterThrowing(pointcut="goodbyePointcut()", throwing="exception")
public void afterThrowingLog(JoinPoint joinPoint, Exception exception){
	Signature signature = joinPoint.getSignature();
	System.out.println("[Log: AfterThrowing] : " + signagure.getName());
	System.out.println("[Log: AfterThrowing] 예외 발생 : " + exception.getMessage());

}
```

5) @Around

핵심코드 실행되기 전과 후에 실행할 공통코드 메서드 위에 선언

첫번째 파라미터로 ProceedingJoinPoint 타입 파라미터를선언해야한다.이 객체를 이요하여 공통코드가 실행되는 도중에 핵심코드를 실행 시킨다.
메서드 호출전,호출후,예외 발생할 경우 어떤 작업이 실행될 수 있도록 한다.


 ![](https://drive.google.com/uc?export=view&id=1PCTuiKbOyAZpMfuO5ACC2OqTJhFPOVwo)

(예제 여러번보기)


---


###### 114

스프링 JDBC
-

DAO 구현에 대한 종류

- Java SE의 JDBC
- 스프링의 jdbcTemplate 또는 JdbcDaoSupport 클래스이용
- MyBatis의 Template 또는 Dao Support 클래스
- Hibernate의 Template 또는 DaoSupport 클래스
- MyBatis SQL Mapper 이용
- Hibernate ORM 이용


스프링JDBC는 사용자가 반복적으로 작성해야하는 코
드를 줄여준다.
쿼리의 결과를 뽑아내 변수에 저장하는것도 스프링이 알아서 해준다.



---


###### 118

Spring JDBC 설정
-

Spring JDBC를 사용하기위해 spring-djbc 의존성과 commons-dbcp(커넥션풀) 의존성을 추가한다.

Oracle JDBC 드라이버는 메이븐 센트럴 리포지토리에 없기 때문에 직접 JDBC 드라이버를 로컬 메이븐 리포지토리 폴더에 복사한후 pom.xml 의존성 설정을 위해 추가한다.

oracle 설치경로에서 ojdbc6.jar파일을

~/.m2/repository/com/oracle/ojdbc/6 폴더아래에

ojdbc-6.jar 이름으로 복사한다.

그리고 의존성 추가 (ojdbc)

```xml
<dependency>
	<groupId>com.oracle</groupId>
	<artifactId>ojdbc</artifactId>
	<version>6</version>
</dependency>
```


(안될경우 프로젝트에서 직접 빌드 패스에서 add external jars 해서
추가해야한다.)

>'이 책에서도 설정정보에 properties를 사용하는데 egov에서 봤던 내 코드랑 조금 다른게 

```<context:~ 를 사용한다는점 , xml경로..' ```

```

src/main/resources/database/jdbc.properties 위치로둠.


// spring/application-config.xml
<context:property-placeholder location="classpath:database/jdbc.properties" />
```

>'이책에서 mybatis가 아닌 jdbcTemplate 빈을 사용하고 자바단에서 쓰는 DAOImpl 이라서 그 부분을 생략하고 넘어가야할듯'



 ![](https://drive.google.com/uc?export=view&id=1eAaeJGD9sp2OEu50KTQDZncoaO6bV_44)

 ---

###### 143

 트랜잭션 처리 -XML
 -

  ![](https://drive.google.com/uc?export=view&id=1daHvPz7emNo9ccW0A2sMdx4nYW3QpNP1)

 (FK로 연결된 두테이블간의 부모 테이블?의 레코드가 먼저 삭제되어야 자식의 레코드가 삭제가 된다는 이야기, 삭제할떄 두가지를 동시에 트랜잭션 처리해서 삭제시켜야한다는 이야기를 함)

 * XML을 이용한 트랜잭션 설정

 트랜잭션 설정을 위해 AOP 설정을 해줘야한다.

 1. pom.xml - dependency

  ![](https://drive.google.com/uc?export=view&id=1BZf01njSCxiMqeW56H4p6r_Hx3W075Ex)

  ![](https://drive.google.com/uc?export=view&id=1id4WYrnP4PRdVAt12_DXi5T6F-RFa55T)

 2. 네임스페이스 추가

 스프링 컨텍스트(root-context)의 aop, tx 네임스페이스 추가

 3. TransactionManager 빈 설정

 TransactionManager 빈은 스프링 트랜잭션 처리를 위한 핵심 클래스이다. xml 뿐만아니라 어노테이션 기반 트랜잭션 처리에도 사용됨.
 ```xml
 <bean id="txManager"
 class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
 <property name="dataSource" ref="dataSource" />
 </bean>
 ```

 4. Advisor 설정

 스프링은 XML기반 AOP를 이용한 트랜잭션 설정을 사용하고 있다.

 트랜잭션을 설정하기 위해서 <tx:advice>태그와 <aop:config> 태그를 이용한다.



 * <tx:advice>

 <tx:advice>태그는 트랜잭션 실행과 관련된 설정

  ![](https://drive.google.com/uc?export=view&id=18ldNRTYOes_la6lNuqarLnI_W6uiQySF)


 * <aop:config>

 <aop:config>는 트랜잭션이 처리되야 할 포인트컷 지정과 트랜잭션 어드바이스,포인트컷을 연결하기 위한 어드바이서를 설정하기 위한 태그이다.

 트랜잭션 설정을 위해 <aop:config>의 서브태그 <aop:pointcut> 태그로 트랜잭션 처리가 되어야 할 포인트 컷 표현식을 기술한다.
 <aop:advisor> 태그는 앞서 설정했던 어드바이스와 포인트컷을 연결하기 위해 사용한다.

  ![](https://drive.google.com/uc?export=view&id=1Xo0XplRuY4jzYKWIELLyrc3xSxQri48e)

  ![](https://drive.google.com/uc?export=view&id=1XmSTzboRRwyFKgt8luO6njFW5r1uN-6p)

  ![](https://drive.google.com/uc?export=view&id=1psrEJVU-ivzP36TAy3FFax-l57TPcZme)



 ---

###### 150

 트랜잭션 처리 - 어노테이션
 -

 클래스의 public method 앞에 @Transactional 어노테이션을 둘 수있다.
 <tx:annotation-driven> 태그는 @Transactional 애너테이션을 설정한 곳에 트랜잭션 동작을 활성화시킨다.

 1. <tx:annotation-dirven>


 ```
 <tx:annotation-driven transaction-manager="txManager" />
 ```
 만일 트랜잭션 매니저의 빈 이름이transactionManager
 일경우에는 다음과 같이 transaction-manager 속성을
 생략할 수 있다.
 ```
 <tx:annotation-driven />
 ```
  ![](https://drive.google.com/uc?export=view&id=1FhzCY_LfqxRrUYxOSXPe6VU2Om7Lldsk)

 설정파일(root-context.xml)
  ![](https://drive.google.com/uc?export=view&id=1F6SXh4_98EAE4kIN6I58a2BKPrudE_L7)


 2. @Transactional 어노테이션

 : 메서드레벨과 클래스 레벨에 사용할 수있다.
 클래스 선언부 위에 사용할 경우 클래스 내의 모든 메소드에 트랜잭션 속성이 추가된다.

 @Transational 어노테이션은 트랜잭션 매니저 이름을 입력해 줘야한다.
 그런데 TransactionManager 빈의 아이디가 transactionmanager일 경우 매니저이름 생략이 가능하다.

  ![](https://drive.google.com/uc?export=view&id=1g20eJuYAZ495_2k-cA5dyO2Fcp0xvTxY)

 메서드 트랜잭션 설정을 평가할떄 가장 깊은위치의 설정을 우선시한다.(패키지 등의 경로 깊이를 말하는건지..)

  ![](https://drive.google.com/uc?export=view&id=1C6Rzkc0vl5C8UHKL4617ZkUyHUiDk3q-)

 스프링은 인터페이스에 어노테이션을 붙인느 것과는 반대로 구현 클래스와 메소드에만 @Transactional 어노테이션을 붙이기를 권장한다.
 인터페이스에도 붙일수 있지만, 인터페이스 기반의 프록시를 사용하는 경우에만 제대로 동작한다(무슨말인지..)
 ('... 프록시 연관된 얘기들이 있다')

 ---


###### 155

 JDBC 연결 정보 메세지 암호화
 -

 평문으로된(Plain Text) 상태로 코딩하면 개발단에 보안 취약점이 존재한다.

 Jasypt(Java Simplified Encryption) 라이브러리를 사용하여
 properties 파일을 암호화 할 수있다.
 비밀번호 인코딩에도 사용할 수 있다.

 우선 dependency를 기입해준다.

 (jasypt , version 1.9.0)
 (jasypt-spring31 , version 1.9.0)

 ```xml
 	<dependency>
 			<groupId>org.jasypt</groupId>
 			<artifactId>jasypt</artifactId>
 			<version>1.9.0</version>
 		</dependency>
 		<dependency>
 			<groupId>org.jasypt</groupId>
 			<artifactId>jasypt-spring31</artifactId>
 			<version>1.9.0</version>
 		</dependency>
 ```

 스프링 설정파일에서 properties를 암호화 하기위한 빈 설정을 추가한다.

 기존의 프로퍼티 위치를 지정하는 ```<context:property>``` 태그는 삭제하고 jasypt 라이브러리의 EncryptablePropertyPlaceholderconfigurer을 이용하여 설정파일 위치를 지정한다.

 EnvironmentStringPBEConfig 빈을 정의할 때 algorithm과 passwordEnvname 속성을 설정할 수 있다.
 standardPGEStringEncrytor 빈을 설정할 때 password 속성에 설정하는 값은 인코딩할 키를 지정한다. 이 키는 JDBC 연결 정보를인코딩 할 떄 사용한다.

  ![](https://drive.google.com/uc?export=view&id=1ylDzSg_QYZouDvtiUrCx_Nmjzt-nAd-v)

  ![](https://drive.google.com/uc?export=view&id=1H8VsOrW7nloV70rMGPsXM9wXhxfiyp0M)


 >'properties를 사용하는 기본방법이 있었고 이번에 보안을 적용하기 위한 jasypt를 쓰는건데 이게 내가 egov에서 처음 접하게된 properties 쓰는 방식과 비슷한빈인 것 같다.'

 ~160

 ```xml
 //root-context.xml

 <bean id="propertyConfigurer" class="org.jasypt.spring31.properties.EncryptablePropertyPlaceholderConfigurer">
 		<constructor-arg ref="configurationEncryptor" />
 		<property name="locations">
 			<list>
 				<value>/WEB-INF/spring/setting.properties</value>
 			</list>
 		</property>
 	</bean>

 	<bean id="environmentVariablesConfiguration" class="org.jasypt.encryption.pbe.config.EnvironmentStringPBEConfig">
 		<property name="algorithm" value="PBEWithMD5AndDES" />
 		<property name="passwordEnvName" value="APP_ENCRYPTION_PASSWORD" />
 	</bean>

 	<bean id="configurationEncryptor" class="org.jasypt.encryption.pbe.StandardPBEStringEncryptor">
 		<property name="config" ref="environmentVariablesConfiguration" />
 		<property name="password" value="rktwlsrud" />
 	</bean>

 ```

 ```java
 import org.jasypt.encryption.pbe.StandardPBEStringEncryptor;

 public class JDBCEncryptor {

 	public static void main(String[] args) {
 		// TODO Auto-generated method stub
 		StandardPBEStringEncryptor enc = new StandardPBEStringEncryptor();
 		enc.setPassword("rktwlsrud");
 		System.out.println(enc.encrypt("인코딩할 value"));
 		System.out.println(enc.encrypt("인코딩할 value"));

 	}

 }


 ```

 ```
 //properties


 # database - local
 Local.Url = ENC(7uUQ327caOu/a3F7nD/YxSqRCgdhXlpqrtwHpANNAfKmmjD29bLYMvapLyBQKaAPtFiW64v1VuI=)

 ```
 값에 ENC(인코딩값)
