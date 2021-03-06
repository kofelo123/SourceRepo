- [TDD 개요](#180815_1)
- [Ch2.JUnit과 Hamcrest](#180827_2)
- [CH3.좀더 나은 TDD](#180830_3)
- [Ch.4 Mock 사용](#180901_2)

---

- [junit테스트에서 profile설정](#181201_16)
- [junit과 contextconfiguration](#181212_1)

- [Junit Test에서 resourceHandlerMapping 빈에러](#181223_2)
-----------------------------------------

###### 180815_1

TDD 개요
-

"만일 당신이 때때로 실패하지 않는다면, 그것은 안이하게 살고 있다는 확실한 증거다 - 우디 앨런-"

 ![](https://drive.google.com/uc?export=view&id=1NmLClsyBQIIfnCz2YbpgZYuDLnz_X9eH)


검증을 위해 콘솔에 찍어보는 고전방법

단점은 이 경우 코드에 문제 유무 판단을 개발자 자신의 두뇌에 상당 부분 의존하게 된다

이러한 개발의 끝은 중간중간 만든 콘솔의 부분을 제거하고 로깅을 이용한 디버깅용 출력을 비활성 상태로 만들어 놓는다는 식으로 마감한다.

어느시점 새 요구사항이나, 수정사항 발생시 다시 로그를 찍고, 변하는 프로그램 상태를 살펴봐야 하는일이 반복된다. 

코드가 커질수록 버그수정에 필요한 부분을 찾기 힘들어진다. 디버깅용 코드와 비즈니스 로직이 물리게 된다.

논리적 오류를 찾기 힘들다-> 여기서 이 값이 들어가면 저것이 나와야 하는게 , 아니던가 맞던가? 이런..

기존코드에 대한 정상작동 보장이 어렵다
(수정시 다른데서 문제가 생기지 않는지)

뭔가 테스트를 위해 소스코드변경,데이터변경등 환경만드는일이 불편할 수있따.


"Test the program before you write it"


TDD에서 말하는 테스트는 일반적으로 말하는 
제품에 대한 테스트보다  작은 메소드 단위테스트 개념이다.


TDD는 질문 - 응답 - 정제  주기를 거친다.

실습 - 은행 계좌 클래스 만들기

은행계좌 클래스{

- 계좌 잔고 조회
- 입금 / 출금
- 예상 복리 이자(추가 개발)

}

![](https://drive.google.com/uc?export=view&id=1vB-S9sTZiLmNYVnrEB_EZUjq4LmdqO46)


테스트 케이스 작성시 두가지 접근 방법이  있다.

1. 구현 대상 클래스의 외형에 해당하는 메소드들을 먼저 만들고 테스트 케이스를 일괄적으로 만듬

2. 테스트 케이스를 하나씩 추가해나가면서 구현 클래스를 점진적으로 만드는 방식

2번 방법이 권장된다.


테스트 케이스는 테스트 하고자 하는 대상에 대한 시나리오를 만들고 그것을 코드로 표현한 모습이다.

"계좌를 생성한다 -> 계좌가 정상적으로 생성되었는지 확인한다."


TDD에서는 하나의 테스트 케이스가 하나의 기능을 테스트 하도록 만드는 것이 기본원칙이다.

대부분 하나의 테스트케이스는 하나의 메소드로 표현된다.

테스트 코드를 만들고 실행하면 시스템에 대해 개발자는 '코드가 예상대로 작동하는지 판단해줄래?

이에 해당하는 부분이 질문-응답-정제 주기 중 질문에 해당하는 부분이다.

그에 대한 응답으로 성공과 실패가 있다.

"TDD의 철학은 가능한 빠른시점내에 자주 실패를 경험하도록 유도한다. 실패를 통해 배움을,경험을, 실패 상황에 대한 극복하고자 노력한다.
성공에 필요한 조건을 만들고, 실패하는 조건 항목을 성공시킨다. 빨리 실패할수록 더 성공에 가까워지는 bu묘한 개발 철학이다."

주기 3단계(질문,응답,정제) 중 '정제'

- 리팩토링을 적용할 부분이 있는지 찾아본다
- ToDo 목록에서 완료된 부분을 지운다.

리팩토링은 정상적 동작하는 코드를 수정하여 좀더 사람친화적인 (이해,변경용이)한 코드로 개선하는 작업이다.


테스트 케이스를 만들어 놓으면 모험을 하더라도 되돌리면 그만이다.

"소스의 가독성, 중복된 코드, 네이밍, 구조개선"


junit에서는 굳이 에러를 던지고 할 필요가 없다.
junit에서 직접 처리되고 알려주기때문

"클래스 설계시 속성을 중요히 생각하는 경우가 많지만 사실 중요한건 동작인 메소드이다. 

동작을 생각하고 그에 필요한 속성을 고려하는 식으로 접근하는것이 불필요한 속성이 클래스에 섞여 들어가는것을 줄여준다."



두번째 질문 : 잔고조회

잔고조회(getBalance)기능 작성을 위한 테스트 케이스

fail() 메소드는 JUnit 제공한느 메소드, 해당케이스를 그 순간 무조건 실패시킨다.

(오류와 실패)
![](https://drive.google.com/uc?export=view&id=1m78QUKyWopO9Wtj1qqqL5RAIjWDvk8M1)

좌측은 오류(에러) , 우측은 실패

실패는 AsserEquals등의 테스트 조건식을 만족시키지 못한것. -> 내부적으로 fail()이 호출됐다는 의미이기도 하다.

오류는 테스트 수행중 에상치 못한 예외가 발생해서 테스트수행을 멈췄다는 것을 뜻한다.


실패가 나오도록 테스트 케이스를 작성해야한다.
오류는 작성자가 의도하지 않은 예상치 못한 실패를 뜻하여 테스트케이스 자체에 문제가 있음을 의미한다.

따라서 오류로 인한 실패가 발생하고 있다면, 빠른시일 내에 실패로 카운트 될 수있게 만들어야한다.

이렇게 테스트 하는것보다
```
if(account.getBalance() != 10000) {
			fail("getBalance() =>" +  account.getBalance());
		}
```

jUnit 테스트 프레임워크에서 제공하는 assertEquals() 메소드를 사용하면 더 편리하다.

```
assertEquals(예상값, 실제값)
assertEquals("설명", 예상값, 실제값)
```

TDD 중요한건 '목표 이미지 세우기-> 자동화된 테스트 케이스 작성 -> 만족 로직작성'

꼭 assert문을 쓰는게 중요한것은 물론 아니다.


일반적으로 실패하는 테스트 케이스를 여러개 동시에 만들고 시작하는것이 권장되지않는다.

Alt Shift R 누르면 변수의 rename을 한번에 하는 기능이 있다.

Account account = new Account(10000);

이런부분이 많이있으면 

이클립스기준 우클릭 -> Refactor (Alt Shift T) -> Convert Local Variable to Filed 해줘서

필드 변수로 사용한다.

private Account account;

이렇게 되게 해놓고

위의 계속 생성하는 부분을 
account = new Account(10000);

이렇게만 되도록 리팩토링한다.


account = new Account(10000);

이런 초기화가 메소드마다 반복 ->

메소드화 -> 퀵픽스 or Refactor의 Extract Method 기능사용(Alt+Shift+M)
(드래그 되어있어야 가능)



테스트 케이스는 아래와 같은 순서로 이루어짐

테스트환경준비 -> 테스트실행 -> 환경정리


보통 테스트에 사용할 자원이나 객체를 준비해놓은 부분을 '픽스처' 라고 부른다.

JUnit은 Before , After 이라는 개념으로 준비와 정리작업을 제공.

이떄 @Before 의 메소드는 private 으로 하면 에러가 나니 public 으로 해야한다.







-----------------------------------------

###### 180827_2

Ch2.JUnit과 Hamcrest
-

테스트 픽스처:

테스트 기반이 되는 상태나 환경

일관된 테스트 실행환경


테스트 메소드 :

테스트 작업에 대한 시나리오적 의미

테스트 메소드:

Junit 메소드를 지칭


리플렉션을 사용해서 테스트 메소드 실행마다 테스트 클래스를 강제로 인스턴스화 한다.

메소드마다 객체를 새로만듬.

영향을 받지않는 독립적인 수행을 위한 원칙

junit3의 경우

try / catch문에서  예외처리 테스트를 했지만


junit 4  에서는
```
@Test (exprected=NumberFormatException.class)
메소드. ..
```

expted를 이용해 예외처리 테스트한다. 만일 테스트 메소드 내에서 해당 예외가 발생하지 않으면 테스트 메소드를 실패로 간주한다.


테스트 제한시간

@Test(timeout=5000)
메소드 ..

(밀리초 단위 시간으로 정함)

@Ignore : 테스트 무시


junit4부터 배열비교도 지원한다 
그러나, 값 집합이 동일해도 순서가 다르면 테스트가 실패함.


@RunWith

:테스트 메소드 실행을 담당하는 클래스를 테스트 러너라고한다.

@SuiteClasses 

: 여러 테스트 클래스를 일괄적으로 수행가능.

```
@RunWith(Suite.class)
@SuiteSclasses(ATest.class, BTest.class, CTest.class)
public class ABCSuite{

}

```

[Hamcrest]

jMock이라는 Mock라이브러리 저자들이 참여해 만든 Matcher 라이브러리

기본적으로 assertEquals 대신에 assertThat이라는 구문 사용을 권장

```
assertEquals("YoungJim",customer.getName());

assertThat(customer,getName(), is("YoungJin"));

```
위 두문장만 봐도 좀더 hamcrest가 추구하는 자연스러운 문장흐름을 알 수 있다.


assertThat("메시지(옵션)", 테스트대상,Matcher구문);

(참고: JUnit 라이브러리 내에도 Hambcrest 라이브러리가 이미 들어있다 )
~140

![](https://drive.google.com/uc?export=view&id=1A57MZ7aCiUedGeA1WOOQfSqrT1_EBVkZ)

![](https://drive.google.com/uc?export=view&id=1eGedNe-TeJPA9IM4ukVw4l6W4vdSwusw)

![](https://drive.google.com/uc?export=view&id=12tO8PF653gyffEpWMuNGpP81Z9eZaHDC)

![](https://drive.google.com/uc?export=view&id=1lBvTRBrXE9tWHzi_FDsZTYtVVzOwnoUg)






-----------------------------------------

###### 180830_3

CH3.좀더 나은 TDD
-


[테스트 케이스 클래스 위치]

폴더는 다르나 패키지는 동일, 컴파일된 클래스는 각각 다른곳으로 하는것이 가장 이상적이다.

접근범위가 default나 protected로 선언된 메소드도 테스트 케이스로 작성 가능.

서로 섞일 염려가 없고, 배포시에 분리가 쉽다.


[테스트 메소드 작성 방식]

'테스트 대상 메소드의 이름 뒤에 추가적인 정보를 기재' 

테스트대상코드 {
public void withdraw(int money){..}
}

테스트코드{
```
@Test
public void testWithdraw_마이너스통장인출(){..}

@Test
public void tetWithdraw_잔고가0원일떄(){...}
```
}

상세 케이스는 _ 를 통해 구분하며 한글을 사용해도 무방 (필자는 한글 사용 권장)

테스트 목록을 뽑아서 엑셀엣서 _로 구분하면 자연스럽게 테스트 케이스 항목이 표로 만들어 지기 때문이라 함.







-----------------------------------------

###### 180901_2

Ch.4 Mock 사용
-

Mock이란, 실물을 흉내낸 모조품을 말한다.

때떄로 테스트 케이스 작성이 어려운상황은 의존성이이 근본적 원인이 되고, 그 의존성을 단절하기위해 Mock 객체가 사용됨.

환경구축, 웹서버 등이 있어야 가능한 경우 Mock이 필요한 상황이다.

또한 타 부서와 협의가 있어야 테스트가 가능할때 

또 속도에 대한 문제도 있다. 너무 오래걸리는 경우 Mock으로 대체할 수 있다.


[테스트 더블]

오리지널 객체로 테스트가 힘든경우, 대신해서 테스트를 진행할 수 있도록 만들어주는 객체 

*테스트 더미와 테스트 스텁

단지 인스턴스화 될 수 있는 객체수준 : 더미

인스턴스화된 객체가 특정 상태나 모습을 대표하면 스텁

*페이크 객체

스텁이 하나의 인스턴스를 대표하는데 주로 쓰고,
페이크는 여러개의 인스턴스를 대표할 수있는 경우이거나, 좀더 복잡한 구현이 들어간 객체를 지칭한다.

복잡성 비교: 더미 < 테스트 스텁 < 페이크 객체 

*테스트 스파이

: 특정 메소드의 정상 호출여부 확인을 목적으로 구현되며, 

보통은 호출여부를 몰래 감시해서 기록했다가, 나중에 요청이 들어오면 해당 기록 정보를 전달해준다.

사실 Mock에도 기능이 있기 때문에 잘 쓰이지 않는다.

*Mock

Mock을 이해하기 위해 상태기반테스트와 행위 기반 테스트에 대한 이해가 필요.


**상태기반 테스트

객체가 특정 시점에 자신만의 상태를 갖는데, 변경될 예상결과와 실제결과를 비교하는 방식이다.

**행위 기반 테스트

올바른 로직 수행에 대한 판단의 결과로
 특정동작 수행여부를 이용. 

예를 들어 메소드 A가 입력되면 메소드B가 호출되지 않아야 정상인 경우

A만 봐서는 여부를 확인이 안되기에 B의 호출여부를 조사하는 경우

*Mock 객체

일반적 테스트 더블은 상태를 기반으로 테스트케이스 작성

Mock 객체는 행위를 기반으로 테스트 케이스를 작성

행위를 검증하기 위해 Mock객체를 쓴다.

수동으로 만들수도 있지만 대부분 Mock 프레임워크를 사용한다.

#Mock 프레임워크







-----------------------------------------

###### 181201_16

junit테스트에서 profile설정
-

SpelEvaluationException ...


Property or field 'config' cannot be found on object of type 'org.springframework.beans.factory.config.BeanExpressionContext' - maybe not public?


junit 테스트에서 

*///profile설정후 hikariconfig의 value "#{config.url}" 다른빈에서 값을 가져오는 부분을 못읽고 있다.

web.xml의 프로파일을 못읽어오는것으로 추리 하고 있었다. 이를 어떻게 읽어오나 몰랐는데 여러 단어로 검색하다가 
spring profile junit으로 검색하여
stackoveflow에서 연관된 어노테이션의 흰트를 얻을수있었다.

그러나 @ActiveProfiles(profiles = "local")

로 해결함..(@ActiveProfiles는 profile지정하기, 테스트에서 많이사용)


@Profile("")
이 어노테이션은 직접 지정한다기보다

이 프로필일때에만 구동되도록 된다.


-----------------------------------------

###### 181212_1

junit과 contextconfiguration
-
 
몰랐는데 junit은 @ContextConfiguration으로 스프링 설정 context 없이는 아예 구동이 안되는걸로보인다.

일단 테스트내에서 빈을 안쓰더라도 context 구동은 시켜야하는데,

지금 thearc 프로젝트 상황이 

root-context.xml , security-context.xml 두개는 꼭 구동시켜야 하는 상황이다(Profile bean을 위한 root-context.xml ,  그 root-context.xml의 userservice 빈의 자동주입 프로퍼티로 사용되는 bcryptencoder의 설정파일인 security-context.xml까지)



-----------------------------------------

###### 181223_2

Junit Test에서 resourceHandlerMapping 빈에러
-


 Error creating bean with name 'resourceHandlerMapping' defined in org.springframework.web.servlet.config


-> ServletConfig 가 있을때만 나는 에러라는것을 비교하면서 알게되었다.

그래서 ServletConfig를 사용하는 테스트를 책소스 코드에서 보다가 @WebAppConfiguration가 Controller단 테스트에 필요하다는걸 알게됨.

