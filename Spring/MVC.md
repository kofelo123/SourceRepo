- [@GetMapping, @PostMapping](#getmappingpostmapping)
- [날짜(Date타입)변환처리](#datetimeformat)
- [정규표현식](#regularexpression)
- [mybatis-캐시 비사용](#8061162)
- [파라미터 map으로 받아 처리하기.](#180824_4)

- [유효성검사](#validation)
- [Validator](#8622_111)
- [JSR 303 Validation](#8622_102)
- [validError](#validError)
- [Valid - 메시지 처리](#8716_3)

- [maven 수동추가와 내부파일](#180913_2)

- [자바단에서 js - alert 사용하기](#8718_2)
- [spring stopwatch](#8719_1)

- [getRequestURI(), getContextPath() - url에서 path값 가져오기](#8719_4)
- [세션에 대한설명](#180902_6)
- [SP EL](#181201_4)
- [log4j 커스텀 설정 에러](#181201_11)


- [에러](#error)
    - [415에러-format not supported](#formatnotsupported)

---


###### getmappingpostmapping

@GetMapping, @PostMapping
-

Spring 4.3 부터 메서드에 쓸수있다(컨트롤러는x). HTTP 프로토콜의 방식에 맞게 설정할수있다.

```java
@Controller
@RequestMapping("/sample") //컨트롤러는 기본 @RequestMapping으로 해야함
@Log4j
public class SampleController {

@GetMapping("/doA")
   public void doA(){
       log.info("/sample/doA........called");
   }

   @PostMapping("/login")
   public void login(LoginDTO loginDTO){
       log.info("-------------");
       log.info(loginDTO);
   }

}
```
---

---



###### datetimeformat

날짜(Date타입)변환처리
-

```java
@PostMapping("/dateEx")
    public void dataEx(@DateTimeFormat(pattern = "yyyy/MM/dd") Date date){
        log.info("--------------");
        log.info(date);
    }


    @Test
    public void testDateEx()throws Exception{
        mockMvc.perform(MockMvcRequestBuilders.post("/sample/dataEX")
        .param("date","2018/05/21"));
    }
```

스프링 3.0 부터 존재하는 날짜 변환을 위해 사용하는 어노테이션

parameter를 문자열로 전송해도 Date타입으로 처리됨.


---


###### formatnotsupported
-

error message :  HTTP Status 415 - request entity is in a format not supported

- 곰곰히 생각해보니 jason 타입 변환해주는 dependency를 넣지 않았다.

```xml
<dependency>
	<groupId>com.fasterxml.jackson.core</groupId>
	<artifactId>jackson-databind</artifactId>
	<version>2.5.4</version>
</dependency>
```



---


###### regularexpression


정규표현식
-

문자열에서 특정 문자를 찾아낸다.(검색,치환용도)

사용하는 기호를 Meta문자 라고한다.

[정규표현식정리](http://www.nextree.co.kr/p4327/)

[정규표현식-예](https://blog.outsider.ne.kr/360)

[정규표현식](http://ktko.tistory.com/74)

[휴대폰번호 유효성검사 - 정규식체크](http://blog.tjsrms.me/jquery-%ED%95%B8%EB%93%9C%ED%8F%B0-%EB%B2%88%ED%98%B8-%EC%B2%B4%ED%81%AC%ED%95%98%EA%B8%B0/)



---


###### 8061162

mybatis-캐시 비사용
-

```xml
//mybatis-config.xml

<configuration>
	<settings>
		<setting name="cacheEnabled" value="false" />
	</settings>
</configuration>
```



---

###### validation

validation
-



폼입력값 검사
(유효성 검사 관련 - 중요한정보는 서버쪽에서 검증하는게 맞다고 한다. jsr -303 스펙에서 구현한(Bean Validation) @Valid 어노테이션을 쓰거나 validator 인터페이스 구현한 검증 두가지 방법을 많이 쓴다.)

<Validator>

<주요메소드>

- boolean supports(Class clazz) : 주어진 객체(clazz)에 대해 Validator가 지원 가능한가?

- void validate(Object target, Errors errors) : 주어진 객체(target)에 대해서 유효성 체크를 수행하고, 유효성 에러 발생시 주어진 Errors객체에 관련 정보가 저장된다.

구현 Validator 클래스 생성시 위 두메소드 구현해야함.


---

###### 8622_111

Validator
-

org.springframework.validation.Validator 인터페이스를 구현해서 스프링 벨리데이터를 만들 수있다.

폼 값에 저장한 커맨드 객체의 유효성 여부를 검사하고, 입력한 값이 유효하지 않은 경우 Errors에 에러메시지를 저장해서 뷰에 전달할 수있다.


애너테이션에 의한 폼 벨리데이션 보다 더 자세하고 정확하게 폼 데이터 검증가능.

알아야할 3가지

- Validator
:org.springframework.validation.Validator 인터페이스는 객체검증에 사용된다.
내부적으로 supports() , validate()메서드가있다.

 boolean supports(Class<?> clazz): Validator가 해당 클래스에 대한 값 검증을 지원하는지 여부 리턴

 void validate(Object target,Errors errors): target 객체에 대한 검증을 실행.
문제가 있을때 errors 객체에 어떤 문제가 있는지에 대한 정보를 저장. 이 인터페이스에는 reject()메서드와 rejectValue()메서드들이 있다.

- Errors

org.springframework.validation.Errors 인터페이스는
유효성 검증에러 저장시 사용

void reject(String errorCode): 전체 객체의 글로벌 에러코드 추가

void reject(String errorCode, String defaultMessage): 글로벌 에러코드추가, 에러코드에 대한 메시지가 존재하지 않을떄 defaultMessage를 사용

...

void rejectValue(String field, String errorCode): 필드에 대한 에러코드추가

...

- BindingResult

org.springframework.validation.BindingResult 인터페이스는 Errors 인터페이스의 하위 인터페이스.

폼값을 커맨드 객체에 바인딩한 결과를 저장.
에러코드로부터 에러메시지 가져온다.

이 인터페이스는 컨트롤러 메서드의 인자로 정의될 때 반드시 커맨드 객체 바로 뒤에 정의되어야한다.

JSR 303 애노테이션과 스프링 벨리데이터를 함께 사용할 경우 스프링 벨리데이터가 적용된다.

이때 JSR 303 애너테이션에 의한 메시지는 오버라이드 되지 않는다.

@InitBinder 메서드를 삭제하거나 setValidator() 메서드 호출부를 삭제하면 JSR 303 벨리데이터가 적용된다.


---

###### 8622_102

JSR 303 Validation
-

Hibernate Validator는 JSR 303을 구현한 라이브러리

폼 객체의 필드위에 JSR 303 애노테이션을 이용하여 폼 데이터의 유효성을 검사

```xml

        <!-- 유효성검증  -->
		<dependency>
			<groupId>javax.validation</groupId>
			<artifactId>validation-api</artifactId>
			<version>1.0.0.GA</version>
		</dependency>
		<!-- https://mvnrepository.com/artifact/org.hibernate/hibernate-validator-annotation-processor -->
		<dependency>
		    <groupId>org.hibernate</groupId>
		    <artifactId>hibernate-validator-annotation-processor</artifactId>
		    <version>4.1.0.Final</version>
		</dependency>

			
```
(책에는 4.1.0.Final인데 둘다 같이하면 충돌나는지 에러남 - 5.1.3.Final로 변경했다가 다시, 4.1.0.Final 쓰고 위에 꺼 지우고 사용했음.)

=> 검색하다가 validation-api 의 버전 1.0.0.GA 버전을 사용하는게 있어서 써보니까 충돌 안난다.

JSR 303 애노테이션

 ![](https://drive.google.com/uc?export=view&id=1anxW2voUT--WBTKvlsnL9LtJr_4CvfMx)


```java

public class EmpVO {
	
	@Min(value=300, message="사원번호는 300이상")
	private int employeeId;
	
	@Pattern(regexp="[a-zA-Z가-힣] {1,}", message="이름 입력")
	private String firstName;
	
	@Pattern(regexp="[a-zA-Z가-힣] {1,}", message="성 입력")
	private String lastName;
	
	@Pattern(regexp="[A-Z0-9] {2,}" , message="영문 대문자와 숫자를 이용해 두 자리 이상")
	private String email;

	//in Controller
	public String insertEmp(@ModelAttribute("emp") @Valid EmpVO emp, BindingResult result, Model model ,RedirectAttributes redirectAttrs) {
		
		return "";
	}
}

```

[참고해볼만한 사이트-구루비작성](http://wiki.gurubee.net/pages/viewpage.action?pageId=12189867)

비밀번호 재확인 같은 것을 '크로스 필드' 검사 라고 하는 것 같다.

이 크로스 필드에 대한 검증 어노테이션은 포함되어있지 않았다(JSR 303) 

stackoverflow를 보니 다른 필드에 대해 한 필드의 유효성 검사주석을 다른필드에 사용하는것이 권장되지 않는다.(별개의 분리된 검사 주석을 사용해야)

크로스필드 검사는 클레스 레벨해서 되야한다.


<@NotNull 적용 안되는것에 대해>

@NotNull이 내가 생각했던 비어있는 value를 말하는게 아닌것이었던것 같다.(말그대로 null이 아닌지만)

@NotNull은
:javax.validation.constraints.NotNull,

@NotBlank,@NotEmpty 라는게 있었다.
:org.hibernate.validator.constraints.NotBlank
org.hibernate.validator.constraints.NotEmpty;
@NotBlank를 써서 내가 원했던 빈칸검사를 할 수 있었다.
(@NotEmpty는 공백까지 인식하기 때문에 공백값으로 통과될수있기때문에 NotBlank쓰는게 맞는것같다.)
[api-validator-constraints annotation](https://docs.jboss.org/hibernate/validator/4.1/reference/en-US/html/validator-usingvalidator.html#validator-defineconstraints-builtin)


(참고 -토비의스프링):  @NotNull 로는 빈 문자열이 검증되지 않는데 이때 @Size(min=1)을 사용해야한다.

---

###### validError

validError
-

Neither BindingResult nor plain target object for bean name 'command' available as request attribute

지정한 객체와 연결이 안되서 나오는 에러

입력폼 만들고 path지정하고, 속성값 지정하면 객체와 비교해야 하는데 연결이 안되어서 에러남

```java
@RequestMapping(value = "/login", method=RequestMethod.GET)
	public void loginGET(Model model){
		model.addAttribute("dto",new LoginDTO()); //객체 정보를 넘겨줘야한다.
	}
```
(넘기고 view단의 form에서 modelAttribute 처리해줘야한다)

```html
<%@ taglib prefix="form" uri="http://www.springframework.org/tags/form" %>

<form:form action="/thearc/user/loginPost" method="post" modelAttribute="dto">

<!-- <input type="text" name='title' class="form-control" placeholder="제목을 입력해주세요"> -->
	<form:input type="text" path="title" name="title" class="form-control" placeholder="제목을 입력해주세요" />
	<form:errors path="title" class="error" style="color:red;"/>
```

error message: An Errors/BindingResult argument is expected to be declared immediately after the model attribute, 
```java
 public String joinPost(BindingResult result,@ModelAttribute("uvo") @Valid UserVO user, RedirectAttributes rttr) throws Exception{
```
		위와 같이 Binding Result를 ModelAttribute 앞에다가 쓰면 안된다.
순서를 바꾸면 된다. 



-----------------------------------------

###### 8716_3

Valid - 메시지 처리
-

입력값 검증후 에러메시지를 폼 객체 안에 넣으면, 수정할때 컴파일을 해야하며, 국제화 처리도 불가능 -> JSR 303 폼 유효성 검증 후 에러를
메시지처리 해야한다.


```xml
//servlet-context.xml
	
	<beans:bean id="messageSource" class="org.springframework.context.support.ReloadableResourceBundleMessageSource">
		<beans:property name="defaultEncoding" value="UTF-8"></beans:property>
		<beans:property name="basenames">
		<beans:list>
			<beans:value>classpath:i18n/hr</beans:value>
		</beans:list>
		</beans:property>
	</beans:bean>

```
이와 같이 위치를 지정해주면 아래 properties들을 인식한다.
 ![](https://drive.google.com/uc?export=view&id=16p4zweCVRGzpSwZNvI08eV-ae9p14WF6)

{constraint-name}.{command-object-name}.{attribute-name}=에러메시지

{command-object-name}에 해당하는 아래의 uvo는 Controller의 joinPost(@ModelAttribute("uvo") @Valid UservVO user ) 에서의 uvo다.
 
//hr_ko.properties
```xml
NotBlank.uvo.uid=아이디를 입력해야 합니다.
NotBlank.uvo.upw=비밀번호를 입력해야합니다.
NotBlank.uvo.uname=이름을 입력해야합니다.
Size.uvo.uid=5~20 글자로 입력
Size.uvo.upw=5~50 글자로 입력
Size.uvo.uname=5~20 글자로 입력

```
//hr_en.properties
```xml
NotBlank.uvo.uid= ID shouldn't be Blank 
NotBlank.uvo.upw= PW shouldn't be Blank
NotBlank.uvo.uname= User Name shouldn't be Blank
Size.uvo.uid= ID should be at least 5 and less than 20
Size.uvo.upw= PW should be at least 5 and less than 50
Size.uvo.uname= User Name should be at least 5 and less than 20
```
영어의 경우 브라우저에서 언어를 영어로 설정한후 확인해볼 수 있다. 

위와 같이 설정해준후 UserVO에
@NotBlank(message="") 이런식으로 있던 메시지의 @NotBlank 이런식으로 message 부분을 지워주면된다.





-----------------------------------------

###### 8718_2

자바단에서 js - alert 사용하기
-

```java
response.setContentType("text/html; charset=UTF-8");
PrintWriter out = response.getWriter();
out.println("<script>alert('로그인 정보를 확인해주세요.'); history.go(-1);</script>");
out.flush();
```

(참고) 
java.lang.IllegalStateException: Cannot call sendRedirect() after the response has been committed

단말에 response 를 한 후 다시 sendRedirect 를 했을때..
http 프로토콜은 request 1 번에 response 1번으로 통신이 이루어지고 connection 을 끊어버리는 stateless 프로토콜이다.

스프링 인터셉터에서 response.sendRedirect를 지워주고 js로 페이지 이동을 했다.

```java
//로그인 실패시
    }else if(userVO == null){
    	System.out.println("login fail - user is not exist");
    	response.setContentType("text/html; charset=UTF-8");
    	PrintWriter out = response.getWriter();
    	out.print("<script>alert('해당 아이디의 비밀번호가 일치하지 않습니다.');location.href='/thearc/user/login';</script>");
    	out.flush();
//    	response.sendRedirect("/thearc/user/login");
    }
```

 - Controller에서 사용시 반드시 view를 Stirng으로 return 반환해야 하는것 떄문에 reponse에러를 피할수 없었다.
  (return 에 꼭 redirect 아니라 String view를 반환해도 마찬가지로 에러)
 -> return null; 해주면 해결가능.
 
 ```java

...	{ ...
	response.setContentType("text/html; charset=UTF-8");
		PrintWriter out = response.getWriter();
		out.print("<script>alert('관리자 아이디혹은 비밀번호가 일치하지 않습니다.');location.href='/thearc/user/login';</script>");
		out.flush();
		}
		return null;

 ```

-----------------------------------------

###### 8719_1

spring stopwatch
-

속도 측정에는 system.currenttimemillis() 를 사용하는 방법도 있지만,

스프링에서 제공하는 stopwatch가 있다.

다음은 예

```java
StopWatch stopWatch = new StopWatch("측정 이름"); // 측정 이름은 보통 클래스 정도(옵션)
  
stopWatch.start("job 이름"); // stopWatch를 측정할 job 이름을 지정 ,메소드라던가... 측정하고 싶은 이름으로...  (옵션)
  
// 요기부터 로직...  
  
stopWatch.stop();  
  
logger.debug(stopWatch.toString()); // 결과를 출력합니다.  
```

예2
```java

@Around("@annotation(LogExecutionTime)")
public Object logExecutionTime(ProceedingJoinPoint joinPoint) throws Throwable {
	StopWatch stopWatch = new StopWatch();
	stopWatch.start();
	Object proceed = joinPoint.proceed();
	stopWatch.stop();
	logger.info(stopWatch.prettyPrint());
return proceed;
}

```


-----------------------------------------
###### 8719_4

getRequestURI(), getContextPath() - url에서 path값 가져오기
-


request.getContextPath() 함수 = 프로젝트 Path만 가져온다.

예)  http://localhost:8080/project/list.jsp

[return]        /project 를 가져온다.


request.getRequestURI() 함수 = 프로젝트 + 파일경로까지 가져온다

예)  http://localhost:8080/project/list.jsp

[return]        /project/list.jsp  를 가져온다



String url = request.getRequestURI.split("/");

String Name = url[url.length -1];       // list.jsp



request.getRequestURL() 함수 = 전체 경로를 가져옵니다.     (L만 바뀜)

예) http://localhost:8080/project/list.jsp

[return]   http://localhost:8080/project/list.jsp

request.ServletPath() 함수 = 파일명만 가져옵니다.

예) http://localhost:8080/project/list.jsp



[return] /list.jsp



request.getRealPath("") 함수 =

서버 or 로컬 웹 애플리케이션 절대결로 가져옵니다.


예) http://localhost:8080/projectname/list.jsp



[return]         c:\project\webapps\projectname\




-----------------------------------------

###### 180824_4

파라미터 map으로 받아 처리하기.
-

@GetMapping("read")
	  public void read(@RequestParam("bno") int bno, Model model)throws Exception{
		  model.addAttribute("list", service.listAll());
	  };
	  @GetMapping("zzzUpdate")
	  public void zzz(@RequestParam Map<String,String> map, Model model)throws Exception{
		  map.get("bno");
		  model.addAttribute("list", service.listAll());
	  };
	  @GetMapping("zzzInsert")
	  public void zzz2(@RequestParam Map<String,String> map, Model model)throws Exception{
		  map.get("uid");
		  model.addAttribute("list", service.listAll());
	  };

si에서는 첫번째 메소드처럼 @RequestParam("bno") int bno

이런식으로 하지않고 아래처럼 Map map 이런식으로 @RequestParam을 받아서
map.get("bno") 이런식으로 매핑되는걸 받아온다.
그러면 @RequestParam 을 map으로 받아서 복사붙여넣기해서 빠르게 메소드를 생성한다.

일종의 상위에서 인터페이스같은 타입으로 두고 하위에서 구체적모듈을 구현하는것과 같다.



-----------------------------------------

###### 180902_6

세션에 대한설명
-

Spring HttpSession의 동작방식이라기 보다는, 모든 backend 언어의 세션의 동작 방식이 동일합니다.



서버에서 세션을 사용 설정한 경우, 클라이언트가 해당 서버에 접속할 때 서버에는 세션 파일을 하나 생성합니다.

그리고 서버는 브라우저에게 JSESSIONID라는 쿠키를 전송합니다.(해당 쿠키 명은 변경할 수 있습니다.)



이제 브라우저는 다른 페이지로 이동할 때마다, JSESSIONID를 서버로 함께 전송합니다.

그럼 서버는 JSESSIONID 쿠키 값을 보고 A 브라우저는 A 세션이 매칭 되어있구나,라고 하고 어떤 사용자인지 체크할 수 있습니다.

브라우저마다 세션 값이 겹치지 않는 이유는 브라우저 별로 별도의 쿠키를 관리하며, 쿠키를 별도로 관리하기 때문에 서버 입장에서는 같은 사용자로 인식할 수 없습니다.

로그인 기능을 만들더라도 다른 브라우저에서 같은 아이디로 접속하게 되더라도 JSESSIONID는 전혀 달라지게 됩니다.

 쿠키는 클라이언트가 요청해서 보내는 것이 아니라, 세션(세션 아이디를 갖는 쿠키)가 없는 클라이언트라면 서버에서 무조건 보내줍니다.(서버에 세션 설정이 선행 되어있는 경우)



또한, 세션 설정 시 서버의 메모리가 아니라 보조기억장치(HDD, SSD 등의 저장장치)에 저장되는 것으로 알 고 있습니다.(PHP의 경우 확실하지만, 자바(서블릿)의 경우 어디에 저장하는지 아직 확인을 안해봤네요.

아마 왜 이렇게 복잡하게 세션을 사용하는건지 이해가 안되실 수 있는데요.

HTTP의 경우 클라이언트가 서버를 호출(url에 따라 특정 페이지를 호출)하고 연결이 끊어집니다.



그럼 클라이언트가 다른 페이지로 넘어갈 때 서버는 이 클라이언트가 방금 전에 들어온 클라이언트인지, 다른데서 들어온 클라이언트인지를 확인 할 방법이 없는거죠.



그래서 웹 초기에는 대부분 쿠키를 통한 인증방식을 채택했습니다.(국내 기준)

로그인(아이디/비밀번호)을 통해 인증을 하고, 해당 값을 클라이언트의 브라우저에 쿠키로 입력하고, 그 브라우저로 접속하게 되면 이 회원의 아이디/비밀번호 값을 쿠키를 통해 알 수 있으니까요.



대신 여기서 보안적인 문제가 발생합니다.

로그인을 하고 로그아웃을 하면 괜찮은데, 대부분의 사용자들이 로그아웃을 하지 않고 브라우저를 종료하게 되는데, 쿠키는 각 클라이언트의 브라우저에 저장이 되기 때문에 PC 방 등 여러사람이 이용하는 곳에선 브라우저 내의 쿠키를 확인해서 아이디와 비밀번호를 볼 수 있기 때문에 해킹 문제가 있었죠.



그래서 결국 세션을 이용해서 중요 데이터는 서버가 관리하고, 그 세션의 ID 값(기본키)을 클라이언트의 브라우저에 전달하여 인증을 구현하고 있습니다.


물론 세션을 통한 인증에도 해킹 방법은 있습니다.



Session Hijacking(세션 탈취)이라는 공격기법이 있는데 실제 사용하는 클라이언트의 브라우저의 쿠키를 확인하여 세션 ID를 탈취하고 해커는 자신의 브라우저에 세션 ID를 쿠키로 입력하게 되면, 서버는 실제 사용자인지 해커인지 분간할 수 없게되어 세션을 제공해줍니다.(로그인 되어있던 세션이라면, 해커도 로그인이 됩니다.)



이를 막는 방법은 로그인을 할 때 특정할 수 있는 정보(IP, Useragent 등)를 세션에 함께 저장하고, 페이지를 넘어갈 때마다 체크를 하는 방법이 있습니다.



자세한 방법들은 직접 검색해보시길 추천드립니다.

서버는 그냥 HttpServletRequest에서 getSession을 통해서 HttpSession을 가져다가 쓰시면 됩니다.



클라이언트에서는 브라우저가 서버의 자원을 요청 시 항상 쿠키를 헤더에 함께 포함시키기 때문에 별도의 작업은 필요 없습니다.



단 브라우저에서 "Cookie 사용안함" 시 세션이 유지되지 않습니다.




-----------------------------------------

###### 180913_2

maven 수동추가와 내부파일
-

maven의 특정 dependency가 이름이 꼭 정해져있다고 볼순없는것같다.

물론 mvn repository 웹에서 받아올떄는 이름이 정해진대로 해야겠지만

적어도 로컬에서 jar를 수동으로 넣을떄만큼은..

groupId 가 com.oracle 이든 oracle이든 아무튼
repository에 해당 디렉터리로 , 내부파일로 제대로 구성되어있으면 pom.xml에서 에러안나는것으로 보인다.

그러나 네이밍 규칙이 있는데

만약 oracle

```xml
<dependency>
        <groupId>oracle</groupId>
        <artifactId>ojdbc6</artifactId>
        <version>11.2.0.3</version>
    </dependency>
```

이런 dependency가 있을때 

메이븐 repository\oracle\ojdbc6\11.2.0.3 경로에 해당 jar파일과 기타 _remote.repositories,  lastupdate 등의 파일이 있을것이다.

해당 jar파일의 이름은 artifactId-version.jar 형식의 이름이 맞아 떨어져야 하는것으로 보인다.


jar파일을 수동으로 넣을때, jar파일만 가지고 있다면 그것을 경로를 만들어 넣는다 한들 jar 파일을 넣는것 안되는것으로 보인다.

메이븐 저장소의 정상적인 설치 폴더를 보면 jar를 포함해서 _remote.repositories , .pom 파일 등이 들어있는데 이것들이 있어야 에러가 안난다.

jar파일만 가지고 있을때는 mvn으로 해당 jar를 인스톨하는 과정을 거쳐야한다.



시작 -> 실행 -> cmd


ojdbc.jar 가 있는곳으로 이동한다

mvn install:install-file "-Dfile=ojdbc6.jar(jar파일 이름)" "-DgroupId=com.oracle.ojdbc(dependency추가시 groupId의 식별자와 같다)" "-DartifactId=ojdbc6(dependency추가시 artifactId의 식별자와 같다)" "-Dversion=6.0(ojdbc 6 이므로 )" "-Dpackaging=jar(라이브러리가 jar 이므로)"

 
최종은 아래와 같이

> mvn install:install-file "-Dfile=ojdbc6.jar" "-DgroupId=com.oracle.ojdbc" "-DartifactId=ojdbc6" "-Dversion=6.0" "-Dpackaging=jar"

자신의 Local repository 에 com\oracle\ojdbc 의 하위 폴더에 추가 된걸 볼수 있을것이다.

 
라이브러리를 사용하기 위해 porm.xml 에 추가시킨다
```xml
<dependency>
    <groupId>com.oracle.ojdbc</groupId>
    <artifactId>ojdbc6</artifactId>
    <version>6.0</version>
</dependency>
```

http://neoty.tistory.com/62 


-----------------------------------------

###### 181201_4

SP EL
-

Spring 에서는 SP EL 이라는 강력한 커스텀 태그가 있다.

그리고 자동으로 Properties 파일을 읽어서 JSP 파일이든 JAVA 파일이든

읽어 줄 수 있다. (spring 3.0 이상 버전 기준)

바로 사용 방법을 알아 보도록 하자.

1. properties 파일 작성 (config.properties)

css.path=/cms_developer_poc/css
js.path=/cms_developer_poc/js
root.path=/cms_developer_poc

2. dispacher-servlet.xml 에 properties 파일 설정

<!-- for config file load, using SpEL -->
<util:properties id="config" location="classpath:conf/spring/config.properties" />

3. JSP 파일에 사용

<script src="<spring:eval expression="@config['js.path']"/>/jquery-1.7.1.js" type="text/javascript"></script>

4. JAVA 파일 사용

 @Value("#{config['root.path']}")
 public String RootPath;

출처: http://shonm.tistory.com/376 [정윤재의 정리노트]


-----------------------------------------

###### 181201_11

log4j 커스텀 설정 에러
-

log4j:WARN Please initialize the log4j system properly. 해결방법

아래와 같이 ContextLoaderListener 보다 위에 Log4jConfigListener가 있어야한다.

```
<listener>
    <listener-class>org.springframework.web.util.Log4jConfigListener</listener-class>
  </listener>
  <listener>
    <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
  </listener>


```
출처: http://fordev.tistory.com/46 [개발자를 위하여...]

