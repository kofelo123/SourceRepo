- [도커](#docker)


-[error]
  -[Unable to find image](#unabletofindimage)
---


###### docker

도커 (kev)
-

일반적으로 개발에서 배포할떄 좋은방법은

따로 developement server에 배포하고(Production과 같은환경) - Test - Staging - 여러사람테스트 - UAT서버에 올림-사용자테스트 - Production

내 로컬에서는 돌아가지만 Production에서는 안돌아가거나 혹은 특정 서버를 내 OS에서 돌리기 힘들때
등등 머리아픈 상황이 있을 수 있다.

VertualMachine 은 자원메모리, VM간의설정 등이 머리아플수 있다.

Docker는 리눅스기반, 독립된환경을 제공한다(각각의 서버별로) - but 실제로는 내 로컬의 하나의 프로세스로 도는데, Docker를 쓸때 독립적으로, 환경이 분리된다. 서로 교류를 할려하면 할수있지만 기본적으로 분리된다.

IP도 따로 할당이 되는데 기본적으로는 호스트머신껄 쓴다.(포트처럼)

완전 독립이라 파일복사가 안되고, 따로 공유를 걸어야..

docker-compose : 두개의 컨테이너간의 커뮤니케이션을 하게 해준다.

기본적으로 도커는 리눅스에서 돌게 만들어져서, 기눅스기반 설치등이 편하다.

기존의 버전은 Virtualbox이용, 도커머신으로 리눅스 호스트를 사용했다고 한다.

도커 for 맥,윈도우는 HyperKit VM 이라는걸 사용한다.

virtualbox보다 가볍고 내가 쓰는 모스트머신이 로컬호스트가 된다.

명령어
```
docker info
docker version
docker image
docker ps

docker --version
docker-compose --version
docker-machine --version
```

도커 설치시 기본적으로 깔리는것들

docker
docker-compose:여러개 동시 돌리면서 교류(접속등)을 쉽게 해줄수있다.
docker-merchine: 도커는 원래 리눅스에서 돌아가게 만들어진건데 도커머신이 virtualbox를 통해 돌아가게 해주는것)

도커안에 리눅스 환경이 딸려있다고 볼수있고, 세팅으로 변경가능(기본이 알파인리눅스(경량,보안 리눅스))

[](https://hub.docker.com)
도커허브 홈페이지에서 이미 있는 도커의 설정을 참고할 수있다.(repositories) 같은것..
```
>docker images:이미지
```
```
> docker ps
: 돌아가고있는 프로세스


```
```
-명령어 --help
: 설명
```
```
 docker run -d -p 80:80 --name webserver nginx

-p는 포트를 도커포트80을 내 로컬 호스트 포트 80이랑 연결한다.

외부에서 80 리퀘스트가 들어오면 nginx로 연결 ,포워딩된다.

이로인해 이미지가 생성되니 image, ps 확인

docker stop webserver(name)
(특정 ps 스톱시키기)

docker rmi --force $(docker images -a -q)

이미지 모두삭제
```


---

###### unabletofindimage

Unable to find image 'nginx:latest' locally
docker: Error response from daemon: Get https://registry-1.docker.io/v2/: net/http: request canceled while waiting for connection (Client.Timeout exceeded while awaiting headers).
See 'docker run --help'.

서버에 request 가 cancel 되고 timeout 되어서 일어난 현상.

구글링후 setting의 네트워크에서 fix 8:8:8:8 로 체크해주니까 잘되었다.



---

```
> docker ps -a (종료된것까지 )
```
