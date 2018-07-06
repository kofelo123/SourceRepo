- [TestCode](#test-code)

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
