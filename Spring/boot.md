
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
https://stackoverflow.com/questions/28310964/project-lombok-the-method-is-undefined-for-the-type-geofence

-----------------------------------------

###### 180828_13

SPRING DATA JPA
-

[SPRING DATA JPA]

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

*페이징 처리와 정렬

쿼리메소드에서 페이지 처리할 수있는 Pageable 인터페이스와 정렬을 처리하는 Sort 인터페이스가 있다.

Pageable인터페이스를 구현한 클래스중에 PageRequest 클래스를 많이 사용한다.

*@Query 이용

DB에서 쿼리수행시 실행계획이라는것을 참고한다. -> 어떤 방식으로 실행될지 결정.

pk의 경우 색인작업을 통해 이미 레코드의 순서가 정해지게된다. -> 정렬시 색인(인덱스)를 이용해 빠른 접근

select * from tbl_boards where title='제목..177'

select * from tbl_boards where title='제목..177' and bno > 0

전자의 경우 풀 테이블스캔을 하게되고

후자의 경우 bno > 0 조건이 있어서 PK를 이용해서 데이터를 검색하는 인덱스 스캔을 하게된다.


이 wehre bno > 0 같은 조건 여부에 따라 이처럼 실행계획이 결정되지만, 쿼리 메소드에서는 이러한 조건을 표현하기가 어색하다.

-> 이럴떄 구체적인 조건 지정위해 쿼리메소드 대신에 @Query(쿼리 어노테이션)을 이용

어노테이션 안에 쿼리를 작성하게 된다.

@Query 에는 JPQL(객체쿼리) 사용하며, JPA에 사용하는 쿼리 랭귀지이다. SQL가 유사한 구문.


테이블대신 엔티티 타입이용, 칼럼명 대신에 엔티티속성 이용.


```java
repo.findByTitle("17")
.forEach(board -> System.out.println(board));
```

#내용에 대한 검색 처리 @Param

내용에 대한 검색 처리또한 동일 방식으로 처리가능.