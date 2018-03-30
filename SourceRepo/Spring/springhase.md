- [스프링4입문](#spring4)
- [1.2.2 JSP, Servlet의 등장](#jspservlet)
- [EJB의 등장과 쇠퇴](#ejb)
- [스프링의 등장](#spring)
- [웹 애플리케이션 설계의 목적](#architecture)
- [각 계층의 역할](#1.4)
- [트랜잭션 관리](#53)
- [부품화에대해](#61)
- [기술 은닉과 부적절한 기술은닉](#67)
- [어노테이션을 이용한 DI](#82)
- [@Component](#95)
- [생명주기 관리](#99)
- [Bean 정의 파일을 이용한 DI](#100)
- [프로퍼티 파일 이용](#106)
- [JavaConfig를 이용한 DI](#108)

## spring4
### 스프링입문4


'애초에 스프링은 사용법을 배운다기 보다 어떻게 사용할 것인가(설계)가 매우 중요한

측면이다.

뭔지 잘 모르겠지만 예제들을 복사해서 붙여넣었더니 어떻게든 뭔가 만들 수 있었다 라

고 한다면 그것이야말로 문제라고 생각한다.

어려운 내용이 있더라도 포기하지 않고 읽어나가면서 그 부분을 기억해두길 바란다.

다른 기술 서적을 접하거나 혹은 경험을 쌓아간다면 언젠가 전체 퍼즐 조각이 딱 맞아

떨어지는것 처럼 그 내용을 이해할 수 있을 것이다.'


스프링은 구현을 위한 프레임워크가 아니라 설계를 위한 프레임워크라고해도 과언이 아니다.

## jspServlet
### 1.2.2 JSP, Servlet의 등장

웹 기술은 처음에 정적 컨텐츠(HTML)를 표시하는 기술이었다.

그를 보완하기 위해 CGI가 나왔다. CGI는 HTTP 요청으로 CGI가 해당 프로그램을

실행함으로써 같은 요청의 처리결과로 다른 콘텐츠를 반환할 수 있게되었다(동적)

하지만 문제점이 있었는데, 처리를 요청할 때마다 프로그램이 실행되고, 세션의 관리

가 없어 성능 저하나 트랜잭션 관리의 어려움을 초래했다.

그래서 JSP, Servlet이 등장했다.

멀티스레드로 실행되고 실행기반인 웹 컨테니어는 개발자가 세션관리를 의식하지 않아

도 되도록 관리해준다.

또한 JSP로 페이지를 생성하고 비즈니스 로직은 Servlet 으로 처리하는 아키텍쳐도 큰장

점이었다.

JSP 와 Servlet으로 디자인과 프로그램을 분리할 수 있다는것도 한 몫했다.


## ejb
### EJB의 등장과 쇠퇴

EJB는 분산 환경을 위한 컴포넌트로 등장했을 뿐, JSP,Servlet과 같은 웹 애플리케이션

을 위한 기술은 아니었다.

과거 JSP,Servlet으로 프레젠테이션을 구현하고 비즈니스로직은 EJB로 구현하는 것이

웹 애플리케이션에서의 권장 설계였다.

그 후 개발자들에게서 불만이 높아졌는데, 애초에 EJB는 분산처리를 위한 기술이었기에

원격 액세스만 지원했고 , 웹 애플리케이션은 분산처리를 거의 사용하지 않으며

로컬 액세스가 필요했다. 또한 EJB는 테스트하기도 어려웠고 사양이 복잡했다.

## spring
### 1.2.4 스프링의 등장

1990년대 말, 자바의 엔터프라이즈 에디션인 J2EE는 JSP,Servlet,EJB의 기능도

복잡해지고 시간이 지날수록 무거워지고 복잡해졌다.

그래서 로드존슨 중량급인 J2EE 컨테이너를 대신할 경량 컨테이너로서 DI와 AOP의

기능을 가진 DIxAOP 컨테이너인 스프링을 고안해낸 것이다.

이는 POJO 라고 불리는 컨테이너와 프레임워크 등에 의존하지 않는 일반 오브젝트의

생명주기 관리나 오브젝트 간의 의존 관계를 해결하는 아키텍쳐를 구현한 컨테이너를

말한다.

POJO는 DI 컨테이너에 의존하지 않는다는 특징 떄문에 단위테스트를 쉽게 수행한다.


---


## architecture

웹 애플리케이션 설계의 목적
-

- 개발효율
	- 의도를 파악하기 쉽고 이해하기 쉬운 구조
	- 테스트하기 쉬운 구조
- 유연성
	- 변경하기 쉽고 기능을 추가하기 쉬운 구조
	- 미래의 환경 변화에 대응할 수 있는 견고한 구조

사용자의 요구는 쉽게 변화한다. 개발중에도, 릴리즈 후에도 발생한다.

변경이나 기능 추가에 유연하지 않은 애플리케이션 아키텍처를 채용한

웹 애플리케이션은 변경 요구에 대응하지 못해 유지하지 어려워 진다.


일반적인 레이어는 3개층으로 나눈다.

프레젠테이션 : UI,컨트롤러,Action

비즈니스 : Service , 도메인(VO)

데이터 액세스 : Dao

오목형 레이어
-

 ![](https://drive.google.com/uc?export=view&id=1QJ3wQQw0cj8M1rh2waLHnBVJsZQbwKwc)

(UML 방식으로 그린것이며, 동그라미는 인터페이스 ,사각형은 클래스)

오목형 레이어는 비즈니스로직에 영향을 미치지 않는 설계를 의미한다.

안정 의존원칙, 의존관계 역전원칙등에 뒤받침된 건실한 구조이다.

 ![](https://drive.google.com/uc?export=view&id=1tqX3O9l1M4TAy1BWBgZd1aj5CNb9vplh)

위그림과 같이 어떤 기술이 변경되었을때 비즈니스 층에 영향을 미치지않는 구조이다.

 ![](https://drive.google.com/uc?export=view&id=1MWYtGO-RIyKSlx0mcLurOSaoLUaKllc9)

---


## 1.4

각 계층의 역할
-

프레젠테이션 층의 역할
-
프레젠테이션 층의 주된 역할은 사용자 인터페이스와 컨트롤러 제공이다.

사용자 인터페이스는 사용자가 직접 조작하는 화면 등을 말한다.

컨트롤러는 사용자 인터페이스를 통해 사용자의 입력을 받아

적절한 비즈니스 로직을 호출하고 그 결과를 사용자 인터페이스로 반환하는

작업을 한다.

그리고 웹 애플리케이션의 상태(세션)를 저자해 이용하는 데이터를 관리.


비즈니스 로직 층의 역할
-

유스케이스로 표현되는 특정 업무나 특정 부서 처리의 서비스 및 도메인으로 구성



---

###### 53

트랜잭션 관리
-

트랜잭션이란 처리단위이다.
트랜잭션에 지켜야할 ACID라는 특성이 있다.

이중 아키텍처가 신경써야 하는것은 원자성(Atomicity),독립성(Isolation)이다.

 ![](https://drive.google.com/uc?export=view&id=15qR4lxNvND2kPSCD0BWJNyh9tIW6Ker_)




 ![](https://drive.google.com/uc?export=view&id=)


---


###### 61

부품화에대해
-

결국 티어및 레이어 패키지 클래스 이런것은 부품화이다.

컴퓨터를 예를들면, 티어나 레이어 같은 큰부품은 컴퓨터,모니터,스피커에 해당하고

패키지나 컴포넌트는 컴퓨터안의 메인보드,cpu,메모리가 된다
클래스는 메인보드와 cpu, 메모리 등에 들어가는더 작은 부품이 된다.

이런 부품화가 효율성이나 유연성을 어떻게 높일까?

전자 제품처럼 부품별로 다른 업체에 테스트를 포함한 제조를 맡길수 있고, 유연성 면에서는 마우스나 뫼니터 처럼 쉽게 교체할수 있기 때문이다.

뭔가 고장났을때 그 부품만 수리하면 정상 작동한다.

객체지향 자체가 부품화를 촉진하는 기술로 여겨지고 있다.

여기서 중요한점은 부품화는 인터페이스가 중요하다는것이다.

전자제품으로 치면 부품은 반드시 콘센트나 모듈러 잭과 같은

인터페이스로 연결된다.

USB 포트에 여러 마우스를 꽂는걸 생각해볼수 있다.

부품화의 두 가지중요점

1. 2개의 부품에서 어느쪽이 인터페이스를 가져가야하는지에 대한 결정.
전자제품으로써는 인터페이스는 꽂을 구멍이 있는쪽, 자바로 말하면 인터페이스의 정의가 있는쪽이 어느 부품인가로 이야기 할 수있다.

전자제품으로 보면 더 중요한 부품이 인터페이스를 가진다.

컴퓨터의 경우도 컴퓨터에 있고 tv와 스피커의 경우에는 그 본체에 구멍이 있다. 그리고 공급받기위한 콘센트가 필요하다.


---


###### 67

기술 은닉과 부적절한 기술은닉
-

부적절한 기술 은닉으로 기술 이용을 어렵게 하는 문제도 흔히 일어난다.

트랜잭션,예외,로깅과 같은 처리를 여러클래스에 걸쳐 존재하면 가독성도 떨어뜨리고, 유닛테스트 역시 어렵게 만든다.
부품화를 촉진하는데 방해가 된다.

그동안의 문제를 정리하면
- 오브젝트 생명주기문제
- 부품화문제
- 기술은닉과 부적절한 기술 은닉 문제

이러한 문제는 리소스이용, 테스트가 어렵고 확장 변이가 어려워진다.

이를위해 스프링이 해결해줄수 있다.

- 오브젝트 생명 주기 문제는 DI 컨테이너로 해결
- 부품화 문제는 DI 컨테이너로 해결
- 기술 은닉과 부적절한 기술 은닉 문제는 AOP로 해결





---


###### 82

어노테이션을 이용한 DI
-

@Autowired는 인젝션(의존관계)를 받기 위한 설정이다.
인스턴스 외에도 메서드 선언 앞으로 도 붙일 수 있다.


기본적으로 인젝션이 필수지만, 필수가 아니게 할 수있다.
@Autowired(required = false)

@Autowired로 인젝션할 클래스형이 2개 존재한다면?
에러가 발생한다. 인젝션할 클래스형은 반드시 하나도 해야한다.



 ![](https://drive.google.com/uc?export=view&id=1esszuoYitLea8tbaZfC1w-dRDdhsQXKp)


인터페이스의 구현 클래스를 다른 클래스로 바꿀경우 불편해서 이를 회피하는 세가지 방법이 있다.

1. Bean을 설정하는 @Primary를 @Bean이나 @Component에 부여하는 방법.
(Bean의 정의파일에서는 <bean primary="ture"> 사용)
```java
@Component
@Primary
public class ...
```

2.@Autowired와 병행해서 @Qualifier를 하는 방법
이 경우는 @Component에도 이름을 지정해야 하는데, 지정하지 않은 경우는 클래스의 선두를 소문자로 한것이 디폴트로 부여된다.
```java
@Autowired
@Qualifier("productDao")
private Dao productDao;
```
```java
@Component("productDao")
public class ...
```

3. Bean의 정의파일에서 context:component-scan을 이용한다.
즉 component-scan을 부분으로 나누어서 기술하는 방법이다.
그리고 필요하지않을때 주석처리하거나 한다.




---


###### 95

@Component
-

Component를 확장해서 좀더 역할에 맞게 설정하는것이
@Controller
@Service
@Repository

@Configuration은 Bean 정의 를 자바 프로그램에서 실행하는 JavaConfig용 어노테이션이다.

* @Scope

| value 속성  | 설명                                                    |
|-------------|---------------------------------------------------------|
| singleton   | 인스턴스를 싱글턴으로 함(default)                       |
| prototype   | 이용할떄마다 인스턴스화                                 |
| request     | Servlet API의 request 스코프인 동안만 인스턴스 생존     |
| session     | Servlet API의 session 스코프인 동안만 인스턴스 생존     |
| application | Servlet API의 application 스코프인 동안만 인스턴스 생존 |



---


###### 99

생명주기 관리
-

스프링 DI 컨테이너는 인스턴스 생성과 소멸타이밍에 호출되는 메소드 설정을 위한 2개의 어노테이션이 있다.

|어노테이션   |설명   |예제
|---|---|---|
|  @PostConstruct |초기 처리를 하는 메서드선언.   |@PostConstruct  public void start(){..}
|@PreDestroy   |종료 처리를 하는 메서드   |@PreDestroy   public void stop(){...}

@PostConstruct는 DI컨테이너에 의해 인스턴스 변수에 뭔가가 인젝션 된후에 호출된다. 인젝션된 값으로 초기처리를 할때 사용.

자바에는 소멸자가 없으므로 종료 처리를 하려면 @PreDestroy를 사용해야.


---


###### 100

Bean 정의 파일을 이용한 DI
-

대규모 개발일수록 어노테이션보다 Bean 정의 파일을 이용해서 DI를 관리하는경우가 많다.(어노테이션 관리가 힘들어서,부주의 예방)


* BeanFactory

DI 컨테이너의 핵심은 BeanFactory이다.
실행시 컨네지는 Bean 정의 파일을 파탕으로 인스턴스를 생성하고 인스턴스의 인젝션을 처리한다.

웹 개발에서 개발자가 직접 이용 하는 일은 별로없지만, DI 컨테이너로 부터 인스턴스를 얻는다는 말은 구체적으로 BeanFactory로 부터 인스턴스를 얻는다는것이다.

ApplicationContext는 BeanFactory를 확장한것이다.

기존의 어노테이션방식에서 Bean 정의 파일로 바꿔쓰려면
기존의 클래스의 어노테이션이 사라지고, 인젝션을 위한 Setter 메서드가 필요해진다.
```xml
<bean id="productService" class= "....productServiceImpl"
	autowire="byType"/>
<bean id="productDao" class="....ProductDaoImpl" />

```
```java

pricate ProductDao productDao;

public void setProductDao(ProductDao productDao){
this.productDao = productDao;
```

 ![](https://drive.google.com/uc?export=view&id=1esszuoYitLea8tbaZfC1w-dRDdhsQXKp)

 ![](https://drive.google.com/uc?export=view&id=18-Ut86COVrWPcrwPUcsODtMvrENQ0sl1)


* Bean 정의파일 분할

기능별로 분할하고 다른 Bean 정의파일을 참조할수있다.

```xml
<beans>
	<import resource="config/services.xml" />
	<import resource="resources/dataaccess.xml"/>

	<bean id="service" class="..."/>
	<bean id="dao" class="..."/>
</beans>
```

---


###### 106

프로퍼티 파일 이용
-

Bean 정의 파일에서 프로퍼티 파일을 이용할 수있다.

 ![](https://drive.google.com/uc?export=view&id=11omnRkZBHruBmndkjNkqmszgfhRzav0M)

 ![](https://drive.google.com/uc?export=view&id=1q3YPqKKh2QXQ-X8asapJkiI0ROAu9V3N)

 ![](https://drive.google.com/uc?export=view&id=150P243ZGYctt03plXcj4cB3iysS7UxRV)

 ![](https://drive.google.com/uc?export=view&id=1ctqj04zW8KkKoEyLWBJS9lPP4_IH5iyl)

```
// message.properties

message = "Hello Spring"

```

---


###### 108

JavaConfig를 이용한 DI
-

스프링 3.1부터 xml이 아닌 Java 프로그램으로 정의를 기술할수있다.

호불호가 갈린다. 하지만 순수 자바라서 앞으로 사용하는 경향이 커질 가능성이 있다.

장단점이 있는데 혼자서 개발하는 경우 JavaConfig를, 리더가 이썽서 관리할 수있는 소규모 개발이면 어노테이션을, 대규모 개발이면 Bean 정의파일을 중심으로한 일부 어노테이션을 이용하는것이 권장된다.

장점으로 타입 세이프(잘못작성되었을때 컴파일 에러로 검증)가 있는데,
XML의 Bean정의도 실은 툴의 진화에 따라서 지적해주기도 한다.

 ![](https://drive.google.com/uc?export=view&id=1FqBYpI7GF-HQW-iryriqg7OKzWAod42z)

기타, @ComponentScan, @Import 등이있다.

>'이후 javaconfig 관련 autowired 사용에 관한 설명등이 있는데, 일단 JavaConfig 사용할일이 없을것 같으므로 Pass
