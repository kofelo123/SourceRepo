토비

- [1.2.1 관심사의 분리](#seperationofconcerns)
- [1.2.3 상속을 통한 확장](#inheritanceextends)
- [1.3.1 클래스의 분리](#1.3.1)
- [1.3.2 인터페이스의 도입](#interface)
- [1.3.3 관계설정 책임의 분리](#1.3.3)
- [1.3.4 원칙과 패턴](#1.3.4)
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

## 1.3.1
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


## 1.3.3

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


## 1.3.4

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
