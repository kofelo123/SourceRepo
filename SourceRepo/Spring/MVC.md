- [@GetMapping, @PostMapping](#getmappingpostmapping)
- [날짜(Date타입)변환처리](#datetimeformat)

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
