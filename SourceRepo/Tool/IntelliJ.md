- [인텔리제이 - 깃허브연동](#intellijgithub)
- [톰캣 디플로이 미지정에러](#tomcatnotdeploy)
- [dispatcher-servlet.xml, applicationContext.xml](#intellijcontext)
---


###### intellijgithub

인텔리제이 - 깃허브연동
-

* Add

우클릭 - git - add

* Commit

우클릭 - git - Commit Directory

* Add 및 Commit시 변경된 파일의 Font-Color의 의미

Red - Workspace Files

Green - Staged Files

Blue - Modified Files

White - Commited Files

Gray - Deleted Files

* Push

우클릭 - Git - Repository - Push

* Clone

VCS -> Checkout from Version Control - Git


* 환경설정

1. File - Setting - Path지정 (\Git\bin\git.exe)

2. Git Repository 설정 (생성이 필요할떄)

 VCS - Import into Version Control - Create Git Repository

3. GItHub 계정등록 : File - Setting - VersionControl - GitHub

[참고블로그](http://secuinfo.tistory.com/entry/Intellij-Github-Link)

---


###### tomcatnotdeploy

톰캣 디플로이 미지정에러
-

인텔리제이에서 톰캣 스타트버튼을 누르면 아래처럼 리스타트,리빌드 등의 옵션이 있으나, 그냥 옵션없이 진행되는 경우는
톰캣에 디플로이먼트를 지정해주지 않아서 그렇다.

그결과 빈 프로젝트니 당연히 페이지를 찾을수없을 페이지만 웹에 띄우게 된다(404도 아님)

 ![](https://drive.google.com/uc?export=view&id=1OEVhPsokBqkOOUX2r60S53vcpBJbqM2E)


 ---
 - [메이븐 프로젝트 생성](#mavenproject)

 ###### mavenproject

 메이븐 프로젝트 생성
 -

 1. New Project - Maven - (Create from archetype(해제))

 2. Add Framework Support - Spring - Spring MVC 체크하기

 3. wel.xml 변경하기

 servlet-mapping의 url-pattern변경하기 : ```*.form -> /```

 4. 라이브러리 추가해주기

 Project Structure - Artifacts (이후에 pom.xml의 dependency 같은 부분에서도 lib를 수동으로 추가해줘야 하던데.. 설정이 따로있는건지..)

 5. dispatcher-servlet.xml에 추가하기

 ```xml
 <?xml version="1.0" encoding="UTF-8"?>
 <beans xmlns="http://www.springframework.org/schema/beans"
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xmlns:context="http://www.springframework.org/schema/context"
        xmlns:mvc="http://www.springframework.org/schema/mvc"
        xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
         http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-4.0.xsd
         http://www.springframework.org/schema/mvc http://www.springframework.org/schema/mvc/spring-mvc-4.0.xsd">

     <mvc:annotation-driven></mvc:annotation-driven> <!-- Annotation 활성화 -->
     <context:component-scan base-package="Controller"></context:component-scan> <!-- Component 패키지 지정 -->

     <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
         <property name="prefix" value="/WEB-INF/views/"></property>
         <property name="suffix" value=".jsp"></property>
     </bean>

 </beans>
 ```

 6. views 디렉토리 파일 만들기 & index.jsp 이동시키기

 7. Run Edit Configuration으로 Tomcat Server 추가하기

 Run - Edit Configuration

---

###### intellijcontext

dispatcher-servlet.xml, applicationContext.xml
-

인텔리제이와 STS는 기본적으로 스프링 MVC의 구조가 약간 다른부분도있고

스프링 컨텍스트 네임부터 달랐다.

STS의 controller 쪽의 처리와 view resolver 등이 존재하는 servlet-context는 (DispatcherServlet의 설정)
인텔리제이의 dispatcher-servlet.xml이 되고

STS의 비즈니스 계층, DAO 계층 등을 구성하는 root-context는  (ContextLoaderListener 라고부르는것같다.)
인텔리제이의 applicationContext.xml이 담당한다.

```xml
//인텔리제이 Deployment Descriptor ( web.xml)
<context-param>
    <param-name>contextConfigLocation</param-name>
    <param-value>/WEB-INF/applicationContext.xml</param-value>
</context-param>

<listener>
    <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
</listener>
<servlet>
    <servlet-name>dispatcher</servlet-name>
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
    <load-on-startup>1</load-on-startup>
</servlet>
<servlet-mapping>
    <servlet-name>dispatcher</servlet-name>
    <url-pattern>/</url-pattern>
</servlet-mapping>

```

기본적으로 <servlet-name>의 이름이 dispatcher면 /WEB-INF/dispatcher-servlet.xml 파일을 로딩한다
그러나 STS처럼 이름이 다르거나, 여러개 지정 할경우 아래처럼 initParam해서 적어준다.

```xml
// STS web.xml
<context-param>
   <param-name>contextConfigLocation</param-name>
   <param-value>/WEB-INF/spring/root-context.xml</param-value>
 </context-param>

 <listener>
   <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
 </listener>
 <servlet>
   <servlet-name>appServlet</servlet-name>
   <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
   <init-param>
     <param-name>contextConfigLocation</param-name>
     <param-value>/WEB-INF/spring/appServlet/servlet-context.xml</param-value>
   </init-param>
   <load-on-startup>1</load-on-startup>
 </servlet>
 <servlet-mapping>
   <servlet-name>appServlet</servlet-name>
   <url-pattern>/</url-pattern>
 </servlet-mapping>

```
