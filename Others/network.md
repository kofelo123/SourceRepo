- [아이피 주소의 개념](#ipAdress)
- [네트워크 - osi 7계층](#180817_6)


---


###### ipAdress

아이피 주소의 개념
-

```
//cmd

nslookup naver.com

도메인 주소로 ip를 얻는 방법
```

공인 ip(public ip): 세계에서 단 하나만 존재하는 IP주소

사설 ip*********(private ip): 공유기를 이용해 만들 수 있는 가상의 ip주소


고정 ip : 해당 컴퓨터의 ip 주소를 고정적으로 사용

유동 ip : 수시로 변화하는 ip 주소


<사설 ip>
공유기로 연결된 내부망에 적용되는 IP주소로

공유기를 사용하는 각 pc에 다른 사설ip가 적용되지만 라우터를 거치게되고 그 라우터가 가진 공인ip를 거치게된다. 

즉 사설ip 컴퓨터와 전혀 다른 곳에 있는 사설ip 컴퓨터와 접속할때 라우터의 공인 ip를 거쳐서 통신 할 수있다는것.

사설ip끼리 직접 접근x, 그리고 사설ip는 중복된다.

<공인ip가 필요할 때>

서버의 역할을 하는 컴퓨터는 반드시 고유한 주소가 필요하다. -> 주소가 변경되면 접속자에게 혼란.

그러나 꼭 유동ip가 서버구축이 불가능한것은 아니다. -> 'DDNS'(Dynamic DNS)
-> 사용자가 도메인 주소로 해당 서버에 접근할떄->DNS서버에 요청해서 해당 ip를 얻어와서 접근

<인터넷 설치>

'ISP': 인터넷 서비스 제공업체(Internet Service Provider) - KT 같은곳

유동 ip를 할당받는다.(공인ip를 유동적으로 빌려준다)

<서버구축>
호스팅 업체 사용이 편함(cafe24 등)
->고정 공인 ip를 하나 할당받는다.

<유동 아이피 변경 방식>
('kt신청 -> 공유기 연결과 pc 연결의 아이피가 다른이유? -> 유동IP의 경우 MAC 주소를 토대로 아이피를 할당해서 장치마다 ip가 바로변한다.)
MAC주소는 모든 컴퓨터 장치마다 본인의 주소를 가지고 있는것, 물리적 주소.

공유기와 pc는 서로 가지고있는 랜카드가 달라서 다른 맥주소를 가져, 다른 유동ip를 가지게된다.


A 클래스 사설 ip : 10.0. ~ 10.255 ~
B 클래스 사설 ip 172.16.~ 172.31 ~
C 클래스 사설 ip : 192.168.0.0 ~ 192.168.~

<사설ip의 단점>
외부에서 내컴퓨터를 찾을 수 없다.
공인 ip주소가 아니라서 하나만 존재하는게 아니다.
내부에서만 의미가 있고 외부에서는 접속 불가

<사설ip에서 서버운영 - 포트포워딩>
포트포워딩 이용

만약 회사의 공인 ip가 하나 있고 세대의 컴퓨터가 공유기의 사설ip를 사용한다고 했을때

공유기가 공인 ip의 80포트로 들어오는 패킷을 사설 ip로 보내준다 -> 사설ip의 컴퓨터를 웹서버 컴퓨터로 사용가능


<NAT> ( Network Address Translation)

공유기가 하나의 공인 IP를 내부 사설 IP로 나누는 기술을 NAT 라고 한다.

사설 IP 사용으로 내부망을 보호하는 역할도 수행

<웹 서버 운용>

웹 서버는 80번 - 이미 정해진 포트 사용

내부 호스트 192.168.0.2와 192.168.0.3이 둘다 웹서버 라고 가정.

80포트를 0.2에 연걸, 81포트를 0.3에 연결한다.

이렇게 포트를 다르게 분배하면 한 개의 공인 IP에서 여러 개의 웹 서버 운용이 가능

도메인 주소를 다르게 설정.


공인 유동 IP 주소를 이용해서 서버를 구축하는 방법?
-> DDNS를 사용한다.

사설 IP 주소를 이용해 서버를 구축하는 방법?
-> 포트포워딩



-----------------------------------------

###### 180817_6

네트워크 - osi 7계층
-

국제 네트워크 표준 모델. 

전반적인 프로토콜이 모두 포함되어있다



1계층 - 물리층: 실제로 장치들을 연결하기 위한 물리적인 사항 정의

2계층 - 데이터 링크층: 두 지점 간의 신뢰성 있는 전송을 보장

3계층 - 네트워크층: 여러 개의 지점을 거칠 때 경로를 찾아줌

4계층 - 전송층: 양 끝단의 사용자들이 송수신에 있어서 신뢰성을 보장

5계층 - 세션층: 양 끝단의 프로세스가 통신을 관리하기 위한 방법을 제공

6계층 - 표현층: 인코딩 및 데이터의 형식 차이를 조절

7계층 - 응용층 : 응용 프로그램에서 서비스를 수행


TCP/IP 프로토콜은 미국 국방성에서 개발한 프로토콜이며 사실 OSI 7계층 보다도 개발 시기가 앞선다.
일반적으로 4계층으로 이루어지며

OSI 7계층과 흡사한 구조를 가지지만 계층을 묶는 기준에 있어서 차이가 있다.

이 둘은 별개라고 하기 힘들만큼 밀접한 관계가 있다.

일반적인 의미로는 OSI 7계층 전체에서 기능적인 표준에 기준의 TCP/IP 개념이 들어가 있다고 할 수 있다.

실제 업계에서는 TCP/IP 프로토콜을 사용하는 점에서 OSI 모델을 이론적인 모델이라고 칭하기도 한다.

OSI7계층은 국제 공식 표준이며
 TCP/IP는 비공식적 표준이다.





