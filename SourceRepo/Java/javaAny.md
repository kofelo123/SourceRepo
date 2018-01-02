## javaAny
- [replaceAll](#replaceall)
- [공백 유효성검사](#check-null)
- [getSession](#getsession)
- [context.getRealpath()](#getrealpath)
- [DecimalFormat()](#decimalformat)
- [ellipsis(...parameter(다중파라미터받기))](#ellipsis)
- [.replace](#replace)
- [.substring](#substring)
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
![substring](https://drive.google.com/uc?export=view&id=1ZEfgvu2HSUhgxti-S8npxOiF5EiJcLqE)

---

### substring

문자열.substring(index) 문자열의 index부터 뒤의 문자를 잘라서 반환한다.

![substring](https://drive.google.com/uc?export=view&id=1OPZZiOIVlWbQplZy2R8wwNBOLfZu6co_)
