- [pom.xml수정시 발생에러](#pomxmlerror)
- [로깅](# logging)

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
