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

- [boot 1장](#180826_2)
