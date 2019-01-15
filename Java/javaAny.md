## javaAny
- [replaceAll](#replaceall)
- [공백 유효성검사](#check-null)
- [getSession](#getsession)
- [context.getRealpath()](#getrealpath)
- [DecimalFormat()](#decimalformat)
- [ellipsis(...parameter(다중파라미터받기))](#ellipsis)
- [.replace](#replace)
- [.substring](#substring)
- [주석](#comment)
- [equals와 ==차이](#equalsidentity)
- [JVM 메모리구조](#jvm)
- [오버로딩,오버라이딩](#overloadingoverriding)
- [상속](#inheritance)
- [다형성](#polymorphism)
- [의존주입](#dependencyinjection)
- [인터페이스](#interface)
- [abstract 추상](#abstract)
- [static](#static)
- [예외에 대해](#exception)
- [String,StringBuffer,StringBuilder 차이](#stringelse)
- [hashCode()](#hashcode)
- [쓰레드](#thread)
- [enum](#enum)
- [지네릭스](#generics)
- [컬렉션](#collection)
- [Map(HashMap,Hashtable)](#map)
- [set(HashSet,TreeSet)](#set)
- [iterator](#iterator)
- [List(ArrayList,LinkedList,Stack,Queue)](#list)
- [javadoc](#javadoc)
- [주석처리에 대한것](#comment)
- [문자열 포함여부 -(contains,indexOf, maches)](#80612103)
- [프로젝트변경(java->maven)](#8062190)
- [자바 5대원칙](#180819_2)
- [50% 확률로 발생시키기](#180823_2)
- [StringBuffer와 StringBuilder차이](#181224_2)

- [자바 람다식에 대해](#181004_1)
- [자바에서 html코드 활용할떄](#181112_7)
- [여러 테이블로 부터 return 되는 값을 위한 Map](#181112_11)
- [indexOf( ), substring( )을 이용해 문자열 자르기](#181129_5)

- [error]
    - [ZipException: invalid LOC header](#8623_94)
    - [프로젝트 전체가 404에러 날떄](#8624_111)
    - [Removing obsolete files from server..](#0623_100)
    
    - [에러처리가 안되어있다는 메시지](#181009_4)

### replaceAll
> //모든 \n을 <b.r/>으로 변경한다.(textarea에서 개행시 \n으로 되는것을 변환하는 코드에서)  

```java
stringvar.replaceAll("\n", "<br/>")
```



### Check null

```java

if(dong!=null && dong.trim().equals("")==false){

}
```

### getSession

>1. getSession(), getSession(true)
 >- HttpSession이 존재하면 현재 HttpSession을 반환하고 존재하지 않으면 새로이 세션을 생성합니다

>2. getSession(false)
> - HttpSession이 존재하면 현재 HttpSession을 반환하고 존재하지 않으면 새로이 생성하지 않고 그냥 null을 반환합니다

```java
HttpSession session=request.getSession(false);
if(session!=null){
  session.invalidate();
}    
```
---

### getRealPath

```java

public void uploadFile(HttpServletRequest request) throws IOException {

		HttpSession session = request.getSession();

		int sizeLimit = 5 * 1024 * 1024;
		String savePath = "/resources/product_images";

		ServletContext context = session.getServletContext();
		String uploadFilePath = context.getRealPath(savePath);

		//(cos.jar에 대한 dependency 넣고 쓰는것).
		MultipartRequest multi = new MultipartRequest(request, uploadFilePath, sizeLimit,"UTF-8", new DefaultFileRenamePolicy() );
	}

```
savePath = "/resources/product_images";
context.getRealPath(savepath)
getRealPath의 기본경로는 아래 사진처럼webapp이 기본 기준이다.

![img](https://drive.google.com/uc?export=view&id=1gTBg4CMFx2qbECzwWPHe4KxkDWPoHEyu)

---

### decimalformat

NumberFormat 클래스와는 달리, DecimalFormat 클래스는 new 연산자를 사용하여 객체를 생성한다.



DecimalFormat 클래스는 format 메소드를 사용하여 특정 패턴으로 값을 포맷할 수 있다. (반환 값 String)



지정할 수 있는 패턴 형식은 '0' 과 '#'을 사용 하여 지정한다.



예를 들어, '0.###', '000.###', '00.#' 등으로 패턴을 지정할 수 잇다.



'0' 은 해당 자리수는 값이 최소한으로 있어야 되며, #은 최대한 해당 소수점 자리수 까지(반올림하여) 보여준다.



예를 들어, 78.53981633 일 경우



'0.###' : 78.54  (78.540 이므로 0은 출력되지 않는다.)

'000.##' : 078.54

'00.#' : 78.5
---
### ellipsis

ellipsis (...) identifies a variable number of arguments, and is demonstrated in the following summation method.
ellipsis 라고 불리는 녀석인 것 같다.
이녀석의 특징은 메소드 등에서 동일한 객체의 파라메터(실행에 필요한 변수, 설정변수?)들을 처리할 때, 메소드마다 파라메터의 갯수를 늘려가며 설정하는 대신,
public void method(Int... args) {}  

로 설정해두면, int 형의 파라메터를 몇개를 받아도 처리가 가능하다.

---

### replace

.replace() 메소드는 스트링의 특정부분을 바꾸는 역할을 한다.


이하 레퍼런스 참조
```
replace

public String replace​(char oldChar,
                      char newChar)
Returns a string resulting from replacing all occurrences of oldChar in this string with newChar.
If the character oldChar does not occur in the character sequence represented by this String object, then a reference to this String object is returned. Otherwise, a String object is returned that represents a character sequence identical to the character sequence represented by this String object, except that every occurrence of oldChar is replaced by an occurrence of newChar.

Examples:

 "mesquite in your cellar".replace('e', 'o')
         returns "mosquito in your collar"
 "the war of baronets".replace('r', 'y')
         returns "the way of bayonets"
 "sparring with a purple porpoise".replace('p', 't')
         returns "starring with a turtle tortoise"
 "JonL".replace('q', 'x') returns "JonL" (no change)

Parameters:
oldChar - the old character.
newChar - the new character.
Returns:
a string derived from this string by replacing every occurrence of oldChar with newChar.
```
---

### substring

1.문자열.substring(index) 문자열의 index부터 뒤의 문자를 잘라서 반환한다.
```
substring
public String substring​(int beginIndex)
Returns a string that is a substring of this string. The substring begins with the character at the specified index and extends to the end of this string.
Examples:

 "unhappy".substring(2) returns "happy"
 "Harbison".substring(3) returns "bison"
 "emptiness".substring(9) returns "" (an empty string)

Parameters:
beginIndex - the beginning index, inclusive.
Returns:
the specified substring.
Throws:
IndexOutOfBoundsException - if beginIndex is negative or larger than the length of this String object.
```
2.문자열.substring(index1,index2)  인덱스1부터 인덱스2까지를 잘라서 반환한다.
```
substring
public String substring​(int beginIndex,
                        int endIndex)
Returns a string that is a substring of this string. The substring begins at the specified beginIndex and extends to the character at index endIndex - 1. Thus the length of the substring is endIndex-beginIndex.
Examples:

 "hamburger".substring(4, 8) returns "urge"
 "smiles".substring(1, 5) returns "mile"

Parameters:
beginIndex - the beginning index, inclusive.
endIndex - the ending index, exclusive.
Returns:
the specified substring.
Throws:
IndexOutOfBoundsException - if the beginIndex is negative, or endIndex is larger than the length of this String object, or beginIndex is larger than endIndex.
```
---
## comment

 ![주석](https://drive.google.com/uc?export=view&id=1wetn4CViTEC2GF-xEiIqcREbPKeTkAJm)

 ---

## equalsidentity

 equals는 객체가 가지고 있는 내용(값)비교
==(identity)는 객체가 같은 참조(가리키는 주소)인지 참조

---

## jvm

JVM의 메모리구조

- Static영역-클래스의 놀이터(java.lang을 기본으로, + 사용클래스와 패키지등이 있는곳)
- Stack영역-메서드가 있는곳으로 중괄호가 생길때 마다 스택프레임이 생겨 메모리할당
- Heap영역- 객체가 있는곳이다.

---

## overloadingOverriding

오버로딩(overloading)
: 메소드 이름은 같으나 매개변수의
타입,갯수가 다른것.

오버라이딩(overriding)
:상속받은 부모클래스의 메소드를
자식클래스에서 재정의하는것
-> 메서드 이름이 같아야하며,매개변수,리턴타입도 같아야한다.

---

## inheritance

상속(Inheritance)
:자식 클래스는 부모 클래스에 존재하는 멤버변수 및 메소드를 모두 상속받는다.
부모클래스의 변화는 자식 클래스에 영향을 바로미친다.(이런점때문에 위험할수있음)
반대로 자식 클래스의 변화는 부모 클래스에게 영향을 주지 못한다.

부모클래스의 메소드를 자식클래스에서 오버라이딩 해서 쓸 수 있다.

 ![](https://drive.google.com/uc?export=view&id=19vfWueTPzml0K-uuEA0HxfivT0BFT48L)

 ---


## static
static 변수
:해당 클래스의 모든 객체들에 의해 공유된다.(같은 메모리 주소값을 바라본다.)
변하지 않고 여러객체에서 같이 쓸때는
static final을 쓴다.
메모리 할당을 한번만 하게 되어 메모리사용의 이점을 볼 수 있다.

static 메소드
:객체가 생성되지 않은 상태에서 호출 되는 메소드.
객체안에서만 존재하는 인스턴스 변수 및 메소드를 내부에서 사용할 수없다.
(static메소드안에서 말하는듯)
클래스명만 있어도 된다.''

---

## abstract
abstract - 추상

선언부만 작성하고 실제 내용은 구현하지 않은것.

abstract가 사용될 수 있는 곳 - 클래스,메소드

abstract 클래스: 클래스내에 추상메서드가 반드시 하나이상 선언되어 있음을 의미한다.
객체를 생성할 수 없다.
슈퍼클래스로 사용할 수 는 있으며, 추상메소드를 사용하기 위해서는 반드시 해당 메소드를 재정의 해야한다.

abstract 메서드: 선언부만 작성하고 구현부는 작성하지 않은 추상 메서드임을 알린다.
추상메소드는 단지 슈퍼클래스의 용도이며, 일반메소드도 포함될 수 있으며 반드시 추상메소드를 구현해야한다.

abstract 슈퍼클래스를 상속받은 서브클래스에서
추상메소드를 구현하지 않으면 일반 클래스가 아니라 추상클래스가 된다고 한다.

---

## interface
인터페이스
:추상클래스의 극단적인 경우이다.
인터페이스는 추상메소드와 상수들로만 이루어져 이쓰며, 메소드의 선언만 가능하다.
모든 멤버변수는 public static final이어야 하며 생략가능
모든 메소드는 public abstract 이어야 하며 생략가능
인터페이스를 사용하기 위해서는 implements 키워드를 사용해야한다.
서로 다른 클래스를 연결시켜주는 연결 장치라고 보면 된다.
타입을 묶는 역할을 한다.
~able로 끝나는게 많다.

>(thearc)"자바 소스상에서 타입을 인터페이스로 두기 때문에 xml에서 원하는 모듈을 변경해도 자바소스상에서는 아무런 수정을 할 필요가 없어지므로 유지보수에 강점을 지닌다.(종속적이지 않은 결합도가 느슨한 코드가 된다.)

---

## 캡슐화

캡슐화
:접근제어자 를 통해 외부에서 접근(함부러변경할수없게)과 내부에서 보호해야할것을 구분하는것.정보은닉.

---

## dependencyinjection
의존주입

>소프트웨어의 유지보수측면에서 중시되는 높은응집도와 낮은결합도 중에 의존주입은 결합도를 낮추는 역할을 한다고 볼 수 있는데,
자바코드상에서 new 의존클래스를 명시해주지 않음으로 인해서 종속적이지 않고
자바코드에서 인터페이스를 타입으로 두고 외부 xml에서 구체적인 의존모듈을 주입함으로 인해서 유지보수에 강점을 가진다
또, 배포후에 class파일만 남게 되어 자바소스상에서 수정하기 힘든데, 의존주입을 통해 xml에서 쉽게 수정이 가능하다.

클래스 자신은 타입으로 된 객체나 인스턴스에 대해 구체적으로 어떤 오브젝트를 써야하는지 알지 몰라도 된다. 런타임시점에서 적용된다.(스프링설정파일에 의해서)
---


## polymorphism

다형성(Polymorphism)
:하나의 클래스나 메소드가 다양한 형태로 가질수 있는것.

메소드:오버로딩,오버라이딩
클래스:상속,인터페이스

---


## exception

예외
 ![](https://drive.google.com/uc?export=view&id=1T2cPKL8sDroSYoFE2E2ilggJfEWP8zcB)

프로그램 오류에는
컴파일에러,런타임에러 정도로 나눌수있고
프로그램 실행(런타임)시 발생할 수 있는 프로그램 오류로 에러와 예외 두가지로 구분한다.

에러는 코드로 수습될 수 없는 심각한 오류(메모리부족,스택오버플로우 등)
에러는 프로그램의 비정상적인 종료를 막을 길이 없다.
예외는 발생하더라도 프로그래머가 이에 대한 적절한 코드를 미리 작성해 놓음으로써 비정상적인 종료를 막을수 있다.
수습가능한 미약한 오류를 말한다.
(런타임 Exception,IOException,Class notfound Exception 등)
 ![](https://drive.google.com/uc?export=view&id=18C3pryCaN5d_3aJab6HvnD183YniJSVk)

 ![](https://drive.google.com/uc?export=view&id=1Pt_hPrYCRQs_5hVqm1Vgq-Bgg9jBWhBH)
1.예외복구는 직접 try catch finally 해줘서 처리하는것
```
int maxretry = MAX_RETRY;  
while(maxretry -- > 0) {  
    try {
        // 예외가 발생할 가능성이 있는 시도
        return; // 작업성공시 리턴
    }
    catch (SomeException e) {
        // 로그 출력. 정해진 시간만큼 대기
    }
    finally {
        // 리소스 반납 및 정리 작업
    }
}
throw new RetryFailedException(); // 최대 재시도 횟수를 넘기면 직접 예외 발생  
```
2.예외처리 회피
```
public void add() throws SQLException {  
    ... // 구현 로직
}
```
는  간단해 보이지만 아주 신중해야하는 로직이다. 예외가 발생하면 throws를 통해 호출한쪽으로 예외를 던지고 그 처리를 회피하는 것이다. 하지만 무책임하게 던지는 것은 위험하다. 호출한 쪽에서 다시 예외를 받아 처리하도록 하거나, 해당 메소드에서 이 예외를 던지는 것이 최선의 방법이라는 확신이 있을 때만 사용해야 한다.
3.예외전환
```
catch(SQLException e) {  
   ...
   throw DuplicateUserIdException();
}
```
예외를 잡아서 다른 예외를 던지는 것이다. 호출한 쪽에서 예외를 받아서 처리할 때 좀 더 명확하게 인지할 수 있도록 돕기 위한 방법이다. 어떤 예외인지 분명해야 처리가 수월해지기 때문이다. 예를 들어 Checked Exception 중 복구가 불가능한 예외가 잡혔다면 이를 Unchecked Exception으로 전환하여서 다른 계층에서 일일이 예외를 선언할 필요가 없도록 할 수도 있다.

[참고사이트](http://www.nextree.co.kr/p3239/)
---



## stringelse

String , StringBuffer, StringBuilder
:
1) String

먼저 String과 다른 클래스(StringBuffer, StringBuilder)의 차이점은 두 문자열 클래스의 아주 기본적인 차이는 String은 immutable(불변), StringBuffer는 mutable(변함)에 있다.

String은 문자열을 대표하는 것으로 문자열을 조작하는 경우 유용하게 사용할 수 있다. 문자열, 숫자, char 등은 concat할때는 StringBuffer, StringBuilder를 사용할 수 있다. 단, 복잡한 경우 의미가 있고, 단순한 경우에는 굳이 StringBuffer, StringBuilder를 쓰지 않고 +연산자를 활용해 직접 합지면 된다.

String 객체는 한번 생성되면 할당된 메모리 공간이 변하지 않습니다. + 연산자 또는 concat 메서드를 통해 기존에 생성된 String 클래스 객체 문자열에 다른 문자열을 붙여도 기존 문자열에 새로운 문자열을 붙이는 것이 아니라,

새로운 String 객체를 만든 후, 새 String 객체에 연결된 문자열을 저장하고, 그 객체를 참조하도록 한다. (즉, String 클래스 객체는 Heap 메모리 영역(가비지 컬렉션이 동작하는 영역)에 생성. 한번 생성된 객체의 내부 내용을 변화시킬 수 없다. 기존 객체가 제거되면 Java의 가비지 컬렉션이 회수한다.)



String 객체는 이러한 이유로 문자열 연산이 많은 경우, 그 성능이 좋지 않다.


하지만, Immutable한 객체는 간단하게 사용가능하고, 동기화에 대해 신경쓰지 않아도 되기때문에(Thread-safe),  내부 데이터를 자유롭게 공유 가능하다.



2) StringBuffer와 StringBuilder


StringBuffer/StringBuilder는 String과 다르게 동작한다

문자열 연산 등으로 기존 객체의 공간이 부족하게 되는 경우,


기존의 버퍼 크기를 늘리며 유연하게 동작한다. StringBuffer와 StringBuilder 클래스가 제공하는 메서드는 서로 동일하다.



두 클래스의 차이점은 바로 동기화 여부이다.

- StringBuffer는 각 메서드별로 Synchronized Keyword가 존재하여, 멀티스레드 환경에서도 동기화를 지원.
- 반면, StringBuilder는 동기화를 보장하지 않음.



그렇기때문에 멀티스레드 환경이라면 값 동기화 보장을 위해 StringBuffer를 사용하고,


단일스레드 환경이라면 StringBuilder를 사용하는 것이 좋다. 단일 스레드환경에서 StringBuffer를 사용한다고 문제가 되는 것은 아니지만, 동기화 관련 처리로 인해 StringBuilder에 비해 성능이 좋지 않다.



String은 짧은 문자열을 더할 경우 사용한다.

StringBuffer는 스레드에 안전한 프로그램이 필요할 때나, 개발 중인 시스템의 부분이 스레드에 안전한지 모를 경우 사용하면 좋다.

StringBuilder는 스레드에 안전한지 여부가 전혀 관계 없는 프로그램을 개발할 때 사용하면 좋다.


단순히 성능만 놓고 본다면 연산이 많은 경우, StringBuilder > StringBuffer >>> String 이다.

---

## hashcode

hashCode():객체를 구별하기 위해 고유한 정수값으로 출력시켜준다.(reference 주소를 반환)
equals()가 true이면 hashCode()의 값은 같다.
하지만 hashCode()의 값이 같다해서 두 객체가 같은 객체라고는 볼 수 없다.
equals 는 두 객체의 내용이 같은지, 동등성(equality) 를 비교하는 연산자
hashCode 는 두 객체가 같은 객체인지, 동일성(identity) 를 비교하는 연산자 (==과 같은 의미일듯)

---

## collection

컬렉션 프레임워크: 데이터 군을 저장하는 클래스들의 표준화된 설계


- List: 순서가 있는 데이터의 집합, 데이터 중복 허용
ex)대기자 명단
구현클래스:ArrayList,LinkedList,Stack,Vector 등

- Set:순서를 유지하지 않는 데이터의 집합, 데이터의 중복 허용X
ex)양의 정수집합,소수의집합
구현클래스: HashSet,TreeSet 등

- Map: 키와 값의 쌍으로 이루어진 데이터의 집합
순서는 유지되지 않으며, 키는 중복을 허용하지 않고, 값은 중복을 허용한다.
ex)우편번호, 지역번호(전화번호)
구현클래스: HashMap, TreeMap,Hashtable,Properties 등

컬렉션 클래스 정리  & 요약
 ![](https://drive.google.com/uc?export=view&id=1q4GgbK_AwpnkRLcvx9Gz1257TgRTTm4i)

 ![](https://drive.google.com/uc?export=view&id=1-b8lppslmNWiwlnYNpnHTyxB27azZaPy)

---


## list

ArrayList: vector를 개선한것으로 기능적으로 거의동일하다.
기존의 호환성떄문에 vector를 남겨둔것이고, 현재는 ArrayList를 사용하는것이 권장된다.
Object 배열을 이용해서 데이터를 순차적으로 저장한다.

LinkedList:

기존의 배열은 두가지 단점이 있다.

1.크기를 변경할수 없다.(메모리 낭비 가능성)
2.비순차적 데이터의 추가 또는 삭제에 시간이 많이 걸린다.
배열의 중간에 데이터를 추가하면, 빈자리를 만들기 위해 다른데이터들의 복사해서 이동해하는 번거로움

위의 배열의 단점을 보완하기 위해 링크드 리스트가 생겼다.

ArrayList vs LinkedList
1.순차적으로 추가/삭제 하는 경우는 ArrayList>LinkedList

2.중간 데이터를 추가/삭제 하는 경우는 ArrayList<LinkedList

배열은 각 요소들이 연속적 메로리 존재-> 간단 계산으로 n번째 데이터 주소를 얻어서 바로 읽을수 있다.
그에반해 링크드 리스트는 불연속적 위치한 요소들이 연결되어있으므로 차례로 n번쨰 데이터로 까지 따라가야 해서 데이터양이 많을수록 느려진다.

읽기: ArrayList >LInkedList
추가/삭제:ArrayList<LinkedList

데이터의 개수가 변하지 않는 경우라면 ArrrayList가 최상의 선택이다. 변경이 잦다면 LinkedList를 사용하는것이 더 나은선택이다.

(변경이 있을때는 배열보다 ArrayList가 좋지만 변경이 빈번할때는 ArrayList도 좋지만 LinkedList가 더좋다.순서대로는 ArrayList가 빠르다)

Stack과 Queue

스택: 마지막에 저장한 데이터를 가장 먼저 꺼내게 되는 LIFO(Last In First Out)구조로 되어 있다.
ArrayList같은 배열기반 컬렉션클래스로 구현하는게 적합
(LIFO라서 배열의 큰 변화없이 바로처리가능)

큐:처음저장한 데이터를 가장 먼저 꺼내게 되는 FIFO(First IN First Out)
LinkedList로 구현하는것이 적합
(배열기반의 컬렉션으로 구현하면 데이터를 꺼낼떄 마다 빈공간을 채우기 위해 데이터복사가 발생)

---


## iterator

Iterator
:컬렉션에 저장된 요소들을 읽어오는 방법을 표준화
메소드
- boolean hasNext():읽어 올 요소가 남아있는지 확인한다. 있으면 true 없으면, false를 반환

- Object next():다음 요소를 읽어 온다.  next()하기전에 hasNext()해서 다음요소가 있는지 확인해야 안전하다.

- void remove() : next()로 읽어 온 요소를 삭제한다. next()를 호출한 다음에 remove()를 호출해야한다.(선택적 기능)

---


## set

HashSet
:Set인터페이스를 구현한 가장 대표적인 컬렉션이며, Set의 특징처럼 중복된 요소를 저장하지 않는다.
저장순서를 유지하지 않으므로 저장순서를 유지하고자 한다면 LinkedHashSet을 사용해야 한다.

```java
Set<Integer> hashSet= new HashSet<>();

hashSet.add(1);
hashSet.add(2);

Iterator iterator = hashSet.iterator();
while(iterator.hasNext()){
	System.out.println(iterator.next());
}
```

TreeSet
:이진 검색 트리라는 자료구조의 형태로 데이터를 저장하는 컬렉션 클래스이다.
Set 인터페이스를 구현했으므로 중복된 데이터의 저장을 허용하지 않으며 정렬된 위치에 저장하므로 저자순서를 유지하지 않아도 된다.

---


## map

HashMap과 Hashtable

HashMap이 후에 나온것이지 떄문에 권장된다.
Map을 구현했으므로 키와 값을 묶어서 하나의 데이터(entry)로 저장한다는 특징을 갖는다.
해싱을 사용하기 때문에 많은 양의 데이터를 검색하는 데 있어서 뛰어난 성능을 보인다.

키는 저장된 값을 찾는데 사용되는 것이기 때문에 유일해야한다.(키는 중복x)

Hashtable은 예전것이며,호환때문에 있지만 HashMap의 하위호환

해싱:해시함수를 이용해서 데이터를 해시테이블에 저장하고 검색하는 기법.->원하는 데이터를 빠르게 찾음.

TreeMap
:TreeMap은 이름에서 알 수 있듯이 이진검색트리의 형태로 키와 값이 쌍으로 이루어진 데이터를 저장한다.
검색과 정렬에 적합한 컬렉션 클래스이다.
대부분의 경우 HashMap이 TreeMap보다 더 뛰어나므로 HashMap을 사용하는것이 좋다. 다만 범위검색이나 정렬이 필요한 경우에는 TreeMap을 사용한다.


---


## generics

지네릭스(Generics)
:다양한 타입의 객체를 다루는 메서드나 컬렉션 클래스에 컴파일 시의 타입체크를 해주는 기능.
객체의 타입을 컴파일 시에 체크하기 때문에 객체의 타입 안정성을 높이고 형변환의 번거로움이 줄어든다.
타입 안정성을 높인다는것은 의도하지 않은 타입의 객체가 저장되는 것을 막고, 저장된 객체를 꺼내올 때 원래의 타입과 다른 타입으로 잘못 형변환되어 발생할 수 있는 오류를 줄여준다.

장점

1.타입의 안정성을 제공

2.타입체크와 형변환을 생략할 수 있으므로 코드가 간결

-> 다룰 객체의 타입을 미리 명시해둠으로써 번거로운 형변환을 줄여준다

지네릭 클래스의 선언
:지네릭 타입은 클래스와 메서드에 선언 할 수 있다.


---


## enum

enum:열거형

:클래스처럼 보이게 하는 상수
서로 관련 있는 상수들을 모아 심볼릭한 명칭의 집합으로 정의한것

---


## thread

쓰레드

프로세스:실행시 OS로 부터 필요한 자원(메모리)을 할당받아 실행중인 프로그램이다.

프로세스= 메모리(자원)+쓰레드로 구성

1프로세스=1이상의쓰레드가 존재

2개이상 쓰레드를 가지면 멀티쓰레딩(멀티태스킹,병렬처리)

멀티쓰레딩 장점
:CPU 사용률 향상
자원을 효율적으로 사용
사용자에 대한 응답성 향상
작업 분리,코드간결

1.Tread클래스 상속
2.Runnable 인터페이스 구현


---


###### javadoc

javadoc
-

JAVA API문서와 같은것이 javadoc으로 만들어진 문서라고 볼 수있다.
```
/** */과 같은 표시가 있다면 인식하고 도움말을 작성하게 된다.
```
주석문 앞에는 항상 * 마크가 있어야 인식한다.

앞에 @로 표시된것은 각 이미 지정된 예약어로써의 기능을 한다. @ 파라메터 설명과 같은 형태로 쓰인다.

@author 개발자
@exception 메소드에서의 예외 확인
@param 메소드의 매개변수
@return 메소드의 반환값
@see 다른 주제에 관한 링크 지정
@serial 직렬화 필드
@since 릴리즈 기록
@throws 메소드에서의 예외
@version 클래스의 버전

* javadoc 만들기

export -> java -> javadoc

* error

error massage:  unmappable character for encoding MS949

-> javadoc 생성시 next해서 마지막페이지에 VM option에 -encoding UTF-8 기입

이런옵션도 있다고한다.
-locale ko_KR -encoding UTF-8 -charset UTF-8 -docencoding UTF-8


---


######comment
주석처리에 대한것
-
1.메소드

```
	/**
	 * 정의된 메세지 조회
	 * @param code - 메세지 코드
	 * @return String
	 */
	public String getMessage(String code) {
		return getReloadableResourceBundleMessageSource().getMessage(code, null, Locale.getDefault());
	}

///

   /**
     * 조직정보를 코드형태로 리턴한다.
     *
     * @param 조회조건정보 vo
     * @return 조직정보 List
     * @throws Exception
     */
    public List<CmmnDetailCode> selectOgrnztIdDetail(ComDefaultCodeVO vo) throws Exception;
```

2.인스턴스
```
	/** 검색조건 */
    private String searchCondition = "";

    /** 검색Keyword */
    private String searchKeyword = "";
```
3.클래스
```
/**
 * 메시지 리소스 사용을 위한 MessageSource 인터페이스 및 ReloadableResourceBundleMessageSource 클래스의 구현체
 * @author 공통서비스 개발팀 이문준
 * @since 2009.06.01
 * @version 1.0
 * @see
 *
 * <pre>
 * << 개정이력(Modification Information) >>
 *
 *   수정일      수정자           수정내용
 *  -------    --------    ---------------------------
 *   2009.03.11  이문준          최초 생성
 *
 * </pre>
 */
```

4. js
```
/****************************************************************
 *
 * 파일명 : EgovBBSMng.js
 * 설  명 : 전자정부 공통서비스 로그관리 기능 사용 JavaScript
 *
 *    수정일      수정자     Version        Function 명
 * ------------    ---------   -------------  ----------------------------
 * 2009.03.10    이삼섭       1.0             최초생성
 *
 *
 * **************************************************************/
```
---



---


###### 80612103

문자열 포함여부 -(contains,indexOf, maches)
-

contains : 문자열에 검색하고자 하는 문자가 있는지 확인 (포함:true , 미포함: false)

indexOf : 문자열에서 검색하는 문자의 위치를 반환 (포함: 문자 위치, 미포함: -1)

matches :정규식을 이용하여 문자열을 검색한다. 
특정 문자열을 검색할때 사용하기 보다는 한글, 숫자 등과 같이 해당 형태의 텍스트가 존재하는지 확인할때 사용하면 좋다
(포함: true, 미포함:false)

```java
  // contains를 이용한 방법(true, false 반환)
        if(txt1.contains("나다"))
            System.out.println("문자열 있음!");
        else
            System.out.println("문자열 없음!");
         
         
        // indexOf를 이용한 방법
        if(txt2.indexOf("테스트") > -1)
            System.out.println("문자열 있음!");
        else
            System.out.println("문자열 없음!");
         
         
        // matches를 이용한 방법
        if(txt2.matches(".*테스트.*"))
            System.out.println("문자열 있음!");
        else
            System.out.println("문자열 없음!");
         
         
        // matches를 이용하여 정규 표현식으로 문자열에 숫자가 있는지 확인
        if(txt3.matches(".*[0-9].*"))
            System.out.println("숫자 있음!");
        else
            System.out.println("숫자 없음!");

```


---


###### 8062190

프로젝트변경(java->maven)
-

java project를 생성후 maven project 등으로 변환가능하다.

프로젝트 우클릭 -> configure -> Convert to Maven Project


-----------------------------------------

###### 180819_2

자바 5대원칙
-

@스프링 입문을 위한 자바객체지향의 원리와 이해


<자바 5대원칙:SOLID>

SRP - 단일 책임 원칙(Single Responsibiity Principle)
OCP - 개방 폐쇄 원칙(Open Closed Principle)
LSP - 리스코프 치환 원칙(Liskov Subsitution Principle)
ISP - 인터페이스 분리 원칙(Interface Segregation Principle)
DIP - 의존 역전 원칙(Dependency Inversion Principle)

5대원칙은 응집도를 높이고(High Cohesion), 결합도는 낮추라는(Loose Coupling)


결합도는 모듈(클래스)간의 상호 의존 정도로서 결합도가 낮으면 모듈 간의 상호 의존성이 줄어들어 객체의 재사용이나 수정,유지보수가 용이하다.

응집도는 하나의 모듈 내부에 존재하는 구성 요소들의 기능적 관련성, 응집도 높은 모듈=하나의 책임에 집중-독립성up으로 인해 재사용,수정,유지보수 용이

SOLID를 잘 녹여내면 상대적으로 이해하기 쉽고,리팩터링와 유지보수가 수월. 디자인패턴의 뼈대이며 스프링의 근간이기도 하다.


- SRP(단일 책임 원칙) -

db의 정규화 역시도 비슷한 느낌이라고 볼수있다.

메소드가 단일 책임원칙을 지키지 않을때 일어나는 대표적인 냄새가 분기처리를 위한 if문이다.

-> 추상메소드를 두는 추상클래스를 만든후 두개의 클래스에서 상속받아 클래스를 구현한다.


- OCP(개방 폐쇄 원칙) - 

"자신의 확장에는 열려 있고, 주변의 변화에 대해서는 닫혀 있어야 한다."


('이해하기 어려워서 여러 예를 봐야할것 같다. - > 토비책에도 있으니 볼것')

JVM의 인터페이스처럼 매개체가 있는것이 공통점인것으로 이해하면 될것같다.

042317


- LSP(리스코프 치환 원칙) -

"서브 타입은 언제나 자신의 기반 타입으로 교체할 수 있어야 한다."


          상속의 조건

상속=분류도(O)  / 조직도,계층도(X)

"하위 클래스 is a kind of 상위클래스"
"구현 클래스 is able to 인터페이스"

구현분류는 인터페이스 할수 있어야한다.
         Auto Closeable:자동으로 닫힐수 있어야 한다.
         Appendable     : 덧붙일 수 있어야 한다.
         Cloneable        : 복제할 수 있어야 한다.
         Runable           : 실행할 수 있어야 한다.

-ISP(인터페이스 분리 원칙)-

단일책임원칙(SRP)과 인터페이스 분할 원칙(ISP)은 같은 문제에 대한 두가지 다른 해결책.

특별한 경우가 아니면 SRP를 적용하는것이 더 좋은 해결책.

상속과 인터페이스 관점에서 상위클래스는 풍성 할 수록 좋고, 인터페이스는 작을수록 좋다.

사람(상위클래스) 김학생 = new 학생(하위클래스)

일때 사람클래스의 참조변수 김학생은 새로운 학생클래스 객체를 만든다?

이떄 상위클래스의 메소드,속성을 기본적으로 사용가능하고 하위클래스것을 쓰려면 캐스팅해야..


-DIP(의존 역전 원칙)-

"자신보다 변하기 쉬운 것에 의존하지 마라"

OCP(개방폐쇄원칙)과 비슷한 개념이다.


-정리-

SRP(단일책임원칙):어떤 클래스를 변경해야 하는 이유는 오직 하나뿐이어야 한다.

OCP(개방폐쇄원칙):자신의 확장에는 열려 있고, 주변의 변화에 대해서는 닫혀 있어야한다.

LSP(리스코프 치환 원칙):서브 타입은 언제나 자신의 기반 타입으로 교체할 수 있어야 한다.

ISP(인터페이스 분리 원칙):클라이언트는 자신이 사용하지 않는 메서드에 의존 관계를 맺으면 안된다.

DIP(의존 역전 원칙): 자신보다 변하기 쉬운 것에 의존하지 마라.






-----------------------------------------

###### 180823_2

50% 확률로 발생시키기
-

if(Math.random() < 0.5)




-----------------------------------------

###### 181004_1

자바 람다식에 대해
-


OOP 언어인 자바에서 함수형 코드를작성 할 수있게 해준다.

간결하고 명확한 코드로 그 의도를 표현가능

놀라운 코드 생산성을 보임


메서드를 하나만 가지는 인터페이스를 함수형 인터페이스라고 한다.

자바에서는 객체를 생성해야 메소드를 전달가능하기 때문에, 매번 객체새성->메소드 전달.

이것을 해소하는게 람다

객체 자체를 직접 생성하지않고, 메소드에 @Override, 메소드명 이런것들 정의부분을 빼고 실제 구현부만 남긴다.


람다식은 다른말로 익명메서드라고 부르기도 한다.


### 람다식문법

(매개변수 목록) -> {실행문}



일반클래스 -> 익명클래스 -> 람다



일반 -> 익명

클래스의 정의부분 생략가능

인스턴스를 만들고 메소드를 호출하는것 밖에 안되지만, 그것을 생략하기 위해서 익명클래스


 ![](https://drive.google.com/uc?export=view&id=1Ksxr2uY8a7_ybaHU6SYIMNZnXKwVucV1)


익명 -> 람다

코드를 확줄여주고, 그로인해 가독성도 높아진다.


람다는 컴파일러가 유추할수 있는것들은 생략한다는 개념이다.



책에서 forEach를 람다로 썻다기보다.

Iterator.forEach() 라는게 람다식으로 쓸수있는 자바8부터 지원되는 메소드인것같다.

Iterator만 가능한듯,.

### for-each의 람다표현

```

ArrayList arrayList= new ArrayList<>(); 
for(int i=1; i<=100; i++) 
arrayList.add(i); 
 
//일반적인 for-loop 문 
for(Integer i: arrayList)
{ System.out.println(i); 
} 
 
//lambda식으로 표현한 for-loop문 
arrayList.forEach( i -> {System.out.println(i);});
```



-----------------------------------------

###### 181009_4

에러처리가 안되어있다는 메시지
-

Unhandled exception: java.lang.Exception

에러처리가 안되어있다는 메시지

try catch나 exception 던지는 처리가 안될때 위와같이 에러난다.




---

###### 0623_100

Removing obsolete files from server..
-

Removing obsolete files from server..
Could not clean server of obsolete files: null
java.lang.NullPointerException

이클립스의 server.xml을 수정했다가 에러나서 롤백 해놨는데

이것이 작업환경인 이클립스가 아닌 실제 베포하는

.metadata/.plugins/org.eclipse.wst.server.core 이쪽에서 실제로 수행되는 쪽인거같은데

이클립스없이 서버로 배포할떄는 war가 풀리고 이클립스에서는 워크스페이스의 이쪽에서 풀리는것같다

이쪽에서 실제로 배포되는 수행되는 server.xml이  롤백이 안되고 오류코드 그대로 수정안되고있었다.


---


###### 8623_94

ZipException: invalid LOC header
-

error message:
java.util.zip.ZipException: invalid LOC header (bad signature)

pom.xml에 특정 dependency 추가할때 에러생길수있다.

내부 dependency 의 jar파일 관련 에러

maven repository 내부 삭제하고 maven update project해주면 된다.


---


###### 8624_111

프로젝트 전체가 404에러 날떄
-

잘되던 프로젝트가 갑자기 실행시 모든 페이지에 404에러가 났다.

path관련문제, maven update, 여러가지 해봐도 안될때 ..

1. 톰캣서버에서 해당프로젝트를 삭제

2. 톰캣을 Clean

3. Project > Clean

4. 톰캣에서 해당 프로젝트 다시 Add

5. 톰캣 실행


정확한 이유는 모르겠으나, 톰캣 서버 내부에서의 잘못된 문제가 계속 해결안되고 얽혀있는것같다.

-----------------------------------------

###### 181112_7

자바에서 html코드 활용할떄
-


/// 사용1
@PostMapping("/admLogPost")
	@PreAuthorize("hasRole('ADMIN')")
	public String admLogPost(UserVO user,HttpServletResponse response,HttpSession session) throws Exception{
		
		UserVO vo=service.adminlogin(user);
		
		if(vo != null && vo.getUid().equals("admin")) {
			
			session.setAttribute("adminlogin", user);
			
			return "redirect:/admin/userlist";
			
		}else if(vo == null || !vo.getUid().equals("admin")){
		response.setContentType("text/html; charset=UTF-8");
		PrintWriter out = response.getWriter();
		out.print("<script>alert('관리자 아이디혹은 비밀번호가 일치하지 않습니다.');location.href='/thearc/user/login';</script>");
		out.flush();
		}
		return null;
	}
	
/// 사용2
```java
response.setContentType("text/html; charset=UTF-8");
            PrintWriter out = response.getWriter();
         /*   out.print("<script>");
            out.print("alert('해당 아이디의 비밀번호가 일치하지 않습니다.');");
            out.print("</script>");
            out.flush();*/
            out.print("<script type='text/javascript' src='/thearc/resources/bootstrap/js/jquery-1.10.2.min.js'></script>");
            out.print("<script>");
            out.print("$(function(){");
            out.print("var form = $('<form></form>');");
            out.print("form.attr({action:'/thearc/login' , method:'post'});");
            out.print("form.appendTo('body');");
            out.print("$('<input></input>').attr({type:'hidden',name:'username',value:'"+userVO.getUid()+"'}).appendTo(form);");
            out.print("$('<input></input>').attr({type:'hidden',name:'password',value:'"+userVO.getUpw()+"'}).appendTo(form);");
            out.print("$('<input></input>').attr({type:'hidden',name:'${_csrf.parameterName}',value: '${_csrf.token}'}).appendTo(form);");
            out.print("form.submit();");
            out.print("});");
            out.print("</script>");
            out.flush();
```

-----------------------------------------

###### 181116_11

여러 테이블로 부터 return 되는 값을 위한 Map
-

join에서 return될때 기존의 VO의 경우 한테이블 기준으로 VO가 되는 경우니까

두개의 테이블의 조인의 결과를 매핑할수없다. 그래서 이럴떄는 Map을 쓴다.

그런데 VO를 List로 가져오는경우

List<Map<String,String>>

이렇게 맵을 리스트로 감싸서 쓰면된다.


-----------------------------------------

###### 181129_5

indexOf( ), substring( )을 이용해 문자열 자르기
-

public class StringTest 
{
    public static void main(String args[])
    {
        
        // @를 기준으로 문자열을 추출할 것이다.
        String mail = "abced@naver.com";
        
        // 먼저 @ 의 인덱스를 찾는다 - 인덱스 값: 5
        int idx = mail.indexOf("@"); 
        
        // @ 앞부분을 추출
        // substring은 첫번째 지정한 인덱스는 포함하지 않는다.
        // 아래의 경우는 첫번째 문자열인 a 부터 추출된다.
        String mail1 = mail.substring(0, idx);
        
        // 뒷부분을 추출
        // 아래 substring은 @ 바로 뒷부분인 n부터 추출된다.
        String mail2 = mail.substring(idx+1);
      
        System.out.println("mail1 : "+mail1);
        System.out.println("mail2 : "+mail2);
    }

    
}
 


출처: http://all-record.tistory.com/118 [세상의 모든 기록]



-----------------------------------------

###### 181224_2

StringBuffer와 StringBuilder차이
-

String에 비해 버퍼와 빌더는 가변적으로 사용이 가능하다.

버퍼는 동기화가능 -> 멀티스레드 환경에서 사용하기 좋다. 

성능은 빌더가 더 좋으므로 스레드 환경이 아닐경우 빌더가 더 좋다.


