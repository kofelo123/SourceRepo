- [AOP기본 , xml 기반 AOP](#180822_1)
- [어노테이션 기반 AOP](#180822_2)

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




