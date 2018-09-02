- [ch2](#180901_1)
- [ch3 스프링 mvc](#180902_2)

-----------------------------------------

###### 180901_1

ch2
-


@Primary

: 후보빈이다. 같은타입 여러 빈이 자동연결 대상으로 겹칠때 우선권을 부여한다.


@Qualifier에 이름을 줘서 후보빈을 명시가능.


@Autowired :스프링전용


자바표준

@Resource(JSR-250)  이름으로 연결

@Inject(JSR-330) : 타입으로 연결



* @PostConstruct

: 스프링에게 빈을 생성한후 이메서드를 자동실행.


[다국어 메세지 해석]

MessageSource 인터페이스의 하위 구현체

ResourceBundleMessageSource 는 가장많이 쓰이는 구현체, 로케일별로 분리된 리소스 번들 메세지 해석가능.

i18n 데이터 가져다 쓸 수 있다.





개발,테스트용 이런식으로 용도에 맞게 그룹해서  사용할 목적으로 @Profile이라는 어노테이션을 자바 설정파일에서 사용가능하다.

@Aspect 에서 @Order 어노테이션을 통해 애스펙트 우선순위를 지정할수있다. 값이 낮을수록 우선순위가높다.


특정 포인트컷이 반복되어 사용될 가능성이 있는경우, @Pointcut을 사용한 메서드를 하나만들어서 재사용가능하다


커스텀 애너테이션으로 매칭하는것 또한 가능하다

```
@Target({ElementType.METHOD, ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
public @interface LoggingRequired{
}
```

구현된 클래스나 메서드에 이 @LoggingRequired
를 붙인다.


그런후  이를 스캔하도록 @Pointcut의 annotation안에 포인트컷 표현식을 넣는다.

```java

@Pointcut("annotation(com.apress.springrecipes.calcurator.LoggingRequired)")
public void loggingOperation(){}
```


-----------------------------------------

###### 180902_2

ch3 스프링 mvc
-

#여러 URL을 하나메소드에 할당

```
@RequestMapping(value={"/member/remove", "/member/delete"}, method=RequestMethod.GET)

```

요청전용 메서드별 전용 어노테이션

@PostMapping  
@GetMapping  
@DeleteMaping  
@PutMapping  






