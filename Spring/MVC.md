- [@GetMapping, @PostMapping](#getmappingpostmapping)
- [날짜(Date타입)변환처리](#datetimeformat)
- [정규표현식](#regularexpression)
- [mybatis-캐시 비사용](#8061162)

- [유효성검사](#validation)
- [Validator](#8622_111)
- [JSR 303 Validation](#8622_102)
- [validError](#validError)
- [Valid - 메시지 처리](#8716_3)

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




---

###### validError

validError
-

Neither BindingResult nor plain target object for bean name 'command' available as request attribute

지정한 객체와 연결이 안되서 나오는 에러

입력폼 만들고 path지정하고, 속성값 지정하면 객체와 비교해야 하는데 연결이 안되어서 에러남

@RequestMapping(value = "/login", method=RequestMethod.GET)
	public void loginGET(@ModelAttribute("dto") LoginDTO dto, Model model){
		model.addAttribute("dto",new LoginDTO()); //객체 정보를 넘겨줘야한다.
	}


error message: An Errors/BindingResult argument is expected to be declared immediately after the model attribute, 

 public String joinPost(BindingResult result,@ModelAttribute("uvo") @Valid UserVO user, RedirectAttributes rttr) throws Exception{
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




