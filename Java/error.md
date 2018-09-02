- [ZipException: invalid LOC header](#8623_94)


---

Removing obsolete files from server..
Could not clean server of obsolete files: null
java.lang.NullPointerException

이클립스의 server.xml을 수정했다가 에러나서 롤백 해놨는데

이것이 작업환경인 이클립스가 아닌 실제 베포하는

.metadata/.plugins/org.eclipse.wst.server.core 이쪽에서 실제로 수행되는 쪽인거같은데

이클립스없이 서버로 배포할떄는 war가 풀리고 이클립스에서는 워크스페이스의 이쪽에서 풀리는것같다

이쪽에서 실제로 배포되는 수행되는 server.xml이  롤백이 안되고 오류코드 그대로 수정안되고있었다.






---

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
- [프로젝트 전체가 404에러 날떄](#8624_111)

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
