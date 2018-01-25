- [기타](#else)
- [설치(apt-get)](#aptget)
- [vim 에디터종료](#vimediterend)
- [디렉터리 권한](#directorypermission)
- [디렉터리 생성](#directorycreate)
- [톰캣로그확인](#tomcatlog)
- [MySQL관련](#mysql)
  - [버전확인](#versionfind)
  - [DB서버쪽 한글세팅](#koreansetting)
  - [리눅스 인스턴스 mysql접속](#mysqlconnect)
## else

### SSH와 RDP

SSH는 리눅스용, RDP는 윈도우용 원격제어 방화벽이라고 보면된다.

윈도우 -> 리눅스 제어시 SSH가 필요해서 SSH역할을 해주는 프로그램 필요 : putty,XSHELL 등.


## aptget

>우분투에서 직접 설치는 서버에서 다운-컴파일로 어렵고 복잡한일.. / 편리하게 다운-설치 위해서 웹스토어 역할을 한다.

```
sudo apt-get install apache2 //설치

sudo apt-get update; //업데이트
```

## vimediterend
vim 에디터- 종료 하는법
```
Esc 한번 + :q

파일 저장후,종료 명령어
:wq    

저장하지 않고, 그냥 종료
:q!

```

## directorypermission

(디렉터리 파일의 권한보기)
```
ls-al
```

drwx-w-r-x
첫번쨰 'd'는 디렉터리를 의미 '-'로 아무것도 안적힌 경우는 일반 파일을 의미

2~10번째 자리는 'r','w','x'가 반복
각 read write execute rwx가 순서로 3번 반복
이것을 끊어서 생각했을때

(2,3,4),(5,6,7),(8,9,10)
소유자   그룹     방문자

이를 권한 수정하는 방법 'chomod'
```
chomod 755 ./filename
//( r w x )
//  4 2 1

//(소유자는 모든권한, 그룹과 방문자는 읽기와실행만 가능)
```

---

## directorycreate

**디렉터리 생성**

```
mkdir 디렉토리명
```
---
## tomcatlog

tmcat\logs 에서
```
tail -f catalina.out
```
빠져나올떄는 ctrl + c
---

## mysql

---
## versionfind
**MySQL버전찾기**
```
sudo mysql --version
```

## koreansetting
우분투서버쪽에서 한글 데이터를 못가져오는 경우가 있다.
한글 인코딩 상의 문제가 있는것..
 ![img](https://drive.google.com/uc?export=view&id=1VgB9hSmilyCR6qEc_p74JpswcoJeQy5R)

기본이 라틴으로.(클라이언트는 utf로 되어잇음-아마 스키마에서 설정한것일까..)
server 가 라틴으로 되어있는것을 수정해야

DB에 값을 입력할때 한글값이 제대로 저장 안되는 경우
character set 설정문제가 대부분이며 이와 같은 경우에 UTF-8
로 설정하면 쉽게 고칠 수 있다.

latin1로 character set 설정되어있는 것을 utf-8로 변경

/etc/mysql/my.cnf 파일 내용 설정 변경

[client]
default-character-set=utf8

[mysql]
default-character-set=utf8


[mysqld]
collation-server = utf8_unicode_ci
init-connect='SET NAMES utf8'
character-set-server = utf8

![image](https://drive.google.com/uc?export=view&id=1XzbnsUCKS57BQVTvL5-J5z4BXjeIYyV5)
주의 점은 기존의 만들어있던  DB,테이블들의 character-set이 변경되는 것은 아니기 때문에 직접 변경해줘야 한다.

ALTER DATABASE [DB명] DEFAULT CHARACTER SET utf8;

---

## MySqlConnect
(리눅스 인스턴스 mysql 접속) (thearc프로젝트-gjwjddnjs012@daum.net)
```
mysql -uroot -pgj0235689m
```
