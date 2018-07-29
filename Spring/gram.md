## SpringGrammars


- [@ModelAttibue()](#modelattribute)
- [logger](#logger)
- [@Resource 주입](#resource)
- [@autowired와 Resource 어노테이션의 차이](#autowiredresource)
- [root-context와 servlet-context](#rootcontextservletcontext)
- [dependency 설정에서 scope 종류와 설명](#dependencyscope)
- [@PathVariable](#8727_1)

- [AOP기본](#aopbasic)
	- [aop설정](#aopsetting)
	- [JoinPoint](#joinpoint)
	- [Around](#around)
	
- [Transaction](#Transaction)

- [String - not equals 할떄](#8719_3)

- [error]
	- [@RequestParam이 Null일떄](#requestparam)
	- [NoClassDefFoundError](#noclassdeffounderror)
	- [slf4j 로그 못찍을떄 에러](#slf4jlogerror)
	- [invalid CEN header](#invalidcenheader)
	- [failOnMissingWebXml](#failOnMissingWebXml)
	- [log4jdbc-log4j2](#log4jdbclog4j2)


---

### requestparam

> @RequestParam은 Null이 될떄 아래와 같은 에러가 발생한다.

```java
org.springframework.web.bind.MissingServletRequestParameterException: Required int parameter 'board_seq' is not present
```

> 파라미터의 값이 필수가 아니라면 required 속성의 값을 설정해주도록 하면 된다. 기본값은 true 이다.

```java
@RequestParam(value="board_seq",required=false)int board_seq
```

---

### ModelAttribute
``
>컨트롤러 파라미터에 @ModelAttribute 를 사용할 경우, 직접 메소드내에 model.addAttribute 하지않아도 자동으로 view단으로 전달된다.

```java
//BoardController
public String listPage(@ModelAttribute("cri") SearchCriteria cri, ...) throws Exception {
  ...
}

```html
// list.jap
          <option value="t"
							<c:out value="${cri.searchType eq 't'?'selected':''}"/>>
							제목</option>
```

---

### logger

```java
private static final Logger logger = LoggerFactory.getLogger(현재클래스.class);
```

---

## resource

```java
//UserServiceImpl.java
@Resource(name = "ipAddress") // ip 가변적이라서 주입(local-server).
	private String ipAddress;

```

```xml
//root-context.xml
	<bean id="ipAddress" class="java.lang.String">
		<constructor-arg value="localhost"> //war배포후 xml에서 수정
		</constructor-arg>
	</bean>

  //scan맞춰줘야함
<context:component-scan base-package="com.thearc.service"></~


```

---


###### autowiredresource

@autowired와 Resource 어노테이션의 차이
-

 ![](https://drive.google.com/uc?export=view&id=1Hs0mwVSliG0nVlnL58dfbwPY7icQvbt_)


---

###### rootcontextservletcontext

root-context와 servlet-context
-

servlet context의 경우 web application의 client의 요청을 받기 위한 entry point로서의 서블릿의 context 설정이라고 볼 수 있습니다.
따라서 해당 context의 설정에는 요청에 대한 처리를 해줄 controller의 매핑설정(handler mapping)과 요청처리 후 view처리를 어떻게 할 것인가에 대한 설정(view resolver) 등이 존재하게 됩니다.

root context의 경우 web application의 실제 비지니스 혹은 목적을 위한 service layer와 해당 service layer에서 조회 및 처리에 필요한 database 와 연결되는 repository layer를 구성하는 bean들에 대한 설정을 하게 됩니다.

위 두개의 context는 계층구조를 가지게 되는데 아래의 그림처럼  serlvet context에서 root context에 등록된 bean들에 대한 참조를 가지는 구조로 형성됩니다.(반대의 참조는 불가능합니다.)

 ![](https://drive.google.com/uc?export=view&id=1eNiyTtNuNMN9WUsHPLbAV1OoyY1dkHap)



 ---


 ###### dependencyscope

 dependency 설정에서 scope 종류와 설명
 -

 pom.xml 설정시 dependency 설정에서 scorp 종류와 설명.

 compile : 컴파일 할때 필요. 테스트 및 런타임에도 클래스 패스에 포함 된다. scorp 을 설정 하지 않는 경우 기본값이다.
 runtime : 런타임에 필요. JDBC 드라이버 등이 예가 된다. 컴파일 시에는 필요하지 않지만, 실행 시에 필요한 경우.
 provided : 컴파일 시에 필요하지만, 실제 런타임 때에는 컨테이너 같은 것에서 제공되는 모듈. servlet, jsp api 등이 이에 해당. 배포시 제외된다.
 test : 테스트 코드를 컴파일 할때 필요. 테스트시 클래스 패스에 포함되며, 배포시 제외된다.
 ```
 예시)
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

```


---


###### noclassdeffounderror

NoClassDefFoundError: javax/servlet/ServletContext

javax.servlet.ServletContext lib관련 에러가 있다는말인데

artifactId에 오타가 있었다.

그런데 그게 원래도 있는 artifactId고 없는 버전이 되니 pom.xml에 에러표시는 따로 안나고 lib를 가져오는데 문제가 생기는 것인데

메뉴의 View -Tool Windows - Maven Project를 들어가보면

GUI로 dependency관련 창이 나타나는데 거기서 어떤 jar파일 에러가 났는지 볼수 있었다.

---


###### slf4jlogerror

slf4j 로그 못찍을떄 에러
-

error message: Failed to load class "org.slf4j.impl.StaticLoggerBinder".

-> dependency에 slf4j-log4j12 추가

error message:
log4j:WARN No appenders could be found for logger (org.springframework.test.context.junit4.SpringJUnit4ClassRunner).
log4j:WARN Please initialize the log4j system properly.

-> log4j.xml , log4jdbc.log4j2.properties, logback.xml 등 log4j관련 설정파일 resources폴더에 넣어줘야(log4j.xml만 넣어도 돌아가긴한다)

-> log4jdbc.log4j2.properties, logback.xml 는 MyBatis관련 로그찍을떄 필요


---


###### aopbasic

AOP 기본
-

Aspect : 공통 관심사에 대한 추상적 명칭

Advice: 실제로 기능을 구현한 객체, 클래스 제작후 @Advice를 붙여 적용

Join points : 공통관심사 적용할 수 있는 대상 Spring AOP에서 각 객체의 메소드 . 활약할수 있는 대상

Pointcuts: 여러 메소드중 실제 Advice가 적용될 대상 메소드 . 여러 JoinPoint중에서 Advice를 적용할 대상

target : 대상 메소드를 가지는 객체

Proxy : Advice가 적용되었을때 만들어지는 객체

Introduction : target에는 없는 새로운 메소드나 인스턴스 변수를 추가하는 기능

Weaving: Advice와 target이 결합되어서 프록시 객체를 만드는 과정


<Advice종류>

Advice는 실제 구현된클래스로 생각할 수 있고 아래와 같이 분류

Before Advice : target의 메소드 호출 전에 적용
After retuning: target의 메소드 호출 이후 적용
After throwing: target의 예외 발생 후 적용
After : target의 메소드 호출 후 예외의 발생에관계없이 적용
Around : target의 메소드 호출 이전과 이후 모두 적용(가장 광범위함)



---


###### aopsetting

설정
-

AOP 관련 라이브러리, 트랜잭션을 위한 'spring-tx' 라이브러리가 필요

```xml
//pom.xml
	<properties>
		<java-version>1.8</java-version>
		<org.springframework-version>4.3.3.RELEASE</org.springframework-version>
		<org.aspectj-version>1.8.9</org.aspectj-version>
		<org.slf4j-version>1.6.6</org.slf4j-version>
	</properties>

	

		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-aop</artifactId>
			<version>${org.springframework-version}</version>
		</dependency>


		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-tx</artifactId>
			<version>${org.springframework-version}</version>
		</dependency>



	<!-- AspectJ -->
		<dependency>
			<groupId>org.aspectj</groupId>
			<artifactId>aspectjrt</artifactId>
			<version>${org.aspectj-version}</version>
		</dependency>


		<dependency>
			<groupId>org.aspectj</groupId>
			<artifactId>aspectjtools</artifactId>
			<version>${org.aspectj-version}</version>
		</dependency>


```

Spring AOP - 애노테이션,XML 두가지 방법

XML 이용한 설정시 servlet-context.xml 이나 root-context.xml에서 사용하는 xml 네임스페이스가 필요-> aop, tx 에 대한 네임스페이스 추가


root-context.xml - AOP 설정을 통한 자동적인 Proxy 객체 생성을 위해

```
//root-context.xml

//자동으로 AspectJ 라이브러리를 이용해 Proxy 객체생성한다.
<aop:aspectj-autoproxy></aop:aspectj-autoproxy>


//xml방식으로 AOP 기능을 설정할떄 사용함.(zerock에서 어노테이션으로 설정함.)
<aop:config>
</aop:config>
```

ex)
```java
package com.aop.aop;

import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.Before;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.stereotype.Component;

@Component
@Aspect
public class SampleAdvice {

	private static final Logger logger = LoggerFactory.getLogger(SampleAdvice.class);
	
	//target 메소드 실행전에 실행될 메소드 / execution으로 시작하는 구문 - Pointcut 지정문법, AjspectJ 문법사용
	@Before("execution(* com.aop.service.MessageService*.*(..))")
	public void startLog() { //제대로 설정되면 메소드 좌측에 aop아이콘이 뜬다.
		
	logger.info("--------------");
	logger.info("--------------");
	
	}
	
}
```




---


###### invalidcenheader

invalid CEN header
-

error message: java.util.zip.ZipError: invalid CEN header (bad signature)

검색해보니 jar문제인데, maven repository(.m2) 쪽 다 지우고 maven update 하라고 해서 했는데 여전히 에러나서

project - clean 한후에

maven - update project 하니 해결되었다.



---


###### failOnMissingWebXml

failOnMissingWebXml
-

error message : failOnMissingWebXml

상황: intellj의 프로젝트를 이클립스에 가져오는데, .project 파일이 없어서 불러오지 못함 - new project해서 .project파일 포함해서 eclipse 스타일의 폴더구조가 부가적으로 생성되었고 그것들을 지운상태.

인텔리제이의 프로젝트구조와 이클립스의 프로젝트의 기본구조가 달라서 web.xml을 찾지 못해서 발생한 에러로 보인다.
("web.xml 이 존재하지 않는 프로젝트를 maven 으로 패키징할때 아래와 같은 메시지를 던지면서 에러가 발생한다.") 

플러그인에 아래 속성 추가
```xml
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
      <artifactId>maven-war-plugin</artifactId>
      <configuration>
         <failOnMissingWebXml>false</failOnMissingWebXml>
      </configuration>
</plugin>
```
그리고 maven - update project해준다


=> 애초에 new project 안하는게 나은거같다. git에서 바로 받아올떄 Import as general project하면 .project 생성된다. 에러도없다. (그러나 일반 프로젝트로 생성되는데, configure -> maven project 하면 일반 스프링 프로젝트 구조로 된다).

아니면 .project .classpath만 빈거 같다 넣는방법.

new project하면 인텔리제이 프로젝트구조와 이클립스 프로젝트구조가 둘다 생겨버림.


---


###### log4jdbclog4j2

log4jdbc-log4j2
-

MyBatis 사용 - 자세한 로그 

```xml
<dependency>
			<groupId>org.bgee.log4jdbc-log4j2</groupId>
			<artifactId>log4jdbc-log4j2-jdbc4</artifactId>
			<version>1.16</version>
		</dependency>

```
```xml
<bean id="dataSource" class="org.springframework.jdbc.datasource.DriverManagerDataSource">
		<property name="driverClassName" value="net.sf.log4jdbc.sql.jdbcapi.DriverSpy"></property>
		<property name="url" value="jdbc:log4jdbc:mysql://127.0.0.1:3306/studydb?useSSL=false&amp;serverTimezone=UTC"></property>
		<property name="username" value="..."></property>
		<property name="password" value="..."></property>
	</bean>
```

제대로 동작하기 위해 별도의 로그 관련 설정 파일 필요.

/src/main/resources  경로에 log4jdbc.log4j2.propertgies 파일과 logback.xml 파일을 추가

```
//log4jdbc.log4j2.properties

log4jdbc.spylogdelegator.name=net.sf.log4jdbc.log.slf4j.Slf4jSpyLogDelegator

```

```
//logback.xml

<?xml version="1.0" encoding="UTF-8"?>
<configuration>
    <include resource="org/springframework/boot/logging/logback/base.xml"/>

    <!-- log4jdbc-log4j2 -->
	<logger name="jdbc.sqlonly"        level="INFO"/>
    <logger name="jdbc.sqltiming"      level="INFO"/>
    <logger name="jdbc.audit"          level="WARN"/>
    <logger name="jdbc.resultset"      level="ERROR"/>
    <logger name="jdbc.resultsettable" level="ERROR"/>
    <logger name="jdbc.connection"     level="INFO"/>
</configuration>

```

---


###### joinpoint

JoinPoint
-

|  메소드 | 설명  |
|---|---|
| Object[] getArgs()  | 전달되는 모든 파라미터들을 Object의 배열로 가져온다.  |
|  String getKind() |  해당 Advice의 타입을 알아낸다. |
| Signature getSignature()  | 실행하는 대상 객체의 메소드에 대한 정보를 알아낼 떄 사용  |
| Object getTarget()  |  target 객체를 알아냄 |
|  Object getThis() | Advice를 행하는 객체를 알아낼 떄 사용  |

```java
@Before("execution(* com.aop.service.MessageService*.*(..))")
	public void startLog(JoinPoint jp) {
		
	logger.info("--------------");
	logger.info("--------------");
	logger.info(Arrays.toString(jp.getArgs()));
	}
	
```

---


###### around

Around
-

가장 강력하게 사용할 수 있다.

메소드 실행에 직접관여, 앞뒤를 감싸서 특정 기능을 실행할 수 있다.

메소드
Object proceed() : 다음 Advice를 실행하거나, 실제 target 객체의 메소드를 직접 실행하는 기능


'ProceedingJoinPoint'는 JoinPoint의 하위 인터페이스이다. 따라서 모든 JoinPoint의 메소드를 가지면서도 직접 target 객체의 메소드를 실행할 수있는 기능이 추가된 형태이다.

proceed()는 특이하게 Exception보다 상위의 Throwable을 처리해야하므로 시간을 체크하는 기능은 다음과 같이 작성됨.

```java
	@Around("execution(* com.aop.service.MessageService*.*(..))")
	public Object timeLog(ProceedingJoinPoint pjp)throws Throwable{
		
		long startTime = System.currentTimeMillis();
		logger.info(Arrays.toString(pjp.getArgs()));
		
		Object result = pjp.proceed(); //실제 메소드 호출
		
		long endTime = System.currentTimeMillis();
		logger.info(pjp.getSignature().getName()+ " : " + (endTime - startTime));
		logger.info("====================================");
		
		return result;
	
	}
```


@Around를 사용하는 메소드의 리턴은Object로 해야함.
다른 Advice와 달리 메소드를 직접 호출하고 결과를 반환해야 정상적으로 처리됨


---


###### Transaction
-

<트랜잭션>

xml방식(trasaction-context.xml사용), 애너테이션 방식(DAO나 Mapper등을 이용하는 xxxService 클래스에 어노테이션 처리)

DB 정규화와 트랜잭션은 관련이 있는 경우가많다.
정규화가 잘되어 있을수록, 관련맺는 데이터는 줄어든다.
반면, 성능상 이유나 구현상 복잡함으로 반정규화가 많이 진행되는 경우 트랜잭션을 처리해야하는 상황이 많아진다.

@Transactional (속성이 많이 있다,검색)

```xml
//root-context.xml

<bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
		<property name="dataSource" ref="dataSource"></property>
	</bean>		

<tx:annotation-driven/> 
```
하나의 DataSource를 사용하는경우 스프링에서 제공하는 DataSourceTransactionManager를 이용한다.

<tx:annotation-driven /> 은 @Transactional 애노테이션을 이용한 트랜잭션 관리가 가능하다.


@Transactional 우선순위 : 인터페이스<클래스<메소드

```java
//예제

@Transactional
	@Override
	public void addMessage(MessageVO vo) throws Exception {
		
		messageDAO.create(vo);
		pointDAO.updatePoint(vo.getSender(), 10);
	}

```

로그를 보면 하나의 커넥션이 열리고 두개의 메소드가 실행된다


---
- [SuppressWarnings ](#suppressWarnings )

###### suppressWarnings 


-
@SuppressWarnings ("옵션") 


:컴파일러가 일반적으로 경고하는 내용 중 이건 하지말라고 제외시킬때 사용.  어떤 경고를 제외시킬지 여러 옵션이 있다.

몇가지 정리하면 

1. all : 모든 경고를 억제
2. cast : 캐스트 연산자 관련 경고 억제
3. dep-ann : 사용하지 말아야 할 주석 관련 경고 억제
4. deprecation : 사용하지 말아야 할 메소드 관련 경고 억제
5. fallthrough : switch문에서의 break 누락 관련 경고 억제
6. finally : 반환하지 않는 finally 블럭 관련 경고 억제
7. null : null 분석 관련 경고 억제
8. rawtypes : 제네릭을 사용하는 클래스 매개 변수가 불특정일 때의 경고 억제
9. unchecked : 검증되지 않은 연산자 관련 경고 억제
10. unused : 사용하지 않는 코드 관련 경고 억제

이외에도 몇가지더 있다.

-----------------------------------------

###### 8719_3

String - not equals 할떄
-

equals를 not equals 와 같이 쓰고싶을떄 아래와 같이 사용
```java
if (!"success".equals(statusCheck))
```



###### 8727_1

@PathVariable
-

@PathVariable - URI 경로에서 원하는 데이터를 추출하는 용도

사용 코드 예 
```java
  @GetMapping("/register/{category}")
	  public String registGET(@PathVariable String category,Model model) throws Exception {
		 
	    logger.info("regist get ...........");
	    
	    
	    return "/sboard/register";
	  }
```

기본적으로 ModelAttribute처럼 view단으로 알아서 넘긴다.

@PathVariable에서 

@PathVariable String category 의경우 넘어온 category 를 변수이름와 일치시켜서 그대로 받아서 view로 보내는경우고

@PathVariable("category") String category22
받아서 view에  보내는것은 category지만 변수는 다른이름일떄

(pathvariable을 비롯해서 다른 애너테이션(ModelAttribute,RequestParam등)도 공통임("") 처리에 관해서)

참고) + 기본타입(int,String)등일때는 보통 이름이 같으면 생략 가능하지만 타입이 사용자가 만든 객체인경우 ("") 처리를 해줘야 전달이 되는것 같다.

```
@ModelAttribute ("cri") SearchCriteria cri
```


