- [TestCode](#test-code)


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
