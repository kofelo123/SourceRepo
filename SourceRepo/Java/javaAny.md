## javaAny
- [replaceAll](#replaceall)
- [공백 유효성검사](#check-null)
- [getSession](#getsession)
- [context.getRealpath()](#getrealpath)
### replaceAll
> //모든 \n을 <b.r/>으로 변경한다.(textarea에서 개행시 \n으로 되는것을 변환하는 코드에서)  

```java
stringvar.replaceAll("\n", "<br/>")
```



### Check null

```java
if(dong!=null && dong.trim().equals("")==false){
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
