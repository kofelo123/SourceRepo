
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



