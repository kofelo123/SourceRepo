## SpringGrammars

- [@RequestParam이 Null일떄](#requestparam)
- [@ModelAttibue()](#modelattribute)
- [logger](#logger)
- [@Resource 주입](#resource)
- [@autowired와 Resource 어노테이션의 차이](#autowiredresource)
- [root-context와 servlet-context](#rootcontextservletcontext)
- [dependency 설정에서 scope 종류와 설명](#dependencyscope)

- [error]
  - [NoClassDefFoundError](#noclassdeffounderror)
  - [slf4j 로그 못찍을떄 에러](#slf4jlogerror)

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
