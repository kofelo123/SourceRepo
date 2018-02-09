- [Elastic Ip](#elasticip)
- [puttygen](#puttygen)
- [80포트 리다이렉트](#redirect)
- [AWS 우분투 중단-다시시작](#awsrestart)
## elasticip

고정아이피를 말한다. aws에서 받을수있고 조건에 따라무료, 원래는 public DNS와 public ip가 주어지면 재시작시에
ip,도메인이 바뀌게되는데 고정아이피로 쓰려거든 Elastic ip를 쓰면되고 1개만 무료, 안붙여쓰고있거나, 여러개붙이면 과금발생

---

## puttygen

ec2를 생성하며 로컬에 내려받은 .pem 파일(aws로 받은 파일 aws_password2.pem)을 이용해서 private key를 생성해 줄 프로그램

접속할떄 이 private key를 이용해서ec2 인스턴스에 접속한다.

## redirect

원래는 .com(80포트) -> .com:8080(8080포트)로 redirect 하는데 있어서 /tomcat/conf/server.xml 을 수정하는 방법이 있으나 리눅스의 경우 일반 유저 권한에서 바인딩 할 수없도록 되어있다고 한다.(보안상)

톰캣서버는 구동시 tomcat 유저 권한으로 구동된다.
root권한 으로 구동되면 80포트에 문제없이 바인딩 통신가능. but 해킹 방지를 위해서..


```
//(sudo)
# iptables -t nat -I PREROUTING -p tcp --dport 80 -j REDIRECT --to-port 8080
# service iptables save
```
---

## awsRestart

AWS 의 우분투 인스턴스를 중단->재시작 할때는 public ip(+public도메인)와 80포트 리다이렉트 설정이 변동되니

다시 설정해줘야한다.

public ip 관련 - datasource 등 xml설정,도메인, xshell(putty),filezilla
