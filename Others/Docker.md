- [도커](#docker)

(#2)
- [도커의 개요](#180814_5)
- [도커설치및 사용](#180814_6)


---
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





###### 180814_5

도커의 개요
-

VM와 도커 차이

 ![](https://drive.google.com/uc?export=view&id=10FAjYijZEklBnX3GrZXch1fdy7JCZzym)

-VM-
VM은 Host OS(윈도우 등)에서 GuestOS(리눅스등을 설치)가 돌아가면서 서로 의존적이지 않지만, GuestOS가 HostOS를 거쳐서 사용되므로 느릴수밖에 없다.

뿐만아니라 Guest OS 자체가 완전히 새로운 운영체제로써 존재해야 하기 때문에 가벼운 OS를 설치하려해도 용량이 크다.

-도커의 Containers-

컨테이너는 가상머신 대신 도커엔진 위에서 동작한다

별도의 GUEST OS가 존재하지 않아 성능이 개선된다. 메모리,용량도 적게 차지.

다만 기본적으로 사용하고 있는 운영체제와 도커의 컨테이너간의 의존성이 존재한다.

그래서 각종 보안 문제로 공격 받은적이 있다.

윈도우 위에 도커엔진위의 컨테이너도 윈도우로 해야 했지만(의존) 최근에는 구분이 모호해 지고 있다.

VM에 비해 파일 크기가 작아 이미지를 실행하기가 빠르다.

대표적 리눅스 배포판중 알파인은 5MB,
컨테이너를 이용하면 크기가 200MB 전후로 줄어들 수 있어서 초 경량 배포도구 라고 할 수 있다.

/

도커 엔진의 핵심로직은 구글의 go언어로 개발되었다. 포인터같은 기능을 지원하고 garbage collector ,class 같은 기능도있다. 
깃서버도 go언어로 만들어졌다. 빠른 성능의 서버를 개발하는데 쓰인다.


- 도커 패러다임

'변경 불가능한 인프라'

:sw의 잦은 수정은 불안정성을 가져온다.

그래서 이력관리의 중요성이 부각되는데

도커는 아예 서버를 구축이후 변경이나 업데이트자체를 하지 않도록 추구한다.

즉 업데이트 해야할경우 -> '삭제'한다.

새롭게 수정된 서버파일을 도커이미지로 만들어서 도커 컨테이너 자체를 들었다 놨다 해서 빠르게 배포할 수있다.(기존의 컨테이너를 완전히 '제거')한다.

도커에서는 이미지를 도커위에 올리는 것만으로 배포가 끝난다.

변경불가능한(Immutable)이라는 의미는 그 자체로 상태가없는(Stateless)', 확장 가능한(Scalable)한 형태이다.

도커로 돌아가는 소프트웨어 자체는 어떠한 데이터도 가지고 있으면 안되고, 임시파일 정도만가지고 있는 상태로 구동되야 한다.

계정정보 같은 실질적인 데이터들은 외부 스토리지로 빼놓아야 한다. 최근에 자주 언급되는 마이크로 서비스의 특징과 매우 닮아있다.


- 리눅스 컨테이너 -

리눅스 컨테이너는(LXC) 리눅스 상에서 여러개의 격리된 리눅스 시스템을 동작하기 위한 운영체제단의 가상화 기술을 의미한다.

구글의 모든 서비스는 컨테이너 기반으로 돌아간다 ?> 성능이 좋기 떄문.

도커 이전에도 리눅스 컨테이너 기술이 있었고, 기업에서 쓰여왔으나 가장 잘 구현되고 편의성이 잘 구현되어 보편화된것이 도커이다.




-----------------------------------------

###### 180814_6

도커설치및 사용
-

윈도우에서 도커  툴 박스 설치 - 구동

```
//현재 돌아가고있는 컨테이너 확인
$ docker ps

// 현재 도커 머신에 설치된 도커 이미지 확인
$ docker images


```

도커는 무료(Community Edition) 유료(Enterprise Edition)버전으로 나뉜다.

도커 엔진 자체가 스웜이나 쿠버네티스 기술을 지원하고있어서 유료서비스만 잘 엮어 쓰면매우 간력한 기능을 쉽게 사용 가능하다.


- 도커 구성품

도커엔진: 특정 sw를 도커 컨테이너로 만들고 실행하게 해주는 데몬을 의미

도커 엔진과 도커 클라이언트 사이에 REST API가 사용되고 , 그 요청을 보내는게 도커 클라이언트

도커 클라이언트: 개발자가 직접 이용하는것, 도커엔진과 통신. 윈도우는 도커를 사용하기에는 최악의 조건이며, 리눅스가 최상이지만 공식적으로는 윈도우를 제대로 지원하고 있다.

도커 호스트 운영체제: 도커 엔진을 설치할 수 있는 운영체제 환경 64비트 리눅스 커널 버전 3.1 이상을 의미

도커엔진은 리눅스 OS 위에서 돌기떄문에
사실 윈도우는 도커 호스트 OS가 될수 없지만,가상화 기능 - 윈도우 파일시스템-도커 파일시스템 연동 되도록 모듈개발-> 현재는 상호통신 가능 추세

(윈도우 위에 리눅스 가상머신을 올린후 도커엔진을 돌리는 방식)

도커 머신: 로컬및 원격지 서버에 도커 엔진을 설치하고, 다양한 환경설정을 자동으로 수행해주는 클라이언트 


설치할 도커 쿨박스는 도커 CE의 이전버전, 윈도우 10이전 버전의 윈도우 사용자는 필수적으로 이러한 도커 툴박스를 이용해야 한다.

기능상으로 큰 문제 없기에 그냥 사용하면 된다.

'도커 툴박스를 꼭 써야하는지?'

아래는 로컬 서버에서 hello-world 라는 이미지를 찾고 없으면 도커허브에서 다운받아서 컨테이너로 구동된것.
```
$ docker run hello-world
```



아래는 현재 도커 머신에 존재하는 모든 컨테이너 내용들을 다 출력한다
```
$ docker ps -a 
```

-a 를 붙이지 않으면 현재 실행중인 컨테이너만 출력한다
```
$ docker ps
```


