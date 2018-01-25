


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
