
- [boot 1장](#180826_2)
- [SPRING DATA JPA](#180828_13)

-----------------------------------------

###### 180826_2

boot 1장
-

war - 톰캣 내장

xml 파일없이 생성되며, application.properties에서 설정..


Application.java의  main()메소드로 프로젝트 실행한다.

기본 클래스 org.zerock.Boot01Application을(를) 찾거나 로드할 수 없습니다.

-> jar 파일 메이븐에서 받다가 에러난것 같다. 그것을 빌드패스에서 확인해고 maven repository에서 해당 폴더들 삭제하고 다시 구동했다.


Failure to transfer org.apache.commons:commons-compress:jar:1.9 from https://repo.maven.apache.org/maven2 was cached in the local repository, resolution will not be reattempted until the update interval of central has elapsed or updates are forced. Original error: Could not transfer artifact org.apache.commons:commons-compress:jar:1.9 from/to central (https://repo.maven.apache.org/maven2): The operation was cancelled. Maven Configuration Problem

이것도 마찬가지.. 


롬복 설치하고 사용했는데 막상 VO를 사용하려하니 자동으로 setter 처리가 안되었다 -> stackoverflow -> version downgrading

```
<dependency>
			<groupId>org.projectlombok</groupId>
			<artifactId>lombok</artifactId>
			 <version>1.14.8</version>
			<optional>true</optional>
		</dependency>
```
[](https://stackoverflow.com/questions/28310964/project-lombok-the-method-is-undefined-for-the-type-geofence)

-----------------------------------------

###### 180828_13

SPRING DATA JPA
-

<SPRING DATA JPA>

JPA 이해를 위해 ORM(Object Relational Mapping)의 이해가 필요

객체지향의 객체와 데이터베이스의 개체 

한번에 처리하려는 시도 (매핑시킨다)

JPA는 ORM의 개념을 JAVA에서 구현하기 위한 스펙.


그 자체로는 스펙이기 때문에, 실제로 구현한 프레임워크의 존재 필요 ->(서블릿->톰캣에서 실행되듯이)

JPA를 구현한 구현체 중에 부트에서는 하이버네이트 사용

장점: DB와 독립적, 익숙해지면 빠른생산성

단점 : 학습해야,객체지향 설계, 특정 튜닝등에 대한 제약 가능성



<엔티티> : 데이터로 관리되는 하나의 대상 -> 상품,회사,지원 등

-> JPA에서는 하나의 클래스를 작성하는 의미

-> 때로는 인스턴스를 의미하는 경우도있다.

<엔티티매니저> : 엔티티 관리, 영속컨텍스트(Persistence Context)에 넣어두고관리




DataSource 설정 방법

1. application.properties 에서 설정

2. @Bean 같은 어노테이션으로 구성

--

부트 프로젝트 생성( 인텔리제이)

Create Project -> Spring Initializer 선택

---

[엔티티 클래스 설계]

jpa는 자동적으로 테이블 생성 기능가짐.

두가지방법 가능

1.sql로 테이블 생성 - 엔티티 클래스 생성

2. jpa로 클래스만 설계 - 자동으로 테이블 생성


{JPA 엔티티 클래스 생성작업}

1. 객체지향의 설계대로 클래스 설계

2. @ID, @Column 등으로 각종 제약조건 추가,설정

3. 엔티티간 연관관계 설정.



@Entity : 클래스가 엔티티 클래스임을 명시

@ID : 식별키(PK)

@GeneratedValue와 함께사용.


@GeneratedValue : 식별키 생성전략


Repository가 인터페이스를 방속받고 부모 의 어떤 메소드를 쓰면서 동작한다.



(참고)

빈 등록 에러 -> 인텔리제이 에러였을지도..
다시 키니까 없어졌따..



쿼리메소드: 메소드 이름만으로도 필요한 쿼리를 만들어낸다.
