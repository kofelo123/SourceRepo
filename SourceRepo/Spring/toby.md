토비

- [1.2.1 관심사의 분리](#seperationofconcerns)
- [1.2.3 상속을 통한 확장](#inheritanceextends)
- [1.3.1 클래스의 분리](#72)
- [1.3.2 인터페이스의 도입](#interface)
- [1.3.3 관계설정 책임의 분리](#77)
- [1.3.4 원칙과 패턴](#83)
- [1.4 제어의역전](#88)
- [오브젝트 팩토리의 활용](#93)
- [오브젝트 팩토리를 이용한 스프링 IoC](#97)
- [애플리케이션 컨텍스트의 동작방식](#101)
- [용어](#sprongword)
- [오브젝트의 동일성과 동등성](#104)
- [싱글톤 레지스트리로서의 애플리케이션 컨텍스트](#107)
- [스프링 빈의 스코프](#111)
- [의존관계주입](#1111)

# seperationofconcerns
## 관심사의 분리 1.2.1

변경이 일어날 때 필요한 작업을 최소화 하고 그 변경이 다른 곳에 문제를 일으키지 않게 한다. -> 분리와 확장을 고려한 설계

변화는 대체로 집중된 한 가지 관심에 대해 일어나지만 그에 따른 작업이 한곳에 집중되지 않은 경우가 많다.

하나의 관심사가 여러 메소드에 중복되고 흩어져 있고 다른 관심사와 얽혀있으면 변경이 일어날때 큰 고통을 일으키는 원인이 된다.

한가지 관심이 한군데 집중되게 모으고, 관심이 다른것을 분리시켜야
번거로움을 막을 수 있다.
-> @ 관심사의 분리(Seperation of Concerns)

관심사가 여러곳에 중복적으로 일어나는 상황에 대해 중복되는 코드를 따로 메소드를 만들어 분리해야한다.

@ 리팩토링
:외부의 동작방식,기능의 변화없이 내부 구조를 변경해서 재구성한다.
코드 내부의 설계가 개선되어 코드 가독성,변화대처가 좋아진다.
생산성이 올라가고, 품질이 높아지며,유지보수가 용이해지고, 견고하면서 유연한 제품을 개발.

메소드로 중복된 코드를 뽑아내는 것을 리팩토링에서는 @메소드추출 기법이라고 부른다.

리팩토링이 절실히 필요한 코드의 특징을 나쁜냄새라고 부르기도 한다.

# inheritanceExtends
## 상속을 통한 확장 1.2.3

Q.UserDao를 판매하는 입장에서 N사와 D사에 납품을 한다는 가정하에
N사와 D사가 다른 종류의 DB를 사용하고 독자적인 방법으로 적용하고 싶어 한다, 그리고 구매한 이후에 DB커넥션을 가져오는 방법이 종종 변경될수 있는 상황.
그러나 UserDao 소스를 직접 공개하고 싶지는 않고, 고객에게는 미리 컴파일된 클래스 바이너리 파일만 제공하고 싶을떄
->상속을 통한 확장. 기존의 구현코드를 제거하고 getConnection()을 추상메소드로 만들면서 추상클래스를 N사와 D사에 판매한다.
N사와 D사는 UserDao를 상속해서 getConnection 메소드를 확장한 후에 쓸 수있다.

 ![](https://drive.google.com/uc?export=view&id=14WTMhZKns2skThQo9MNpL8lDuEZGdmgV)

이제는 단순히 변경이 용이하다 수준을 넘어서 쉽게 확장된다고 할 수 있다.

@ 템플릿 메소드 패턴
이렇게 슈퍼클래스에 기본적인 로직의 흐름을 만들고, 그 기능의 일부를 추상 메소드나 오버라이딩이 가능한 protected 메소드 등으로 만든 뒤 서브클래스에서 이런 메소드를 필요에 맞게 구현해서 사용하도록 하는 방법을 디자인 패턴에서 '템플릿 메소드 패턴'이라고 한다.
(상속을 통해 슈퍼클래스의 기능을 확장할 떄 사용하는 가장 대표적인 방법)
서브 클래스에서 선택적으로 오버라이드 할 수 있도록 만들어둔 메소드를 훅(hook)메소드 라고 한다.

슈퍼클래스에서 디폴트기능을 정의해두거나 비워뒀다가
서브클래스에서는 추상 메소드(서브클래스에서 반드시 구현해야 하는 추상메소드)를 구현하거나, 훅 메소드(선택적으로 오버라드 가능한 훅 메소드)를 오버라이드 하는 방법을 이용해 기능의 일부를 확장한다.

 ![](https://drive.google.com/uc?export=view&id=1F5eUrjDztIwhm3xG6iUYFZJxqqxWzLhg)

@ 팩토리 메소드 패턴
UserDao의 서브클래스의 getConnection() 은 어떤 Connection 클래스의 오브젝트를 어떻게 생성할 것인지를 결정하는 방법이라고 볼 수 있다. 이렇게 서브클래스에서 구체적인 오브젝트 생성 방법을 결정하게 하는 것을 '팩토리 메소드 패턴'이라고 부르기도 한다.
슈퍼클래스에서는 정확히 어떤 클래스의 오브젝트를 만들어 리턴할지는 모른다.(인터페이스 타입으로 오브젝트 리턴하므로)

UserDao는 Connection 인터페이스 타입의 오브젝트라는 것 외에는 관심을 두지 않는다.
UserDao 는 어떤 기능을 사용한다는 데에만 관심이 있고, NUserDao나 DUserDao에서는 어떤 식으로 Connection 기능을 제공하는지에 관심을 두고 있는 것.
어떤 방법으로 Connection 오브젝트를 만들어내는지도 NUserDao와 DuserDao의 관심사항이다.


@ 디자인패턴
특정상황에서 자주 만나는 문제를 해결하기 위해 사용할 수 있는 재사용 가능한 솔루션을 말한다.
주로 객체지향 설계에 관한 것이고, 설계 구조가 비슷한점이 많은데
그 이유는 객체지향적인 설계로부터 문제를 해결하기 위해 적용할 수 있는 확장성 추구 방법이 대부분 두 가지 구조로 정리 되기 때문
1.클래스상속
2.오브젝트 합성

@상속의문제(한계점).

1.UserDao가 만약 다른목적을 위해 이미 상속을 사용하고 있다면?
다중상속이 안되는 문제가 있다.
2.상속을 통한 상하위 클래스의 관계는 생각보다 밀접하다는 점.
관심이 다른 기능을 분리하고, 필요에 따라 변화가 가능하도록 확장성을 주긴 했지만 여전히 상속관계는 두가지 다른 관심사에 대해 긴밀한 결합을 허용한다.
서브클래스는 슈퍼클래스의 기능을 직접 사용할 수 있다.
슈퍼클래스 내부의 변경이 있을때 모든 서브클래스를 함께 수정하거나 다시 개발해야 할 수 있다.
반대로 그런 변화에 따른 문제때문에 슈퍼클래스를 더이상 변화하지 못하게 제약을 가해야 할수 있다.

3.확장된 기능인 DB 커넥션을 생성하는 코드를 다른 DAO 클래스에 적용할 수 없다는 것.
UserDao외에 DAO 클래스들이 계속 만들어진다면 그때는 상속을 통해서 만들어진 getConnection()의 구현 코드가 매 DAO 클래스마다 중복되서 나타나는 문제가 발생.

---

# 1.3 DAO의 확장

## 72
@ 1.3.1 클래스의 분리

이전까지 독립된 메소드를 분리하고, 그 다음은 상하위 클래
스로 분리했다.

이번에는 connection 관련 부분을 상속관계도 아닌 완전히 독립된 클래스로 만든다.

추상메소드를 사용할 필요가 없어졌다.

```java
public class SimpleConnectionMaker {
	public Connection getConnection() throws ClassNotFoundException,
			SQLException {
		Class.forName("com.mysql.jdbc.Driver");
		Connection c = DriverManager.getConnection(
				"jdbc:mysql://localhost/springbook?characterEncoding=UTF-8", "spring", "book");
		return c;
	}
}
```

이번은 다른 문제가 생겼다.

기존의 N사와 D사에 UserDao 클래스만 공급하고 상속을 통해 DB 커넥션 기능을 확장해서 사용했던게 다시 불가능해졌다.

UserDao의 코드가 SimpleConnectionMaker라는 특정 클래스에 종속되어 있기 떄문에 상속을 사용했을때처럼 UserDao 코드의 수정없이 DB 커넥션 생성 기능을 변경할 방법이 없다.

UserDao에서 simpleConnectionMaker = new SimpleConnectionMaker();

이런 구체적인 사용클래스를 명시해야하는것은 저것이 SimpleConnectionMaker가 아닌

다른방식으로 구현되게 할때 기존의 add(),get() 메소드에서 커넥션을 가져오는 코드를 모두 수정해야하는 번거로움이 생겼다.
확장이 힘들어짐.
---


## interface

@ 1.3.2 인터페이스의 도입

클래스를 분리 하면서도 이런 문제를 해결하기 위해서는

두 개의 클래스가 서로 긴밀하게 연결되어 있지 않도록 중간에 추상적인 느슨한 연결고리를 만들어 주는 것이다.

추상화란 어떤 것들의 공통적인 성격을 뽑아내어 이를 따로 분리해 내는 작업이다.

자바가 추상화를 위해 제공하는 가장 유용한 도구는 바로 인터페이스이다.

인터페이스로 추상화해놓은 최소한의 통로를 통해 접근하는 쪽에서는 오브젝트를 만들때 사용할 클래스가 무엇인지 몰라도 된다.

인터페이스는 어떤 일을 하겠다는 기능만 정의해둔채, 어떻게 하겠다는 구현방법은 나타나있지 않다. 그것은 인터페이스를 구현한 클래스들이 알아서 결정할 일이다.

```java
public interface ConnectionMaker {

	public abstract Connection makeConnection() throws ClassNotFoundException,
			SQLException;

}
```

고객에게 납품을 할 때는 UserDao 클래스와 함꼐 ConnectionMaker 인터페이스를 전달하고.
D사의 개발자라면 아래와 같이 ConnectionMaker 인터페이스를 구현한 클래스를 만들고, 자신들의 DB 연결 기술을 이용해 DB 컨넥션을 가져오도록 메소드를 작성해주면 된다.

```java
public class DConnectionMaker implements ConnectionMaker {
	public Connection makeConnection() throws ClassNotFoundException,
			SQLException {
		Class.forName("com.mysql.jdbc.Driver");
		Connection c = DriverManager.getConnection(
				"jdbc:mysql://localhost/springbook?characterEncoding=UTF-8", "spring", "book");
		return c;
	}
}
```

```java
public class UserDao{
	private ConnectionMaker connectionMaker; //인터페이스를 통해 오브젝트에 접근하므로 구체적인 클래스 정보를 알 필요가 없다.

	public UserDao(){
		connectionMaker = new DConnectionMaker(); /앗! 그런데 여기에는 클래스이름이 나오네!!
	}

	public void add(User user)... {
		Connection c = connectionMaker.makeConnection();
		// 인터페이스에 정의된 메소드를 사용하므로 클래스가 바뀐다고해도
		메소드 이름이 변경될 걱정은 없다.(변화에 유연)
	}
}
```
위 코드중 new DConnectionMaker(); 이부분이 문제가 된다.

초기에 한번  어떤 클래스의 오브젝트를 사용할지를 결정하는 생성자의 코드는 제거되

지 않고 남아있다.

 ![](https://drive.google.com/uc?export=view&id=1k288-5CSa5QuY-3xnBFHCvSpYc6pYPod)


---


## 77

@ 1.3.3 관계설정 책임의 분리

인터페이스를 써가면서 분리를 해왔지만, UserDao가 인터페이스 뿐만아니라

구체적인 클래스까지 알아야 하는 문제가 발생한다.

여전히 UserDao 에는 어떤 ConnectionMaker 구현 클래스를 사용할지를 결정하는

코드가 남아있다.

그 이유는 UserDao에 분리되지않은, 또 다른 관심사항이 존재하고 있지 때문이다.

어떤 ConnectionMaker 구현 클래스를 사용할지를 결정하는 new DConnectionMaker()

라는 매우 짧고 간단하지만 그 자체로 충분히 독립적 관심사를 담고있다.

즉, ConnectionMaker의 특정 구현 클래스 사이의 관계를 설정해주는것에 대한 관심사.

이 관심사의 코드를 분리하지 않으면 UserDao는 독립적으로 확장 가능한 클래스가 될 수 없다.

UserDao의 클라이언트라고 할만한 존재가 있다. 여기서의 클라이언트의 의미는

UserDao를 사용하는 오브젝트이다. 그곳이 이 관심사가 있어야 할 적당한 위치이다.

UserDao의 클라이언트에서  UserDao를 사용하기 전에 먼저 UserDao가 어떤 Connection

Maker의 구현클래스를 사용할지를 결정하도록 만들어본다.

즉 UserDao오브젝트와 특정 클래스로 만들어진 ConnectionMaker 오브젝트 사이의 관계를 설정해주는 것이다.

UserDao의 모든코드는 ConnectionMaker 인터페이스 외에는 어떤 클래스와도 관계를

가지게 해서는 안된다.

물론 UserDao가 동작하려면 특정 클래스의 오브젝트와 관계를 맺어야하지만,

@클래스사이의관계 != 오브젝트 사이의 관계

클래스사이의 관계는 코드에 다른 클래스 이름이 나타나기 때문에 만들어지는 것이다.

하지만 오브젝트 사이는 그렇지 않다. 코드에서는 특정 클래스를 전혀 알지 못하더라도

해당클래스가 구현한 인터페이스를 사용했다면, 그 클래스의 오브젝트를 인터페이스타입으로 받아서 사용할 수 있따.

그것이 객체지향프로그램에는 다형성이라는 특징덕분이다.

UserDao 오브젝트가 DConnectionManager 오브젝트를 사용하게 하려면

오브젝트 사이에 런타임 사용관계 또는 링크, 또는 의존관계라고 불리는 관계를 매정준다.

 ![](https://drive.google.com/uc?export=view&id=1cqY38NwD4G9ow87HJVpyoP2lt_I9Zjv6)

위 그림은 모델링 시점의 클래스 다이어그램이 아니라, 런타임 시점의 오브젝트간 관계를 나타내는 오브젝트 다이어그램이다.

모델링 시에는 없었던, 그래서 코드에서는 보이지 않던 관계가 오브젝트로 만들어진 후에 생성되는 것이다.

UserDao도 아니고 ConnectionMaker 구현 클래스도 아닌 제 3의 오브젝트라고 했던

UserDao 클라이언트의 역할은 위의 그림과 같은 런타임 오브젝트 관계를 갖는 구조로

만들어주는게 클라이언트의 책임이다.

현재는 UserDao 클래스의 main() 메소드가 UserDao의 클라이언트라고 볼수있으며,

이를 UserDaoTest라는 이름의 클래스를 만들어 옮긴다.

그리하여 기존의 생성자 코드에서 아래와 같이 생겼던 것이
```java
	public UserDao(){
		connectionMaker = new DConnectionMaker();
	}
```
아래와 같이 수정되게 된다.

```java
public UserDao(ConnectionMaker connectionMaker){ //클라이언트에서 주입받기위함.
	this.connectionMaker = connectionMaker;
}
```


 ![](https://drive.google.com/uc?export=view&id=15W89QNfcWmmDU8zOGINun_FCVvbYWLCz)


UseDaoTest는 UserDao와 ConnectionMaker 구현 클래스와의 런타임 오브젝트 관계를
설정하는 책임을 담당해야한다.

그래서 ConnectionMaker 구현 클래스의 오브젝트를 만들고, UserDao 생성자 파라미터

에 넣어 두 개의 오브젝트를 연결해 준다.

이렇게 인터페이스를 도입하고 클라이언트의 도움을 얻는 방법은 상속을 사용해 비슷한

시도를 했을 경우에 비해서 훨씬 유연하다.

DAO가 아무리 많아져도 DB 접속방법에 대한 관심은 오직 한군데에 집중되게 할 수 있고, DB 접속 방법을 변경해야 할 때도 오직 한 곳의 코드만 수정하면 된다.

다시 과정을 살펴보면

 ![](https://drive.google.com/uc?export=view&id=1Sv5ghSoMg3m6beDY6IxoRvhpSk5qUAYB)

1.UserDaoTest는 UserDao가 실제로 사용할 DConnectionMaker 오브젝트를 생성한다.

2.UserDao 오브젝트를 생성하면서 생성자를 통해 DConnectionMaker 오브젝트를 전달
(인터페이스 타입으로 전달받기 때문에 ConnectionMaker 인터페이스를 구현했다면 어떤 클래스의 오브젝트라도 상관없으며, 받는쪽에서도 관심이 없다.)

3.테스트를 위해 UserDao의 add(), get() 메소드를 사용한다. 이떄 UserDao는 생성자를
통해 전달받아 인스턴스 변수에 저장해둔 DConnetionMaker 오브젝트의 makeConnection() 메소드를 호출하여 DB 커넥션을 생성해 사용한다.


---


## 83

@ 1.3.4 원칙과 패턴

@ 개방 폐쇄 원칙

개방 폐쇄 원칙(OCP,oepn-Closed principle)은 '클래스나 모듈은 확장에는 열려 있어야 하고, 변경에는 닫혀 있어야한다.'

지금까지 해본 리팩토링 작업의 특징을 설명하기에 좋은 설계원칙이다.

UserDao는 DB 연결 방법이라는 기능을 확장하는 데는 열려 있다.

UserDao에 전혀 영향을 주지 않고도 얼마든지 기능을 확장할 수 있따.

동시에 UserDao 자신의 핵심 기능을 구현한 코드는 그런 변화에 영향을 받지 않고

유지할 수 있으므로 변경에는 닫혀 있다고 말할 수 있다.

초난감DAO의 경우 연결방법을 확장하기에 매우 불편하니 확장에 필요한 유연성은 부족하고, 확장하고자 하면 DAO 내부도 변경해야 하니 변화에 구멍이 나있는 셈이다.


@ 객체지향 설계 원칙(SOLID)
:객체지향 특징을 잘 살릴 수 있는 설계의 특징을 말한다.

'디자인 패턴'이 특별한 상황에 발생하는 문제에 대한 좀 더 구체적인 솔루션이라면

'객체지향 설계 원칙'은 좀더 일반상황에서 적용 가능한 설계 기준이라고 볼 수있다.

디자인패턴은 대부분 객체지향 설계원칙을 잘 지켜서 만들어져 있다.

SOLID의 5가지 원칙

1.SRP(The Single Reponsibility Principle): 단일 책임 원칙

2.OCP(The Open Closed Principle):개방 폐쇄 원칙

3.LSP(The Liskov Substitution Principle): 리스코프 치환 원칙

4.ISP(The Interface Segregation Principle): 인터페이스 분리 원칙

5.DIP(The Dependency Inversion Principle): 의존관계 역전 원칙


@ 높은 응집도와 낮은 결합도

개방폐쇄원칙은 높은 응집도와 낮은 결합도(high coherence and low cupling)라는

소프트웨어 개발의 고전적인 원리로도 설명이 가능하다.

응집도가 높다는 건 하나의 모듈, 클래스가 하나의 책임 또는 관심사에만 집중되어 있다는 뜻이다.


@ 높은 응집도

응집도가 높다는 것은 변화가 일어날떄 해당 모듈에서 변하는 부분이 크다는것으로도 설명할 수 있다.

변경이 일어날떄 모듈의 많은 부분이 함께 바뀐다면 응집도가 높다고 할 수있다.

모듈의 일부분만 변경된다면 나머지 부분에 영향을 미치는지 확인을 해야하는 이중부담이 생긴다.

관심사와 책임이 얽혀 있는 복잡한 코드에서는 변경이 필요한 부분을 찾기도 힘들지만

변경시에 다른 기능에 영향을 줘서 오류를 발생하지 않는지에 대해 일일이 확인해야한다.

@ 낮은 결합도

책임과 관심사가 다른 오브젝트 또는 모듈과는 낮은 결합도 , 즉 느슨하게 연결된 형태를 유지하는게 바람직 하다.

느슨한 연결은 관계를 유지하는데 꼭 필요한 최소한의 방법만 간접적인 형태로 제공하고 나머지는 서로 독립적이고 알 필요도 없게 만들어 준다.

결합도가 낮아지면 변화에 대응하는 속도가 높아지고, 구성이 깔끔해 진다. 확장이 편하다.

여기서 결합도란 '하나의 오브젝트가 변경할때 관계를 맺고 있는 다른 오브젝트에게 변화를 요구하는 정도'라고 설명할수있다.
> '내생각에 이런 결합도는 상속을 생각해보면 이해가 빠르다. 결합도가 높은 상속'

ConnectionMaker 인터페이스의 도입으로 인해 DB 연결 기능을 구현한 클래스가 바뀌더라도 DAO의 코드는 변경될 필요가 없게 됬다.

ConnectionMaker오 UserDao의 결합도가 낮아진 것이다.

더 나아가서 ConnectionMaker의 클래스를 결정하는 책임을 DAO의 클라이언트로 분리한 덕분에 사용할 ConnectionMaker 구혀느 클래스가 바뀌어도 DAO 클래스의 코드를 수정할 필요가 없게 됬다.

@ 전략 패턴

개선한 UserDaoTest-UserDao-ConnectionMaker 구조를 디자인 패턴의 시각으로 보면

전략 패턴(Stategy Pattern)에 해당한다.

전략패턴은 디자인 패턴의 꽃이라고 불릴만큼 많이 사용한다.
(개방폐쇄원칙의 실현에도 잘들어맞는 패턴)

전략패턴은 자신의 기능 맥락(context)에서 필요에 따라 변경이 필요한 알고리즘(독립적

책임으로 분리 가능한 기능) 클래스를 필요에 따라 바꿔 사용할 수 있게 하는 디자인패턴이다.

UserDao는 전략 패턴의 context에 해당된다. context는 자신의 기능을 수행하는데 필요

한 기능 중에서 변경 가능한, DB 연결방식이라는 알고리즘을 ContextMaker라는 인터페

이스로 정의하고, 이를 구현한 클래스,즉 전략을 바꿔가면서 사용할 수 있게 분리했다.

전략패턴은 클라이언트의 필요성에 대해서도 잘 설명하고 있다.

context(UserDao)를 사용하는 클라이언트(UserDaoTest)는 context가 사용할 전략

(ConnectionMaker를 구현한 클래스 - DCOnnectionMaker)을 context의 생성자를 통해

제공해 주는게 일반적이다.


즉 UserDao는 개방폐쇄원칙을 따르고 있으며, 응집력이 높고 결합도는 낮으며, 전략패턴을 적용했음을 알 수 있다.

스프링이란 바로 지금까지 설명한 객체지향적 설계 원칙과 디자인 패턴에 나타난 장점

을 자연스럽게 개발자들이 활용할 수 있게 해주는 프레임워크이다.



---

###### 88
1.4 제어의역전
-

오브젝트 팩토리
-

현재 클라이언트 역할을하는 UserDaoTest는 복수의 관심사를 가짐.
즉, dao를 테스트하는 기능 + 어떤 ConnectionMaker 구현 클래스를 사용할지 결정하는 클라이언트 기능.

그래서 분리할 필요가 있음.

팩토리
-

객체의 생성 방법을 결정하고 그렇게 만들어진 오브젝트를 돌려주는것
을 팩토리라고한다.

오브젝트의 생성과, 사용의 책임을 분리하기 위한 목적이다.

기존의 UserDaoTest에 있던 ConnectionMaker관련 생성작업을

DaoFactory에 옮기고 UserDaoTest에서는 DaoFactory에 요청해서

미리만들어진 UserDao 오브젝트를 가져와 사용하도록 만든다.

![](https://drive.google.com/uc?export=view&id=1JLQp7xi1T8PFarjqUuDDZaotDhTX8c4q)

![](https://drive.google.com/uc?export=view&id=1yLxvLD6NEWNNWyrNGTDlD8dkRfldIDnC/)

 ![](https://drive.google.com/uc?export=view&id=1Ndia5y5Nvx2ivq7cxXz5m48-zFD6jX8y)


---


###### 93
오브젝트 팩토리의 활용
-

기존의 Factory의 userdao 생성코드에는 한가지 문제점이 있는데

코드안에 어떤dao를 사용할지 명시 되어있다는점이다.

만약 dao가 여러 다른종류가 생길때?

ConnectionMaker 구현클래스의 오브젝트를 생성하는 코드가 메소드마다 반복되게 된다.

좋지않은 현상이다. ConnectionMaker의 구현클래스를 바꿀때마다

모든메소드를 일일이 수정해 줘야 하는 일이 생긴다.

 ![](https://drive.google.com/uc?export=view&id=1Eqc3Yq3QuoXvXxp6d58oGDsRuCp43lPw)

이같은 중복문제를 해결하기위해 역시 분리하는게 좋다.

ConnectionMaker의 구현 클래스를 결정하고 오브젝트를 만드는 코드를

별도의 메소드로 뽑아낸다.

DAO를 생성하는 각 메소드에서는 새로 만든 ConnectionMaker 생성용 메서드를 이용하도록 수정한다.

이제는 ConnectionMaker 의 구현클래스가 바뀌어도 한군데서 수정할 수 있다.

 ![](https://drive.google.com/uc?export=view&id=1zCn9qalVtKpnYvykQSob1m4phmMuyB4K)

제어권의 이전을 통한 제어관계 역전
-

제어의역전은 사용하는쪽에서 어떤 클래스를 쓸지 정하는 구조에서

반대로 자신이 어떻게 만들어지고 어디서 사용되는지 알수없는 구조이다.(getConnection처럼)

모든 제어권한을 자신이 아닌 다른대상에 위임하기 때문이다.

>'DI,IoC 이런 개념은 모두 변화에 흔들리지 않는구조라고 생각이 된다.'

프로그램의 시작을 담당하는 main() 과 같은 엔트리 포인트를
제외하면 모든 오브젝트는 이렇게 위임받는 제어 권한을 갖는
특별한 오브젝트에 의해 결정되고 만들어진다.

프레임워크 또한 제어의 역전 개념이 사용된것이다.

라이브러리와는 다른데 라이브러리를 사용하는 애플리케이션 코드는 애플리케이션 흐름을 직접 제어한다.

반면 프레임워크는 거꾸로 애플리케이션 코드가 프레임워크에 의해  사용된다.

프레임워크 위에 개발한 클래스를 등록하고, 프레임워크가 흐름을 주도하는 중에 개발자가 만든 애플리케이션 코드를 사용하도록 만드는 방식이다.



---

######97
오브젝트 팩토리를 이용한 스프링 IoC
-

스프링이 제어권을 가지고 직접 만들고 관계를 부여하는 오브젝트를 빈(bean)이라 부른다, 동시에 제어의 역전이 적용된 오브젝트를 말한다.

빈의 생성과 관계설정 같은 제어를 담당하는 IoC 오브젝트를 빈 팩토리 라고 부른다.
보통 빈 팩토리보다는 이를 좀더 확장한 애플리케이션 컨텍스트를 주로 사용한다.

애플리케이션 컨텍스트는 일종의 설계도라고 보면된다.
IoC방식을 이용해 애플리케이션 컴포넌트를 생성하고, 사용할 관계를 맺어주는 등의 책임을 담당한다.

* DaoFactory를 사용하는 애플리케이션 컨텍스트

DaoFactory를 스프링의 빈 팩토리가 사용할 수있는 설정정보로 만들어본다.
빈 팩토리를 위한 오브젝트 설정을 담당하는 클래스라고 인식할 수 있도록 @Configuration이라는 애노테이션을 추가한다.

오브젝트를 만들어주는 메소드에는 @Bean 이라는 애노테이션을 붙여준다.
1.userDao() 메소드는 UserDao 타입 오브젝트를 생성하고 초기화해서 돌려주는 것이니 당연히 @Bean이 붙는다.
2. ConnectionMaker 타입의 오브젝트를 생성해주는 connectionMaker()메소드에도 @Bean을 붙여준다.

//아래는 적용된 DaoFactory클래스, 자바코드의 탈을 쓰고있지만 사실은 XML과 같은 스프링 전용 설정정보라고 보는것이좋다.

 ![](https://drive.google.com/uc?export=view&id=1NWqC7jp5vdqWvI2_hmbBKq93B6Oo6hic)

 ![](https://drive.google.com/uc?export=view&id=1VDVCzAq0095Hlf02U6kDo4Bj9WWyzDzH)

이제 DaoFactory를 설정정보로 사용하는 애플리케이션 컨텍스트를 만든다.

ApplicationContext 타입의 오브젝트인데 이를 구현한 클래스 중 DaoFactory처럼 @Configuration이 붙은 자바코드 설정정보를 사용하려면 AnnotationConfigApplicationContext를 사용한다.
생성자로 DaoFactory 클래스를 넣어준다.
그리고 준비된 applicationcontext의 getBean() 메소드를 이용해서
UserDao의 오브젝트를 가져올 수 있다.

 ![](https://drive.google.com/uc?export=view&id=1cAiT1HukRnUwW1m6D5CswoFyzAxqyebR)

getBean()의 파라미터인 "userDao"는 ApplicationContext에 등록되니 빈의 이름이다.
DaoFactory에서 @Bean이라는 애노테이션을 userDao라는 이름의 메소드에 붙였는데 , 이 메소드의 이름이 빈의 이름이된다.
userDao라는 이름의 빈을 가져온다는것은 DaoFactory의  userDao() 메소드를 호출해서 그 결과를 가져온다고 생각하면 된다.

왜 userDao를 가져오는 메소드는 하나뿐인데 왜 굳이 이름을 사용할까?
그것은 UserDao를 생성하는 방식이나 구성을 다르게 가져가는 메소드를 추가할 수 있기 떄문이다.

그떄는 sspecialUserdao()라는 메소드를 만들고 getBean("specialUserDao", UserDao.class)로 가져오면 된다.

getBean()은 기본적으로 Object 타입으로 리턴하게 되어있어서 매번 리턴되는 오브젝트에 다시 캐스팅을 해줘야 하는 부담이 있으나, 자바 5이상의 제네릭 메소드방식을 이용해
getBean()의 두번쨰 파라미터에 리턴 타입을 주면 지저분한 캐스팅 코드를 사용하지 않아도 된다.

---


######101
애플리케이션 컨텍스트의 동작방식
-

기존의 오브젝트팩토리 vs 스프링의 애플리케이션 컨텍스트
방식을 비교해본다.

스프링에서는 이 애플리케이션 컨텍스트를 IoC 컨테이너라고 하기도 하고 스프링 컨테이너 라고 하기도 한다.

애플리케이션 컨텍스트는 ApplicationContext 인터페이스를 구현했고, 이것은 또 빈 팩토리가 구현하는 BeanFactory 인터페이스를 상속했으므로 애플리케이션 컨텍스트는 일종의 빈 팩토리인 셈이다.

DaoFactory가 UserDao를 비롯한 DAO 오브젝트생성 및 DB 생성 오브젝트와 관계를 맺어주는 제한적인 역할인데 반해,
애플리케이션 컨텍스트는 IoC를 적용해 관리할 모든 오브젝트에 대한 생성과 관계설정을 담당한다.

대신 ApplicationContext에는 DaoFactory와 다리 직접 옵젝트를 생성하고 관계를 맺어주는 코드가 없고, 생성정보,연관관계 정보를 별도의 설정정보를 통해 얻는다.

@Configuration이 붙은 DaoFactory는 이 애플리케이션 컨텍스트가 활용하는 IoC의 설정정보다.

내부적으로는 애플리케이션컨텍스트가 DaoFacotry의 userDao() 메소드를 호출해서 오브젝트를 가져온것을 클라이언트가 getBean()으로 요청할 떄 전달해준다.

 ![](https://drive.google.com/uc?export=view&id=14x1_s9uSPH7dc53APMnPSMZ_hzL3g19Z)

애플리케이션 컨텍스트는 DaoFactory 클래스를 설정정보로 등록해두고 @Bean이 붙은 메소드의 이름을 가져와 빈 목록을 만들어둔다.

애플리케이션 컨텍스트의 getBean()메소드 호출시 빈 목록에서 요청한 이름이 있는지 찾고, 있다면 빈생성 메소드를 호출하여
오브젝트 생성시킨 후 클라이언트에 돌려준다.

애플리케이션 컨텍스트를 사용하는 이유는 범용적이고  유연한 방법으로 IoC의 기능을 확장하기 위해서이다.
DaoFactory를 오브젝트 팩토리로 직접 사용했을떄와 비교해서 애플리케이션 컨텍스트를 사용했을떄 얻을수 있는 장점은 아래와 같다.

1. 클라이언트는 구체적인 팩토리 클래스를 알 필요가 없다.

DaoFactory 처럼 IoC를 적용한 오브젝트도 계속 추가되어야 하는 번거로움이 없어진다.
오브젝트 팩토리가 많아져도 이를 알아야하거나 직접 사용할 필요가 없다.
그리고 XML처럼 단순한 방법으로 IoC 설정정보를 만들수도있다.

2.애플리케이션 컨텍스트는 종합 IoC 서비스를 제공해준다.

관계설정외에도, 오브젝트 만드는 방식, 시점과 전략을 다르게 가져갈수있고, 자동생성, 후처리, 조합, 설정, 인터셉팅 등 오브젝트를 효과적으로 활용 가능.

3.애플리케이션 컨텍스트는 빈을 검색하는 다양한 방법을 제공

getBean()은 빈의 이름을 이용해 빈을 찾는데, 타입만으로 빈을 검색하거나 특별한 애노테이션 설정이 되어 있는 빈을 찾을수도있다.


---

######sprongword
용어
-

빈:스프링에서 IoC 방식으로 관리하는 오브젝트(스프링이 직접 그 생성과 제어를 담당하는 오브젝트만)

빈팩토리: IoC 담당 핵심 컨테이너. 보통 이것을 구현한 애플리케이션 컨텍스트를 사용

애플리케이션 컨텍스트:빈팩토리를 상속받아 구현한것

설정정보/메타정보:
빈 팩토리가 IoC를 적용하기 위해 사용하는 메타정보


---

######104
오브젝트의 동일성과 동등성
-

동일성
:완전히 동일한 오브젝트 (==)

동등성
:정보만 동일한 오브젝트 (equals())
(두오브젝트가 다른메모리에 있는것)

DaoRactory를 직접 사용하는것과 @Configuration 애노테이션으로 스프링 애플리케이션 컨테그트를 통해 쓰느것의 차이는?

DaoFactory의 UserDao()를 여러번 호출할때는 동등성에 해당하는 것이다.
userDao를 매번 호출하면 계속 새로운 오브젝트가 만들어진다.

 ![](https://drive.google.com/uc?export=view&id=1HFTg3xK-1HaDQOqf0nZUVi3MD0cBudMc)

스프링의 애플리케이션 컨텍스트에 DaoFactory를 설정정보로 등록하고 getBean() 을 통해 오브젝트를 가져오면 DaoFactory의 suerDao() 메소드를 호추해서 userDao 타입의 오브젝트를 가져오는것은 마찬가지 이나
스프링은 여러 번에 걸쳐 빈을 요청하더라도 매번 동일한 오브젝트를 돌려준다.

 ![](https://drive.google.com/uc?export=view&id=1kAY29wr8ADWvZAfcfiStKhHSQujY8nlX)




 ![](https://drive.google.com/uc?export=view&id=)

---

######107
싱글톤 레지스트리로서의 애플리케이션 컨텍스트
-

애플리케이션 컨텍스트는 IoC 컨테이너이다. 동시에 싱글톤을 저장하고 관리하는 싱글톤 레지스트리이다.

스프링은 기본적으로 내부적으로 생성한느 빈 오브젝트를 모두 싱글톤으로 만든다. (디자인패턴에서 나오는 싱글톤과 비슷한 패턴과 개념이지만 그 구현방법은 확연히 다르다)

스프링은 태생이 자바 엔터프라이즈 기술을 사용하는 서버환경을 목적으로 두는데, 수많은 클라이언트의 요청이 올때 각 로직을 담당하는 오브젝트를 새로 만들어서 사용하면 서버에 부하가 심하다.

서블릿은 대부분 멀티스레드 환경에서 싱글톤으로 동작한다.
서블릿 클래스당 하나의 오브젝트만 만들어두고, 사용자 요청을 담당하는 여러 스레드에서 한의 오브젝트를 공유해 동시에 사용한다.

디자인패턴의 싱글톤은 그와 좀 다른데, 사용하기 까다롭고 여러가지 문제점이 있다. 비판을 받는 패턴이기도 하다.

하나만 만들어지는 오브젝트를 전역적으로 접근한다.

* 싱글톤 패턴의 한계

자바에서 구현하는 일반적 방법

 ![](https://drive.google.com/uc?export=view&id=1sEBdfcXTqhHoqEvUpDeY5xOKXhvO-UhK)

 ![](https://drive.google.com/uc?export=view&id=1ki7wyw59BEvAbkJ0wexR3FgzFLRTIyNB)

생성자가 private라서 외부호출이 불가능하다 DaoFactory에서 UserDao를 생성하여 ConnectionMaker 오브젝트를 넣어주는게 불가능해진다.

* 일반적인 싱글턴 패턴 구현방식의 문제점

- private 생성자로 인해 상속할 수 없다
:OOP적인 설계가 힘들수 있다. 객체지향의 특징이 적용되지 않는 스태틱필드와 메소드를 사용하는것도 동일한 문제를 발생

- 테스트가 힘들다.
:만들어지는 방식이 제한적이라 목오브젝트 등에서 대체하기 힘들다.
직접 오브젝트를 만들어 써야하며 , 테스트용으로 대체하기 힘들다.

- 서버환경에서는 싱글톤이 하나만 만들어지는것을 보장하지 못한다.
자바환경의 클래스 로더의 구성상태와, JVM의 분산 설치경우 등 상황에 따라 오브젝트가 여러개 생길수있어 싱글톤으로써 가치가 덜어진다.

- 전역상태를 만들 수 있기 떄문에 바람직하지 못하다.
아무객체나 자유롭게 접근하고 수정하고 공유할수 있는 전역상태를 갖는것은 객체지향 프로그래밍에서는 권장되지 않는 모델이다.

* 싱글톤 레지스트리

스프링은 서버환경에서 싱글톤이 만들어져 서비스 오브젝트방식으로 사용되는것은 지지하나, 자바의 기본적인 싱글톤 패턴의 구현은 여러 단점이 있어서, 스프링은 직접 싱글톤형태의 오브젝트를 만들고 관리하는 기능을 제공한다.
그것이 싱글톤 레지스트리 이다.
싱글톤 레지스트리는 기존의 스태틱메소드,private생성자 를 써야하는 비정상적인 클래스가 아니라 평범함 클래스를 싱글톤으로 활용하게 한다.

public 생성자를 가질수 있으며 테스트를 위한 목오브젝트로대체하는것도 간단하며 자유롭다.

스프링이 지지하는 OOP 설계방식,디자인패턴등을 적용하는데 제약이없다.

* 싱글톤과 오브젝트의 상태

싱글톤은 멀티스레드 환경에서 여러 스레드가 동시접근한다.
이때 상태정보를 내부에갖지 않는 무상태 방식으로만들어야한다.

여러 사용자가 하나의 오브젝트를 수정하는일이 생기면 같은 저장공간이라 데이터에 심각한 문제가 생긴다.

 ![](https://drive.google.com/uc?export=view&id=1U2dU-wxez2c5zqSH8GI9tmeLVCn7Rd2e)

스프링의 싱글톤 빈으로 사용되는 클래스는 개별적으로 변경하는 정보는 로컬 변수로 정의하거나 파라미터로 주고받아야한다.
그러나 인스턴스 변수로 정의해서 사용한 것이 있으니, ConnectionMaker인터페이스 타입의 connectionMaker이다.
이것은 인스턴스 벼수로 사용해도 상관없다.읽기전용 정보이기 때문이다.

~112p


---

###### 111

스프링 빈의 스코프
-

스프링의 빈 스코프의 기본은 싱글톤이다.

경우에 따라서 여러 옵션을 줄수있다.

프로토타입: 매번 새로운 오브젝트
request(요청) 스코프: HTTP 요청시마다
session 스코프

---


###### 1111

의존관계주입
-

DI는 오브젝트 레퍼런스를 외부로부터 제공(주입)받고 이를 통해 여타 오브젝트와 다이나믹하게 의존관계가 만들어지는것이 핵심이다.

* 의존관계란

두 클래스 또는 모듈이 의존관계에 있다고 할때는 항상 방향성이 부여된다. UML에서는 점선으로 된 화살표로 표시한다.

 ![](https://drive.google.com/uc?export=view&id=14_OhNJoJM1jcANUtEmrFkI8ZRcDLqd3h)


의존관계가 있다는것은 B의 변경이 A에 영향을 미친다는 뜻이다. '사용에 대한 의존관계'가 있다고 말할 수 있다.


기존의 UserDao가 ConnectionMaker 인터페이스에 의존한다.

하지만 ConnectionMaker 인터페이스를 구현한

DConnectionMaker의 변경에는 영향을 받지 않는다.

이렇게 인터페이스에 대해서만 의존관계를 만들어면 인터페이스 구현 클래스와의 관계는 느슨해지면서 변화에 영향을 덜 받는 상태가 된다. 즉 결합도가 낮아진다.

 ![](https://drive.google.com/uc?export=view&id=1eIlHOodxQrcmDVv_RJbEchwPegp29X_B)

UML에서 말하는 의존관계는 이런 설계 모델의 관점의 의미다.

그런데 런타임시에 오브젝트 사이에 만들어지는 의존관계도 있다. 모델링 시점의 의존관계와는 성격이 다르다.

UserDao 오브젝트가 런타임시에 어떤 클래스로 만든것인지 미리 알 수없다. 즉 설계와 코드속에서 드러나지 않는다는 말이다.

런타임시에 의존관계를 맺는 대상, 실제 사용대상인 오브젝트를 '의존 오브젝트'라고 말한다.

의존주입은 구체적인 의존 오브젝트와 그것을 사용할 주체, 보통 클라이언트라고 부르는 오브젝트를 런타임시에 연결해주는 작업을 말한다.

다시 의존주입을 세가리 조건으로 말하면

1. 클래스 모델이나 코드에는 런타임 시점의 의존관계가 드러나지 않는다. 그러기 위해 인터페이스만 의존한다.

2. 런타임 시점의 의존관계는 컨테이너나 팩토리 같은 제 3의 존재가 결정한다.

3.의존관계는 사용할 오브젝트에 대한 레퍼런스를 외부에서 제공(주입)해줌으로써 만들어진다.

제 3의존재가 핵심이다.

```java
public UserDao(){
	connectionMaker = new DConnectionMaker();
}

이 코드는 설계 시점에서 DConnectionMaker라는 구체적인 클래스 존재를 알고있다.

이미 런타임시의 의존관계가 코드속에 다 미리 결정되어 있다는것이 문제.
제 3의 존재에 런타임 의존관계 결정권한을 위임해야 한다

		<<interface>>
UserDao - - - > ConnectionMaker

이렇게 될떄 의존관계는 인터페이스 타입 뿐인데, 런타임 시점에도 변경되지 않는다.

제3의 존재가 필요하기 때문

DaoFactory가 그 역할을 담당한다고 했을때, 의존관계 주입을 주도하는 존재이며 두오브젝트사이의 런타임 의존관계를 설정한다.

따라서 DI컨테이너/ IoC 컨테이너라고 부를수있다.

```java

public class UserDao{
	private ConnectionMaker ocnnectionMaker;

	public UserDao(ConnectionMaker connectionMaker){
		this.connetionMaker = connectionMaker;
	}
}

```

 ![](https://drive.google.com/uc?export=view&id=1Hss72ir2Y5H-Y5WAS28RqAOh_dD5d-mw)

자신이 사용할 오브젝트에 대한 선택과 생성제어권을 외부로 넘기고 자신은 수동적으로 주입받은 오브젝트를 사용한다.


* 의존관계 검색과 주입

IoC 방법에는 의존관계 주입만 있는게 아니라


외부에서의 주입이 아니라 스스로 검색을 이용 스스로 검색을 이용하기 때문에 의존관계 검색 이라고 불리는 것도 있다.

런타임시 의존관계를 맺을 오브젝트를 결정하는 것과 오브젝트의 생성작업은 외부 컨테이너에게 IoC로 맡기지만, 이를 가져올때는 메소드나 생성자를 통한 주입대신 스스로 컨테이너에게 요청하는 방법을 사용한다.

DL이라고도 부른다.

> '(중요한게 아니니 자세한 코드는 생략)'

일반적으로는 DI가 권장되는데, DL의 경우 결국 코드에 오브젝트가 명시되기 때문인데, 가끔 써야하는 상황이 있긴하다. Test등에서.

~120p
|   |자바 코드 설정정보   |XML 설정정보   |
|---|---|---|
|빈 설정파일  |@Configuration   | <beans>  |
|빈의 이름   | @Bean methodName()  | <bean id="methodName"  |
|빈의 클래스   | return new BeanClass()  | class="a.b.c... BeanClass">  |
