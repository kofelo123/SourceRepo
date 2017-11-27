##취업면접질문정리

- [인터페이스와 추상클래스의 차이점](#인터페이스와-추상클래스의-차이점)
- [스프링의 장점](#스프링의-장점)

### 인터페이스와 추상클래스의 차이점

1. 공통점 

abstract class(추상 클래스)와 interface 는 선언만 있고 구현 내용이 없는 클래스이다.

그래서 자기 자신이 new를 해서 객체를 생성할 수 없으며,
추상클래스를 extends 받거나, interface를 implements 한 자식만이 객체를 생성할 수 있다.
상속받은 자식이 구현을 반드시 하도록 해야할 때 사용한다.

추상클래스의 정의는 abstract 메소드가 하나라도 존재하는 클래스를 일컫는다.
때문에 일부는 구현된 메소드도 있고, abstract라고 붙어있는 메소드는 구현이 안되어있다.

추상클래스를 상속받는 클래스는 반드시 추상메소드를 구현해야한다.
그래서 필수적으로 구현해야할 메소드가 있을 때 추상클래스를 쓰게된다.


인터페이스는 구현체 없이, 메소드에 대한 명세만 되어있다.
인터페이스를 상속받는 클래스에서는 반드시 인터페이스에 있는 메소드를 다 구현해야한다.

자바는 단일상속을 지원하기 때문에 추상클래스는 단일상속이지만, 
interface를 사용하게 되면, implements를 구현하는 부분에서 extends 또한 사용할 수 있다. 
즉, 다중상속이 가능해진다.


'이러이러한 메소드를 쓸 것이다.' 인터페이스에 선언을 해놓고, 가져다가 반드시 선언된 그대로 모두 구현하면 되는게 인터페이스이고,    

이러이러한 메소드가 있지만 가져다 쓰거나 오버라이드 하거나, abstract가 붙은 메소드는 반드시 구현하면 되는게 abstract class이다.

---

### 스프링의 장점

- POJO 기반의 구성
일반적인 java 코드를 이용해서 객체를 구성하는 방식을 그대로 스프링에서 사용 가능.
특정 라이브러리나 컨테이너의 기술에 종속적이지 않는다.
생산성에도 유리하고,코드에 대한 테스트 작업 역시 유연하게 할 수 있다.

- 의존성 주입(DI)
의존적인 객체를 직접 생성하거나 , 제어하는 것이 아니라 , 제어의 역행으로 특정 객체에 필요한 객체를 외부에서 결정해서 연결 시키는 것을의미한다.
외부에서 필요한 객체를 결정하기 때문에 관계를 직접 처리할 필요가 없고, 인터페이스를 활용해서 유연한 구조를 사용할 수있다.
코드에 필요한 객체는 스프링을 통해서 주입받는 구조로 작성된다.

-AOP의 지원
핵심 비즈니스 로직에 집중하고 공통적이고 반복적인 코드(횡심관심사)를 분리한다.

-트랜잭션의 지원하기




Q.게시판 만든다면?몇일내로

Q.static과 session의 차이점

Q.DML,DCL,DDL에 대해 말하시오

Q.join의 종류에 대해 말하시오

Q.stringbuffer와 stringbuilder의 차이점

Q.xml 파서의 종류

A. 1.SAX 2.DOM

(google -> 기출기술면접 검색해서 보라고 함)


Q. 오버라이딩 & 오버로딩차이

Q.상사와의 갈등시

Q.Proeject에서 뭘했는지

Q.jQuery

Q.preparedStatement <-> statement

Q.3개국어 사이트 -> 구현시 어떻게 할지

Q.회원가입시 '유효성검사' How?

Q.403,304

Q. http <-> https

Q.DB Inner Join <-> left join 차이

Q. 자바의장점

Q.성격의 장단점

Q.JVM , 자바 메모리 구조

