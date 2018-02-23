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
- [equals와 ==차이](#equals)
- [JVM 메모리구조](#jvm)
- [오버로딩,오버라이딩](#overloadingoverriding)
- [상속](#inheritance)
- [다형성](#polymorphism)
- [의존주입](#dependencyinjection)
- [인터페이스](#interface)
- [abstract](#abstract)
- [static](#static)
- [예외에 대해](#exception)
- [String,StringBuffer,StringBuilder 차이](#stringelse)

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

## equals

 equals는 객체가 가지고 있는 내용(값)비교
==는 객체가 같은 참조(가리키는 주소)인지 참조

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
