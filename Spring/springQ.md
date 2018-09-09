- [AOP기본 , xml 기반 AOP](#180822_1)
- [어노테이션 기반 AOP](#180822_2)
- [PropertyPlaceholderConfigurer - 외부프로퍼티 참조](#180905_2)
- [정리](#180905_1)
- [예외처리](#180907_3)
- [다국어 처리](#180907_4)
- [LocaleResolver 등록](#180907_5)
- [JPA](#180907_10)
- [데이터 변환](#180907_8)

-----------------------------------------

###### 180822_1

AOP기본 , xml 기반 AOP
-

aop는 응집도

관심사의 분리 - 핵심관심사 집중

조인포인트 : 모든 비즈니스 메소드  포인트컷 후보


포인트컷: 필터링된 조인포인트 즉 횡단 관심사를 적용할 메소드.(공통관심사가 필요한 메소드)
```
<aop:config>
	<aop:pointcut id="allPointcut" expression="execution(* com.springbook.biz..*Impl.*(..))" />
```


어드바이스 : 횡단 관심에 해당하는 공통 기능의 코드

독립된 클래스의 메소드로 작성

5가지 시점이 있다.

before , after , after-returning, after- throwing, around



<위빙>

어드바이스에 해당하는 횡단 관심 메소드가 삽입되는 과정을 의미.

위빙을 처리하는 방식은 컴파일타임 위빙, 로딩타임 위빙, 런타임 위빙이 있고 스프링에서는
런타임 위빙방식만 지원


<Aspect or Advisor>

애스팩트는 포인트컷과 어드바이스의 결합으로 어떤 포인트컷 메소드에 대해서 어떤 어드바이스 메소드를 실행할지 결정한다.

<aop:aspect> 엘리먼트를 사용하는데 가끔 <aop:advisor> 엘리먼트 사용하는 경우가 있다 -> 대표적 상황이 트랜잭션



```
public class LogAdvice{
    public void printLog(){
        System.out.println("[공통 로그] 비즈니스 로직 수행 전 동작");
    }
}

<bean id="log" class="com.springbook.biz.common.LogAdvice"></bean>

<aop:config>
    <aop:pointcut id="allPointcut" expression="execution(* com.springbook.biz..*Impl.*(..))"/>
    <aop:pointcut id="getPointcut" expression="execution(* com.springbook.biz..*Impl.get*(..)"/>

    <aop:aspect ref="log">
        <aop:before pointcut-ref="getPointcut" method="printLog"/>
    </aop:aspect>

</aop:config>

```
위에서 포인트컷을 aspect안에 넣어서 사용하는방법도있다.


<aop 엘리먼트>

<aop:config>는 루트 엘리먼트이다.



<aop:aspect> 대신 <aop:advisor>엘리먼트가 사용하는 경우는 

어드바이스 객체의 아이디를 모르거나 메소드 이름을 확인할 수 없을때는 aspect를 사용할 수없게 되는데 그때 사용.


<tx:advice id="txAdvice" transaction-manager="txManager">
	<tx:attributes>
		<tx:method name="get*" read-only="true"/>
		<tx:method name="*" />
	</tx:attributes>
</tx:advice>

<aop:config>
	<aop:pointcut id="allPointcut" expression="execution(* com.springbook.biz..*Impl.*(..))"/>

	<aop:advisor pointcut-ref="allPointcut" advice-ref="txAdvice"/>
</aop:config>

![](https://drive.google.com/uc?export=view&id=1jnCMMoAlFHCZtTIlMX9xZE3gDQNpomDX)

![](https://drive.google.com/uc?export=view&id=17r2D-GxxTdiDqPFLWLkZ2Om-APfsB6YZ)

![](https://drive.google.com/uc?export=view&id=1b46eoMNgsAPp5HpPqF2btgWSoVF4gOGs)

![](https://drive.google.com/uc?export=view&id=1jY47c9LL0YnqJ4qgBHsbuRzLDUOJnt0L)

(아래는 최범균책 참고)
 ![](https://drive.google.com/uc?export=view&id=1w5vX3ojSV7sp4hkaocLvXiVxVs2hnQVf)
 

어드바이스 - Around

Around 어드바이스는 다른 어드바이스와 형태가 좀 다르다.

사전처리,사후처리 로직을 수행한다.

```java
public class AroundAdvice{
	public Object aroundLog(ProceedingJoinPoint pjp)throws Throwable{

		System.out.println("[BEFORE]: 비즈니스 메소드 후행 전에 처리할 내용...");
		Object returnObj = pjp.proceed();
		System.out.println("[AFTER]: 비즈니스 메소드 수행 후에 처리할 내용...");
		return returnObj;
	}
```
```xml
<bean id="around" class="com.springbook.biz.common.AroundAdvice" />

<aop:config>
	<aop:pointcut id="allPointcut" expression="execution(* com.springbook.biz..*Impl.*(..))" />
	
	<aop:aspect ref="around">
		<aop:around pointcut-ref="allPointcut" method="aroundLog" />
	</aop:aspect>	
</aop:config>
```

<JoinPoint와 바인드 변수>

어드바이스에서 좀더 의미있게 구현하기 위해 클라이언트가 호출한 비즈니스 정보가 필요

- Signature getSignature() : 클라이언트가 호출한 메소드의 시그니처(리턴타입,이름,변수)정보가 저장된 Signature 객체 리턴

- Object getTarget() : 클라이언트가 호출한 비즈니스 메소드를 포함하는 비즈니스 객체 리턴

- Object[] getArgs() : 클라이언트가 메소드를 호출할 때 넘겨준 인자 목록을 Object 배열로 리턴


Around 어드바이스 구현시 사용되는 ProceedingJoinPoint 인터페이스는 JoinPoint를 상속했다. 즉 JoinPoint가 가진 모든메소드를지원 + proceed()메소드를 추가
-> Around에서는 proceed()메소드가 따로 필요해서, 나머지는 그냥 JoinPoint를 사용해야함


Signature객체가 제공하는 메소드

- String getName()  : 호출된 메소드 이름 리턴

- String toLongString() : 호출된 메소드의 리턴타입,이름,매개변수 패키지경로까지 포함하여 리턴

- String toShortString() : 호출한 메소드 시그니처를 축약한 문자열로 리턴



JoinPoint 객체 사용위해 단지 어드바이스 메소드의 매개변수로  선언만 하면 됨.

```
public class BeforeAdvice{
	public void printLog(JoinPoint jp){
		
		String method = jp.getSignature().getName();
		Object[] args= jp.getArgs();

		System.out.println("[사전처리] " + method + " () 메소드 ARGS 정보 : " + args[0].toString());
	}
}
```

```java
public class AfterReturningAdvice{

    public void afterLog(JoinPoint jp ,Object returnObj){
        String method = jp.getSignature().getName();

        if(returnObj instanceof UserVO){
            UserVO user = (UserVO) returnObj;
            if(user.getRole().equals("Admin")){
                System.out.println(user.getName() + " 로그인 (Admin)");
            }
        }

        System.out.println("[사후 처리]" + method + " () 메소드 리턴값 : " + returnObj.toString());;
    
    }

}
```

위에서 두번째 매개변수를 바인드변수라고 한다. 비즈니스 메소드가 리턴한 결괏값을 바인딩할 목적으로 사용. 

AfterReturning 어드바이스 메소드에 JoinPoint만 선언되면 스프링 설정 파일 수정하지않아도되지만,
바인드 변수가 추가되면 반드시 바인드 변수에대한 매핑설정이 추가되어야 한다.

```
public class LogAdvice{
    public void printLog(){
        System.out.println("[공통 로그] 비즈니스 로직 수행 전 동작");
    }
}

<bean id="afterReturning" class="com.springbook.biz.common.AfterReturningAdvice"></bean>

<aop:config>
    
    <aop:pointcut id="getPointcut" expression="execution(* com.springbook.biz..*Impl.get*(..)"/>

    <aop:aspect ref="afterReturning">
        <aop:after-returning pointcut-ref="getPointcut" method="afterLog" returning="returnObj"/>
    </aop:aspect>

</aop:config>

```

여기서returning 속성은 <aop:after-returning> 엘리먼트에서만 사용가능한 속성.

//

After Thorwing 어드바이스의 경우 매개변수로 Exception을 받는다.

이떄는 <aop:after-throwing> 엘리먼트의 throwing속성을 사용

아래와 같이 예외객체의 종류에 따라 다양하게 예외처리 할 수있다.

```java
public class AfterThrowinnAdvice{

    public void ExceptionLog(JoinPoint jp ,Exception exceptObj){
        String method = jp.getSignature().getName();
        System.out.println(method + " () 메소드 수행 중 예외 발생!");
        
        if(exceptionObj instanceof IllegalArgumentException){
            System.out.println("부적합한 값이 입력되었습니다.");
        }else if(exceptionObj instanceof NumberofFormatException){
            System.out.println("숫자 형식의 값이 아닙니다.");
        }else if(exceptionObj instanceof Exception){
            System.out.println("문제가 발생했습니다.");
        }
    }

}
```
<around 어드바이스>
```java
public class AroundAdvice{

    public Object aroundLog(ProceedingJoinPoint pjp ) throws Throwable{
        String method = jp.getSignature().getName();

        StopWatch stopWatch = new StopWatch();
        stopWatch.start();

        Object obj = pjp.proceed();

        stopWatch.stop();

        System.out.println(method + "() 메소드 수행에 걸린 시간 : " + stopWatch.getTotalTimeMillis() + " (ms)초");
        return obj;

    }

}
```


-----------------------------------------

###### 180822_2

어노테이션 기반 AOP
-

AOP를 어노테이션으로 설정하러면 스프링 설정파일에 <aop:aspectj-autoproxy> 엘리먼트를 선언해야한다.

```xml
...
<aop:aspectj-autoproxy></aop:aspectj-autoproxy>
...
```

AOP관련 어노테이션들은 어드바이스 클래스에 설정한다.

어노테이션들을 스프링 컨테이너가 처리하게 하려면, 반드시 어드바이스객체가 생성되어 있어야함.

그러므로 어드바이스 클래스는 반드시 스프링 설정파일에 <bean>등록하거나 @Service 어노테이션을 사용하여 컴포넌트가 검색될 수있도록 해야한다.


Annotation 설정 :
```
@Service
public class LogAdvice{}
```

XML 설정:
```
<bean id="log" class="com.springbook.biz.common.LogAdvice"></bean>
```

이책에서 @Service를 사용.

<포인트컷 설정>

어노테이션에서 @Pointcut사용, 하나의 어드바이스 클래스안에 여러개 선언가능.
따라서 이들을 각기 식별할 때 참조메소드를 이용.

참조메소드는 몸체가 비어있는 메소드 -> 단순 이름으로 식별하기 위한 용도

```java
@Service
public class LogAdvice{
    @Pointcut("execution(* com.springbook.biz..*Impl.*(..))")   
    public void allPointcut(){}

    @Pointcut("execution(* com.springbook.biz..*Impl.get*(..))")   
    public void getPointcut(){}

}
```

<어드바이스 설정>

어드바이스 클래스에서 횡단 관심에 해당하는 어드바이스메소드가 구현되어 있다.

언제 동작할지 결정과 관련된 어노테이션을 메소드위에 설정하면 된다.

어드바이스 동작 시점은XML 과 마찬가지로 5가지

반드시 어드바이스 메소드가 결합될 포인트컷을 참조해야한다.

그 방법은 어드바이스 어노테이션 뒤에 괄호를 추가하고 포인트컷 참조 메소드를 지정하면 된다.

```java
@Service
public class LogAdvice{
    @Pointcut("execution(* com.springbook.biz..*Impl.*(..))")   
    public void allPointcut(){}

    @Before("allPointcut()")
    public void printLog(){
        System.out.println("[공통 로그] 비즈니스 로직 수행 전 동작");
    }
}

```

<Aspect 설정>

@Aspect 어노테이션을 사용한다.

애스팩트는 포인트컷과 어드바이스의 결합이므로 이를 결합하는 설정이 있어야 한다.

```java
@Service
@Aspect // Aspect = Pointcut + Advice
public class LogAdvice{
    @Pointcut("execution(* com.springbook.biz..*Impl.*(..))")   //포인트컷
    public void allPointcut(){}

    @Before("allPointcut()") // 어드바이스
    public void printLog(){
        System.out.println("[공통 로그] 비즈니스 로직 수행 전 동작");
    }
}
```

AfterReturning의 returning을 지정하고 싶은 경우

@AfterReturning(pointcut="getPointcut()",returning="returning="returnObj")

이런식으로 처리한다.


<외부 Pointcut 참조하기>

xml 설정에서는 포인트컷을 여러개 등록했다.

aspect 설정시 pointcut-ref 속성으로 특정 포인트컷을 참조할 수 있었기 때문에
포인트컷을 재사용 할 수 있었다.

아래와같이..
```
public class LogAdvice{
    public void printLog(){
        System.out.println("[공통 로그] 비즈니스 로직 수행 전 동작");
    }
}

<bean id="log" class="com.springbook.biz.common.LogAdvice"></bean>

<aop:config>
    <aop:pointcut id="allPointcut" expression="execution(* com.springbook.biz..*Impl.*(..))"/>
    <aop:pointcut id="getPointcut" expression="execution(* com.springbook.biz..*Impl.get*(..)"/>

    <aop:aspect ref="log">
        <aop:before pointcut-ref="getPointcut" method="printLog"/>
    </aop:aspect>

</aop:config>

```

하지만 어노테이션 설정으로 변경후부터는 클래스마다 포인트컷 설정이 포함되면서 , 비슷하거나 같은 포인트컷이 반복 선언되는 문제가 발생된다.

스프링은 이런 문제를 해결하고자 포인트컷을 외부에 독립된 클래스에 따로 설정되도록 한다.

다음처럼 시스템에서 사용할 모든 포인트컷을 PointcutCommon 클래스에 등록한다

```java
@Aspect
public class PointcutCommon {
    @Pointcut("execution(* com.springbook.biz..*Impl.*(..))")
    public void allPointcut(){}

    @Pointcut("execution(* com.springbook.biz..*Impl.get*(..))")
    public void getPointcut(){}

}


@Service
@Aspect
public class BeforeAdvice{

    @Before("PointcutCommon.allPointcut()")
    public void beforeLog(JoinPoint jp){
        String method = jp.getSignature().getName();
        Object[] args = jp.getArgs();

        System.out.println("[사전 처리] " + method + "() 메소드 ARGS 정보 : " + args[0].toString());

    }
}
```




-----------------------------------------

###### 180905_2

PropertyPlaceholderConfigurer - 외부프로퍼티 참조
-

// .properties
```
jdbc.driver=org.h2.Driver
```

// .xml
```
<context:property-placeholder location="classpath:config/~.properties" />

<bean id~

	<property name=~ value="${jdbc.driver}"/>
</bean>
```


-----------------------------------------

###### 180905_1



정리
-

#팩토리 패턴

->타입내에서 어떤 객체를 반환하는 클래스


스프링 컨테이너 종류
-

BeanFactory와  이를 상속한 ApplicationContext 두가지 유형의 컨테이너가 있다.

#BeanFactory


빈객체를 생성하고 관리하는 가장 기본적인 컨테이너 기능만 제공	
클라이언트의 요청(Lookup)에 의해서만 <bean>객체가 생성되는 지연로딩(Lazy Loading)방식 사용.

-> 일반적인 스프링에서 쓸일이 거의 없다

#ApplicationContext


빈팩토리의 빈 객체관리 기능 외에도 트랜잭션 관리나 메시지 기반의 다국어 처리등 다양한 기능을 지원.

컨테이너가 구동되는 시점에 <bean>이 등록된 클래스들을 객체 생성하는 즉시로딩(pre-loading)방식으로 동작.

대부분 프로젝트에서 ApplicationContext를 사용


##ApplicationContext의 구현클래스

- GenericXmlApplicationContext	
:파일 시스템이나 클래스 경로에 있는 xml 파일을 로딩하여 구동하는 컨테이너

- XmlWebApplicationContext	
:웹 기반의 스프링 애플리케이션을 개발할때 사용하는 컨테이너


#xml에서의 import

```xml
<beans>
	<import resource="context-datasource.xml"/>

</beans>
```

(bean속성중)	
*init-method	
*destroy-method	
*lazy-init		
*scope	


스프링 컨테이너의 의존성관리
-

두가지 형태로 IoC를 지원한다.

- Dependency Lookup	
:클라이언트가 자신이 필요한 객체를 컨테이너가 생성한 객체들중 검색해서 사용한다.

- Dependency Injection	
:스프링 설정파일에 등록된 정보를 바탕으로 컨테이너가 자동으로 처리 -> 프로그램 코드 변경없이 스프링 설정 파일수정만으로 변경사항 적용가능


#xml빈에서 매개변수있는 빈생성

아래같은 생성자가 있으면
```
public SamsungTV(SonySpeaker speaker, int price){
}
```

```xml
<bean id="tv" class="polymorphism.SamsungTV">
	<constructor-arg ref="sony"></constructor-arg>
	<constructor-arg value="2700000"></constructor-arg>
</bean>

<bean id="sony" class="polymorphism.SonySpeaker"></bean>
```

#생성자가 여러개 오버로딩 되어있을때

index 속성을 사용하여 몇번째 어떤값이 몇번째 매개변수로 매핑되는지 지정가능 index는 0부터

```xml
<bean id="tv" class="polymorphism.SamsungTV">
	<constructor-arg index="0" ref="sony"></constructor-arg>
	<constructor-arg value="2700000"></constructor-arg>
</bean>
```


#p네임스페이스 라는게 있는데

해놓으면 좀더 설정을 간단히 힌다

```
xmls:p="http://www.springframework.org/schema/p"
```

```
p:변수명-ref="참조할 객체의이름이나 아이디"

p:변수명="설정할값"
```

```xml
<bean id="tv" class="polymorphism.SamsungTV" p:speaker-ref="sony" p:price="2700000" />

<bean id="sony" class="polymorphism.SonySpeaker"/>
<bean id="apple' class="polymorphism.AppleSpeaker"></bean>
```

xml빈에서 컬렉션사용
-

##List
```xml
<bean id =~ class=~
	<property name="~list">
		<list>
			<value>값1</value>
			<value>값2</value>
		</list>
	</property>
```

##Set

```xml
<bean id =~ class=~
	<property name="addresssList">
		<set value-type="java.lang.String">
			<list>
				<value>값1</value>
				<value>값2</value>
			</list>
		</set>
	</property>
```
(set은 중복값허용x -> 같은값이면 하나만저장)


##Map
```xml
<bean id =~ class=~
	<property name="addresssList">
		<map>
			<entry>
				<key>키1</key>
				<value>값1</value>
			</entry>
			<entry>
				<key>키2</key>
				<value>값2</value>
			</entry>
		<map>
	</property>
```

##properties 
```xml
<bean id =~ class=~
	<property name="addresssList">
		<props>
			<prop key="고길동">서울시 강남구 역삼동</prop>
			<prop key="마이콜">서울시 강서구 화곡동</prop>
		</props>
	</property>
```

#Presentation Layer(MVC)와 Business Layer(IoC,AOP) 의 경계

##Presentation Layer

xml(로딩) -> DispatcherServlet -> Controller / view 여기까지가 PresentationLayer

## Business Layer

Service 인터페이스, ServiceImpl, DAO , VO 에해당


트랜잭션
-

스프링의 트랜잭션은 XML 기반의 AOP설정만 사용가능.(어노테이션은 안됨)

<aop:aspect> 사용불가, <aop:advisor> 사용.

이는 트랜잭션 관리에 사용되는 어드바이스가 독특해기때문.

(tx namespace 적용해야한다)

```xml
//root-context.xml

xmlns:tx="http://www.springframework.org/schema/tx"

(아래는 xml:schemaLocation)
http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx-4.3.xsd"

//

	<bean id="transactionManager"
		class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
		<property name="dataSource" ref="dataSource"></property>
	</bean>
```

모든 트랜잭션 관리자는 PlatformTransactionManager 인터페이스를 구현한 클래스

commit() , rallback() 메소드를 가짐


트랜잭션 관리 기능의 어드바이스는 <tx:advice> 엘리먼트 사용


```xml
<bean id="transactionManager"
		class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
		<property name="dataSource" ref="dataSource"></property>
	</bean>

<tx:advice id="txAdvice" transaction-manager="transactionManager">

	<tx:attributes>
		<tx:method name="get*" read-only="true" />
		<tx:method name="*" />
	</tx:attributes>

</tx:advice>
```

#< tx:method>관련 속성

name , read-only , no-rollback-for , rollback - for


트랜잭션에 애스팩트를 사용하지 못하는 이유는 어드바이스 객체의 아이디와 메소드이름을 모르기때문.

어드바이저를 사용한다.

```xml
<tx:advice id="txAdvice" transaction-manager="transactionManager">

	<tx:attributes>
		<tx:method name="get*" read-only="true" />
		<tx:method name="*" />
	</tx:attributes>

</tx:advice>

<aop:config>
	<aop:pointcut id="txPointcut" expression="execution(* com.multicampus.biz..*(..))"/>

	<aop:advisor pointcut-ref="txPoint" advice-ref="txAdvice"/>
</aop:config>
```


//세션 종료
session.invalidate();

#HandlerMapping

모든 Controller 객체를 저장하고 있다가, 클라이언트 요청이 들어오면 처리할 특정 Controller를 검색하는 기능을 제공

DispatcherServlet이 사용하는 객체이다.

따라서 DispatcherServlet이 생성되고 단 한번 생성된다.


web.xml에 지정된 path(.do , /* 등) 가 서버에 전달되면 서블릿 컨테이너는 web.xml에 등록된 Dispatcher 클래스의 객체를 생성한다.

클라이언트 -> DispatcherServlet 객체생성 되면 그안에 재정의된 init()메소드 자동실행(>init()메소드가 스프링 설정파일을(~.xml) 로딩하여) -> XmlWebApplicationContext라는 스프링 컨테이너가 구동.(ApplicationContext를 구현한클래스)
,(DispatcherServlet이 생성) -> HandlerMapping, Controller,ViewResolver 클래스를 <bean> 등록하면 스프링 컨테이너가 해당 객체들을 생성해준다.



DispatcherServlet이 SpringMVC의 유일한 서블릿클래스이다.

따라서 서블릿 컨테이너는 web.xml 파일에 등록한 DispatcherServlet만 생성해준다

DispatcherServlet은 클라이언트 요청 처리에 필요한 handlerMapping, Controller, ViewResolver 객체들을 생성하기 위해 스프링 컨테이너를 구동한다.

![](https://drive.google.com/uc?export=view&id=181CBIqiVh-8dbkX4p-VO2xeMIm_mN6Y8)


#세션과 HttpServletRequest

세션을 브라우저당 하나씩 서버메모리에 생성되어  클라이언트의 상태정보를 유지하기 위해 사용되며 그로인해 세션에 많은 정보가 저장되면 될수록 서버에 부담이 된다.

HttpServletRequest객체에 저장하게 되면 클라이언트 요청으로 생성되었다가 응답메시지가 클라이언트로 전송되면서 자동으로 삭제되는 일회성 객체


HandlerMaping을 xml에서 bean으로 등록해서 쓰는것 -> @RequestMapping이 그 설정을 대체하는것

메소드의 매개변수로 받은 VO를 커맨드객체라고 부른다.

#@RequestParam은 커맨드 클래스에 없는 파라미터 정보를 추출할떄 쓴다.

속성으로 value(view에 전달될 이름) , defaultValue(값없을때 설정할 기본값, required가 있다


#@ModelAttribute 는 매개변수로 선언된 커맨드 객체의 이름을 변경할때 사용하기도 하지만,

메소드위에 붙여서 메소드가 리턴하는 객체를 자동으로 뷰에 보내는 역할도 할 수있다.

@ModelAttribute 가 설정된 메소드는 @RequestMapping 어노테이션이 적용된 메소드보다 먼저 호출되어 자동으로 model에 저장된다 즉 다른 @Requestmapping이 붙은 메소드에도 같이 포함되어 사용할 수 있다는것.

아래의 getBoardList()가 반환하는 getBoardList.jsp 에서  searchConditionMap이 반환하는 return 객체를 사용가능하다는것.

```java
@ModelAttribute("conditionMap")
public Map<String, String> searchConditionMap(){
	Map<String,String> conditionMap = new HashMap<String, String>();
	conditionMap.put("제목", "TITLE");
	conditionMap.put("내용", "CONTENT");
	return confitionMap;

}

@RequestMapping("~")
public String getBoardList(~,Model model){
	//Model 정보 저장
	model.addAttribute("boardList", boardDAO.getBoardList(vo));
	return "getBoardList.jsp";
}
```

```html
<c:forEach items="${conditionMap}" var="option"> ~
~
```


@SessionAttribute("board")


비즈니스 레이어에 해당하는 스프링 설정파일이 


#파일업로드관련

##세팅

```html
<form ~ enctype="multipart/form-data">

~

<input type="file"  ~
```

//VO
```java

private MultipartFile uploadFile;

public MultipartFIle getUploadFile(){
	return uploadFile;
}

public void setUploadFile(MultipartFile uploadFile){
	this.uploadFile = uploadFIle;
}
```

//pom.xml
```xml
<dependency>
	<groupId>commons-fileupload</groupId>
	<artifactId>commons-fileupload</artifactId>
	<version>1.3.1</version>
</dependency>
```



```xml
<bean id="multipartResolver" class="org.springframework.web.multipart.commons.CommonsMultipartResolver">
	<property name="maxUploadSize" value="100000" /> // 디폴트는 -1 이고 무제한이됨.
</bean>
```

여기서 아이디나 이름값이 정해져있다. DispatcherServlet에 특정이름으로만 객체가 인식되도록 정해져있어서 multipartResolver를 꼭 이름으로 사용해야함.	
("Resolver"로 끝나는 클래스들 대부분 이렇게 아이디가 정해놓아져있다.)

//Controller
```java

메소드(BoardVO vo) throws Exception{
MultipartFIle uploadFIle = vo.getUploadFIle();
if(!uploadFIle.isEmpty()){
	String fileName = uploadFIle.getOriginalFilename();
	uploadFile.transferTo(new File("D:/" + fileName));
}

...

}

```

##MultipartFile 인터페이스가 제공하는 주요메소드

String getOriginalFileName() : 업로드한 파일명을 문자열로 리턴

void transferTo(FIle destFile) 업로드한 파일을 destFile에 저장

boolean isEmpty() : 업로드한 파일 존재여부 리턴(없으면 true)





-----------------------------------------

###### 180907_3

예외처리
-

#예외처리

예기치 못한 예외발생시 500에러가 아닌 적절한 메시지가 담긴 화면을 보여줘야한다.

로그인 요청시 아이디 입력하지않으면 IllegalArgumentException이 발생한다고 가정할때

```java
if(vo.getId() == null || vo.getId().equals("")){
	throw new IllegalArgumentExeption("아이디는 반드시 입력해야 합니다.");
}
```

일반사용자는 이런 메시지의 의미도 모를 뿐더러 이런시긍로 예외를 내버려둬서도 안된다.
스프링에서는 이런 예외를 처리하기 위해 XML 설정과 어노테이션 설정두 가지 방법을 제공

## 어노테이션 기반의 예외 처리

@ControllerAdvice , @Exceptionhandler 로 컨트롤의 메소드를 수행 중 발생하는 예외를 일괄적으러 처리 가능

```xml
<mvc:annotation-driven></mvc:annotation-drven>
```
```java
package com.springbook.view.common;

import org.springframework.web.bind.annotation.ControllerAdvice;
import org.springframework.web.bind.annotation.ExceptionHandler;
import org.springframework.web.servlet.ModelAndView;

@ControllerAdvice("com.springbook.view")
public class CommonExceptionHandler {
	@ExceptionHandler(ArithmeticException.class)
	public ModelAndView handleArithmeticException(Exception e) {
		ModelAndView mav = new ModelAndView();
		mav.addObject("exception", e);
		mav.setViewName("/common/arithmeticError.jsp");
		return mav;
	}

	@ExceptionHandler(NullPointerException.class)
	public ModelAndView handleNullPointerException(Exception e) {
		ModelAndView mav = new ModelAndView();
		mav.addObject("exception", e);
		mav.setViewName("/common/nullPointerError.jsp");
		return mav;
	}

	@ExceptionHandler(Exception.class)
	public ModelAndView handleException(Exception e) {
		ModelAndView mav = new ModelAndView();
		mav.addObject("exception", e);
		mav.setViewName("/common/error.jsp");
		return mav;
	}
}
```

@ControllerAdvice("com.springbook.view") 어노테이션 의해 CommonExceptionhandler객체가 자동으로 생성.

"com.springbook.view" 패키지로 시작하는 컨트롤러에서 발생하는 예외가 발생하는 순간 @ExceptionHandler 어노테이션으로 지정한 메소드가 실행된다. 

(어떤 예외가 발생했느냐에 따라 다른 메소드가 수행)

이후는 에러 발생시 화면에 전송할 뷰만 만들면 된다.

화면에서 @{exception.message} 이런식으로 보낸 exception을 뿌려준다.



##XML 기반의 예외처리

xml로 처리하는게 좀더 쉬운방법이다.

```xml
<!-- 예외 처리 설정 -->
	<bean id="exceptionResolver"
		class="org.springframework.web.servlet.handler.SimpleMappingExceptionResolver">
		<property name="exceptionMappings">
			<props>
				<prop key="java.lang.ArithmeticException">
					common/arithmeticError.jsp
				</prop>
				<prop key="java.lang.NullPointerException">
					common/nullPointerError.jsp
				</prop>
			</props>
		</property>
		<property name="defaultErrorView" value="common/error.jsp" />
	</bean>
```






-----------------------------------------

###### 180907_4

다국어 처리
-

각 언어에 따른 메시지 파일을 작성해야한다.

.properties 확장자 사용, 파일명은 언어에 해당하는 Locale 정보를 결합하여 작성.


messageSource_en.properties
```xml
# login.jsp
message.user.login.title=LOGIN
message.user.login.id=ID

. . .

message.user.login.language.en=English
message.user.login.language.ko=Korean


# getBoardList.jsp
message.board.list.mainTitle=BOARD LIST
message.board.list.table.head.seq=SEQ

. . .

```

영어는 문제없지만 ,한국어 중국어 일본어 등의 아시아권 언어 사용시 반드시 유니코드로 변환하여 등록해야한다.

(책에서는 txt 파일을 하나만들어서 
아래와 같이 작성한후 properties 파일에 복사 붙여넣기 하는것을 권장했다.)

messageSource_ko.txt
```xml
#login.jsp
message.user.login.title=로그인

 . . .
```

messageSource_ko.properties
```xml
# login.jsp
message.user.login.title=\uB85C\uADF8\uC778

. . .
```

이후 스프링 설정파일에 이 메시지 파일들을 읽어 들이는 MessageSource 클래스를 <bean> 등록한다.


```xml
<!-- 다국어 설정 -->
	<!-- MessageSource 등록 -->
	<bean id="messageSource"
		class="org.springframework.context.support.ResourceBundleMessageSource">
		<property name="basenames">
			<list>
				<value>message.messageSource</value>
			</list>
		</property>
	</bean>
```

주의사항으로 이름이 "messageSource"로 고정되어있다.

MessageSource에 메시지 파일을 등록시, 확장자 .properties 생략, 

_en , _ko 도 생략

일반적으로 컴포넌트 하나당 하나의 메시지 파일을 작성한다.



-----------------------------------------

###### 180907_5

LocaleResolver 등록
-

웹브라우저가 서버에 요청시 브라우저의 Locale정보가 HTTP 요청 메시지 헤더에 자동으로 설정되어 전송된다.

이때 스프링은 LocaleResolver를 통해 클라이언트의 Locale 정보를 추출하고, 이 Locale 정보에 해당하는 언어의 메시지를 적용한다.

스프링에서는 다음과 같은 네 개개의 LocaleResolver를 지원, 만약 스프링 설정 파일에 LocaleResolver가 등록되지 않았으면 기본적으로

AcceptHeaderLocaleResolver가 적용됨.

- AcceptHeaderLocaleResolver 	
: 브라우저에서 전송된 HTTP 요청 헤더에서 Accept-Language에 설정된 Locale로 메시지를 적용

- CookieLocaleResolver	
: 쿠키에 저장된 Locale정보를 추출하여 메시지에적용

- SessionlocaleResolver	
:HttpSession에 저장된 Locale 정보를 추출하여 메시지를 적용

- FixedLocaleResolver	
:웹 요청과 무관하게 특정 Locale 로 고정



세션으로부터 Locale 정보를 추출하고 유지하는 SessionLocaleResolver를 가장 많이 사용.

```xml
<!-- LocaleResolver 등록 -->
	<bean id="localeResolver"
		class="org.springframework.web.servlet.i18n.SessionLocaleResolver"></bean>


```

LocaleResolver 역시 다른 Resolver클래스처럼 고정된 아이디를 등록해야 함.


#Locale 변경하기

특정언어로 화면을 보다가 해당 화면의 언어를 변경하고 싶을때

LocaleChangeInterceptor 클래스를 사용한다.

이는 HandlerInterceptor인터페이스를 구현한 클래스로, 스프링 설정 파일에 인터셉터로 등록해야함.

```xml
(mvc 네임스페이스 추가필요)

. . .

<!-- LocaleChangeInterceptor 등록 -->
	<mvc:interceptors>
		<bean class="org.springframework.web.servlet.i18n.LocaleChangeInterceptor">
			<property name="paramName" value="lang" />
		</bean>
	</mvc:interceptors>
```

setter주입으로 lang 이 지정되어있는데, 이는 클라이엍느로 부터 lang이라는 파라미터로 특정 Locale이 전송되면 해당 Locale로 변경하겠다는 설정이다.


#JSP 파일 작성

##로그인 화면처리

메시지 파일에 등록한 메시지로 화면을 구성하려면 스프링에서 제공하는 태그 라이브러리를 이용해야 한다.

```
<%@taglib uri="http://www.springframework.org/tags" prefix="spring" %>

```

이후 <spring:message/> 태그의 code 속성값으로 메세지 파일에 등록된 메시지 키를 등록하면 Locale에 해당하는 메시지를 출력할 수 있다.

```
<spring:message code="메시지 키값" />
```

login.jsp
```html
<%@page contentType="text/html; charset=EUC-KR"%>
<%@taglib uri="http://www.springframework.org/tags" prefix="spring" %>

<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN"
"http://www.w3.org/TR/html4/loose.dtd">
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=EUC-KR">
<title><spring:message code="message.user.login.title"/></title>
</head>
<body>
	<center>
		<h1><spring:message code="message.user.login.title"/></h1>
<a href="login.do?lang=en">
	<spring:message code="message.user.login.language.en"/></a>&nbsp;&nbsp;
<a href="login.do?lang=ko">
	<spring:message code="message.user.login.language.ko"/></a>
	
		<hr>
		<form action="login.do" method="post">
			<table border="1" cellpadding="0" cellspacing="0">
				<tr>
					<td bgcolor="orange"><spring:message code="message.user.login.id"/></td>
					<td><input type="text" name="id" value="${user.id }" /></td>
				</tr>
				<tr>
					<td bgcolor="orange"><spring:message code="message.user.login.password"/>
					<td><input type="password" name="password" value="${user.password }"/></td>
				</tr>
				<tr>
					<td colspan="2" align="center">
					<input type="submit" value="<spring:message code="message.user.login.loginBtn"/>"/>
				</tr>
			</table>
		</form>
		<hr>
	</center>
</body>
</html>
```

getBoardList.jsp
```html
<%@page contentType="text/html; charset=EUC-KR"%>
<%@taglib uri="http://java.sun.com/jstl/core_rt" prefix="c"%>
<%@taglib uri="http://www.springframework.org/tags" prefix="spring" %>

<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN"
"http://www.w3.org/TR/html4/loose.dtd">
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=EUC-KR">
<title>글 목록</title>
</head>
<body>
	<center>
		<h1><spring:message code="message.board.list.mainTitle"/></h1>
		<h3>${userName }<spring:message code="message.board.list.welcomeMsg"/>...
			<a href="logout.do">Log-out</a>
		</h3>
		<!-- 검색 시작 -->
		<form action="getBoardList.do" method="post">
			<table border="1" cellpadding="0" cellspacing="0" width="700">
				<tr>
					<td align="right">
					<select name="searchCondition">
						<c:forEach items="${conditionMap }" var="option">
							<option value="${option.value }">${option.key }
						</c:forEach>							
					</select> 
					<input name="searchKeyword" type="text" /> 
					<input type="submit" value="<spring:message code="message.board.list.search.condition.btn"/>"/>
					</td>
				</tr>
			</table>
		</form>
		<!-- 검색 종료 -->
		<table border="1" cellpadding="0" cellspacing="0" width="700">
			<tr>
				<th bgcolor="orange" width="100">
				<spring:message	code="message.board.list.table.head.seq" /></th>
				<th bgcolor="orange" width="200">
				<spring:message code="message.board.list.table.head.title" /></th>
				<th bgcolor="orange" width="150">
				<spring:message code="message.board.list.table.head.writer" /></th>
				<th bgcolor="orange" width="150">
				<spring:message code="message.board.list.table.head.regDate" /></th>
				<th bgcolor="orange" width="100">
				<spring:message code="message.board.list.table.head.cnt" /></th>
			</tr>
			<c:forEach items="${boardList }" var="board">
				<tr>
					<td>${board.seq }</td>
					<td align="left"><a href="getBoard.do?seq=${board.seq }">
							${board.title }</a></td>
					<td>${board.writer }</td>
					<td>${board.regDate }</td>
					<td>${board.cnt }</td>
				</tr>
			</c:forEach>
		</table>
		<br> <a href="insertBoard.jsp"><spring:message code="message.board.list.link.insertBoard"/></a>
	</center>
</body>
</html>
```



-----------------------------------------

###### 180907_8

데이터 변환
-

시스템이 복잡해지면서 다른 시스템과 정보를 주고받을 일이 발생하는데 이때 데이터 교환 포맷으로 JSON 을 사용할 수 있따.

```xml
	<!-- Jackson2 -->
		<dependency>
			<groupId>com.fasterxml.jackson.core</groupId>
			<artifactId>jackson-databind</artifactId>
			<version>2.7.2</version>
		</dependency>

```

브라우저 -> 요청 -> 뷰 반환

HTTP 상태코드 + 메시지헤더 + 메시지바디(HTML코드)

이 응답 결과를 HTML이 아닌 JSON 이나 XML로 변환하여 메시지 바디에 저장하려면 스프링에서 제공하는 변환기(Converter)를 사용해야한다.

스프링은 HttpMessageConverter 를 구현한 다양한 변환기를 제공,
이를 통해 자바 객체를 다양한 타입으로 변환하여 HTTP 응답 바디에 설정할 수 있다.

HttpMessageConverter 를 구현한 클래스들 가운데 자바 객체를 JSON 응답 바디로 변환할 때는 MappingJackson2HttpMessageConverter를 사용한다.

따라서 이를 스프링 설정파일에 등록하는데, 이후에 XML 변환도 처리할것이라서 <mvc:annotation-driven> 을 설정한다.

```
<mvc:annotation-driven></mvc:annotation-driven>
```

```java
	@RequestMapping("/dataTransform.do")
	@ResponseBody
	public List<BoardVO> dataTransform(BoardVO vo) {
		vo.setSearchCondition("TITLE");
		vo.setSearchKeyword("");
		List<BoardVO> boardList = boardService.getBoardList(vo);
		return boardList;
	}
```

@ResponseBody -> 자바객체를 Http 응답 프로토콜의 몸체로 변환하기 위해 사용.

VO를 JSON 으로 변환할때 원하지 않는 VO의 속성까지 JSON으로 변환되어 출력되는것을 막기 위해 getter메소드 위에 @JsonIgnore를 붙여서 무시하게 할 수 있다.


#XML로 변환하기

JAXB2 API에서 제공되는 어노테이션을 사용해야하는데, 자바 6이후 기본으로 포함되어 이썽서 라이브러리 추가없이 사용가능.

```java
@XmlAccessorType(XmlAccessType.FIELD)
public class BoardVO {
	@XmlAttribute
	private int seq;
	private String title;
	private String writer;
	private String content;
	private Date regDate;
	private int cnt;
	@XmlTransient
	private String searchCondition;
	@XmlTransient
	private String searchKeyword;
	@XmlTransient
	private MultipartFile uploadFile;

```
@XmlAccessorType 은 BoardVO 객체를 XML로 변환할 수 있다는 의미.

XmlAccessType.FEILD 때문에 이 객체가 가진 필드, 즉 변수들은 자동으로 자식 엘리먼트로 표현된다. 

이중에서 seq 변수에만 @XmlAttribute가 붙었는데 이는 seq를 속성으로 표현하라는 의미


@XmlTransient가 설정되어있는 세 개 변수는 XML 변환에서 제외하라는 의미.

#BoardListVO 추가

XML문서는 반드시 단 하나의 루트 엘리먼트를 가져야한다.

BoardVO는 하나의 게시글 정보를 저장하지만, 여러 게시글 목록을 XML로 표현해야 하므로 BoardVO 객체 여러개를 포함하면서 루트엘리먼트로 사용할 또 다른 자바클래스가 필요함.

루트엘리먼트로 사용할 BoardListVO를 추가한다.

```java

@XmlRootElement(name = "boardList")
@XmlAccessorType(XmlAccessType.FIELD)
public class BoardListVO {
	@XmlElement(name = "board")
	private List<BoardVO> boardList;

	public List<BoardVO> getBoardList() {
		return boardList;
	}

	public void setBoardList(List<BoardVO> boardList) {
		this.boardList = boardList;
	}
}
```

@XmlRootElement 부분이 루트 엘리먼트 이름을 boardList로 설정하겠다는것.

@XmlElement(name = "board") 이 설정이 없으면 변수 이름인 boardList가 엘리먼트 이름으로 사용됨.

##Controller수정

```java
	@RequestMapping("/dataTransform.do")
	@ResponseBody
	public BoardListVO dataTransform(BoardVO vo) {
		vo.setSearchCondition("TITLE");
		vo.setSearchKeyword("");
		List<BoardVO> boardList = boardService.getBoardList(vo);
		BoardListVO boardListVO = new BoardListVO();
		boardListVO.setBoardList(boardList);
		return boardListVO;
	}
```

-----------------------------------------

###### 180907_9

mybatis
-

Ibatis => Apache

MyBatis => Google


mybatis에서 parameterType 속성은 생략할 수있으며 대부분 생략한다.

resultType은 절대 생략못하며, 다만 대신에 resultMap속성을 사용할 수는 있다.

*selectKey 라는게 있는데 mybatis의 insert문안에서 RDB의 기본키가 자동생성될떄 그 값을 가져올 수있는 방법을 제공.

#resultMap 속성

검색결과를 parameterType 속성을 매핑할 수 없는 몇몇 사례가 있다.

단순 테이블 조회가 아닌 JOIN 등의 경우 정확하게 하나의 자바객체로 매핑할 수 없고, 테이블칼럼이름과 매핑에 사용될 자바 객체의 변수이름이 다를때에 매핑이 되지 않는다.

이럴때 resultMap속성을 사용하여 처리하면 된다.


```xml

<resultMap id="boardResult" type="board">
		<id property="seq" column="SEQ" />
		<result property="title" column="TITLE" />
		<result property="writer" column="WRITER" />
		<result property="content" column="CONTENT" />
		<result property="regDate" column="REGDATE" />
		<result property="cnt" column="CNT" />
	</resultMap>

	<select id="getBoardList" resultMap="boardResult">
		<![CDATA[
		SELECT *
		FROM BOARD
		WHERE TITLE LIKE '%'||#{searchKeyword}||'%'
		ORDER BY SEQ DESC
		]]>
	</select>
```

CDATA는 언제라도 쿼리가 수정되거나 할수있기때문에 다 넣어주기를 권장하고 있다.

Mapper 파일에 등록되는 SQL 구문은 일반적으로 대문자로 작성 -> SQL은 대소문자 구분하지 않지만 

파라미터를 바인딩할때 대부분 칼럼명과 변수명이 같으므로 SQL 구문이 조금이라도 복잡해지면 구분하기 쉽지않다. 따라서 SQL은 대문자로 표현하여 식별성을 높인다.

#Dynamic SQL

만약 제목,내용을 검색하는 기능이 필요할떄

제목검색에 대한쿼리 , 내용에 대한 쿼리를 각 매퍼마다 만들때

이러한 중복SQL 로직이 앞으로도 계속 발생한다면 비효율적이다.

이때 Mybatis가 제공하는 Dynamic SQL을 사용할 수 있다.

1.적용전
```xml
	<select id="getBoardList_T" resultMap="boardResult">
		<![CDATA[
		SELECT *
		FROM BOARD
		WHERE TITLE LIKE '%'||#{searchKeyword}||'%'
		ORDER BY SEQ DESC
		]]>
	</select>
	<select id="getBoardList_C" resultMap="boardResult">
		<![CDATA[
		SELECT *
		FROM BOARD
		WHERE CONTENT LIKE '%'||#{searchKeyword}||'%'
		ORDER BY SEQ DESC
		]]>
	</select>
```

2. Dynamic SQL 적용후

```xml
	<select id="getBoardList" resultMap="boardResult">
		SELECT *
		FROM BOARD
		WHERE 1 = 1
		<if test="searchCondition == 'TITLE'">
			AND TITLE LIKE '%'||#{searchKeyword}||'%'
		</if>
		<if test="searchCondition == 'CONTENT'">
			AND CONTENT LIKE '%'||#{searchKeyword}||'%'
		</if>
		ORDER BY SEQ DESC
	</select>
```

- [mybatis](#180907_9)

-----------------------------------------

###### 180907_10

JPA
-

자바객체와 데이터베이스 테이블이 정확히 일치하지 않으므로 둘 사이를 매핑하는 많은 SQL구문과 자바코드가 필요하게 되는데

ORM은 이런 자바객체와 테이블사이를 매핑한다.

이 과정에서 사용되는 SQL구문과 자바 코드는 ORM 프레임워크가 자동으로 만들어준다.


최고장점은 SQL문을 자동으로 생성한다는 것.

그리고 DBMS가 변경될떄 DBMS의 변경 설정부분만 수정하면 된다.


그중 가장 잘 쓰이는것이 Hibernate


그외에도 ORM이 많아서 그 들에 대한 표준화작업이 'JPA'


JPA는 모든 ORM 구현체(ORM프레임워크)들의 공통 인터페이스를 제공한다.
(JDBC API 를 비교하여 이해하면 편하다)








