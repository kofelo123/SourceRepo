- [@GetMapping, @PostMapping](#getmappingpostmapping)
- [날짜(Date타입)변환처리](#datetimeformat)
- [정규표현식](#regularexpression)
- [mybatis-캐시 비사용](#8061162)

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
