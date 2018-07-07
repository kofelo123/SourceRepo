- [TestCode](#test-code)

- [JUnit4 의존설정](#8706_170)
- [JUnit 기본](#8706_171)
- [스프링 테스트의 MockMvc 사용](#8706_172)

-[error]
- [test 세팅 에러 - sessionCookieConfig](#8705_140)

### Test Code

```java
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(locations = {"file:src/main/webapp/WEB-INF/spring/**/*.xml"})
public class LoginTest {

	@Inject
	private MemberDAO dao;

	private static final Logger logger=LoggerFactory.getLogger(LoginTest.class);

	@Test
	public void testLogin() throws Exception{
		MemberVO mvo = new MemberVO();
		mvo.setId("kofelo123");
		mvo.setPwd("gj0123");
		MemberVO vo =dao.login(mvo);
	}

}
```



---


###### 8705_140

test 세팅 에러 - sessionCookieConfig
-



테스트 할때 라이브러리가 제대로 세팅되지 않은채로 테스트코드 실행시 다음과 같은 에러가 난다.

error message:
ClassNotFoundException: javax.servlet.SessionCookieConfig

```xml
	<!-- Servlet before-->
		<dependency>
			<groupId>javax.servlet</groupId>
			<artifactId>servlet-api</artifactId>
			<version>2.5</version>
			<scope>provided</scope>
		</dependency>
		<!-- Servlet after-->
		<dependency>
			<groupId>javax.servlet</groupId>
			<artifactId>javax.servlet-api</artifactId>
			<version>3.1.0</version>
		</dependency>

```


---


###### 8706_170

JUnit4 의존설정
-

```xml
		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-test</artifactId>
			<scope>test</scope>
			<version>4.0.4.RELEASE</version>
		</dependency>
		
		<!-- Test -->
		<dependency>
			<groupId>junit</groupId>
			<artifactId>junit</artifactId>
			<version>4.11</version>
			<scope>test</scope>
		</dependency>
		
		<dependency>
			<groupId>org.hamcrest</groupId>
			<artifactId>hamcrest-library</artifactId>
			<version>1.3</version>
			<scope>test</scope>
		</dependency>
```
spring-test , junit이 필수며, 테스트코드에서 사용할 다양한 비교기능을 사용하기위해 hamcrest-library를 추가한다.




---


###### 8706_171

JUnit 기본
-

```java

@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration("classpath:spring/test-root-context.xml")

//xml파일 두개이상일떄 배열로 설정파일 목록 전달.
@ContextConfiguration(locations={"classpath:spring/root-context.xml","classpath:spring/servlet-context.xml"})

(locations는 해도되고 안해도 되는것 같다.)
```

스프링 MVC를 위한 설정을 이용해서 테스트 코드 작성하려면

@WebAppConfiguration 을 추가로 적용한다.


JUnit은 각 테스트 메서드를 실행할 때마다 테스트 클래스의 객체를 생성한다.

각 객체를 실행하지만 스프링 컨텍스트 는 하나를 재사용한다.

?> 각 통합 테스트 실행시 컨텍스트를 초기화 하는시간이 길어지기때문

(각 테스트 마다 다시 컨텍스를 초기화 로딩하도록 하기위해서는 @DirtiesContext 애노테이션을 사용)


(트랜잭션 - 테스트 수행시마다 insert수행하면 값이 증가하는데 이것을 바로 초기화 롤백하는 방법이 있다 - @TransactionConfiguration, @Transactional  / @Rollback(true) 사용) - ( 외에도 DBUnit과 같은 테스트 도구를 통해서 DB상태를 동일상태로 유지하는 방법도 있다 - 이방법을 필자는 선호한다고 한다 - "Spring Test DBUnit")



---


###### 8706_172

스프링 테스트의 MockMvc 사용
-


```java
@RunWith(SpringJUnit4ClassRunner.class)
@WebAppConfiguration //웹 어플리케이션을 위한 스프링 컨텍스트를 사용하도록 설정한다.
@ContextConfiguration("classpath:/spring-mvc.xml")	
public class ValidTest {

//MockMvc를 생성할때 사용할 스프링 컨텍스트를 자동 주입 받는다.
@Autowired
private WebApplicationContext ctx;

private MockMvc mockMvc

@Before
public void setUp(){
//스프링 컨텍스트를 이용해서 MockMvc를 생성한다.
	mockMvc = MockMvcBuilders.webAppContextSetup(ctx).build();
}

@Test
public void testHello() throws Exception{

// 스프링 mvc에 get 방식의 "/hello" 요청을 전송, name 요청 파라미터로값으로 bkchoi를 전달
	mockMvc.perform(get("/hello").param("name","bkchoi"))
		.andExpect(status().isOk()) //응답 결과가 200(OK)인지 검사
		.andExpect(model().attributeExists("greeting"));//응답 결과 모델에 이름이 greeting인 값이 존재하는지 검사
	}
}
```
perform()메서드는 RequestBuilder를 이용해서 HttpServletRequest를 생성하고 DispatcherServlet을 실행한다.
andExpect() 메서드는 컨트롤러가 생성한 응답을 확인한다.


( .andFilter(sequrityFilter,"/*").build() ~ 이런식으로
스프링 시큐리티 처럼 DispatcherServlet을 실행하기 전에 특정필터 적용가능.)


get(), post() 등의 메서드는 MockHttpServletRequestBuilder 객체 리턴,

이 객체는 헤더설정,쿠키설정 등을 할수 있는 메서드를 제공

 ![](https://drive.google.com/uc?export=view&id=1h5S8aAiNCOsVx-Vo_LbIw3xm5xmT4WcL)

```java
mockMvc.perform(get("/hello").param("name","bkchoi")) ...
```



<상태코드검증>

status() 메서드는 StatusResultMatcher 객체 리턴, 이 클래스에 정의된 메서드로 검사한다.(생략)


```java
mockMvc.perform(get("/hello").param("name","bkchoi"))
.andExpect(status().isOk())
.andExpect(view().name("hello"))
.andExpect(model().attributeExists("grreeting")); // ModelResultMatchers객체리턴(모델값 검증위한 다양하 메서드 제공(생략));
.andExpect(redirectedUrl("/main/home"));
```

(이외에도 헤더검증, 쿠키검증 , JSON응답검증,XML,요청/응답 내용출력 등 있다(생략))
