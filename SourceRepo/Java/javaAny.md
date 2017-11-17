## javaAny
- [replaceAll](#replaceall)
- [공백 유효성검사](#check-null)
- [getSession](#getsession)

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
