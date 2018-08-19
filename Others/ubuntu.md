- [기타](#else)
- [설치(apt-get)](#aptget)
- [vim 에디터종료](#vimediterend)
- [vim에서 단어찾기](#vimsearch)
- [디렉터리 권한](#directorypermission)
- [현재 사용자로 디렉터리 권한지정](#chown)
- [디렉터리 생성](#directorycreate)
- [디렉터리/파일 삭제](#directorydelete)
- [톰캣로그확인](#tomcatlog)
- [MySQL관련](#mysql)
  - [버전확인](#versionfind)
  - [DB서버쪽 한글세팅](#koreansetting)
  - [리눅스 인스턴스 mysql접속](#mysqlconnect)
- [su](#su)
- [파일찾기](#find)
- [오라클 설치과정](#installoracle)
- [메모리 실시간보기(sar,htop)](#memoryscan)
- [현재 열려있는 포트 확인](#portfind)
- [프로세스 강제종료](#processkill)
- [톰캣 8 설치](#tomcat8install)
- [Mysql 설치](#mysqlinstall)
- [패키지 파일 삭제](#packagedelete)
- [mv - 파일이동,파일이름 변경](#mv)

- [에러]
  - [write error in swap file](#error1)
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

이를 권한 수정하는 방법 'chmod'
```
chmod 755 ./filename
//( r w x )
//  4 2 1

//(소유자는 모든권한, 그룹과 방문자는 읽기와실행만 가능)
```

---




###### chown

현재 사용자로 디렉터리 권한지정
-


ubuntu에서

id 를 입력하면 사용자 관련 해서 이름이 나오고(ubuntu)

sudo chown ubuntu .
입력하면 현재 디렉터리의 권한을 우분투로 만드는것이다.
permission denied에 대해 이런식으로도 접근 가능.


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
## directorydelete

우분투에서의 파일/디렉터리삭제
//test 파일삭제
rm test

//파일삭제시 확인과정 거치지 않음
rm -f test

//home디렉터리를 삭제

rm -r home/
(비어있지 않은 디렉터리는 -r 옵션 없이는 삭제할수없다)

rmdir을 디렉터리 삭제시 사용하기도 한다.


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

ALTER DATABASE DB명(스키마) DEFAULT CHARACTER SET utf8;


현재 Character-Set 확인하기
show variables like ‘c%’
status

---

## MySqlConnect
(리눅스 인스턴스 mysql 접속) (thearc프로젝트-gjwjddnjs012@daum.net)
```
mysql -uroot -pgj0(~+pw)
```

---

## su

su -> superuser
sudo -> superuser do (superuser권한으로 실행)
-> 그러므로 우리가 우분투에서 애초에 SuperUser권한으로 접근되어있는 상태였다면 sudo없이도 할수있다는것.

$ su
->슈퍼관리자(root)로 접근한다.
$ sudo passwd root
처음에 su 를쓰기위해 비번설정한다.

---

## find

파일 찾기 (파일명 검색)
```
현재 디렉토리에서, pl 확장자를 가진 모든 파일 찾기
find -name '*.pl'

(현재 디렉토리 밑의 하위 디렉토리까지 다 찾습니다.)


루트에서부터, 즉 전체 하드에서, pl 확장자를 가진 모든 파일 찾기
find / -name '*.pl'



전체 하드 디스크에서, 파일명이 ab 로 시작하는 모든 파일 찾기
find / -name 'ab*'



전체 하드 디스크에서, 파일명이 .bash 로 시작하는 모든 파일 찾기
find / -name '.bash*'
```
----

## installOracle

>오라클 설치하는것에 잘안되서 재설치를 3번하며 삽질을 좀 했다. 그 과정을 기록해 놓을것.

많이 참조한 사이트
- [blog1](http://blog.saltfactory.net/install-oracle-xe-on-ubuntu/)
- [blog2](http://sarc.io/index.php/oracledatabase/506-linux-oracle-database-11g-express-edition-oracle-xe-11-2-0-1-0-x86-64)

>blog1에 나오는것을 따라서 쭉 설치하면서 설명과는 조금 다르게 에러가 나오거나 했었는데 그에 대한 에러중 blog2에서 해결할수 있는것들이 좀 있었다.
>설치완료후 최종적으로 세팅할떄,오라클에 접속할때 에러들이 나서 쓰지 못하는상황.
>xe 설치후 나오는 설치보고의 bc에러의 경우도 그중하나(blog2에서는 linux기반 설치이므로 yum이 아니라 apt 로 bc설치해야됨).
>나의 경우 blog1대로 설치-> 설치중의 잡에러 + 'shared memory realm does not exist' ,로그인실패문제등. ->2차설치에서 blog2의 해결책+bc설치
> -> 개선되었으나 이번에는 "Oracle initialization or shutdown in progreess"에러 -> 3차 재설치 - blog2의 # /etc/init.d/oracle-xe configure 세팅 그대로 따라한후 접속하니까 드디어 잘되었다.(지금도 구체적인 이유는 모른다.)
> 정리) bc설치 -> blog1 대로설치 -> 마지막 포트세팅 하는파트부터 다시 blog2 처럼 세팅.

**blog1내용**
Oracle XE를 설치하기 위해서는 서버 운영체제가 64bit 운영체제야 한다. Ubuntu에서 현재 시스템이 64bit인지를 확인해보자.
```
uanme -i
```
결과가 x86_64로 나오면 현재 설치된 운영체제는 64bit이다.

Oracle XE는 Oracle 공식 사이트에 RPM 파일 형태로 배포되고 있다.


**Oracle XE 설치에 필요한 패키지**
Ubuntu에서는 .rpm 파일을 사용해서 패키지를 설치할 수 없기 때문에 PRM 파일을 Debian 계열의 패키지 설칠를 위한 .deb 파일로 변경하여 패키지를 설치해야한다.

Ubuntu에 Oracle XE를 설치하기 위해서 필요한 패키지들이 있는데 다음과 같다.

alien : RPM 패키지를 Debian 패키지로 변환하는 툴
libaio1 : Linux 커널 AIOAsynchronous I/O 엑세스 라이브러리
unixodbc : ODBC(Open Database Connectivity) 라이브러리

필요한 패키지를 apt-get 명령어를 이용하여 root 권한으로 설치한다.
```
sudo apt-get install -y alien libaio1 unixodbc  
```

**RPM 파일을 DEB 파일로 변환**
다운받은 .rpm 파일을 Ubuntu에 설치하기 위해서 alien을 사용하여 .deb 파일로 변경한다.

[Oracle-xe Download](http://www.oracle.com/technetwork/database/database-technologies/express-edition/downloads/index.html)

>'로컬에서 오라클xe.rpm 파일을 받은후 filezilla를 통해 ubuntu의 루트패스에 넣었다.'

```
sudo alien --scripts -d oracle-xe-11.2.0-1.0.x86_64.rpm  
```

위 명령어를 실행하면 한참의 시간이 지난 이후 같은 경로에 oracle-xe_11.2.0-2_amd64.deb 파일이 생성된다.

**/sbin/chkconfig**
Rethat 패키지들은 설치할 때 /sbin/chkconfg를 사용하는데 Ubuntu에는 없기 때문에 이와 동일한 환경을 만들어주기 위해서 아래 내용을 가지고 /bin/chkconfig 파일을 생성한다.
```
sudo vi /sbin/chkconfig  
```
```
#!/bin/bash
# Oracle 11gR2 XE installer chkconfig hack for Ubuntu
file=/etc/init.d/oracle-xe  
if [[ ! `tail -n1 $file | grep INIT` ]]; then  
echo >> $file  
echo '### BEGIN INIT INFO' >> $file  
echo '# Provides: OracleXE' >> $file  
echo '# Required-Start: $remote_fs $syslog' >> $file  
echo '# Required-Stop: $remote_fs $syslog' >> $file  
echo '# Default-Start: 2 3 4 5' >> $file  
echo '# Default-Stop: 0 1 6' >> $file  
echo '# Short-Description: Oracle 11g Express Edition' >> $file  
echo '### END INIT INFO' >> $file  
fi  
update-rc.d oracle-xe defaults 80 01  
#EOF
```
파일 생성 이후 이 파일을 실행할 수 있도록 파일 권한을 755 변경한다.
```
sudo chmod 755 /sbin/chkconfig  
```

**Kernel 파라미터 설정**
```
sudo vi /etc/sysctl.d/60-oracle.conf  
```

```
# Oracle 11g XE kernel parameters
fs.file-max=6815744  
net.ipv4.ip_local_port_range=9000 65000  
kernel.sem=250 32000 100 128  
kernel.shmmax=536870912  
```

시스템컨트롤러 데몬에 커널파라미터를 설정하였으면 커널 파라미터를 로드한다.
```
sudo service procps start  
```


**Oracle XE가 사용할 파일 추가**
Oracle XE는 /bin/awk를 사용하게 되는데 Ubuntu에는 /usr/bin/awk에 설치되어 있기 때문에 다음과 같이 심볼릭링크를 만들어준다.
```
sudo ln -s /usr/bin/awk /bin/awk  
```
Oracle XE는 리스너가 사용할 lock 파일을 만들어준다.
```
sudo mkdir /var/lock/subsys  
```
```
sudo touch /var/lock/subsys/listener  
```

**메모리 설정**
Oracle XE를 설치하고 나서 특별한 에러 없이 Oracle 프로세스가 리스너만 시작되고 다른 프로세스가 실행되지 않는 문제를 만날 수 있다. Oralce XE를 설치할 때 정상적으로 설치가 되지 않거나 오류가 발생하게되면 $ORACL_HOME안의 log 디렉토리를 살펴보면 된다. 설치가 정상적으로 되지 않거나, 설치는 되었는데 에러 없이 Oracle이 정상적으로 시작이 되지 않을 경우 로그를 살펴보면ORA-000845:MEMORY_TARGET 에러가 발생하게 되는 경우가 있는데 메모리의 설정이 잘못되거나 사이즈가 부족해서 그런 경우이다. 이런 경우 메모리 설정을 위해서 다음 과정을 진행한다.

먼저 현재 설정되어 있는 shared memeory를 삭제한다.
```
sudo rm -rf /dev/shm  
```
새롭게 SHM을 만들어서 마운트를 시킬 수 있게 다시 만든다.
```
sudo mkdir /dev/shm  
```
```
sudo mount -t tmpfs shmfs -o -size=4096m /dev/shm  
```
shm 설정을 데몬에 등록해서 로드하도록 하기 위해서 다음 내용을 /etc/rc2.d/S01shm_load 파일로 등록한다.
```
sudo vi /etc/rc2.d/S01shm_load  
```
```
#!/bin/sh
case "$1" in  
start) mkdir /var/lock/subsys 2>/dev/null  
touch /var/lock/subsys/listener  
rm /dev/shm 2>/dev/null  
mkdir /dev/shm 2>/dev/null  
mount -t tmpfs shmfs -o size=4096m /dev/shm ;;  
*) echo error
exit 1 ;;  
esac  
```
```
sudo chmod 755 /etc/rc2.d/S01shm_load  
```


**Oracle XE 패키지 설치**

Oracle XE를 설치하기 위한 Ubuntu의 환경설정은 끝났다. 이제 Oracle XE를 설치하기 위해 .rpm을 .deb 파일로 변환한 패키지를 설치한다.

```
sudo dpkg --install oracle-xe_11.2.0-2_amd64.deb  
```

정상적으로 설치가 되면 다음과 같이 Oracle XE가 설치되고 데몬을 자동으로 등록하게 된다.

```
Selecting previously unselected package oracle-xe.  
(Reading database ... 72392 files and directories currently installed.)
Preparing to unpack ./oracle-xe_11.2.0-2_amd64.deb ...  
Unpacking oracle-xe (11.2.0-2) ...  
Setting up oracle-xe (11.2.0-2) ...  
Executing post-install steps...

 Adding system startup for /etc/init.d/oracle-xe ...
   /etc/rc0.d/K01oracle-xe -> ../init.d/oracle-xe
   /etc/rc1.d/K01oracle-xe -> ../init.d/oracle-xe
   /etc/rc6.d/K01oracle-xe -> ../init.d/oracle-xe
   /etc/rc2.d/S80oracle-xe -> ../init.d/oracle-xe
   /etc/rc3.d/S80oracle-xe -> ../init.d/oracle-xe
   /etc/rc4.d/S80oracle-xe -> ../init.d/oracle-xe
   /etc/rc5.d/S80oracle-xe -> ../init.d/oracle-xe

You must run '/etc/init.d/oracle-xe configure' as the root user to configure the database.

Processing triggers for ureadahead (0.100.0-16) ...  
Processing triggers for desktop-file-utils (0.22-1ubuntu1) ...  
Processing triggers for mime-support (3.54ubuntu1.1) ...  
Processing triggers for libc-bin (2.19-0ubuntu6.6) ...  
```

설치가 모두 마친 이후 Oracle XE를 사용하기 위해 Oracle 최초 설정을 /etc/init.d/oracle-xe configure로 설정한다.

>재설치후에는 blog2에 따라 설정했다. 그내용 .
> port 9080 / 2521 / (started on boot(y/n): n)
>$ cd /u01/app/oracle/product/11.2.0/xe/bin
>$ . ./oracle_env.sh
>$ sqlplus "/as sysdba"
>아래는 Oracle 11g XE GUI 원격 접속을 위한 조치이다.
>SQL> EXEC DBMS_XDB.SETLISTENERLOCALACCESS(FALSE);
>PL/SQL procedure successfully completed.

```
sudo /etc/init.d/oracle-xe configure  
```

최초 설정 내용은 다음과 같이 서비스 포트설정과 패스워드 설정이다.


```
Specify the HTTP port that will be used for Oracle Application Express [8080]:
Specify a port that will be used for the database listener [1521]:
Specify a password to be used for database accounts.
```

**환경변수 설정**

>3차 재설치하는 과정에서 따로 다시 세팅하지는 않았다.

Oracle XE 설치가 모두 끝나면 Oracle에 관련된 환경 변수를 추가한다.
```
vi ~/.bashrc  
```
```
export ORACLE_HOME=/u01/app/oracle/product/11.2.0/xe  
export ORACLE_SID=XE  
export NLS_LANG=`$ORACLE_HOME/bin/nls_lang.sh`  
export ORACLE_BASE=/u01/app/oracle  
export LD_LIBRARY_PATH=$ORACLE_HOME/lib:$LD_LIBRARY_PATH  
export PATH=$ORACLE_HOME/bin:$PATH  
```
```
source ~/.bashrc  
```


**상태 확인**

설치가 모두 끝나면 정상적으로 운영되고 있는지 다음과 같이 확인한다. Oracle의 네트워크 상태를 확인하기 위해서 리스너가 정상적으로 동작하고 있는지 확인한다.
```
lsnrctl status  
```
만약 리스너가 정상적으로 동작하고 있으면 다음과 같은 결과를 출력할 것이다.
```
LSNRCTL for Linux: Version 11.2.0.2.0 - Production on 13-MAR-2015 13:54:41

Copyright (c) 1991, 2011, Oracle.  All rights reserved.

Connecting to (DESCRIPTION=(ADDRESS=(PROTOCOL=IPC)(KEY=EXTPROC_FOR_XE)))  
STATUS of the LISTENER

Alias                     LISTENER  
Version                   TNSLSNR for Linux: Version 11.2.0.2.0 - Production  
Start Date                13-MAR-2015 10:32:50  
Uptime                    0 days 3 hr. 21 min. 53 sec  
Trace Level               off  
Security                  ON: Local OS Authentication  
SNMP                      OFF  
Default Service           XE  
Listener Parameter File   /u01/app/oracle/product/11.2.0/xe/network/admin/listener.ora  
Listener Log File         /u01/app/oracle/diag/tnslsnr/brainoffice1/listener/alert/log.xml  
Listening Endpoints Summary...  
  (DESCRIPTION=(ADDRESS=(PROTOCOL=ipc)(KEY=EXTPROC_FOR_XE)))
  (DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=brainoffice1.hibrain.net)(PORT=1521)))
  (DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=brainoffice1.hibrain.net)(PORT=8080))(Presentation=HTTP)(Session=RAW))
Services Summary...  
Service "PLSExtProc" has 1 instance(s).  
  Instance "PLSExtProc", status UNKNOWN, has 1 handler(s) for this service...
Service "XE" has 1 instance(s).  
  Instance "XE", status READY, has 1 handler(s) for this service...
Service "XEXDB" has 1 instance(s).  
  Instance "XE", status READY, has 1 handler(s) for this service...
The command completed successfully
```

설치한 Oracle에 로그인 해보자. SYS와 SYSTEM 계정의 초기 비밀번호는 설치할 때 입력한 비밀번호를 사용한다.

```
sqlplus system  
```

정상적으로 로그인이 되면 다음과 같은 화면이 출력되면서 Oracle에 접속이 된다.
```
SQL*Plus: Release 11.2.0.2.0 Production on Fri Mar 13 13:59:40 2015

Copyright (c) 1982, 2011, Oracle.  All rights reserved.

Enter password:

Connected to:  
Oracle Database 11g Express Edition Release 11.2.0.2.0 - 64bit Production

SQL>  
```

**Oracle XE 삭제**

만약 Oracle XE를 설치하는 도중에 에러가 발생하여 재설치를 해야하는 경우나 완전히 삭제해야하는 경우는 다음과 같이 삭제한다.

Oracle XE 서비스를 정지한다.

```
sudo service oracle-xe stop  
```

Oracle XE 패키지를 삭제한다.

```
 sudo dpkg --purge oracle-xe
```

Oracle 디렉토리를 삭제한다.
```
sudo rm -rf /u01/app  
```

Oracle XE 데몬을 삭제하고 갱신하다.
```
sudo rm /etc/default/oracle-xe  
```
```
sudo update-rc.d -f oracle-xe remove  
```
Ubuntu 설정 파일을 삭제한다.
```
sudo rm /sbin/chkconfig /etc/rc2.d/S01shm_load /etc/sysctl.d/60-oracle.conf  
```
Oracle XE 리눅스 계정을 삭제한다.
```
sudo userdel -r oracle  
```
```
sudo delgroup dba  
```

**blog2관련내용**

설치 완료. 다시 rpm 설치를 수행한다.
```
# rpm -ivh oracle-xe-11.2.0-1.0.x86_64.rpm
준비 중...               ########################################### [100%]
/var/tmp/rpm-tmp.TCRVXo: line 186: bc: command not found
  1:oracle-xe              ########################################### [100%]
Executing post-install steps...
/var/tmp/rpm-tmp.ddKna3: line 76: bc: command not found
/var/tmp/rpm-tmp.ddKna3: line 77: bc: command not found
/var/tmp/rpm-tmp.ddKna3: line 78: [: -gt: unary operator expected
/var/tmp/rpm-tmp.ddKna3: line 82: bc: command not found
You must run '/etc/init.d/oracle-xe configure' as the root user to configure the database.
```
bc? 관련 에러가 났지만 튕기지 않고 완료되었다. (아마.. 잘 된 것 같다) 시키는대로 계속 해보자.
```
# /etc/init.d/oracle-xe configure
```

```
Oracle Database 11g Express Edition Configuration
-------------------------------------------------
This will configure on-boot properties of Oracle Database 11g Express
Edition.  The following questions will determine whether the database should
be starting upon system boot, the ports it will use, and the passwords that
will be used for database accounts.  Press  to accept the defaults.
Ctrl-C will abort.

Specify the HTTP port that will be used for Oracle Application Express [8080]:9080

Specify a port that will be used for the database listener [1521]:2521

Specify a password to be used for database accounts.  Note that the same
password will be used for SYS and SYSTEM.  Oracle recommends the use of
different passwords for each database account.  This can be done after
initial configuration:
Confirm the password:

Do you want Oracle Database 11g Express Edition to be started on boot (y/n) [y]:n

Starting Oracle Net Listener...Done
Configuring database...
Database Configuration failed.  Look into /u01/app/oracle/product/11.2.0/xe/config/log for details
```

Database Configuration failed.  Look into /u01/app/oracle/product/11.2.0/xe/config/log for details 에러 발생.

참고로 log 디렉토리 안에 로그 내용들은 대략 다음과 같다.
```
Session ID: 0 Serial number: 0


create spfile='/u01/app/oracle/product/11.2.0/xe/dbs/spfileXE.ora' FROM pfile='/u01/app/oracle/product/11.2.0/xe/config/scripts/init.ora'
*
ERROR at line 1:
ORA-01078: failure in processing system parameters
LRM-00116: syntax error at 'sessions' following '='


ORA-01034: ORACLE not available
ORA-27101: shared memory realm does not exist
Linux-x86_64 Error: 2: No such file or directory
ORA-01078: failure in processing system parameters
LRM-00109: could not open parameter file '/u01/app/oracle/product/11.2.0/xe/dbs/initXE.ora'
select 'utl_recomp_begin: ' || to_char(sysdate, 'HH:MI:SS') from dual
*
ERROR at line 1:
ORA-01034: ORACLE not available
Process ID: 0
Session ID: 0 Serial number: 0


BEGIN utl_recomp.recomp_serial(); END;

*
ERROR at line 1:
ORA-01034: ORACLE not available
 ```

여기부터가 고난기이다. 정말 몇시간을 구글링했는데, 대체적으로 아래와 같은 해결책들이 제시되었다.

  swap 메모리를 추가하라.
  /etc/hosts에 호스트 정보를 넣어라.
  bc 패키지를 깔아라.
다 했다. 다 안된다. swap도 계속 늘려가면서 해봤지만 안된다.
아래는 swap 추가이다.
```
# mkdir /swap
# dd if=/dev/zero of=/swap/swapfile bs=1024 count=2097152

2097152+0 records in
2097152+0 records out
2147483648 bytes (2.1 GB) copied, 127.844 s, 16.8 MB/s
# cd /swap
# mkswap swapfile
mkswap: swapfile: warning: don't erase bootbits sectors
        on whole disk. Use -f to force.
Setting up swapspace version 1, size = 2097148 KiB
no label, UUID=2c6a1277-a5c3-4f48-b64c-7010dd161c72
# swapon swapfile
```

아래는 bc 설치 화면이다.
>나는 ubuntu라서  yum이 아니라 apt로 해야된다.
```
# yum install bc
Loaded plugins: fastestmirror
Loading mirror speeds from cached hostfile
 * base: mirror.oasis.onnetcorp.com
 * extras: mirror.oasis.onnetcorp.com
 * updates: mirror.oasis.onnetcorp.com
Setting up Install Process
Resolving Dependencies
--> Running transaction check
---> Package bc.x86_64 0:1.06.95-1.el6 will be installed
--> Finished Dependency Resolution

Dependencies Resolved

===============================================================================================
 Package          Arch                 Version                        Repository          Size
===============================================================================================
Installing:
 bc               x86_64               1.06.95-1.el6                  base               110 k

Transaction Summary
===============================================================================================
Install       1 Package(s)

Total download size: 110 k
Installed size: 212 k
Is this ok [y/N]: y
Downloading Packages:
bc-1.06.95-1.el6.x86_64.rpm                                             | 110 kB     00:00
Running rpm_check_debug
Running Transaction Test
Transaction Test Succeeded
Running Transaction
Warning: RPMDB altered outside of yum.
  Installing : bc-1.06.95-1.el6.x86_64                                                     1/1
  Verifying  : bc-1.06.95-1.el6.x86_64                                                     1/1

Installed:
  bc.x86_64 0:1.06.95-1.el6

Complete!
 ```

그런데 어느 구글링 결과에서, "bc 패키지 설치 후에 다시 rpm 설치를 해야 정상적으로 설치된다"라는 말이 있었다.

문득 스쳐지나가는 것은 아까 rpm 설치 시에 이상한 bc 관련 에러가 났었던 것. 다시 보고 가자.
```
Executing post-install steps...
/var/tmp/rpm-tmp.ddKna3: line 76: bc: command not found
/var/tmp/rpm-tmp.ddKna3: line 77: bc: command not found
/var/tmp/rpm-tmp.ddKna3: line 78: [: -gt: unary operator expected
/var/tmp/rpm-tmp.ddKna3: line 82: bc: command not found
```
그래서 rpm -e oracle-xe-11.2.0-1.0.x86_64로 삭제한 후, 다시 rpm -ivh oracle-xe-11.2.0-1.0.x86_64.rpm로 설치, 그리고 다시 /etc/init.d/oracle-xe configure하여, 아래와 같이 configure 과정이 성공적으로 완료되었다!
```
# /etc/init.d/oracle-xe configure

Oracle Database 11g Express Edition Configuration
-------------------------------------------------
This will configure on-boot properties of Oracle Database 11g Express
Edition.  The following questions will determine whether the database should
be starting upon system boot, the ports it will use, and the passwords that
will be used for database accounts.  Press  to accept the defaults.
Ctrl-C will abort.

Specify the HTTP port that will be used for Oracle Application Express [8080]:9080

Specify a port that will be used for the database listener [1521]:2521

Specify a password to be used for database accounts.  Note that the same
password will be used for SYS and SYSTEM.  Oracle recommends the use of
different passwords for each database account.  This can be done after
initial configuration:
Confirm the password:

Do you want Oracle Database 11g Express Edition to be started on boot (y/n) [y]:n

Starting Oracle Net Listener...Done
Configuring database...Done
Starting Oracle Database 11g Express Edition instance...Done
Installation completed successfully.
```

이제 DB에 접속해 보자.
```
$ cd /u01/app/oracle/product/11.2.0/xe/bin
$ . ./oracle_env.sh
$ sqlplus "/as sysdba"

SQL*Plus: Release 11.2.0.2.0 Production on Wed Jun 22 08:51:27 2016

Copyright (c) 1982, 2011, Oracle.  All rights reserved.


Connected to:
Oracle Database 11g Express Edition Release 11.2.0.2.0 - 64bit Production

SQL>

```

아래는 Oracle 11g XE GUI 원격 접속을 위한 조치이다.
```
SQL> EXEC DBMS_XDB.SETLISTENERLOCALACCESS(FALSE);

PL/SQL procedure successfully completed.
```

이미 생성되어 있는 계정들을 살펴볼까?
```
SQL> SELECT USERNAME, USER_ID, DEFAULT_TABLESPACE, CREATED FROM DBA_USERS;

USERNAME                USER_ID DEFAULT_TABLESPACE             CREATED
-------------------- ---------- ------------------------------ ------------------
SYS                           0 SYSTEM                         28-AUG-11
SYSTEM                        5 SYSTEM                         28-AUG-11
ANONYMOUS                    35 SYSAUX                         28-AUG-11
APEX_PUBLIC_USER             45 SYSTEM                         28-AUG-11
APEX_040000                  47 SYSAUX                         28-AUG-11
XS$NULL              2147483638 SYSTEM                         28-AUG-11
OUTLN                         9 SYSTEM                         28-AUG-11
FLOWS_FILES                  44 SYSAUX                         28-AUG-11
MDSYS                        42 SYSAUX                         28-AUG-11
CTXSYS                       32 SYSAUX                         28-AUG-11
XDB                          34 SYSAUX                         28-AUG-11
HR                           43 USERS                          28-AUG-11

12 rows selected.
```

Tablespace는 뭐가 있을까?
```
SQL> SELECT TABLESPACE_NAME FROM DBA_TABLESPACES;

TABLESPACE_NAME
------------------------------
SYSTEM
SYSAUX
UNDOTBS1
TEMP
USERS
```

마지막으로 Datafile이다.
```
SQL> SELECT FILE_NAME, TABLESPACE_NAME FROM DBA_DATA_FILES;

FILE_NAME                                TABLESPACE_NAME
---------------------------------------- ------------------------------
/u01/app/oracle/oradata/XE/users.dbf     USERS
/u01/app/oracle/oradata/XE/sysaux.dbf    SYSAUX
/u01/app/oracle/oradata/XE/undotbs1.dbf  UNDOTBS1
/u01/app/oracle/oradata/XE/system.dbf    SYSTEM
```

---

## memoryscan

메모리 실시간 보기

```
sar -r 1
```

```
htop
```

---
## portfind

현재 열려있는 포트 확인

```
netstat -tnlp
```

---

## error1

```
//에러 메시지
write error in swap file
```

swap file을 쓸수 없다는 말인데
하드디스크 내 여분의 공간이 없어서 그렇다.
-
우분투 하드디스크 용량 확인
df -h


---

## processkill

프로세스 강제종료

```
sudo kill -9 pid(프로세스아이디)
```

## tomcat8install

톰켓 8 설치
```
 sudo apt-get install tomcat8 tomcat8-admin
```
[참조](https://gs.saro.me/#!m=elec&jn=679)
---

## packagedelete

```
sudo apt-get remove 패키지명
```
---
## mysqlinstall

```
sudo apt-get install mysql-server mysql-client
```

- mysql client 접속
```
 mysql -u root -p
```
- 외부에서 접근할 권한 부여 / 적용 / 나가기
mysql> GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY '접속에 필요한 암호';
mysql> flush privileges; 서비스가 켜진 상태에서 값을 적용하는 명령이지만, 곧바로 restart 할 예정임으로 의미가 없습니다.
mysql> quit

접근설정
```
sudo vim /etc/mysql/my.cnf
```
===========================================================
- 만일 !includedir /etc/mysql/mysql.conf.d/ 구문이 있다면 :q로 나갑니다.
- 그리고 다시 입력!!
```
sudo vim /etc/mysql/mysql.conf.d/mysqld.cnf
```
===========================================================
- bind-address = 127.0.0.1 를 찾아 i키로 insert 모드로 바꾼 후 앞에 #를 붙여 주석처리합니다.
#bind-address = 127.0.0.1
- 위와같이 앞에 #를 붙여 주석처리
- :wq 저장하고 나갑니다.
```
 sudo service mysql restart
```
워크밴치에서 접속여부를 확인 (aws에서는 mysql포트열어 놔야함)

[서버쪽 인코딩설정](https://github.com/kofelo123/SourceRepo/blob/master/SourceRepo/Others/ubuntu.md#koreansetting)



---


###### vimsearch

vim에서 단어찾기
-

vim 관련

간단 찾기 /찾을단어 -> n누르면 다음,

?찾을단어 (아래에서 위로찾음)


문자열 바꾸기(치환)

* 현재 문서를 편집중이라면, 키보드의 Esc 키를 눌러 편집모드에서 빠져나옵니다.

* 콜론(:)을 누르고 %s/foo/bar 라고 하면 모든 foo라는 문자열이 bar로 한꺼번에 치환됩니다.

다음과 같이 c 라는 옵션을 붙이면
:%s/foo/bar/c
바꿀 때마다 바꾸어도 좋은지 물어보기에 더 안전합니다. y를 누르면 바꾸고, n을 누르면 다음으로 건너뛰고, a를 누르면 모두 바꿉니다.


:%s/\<foo\>/bar

이렇게 하면 정확하게 foo에 일치될 때만 바꿉니다. 즉 foo는 바꾸지만, foo 앞뒤로 다른 문자열이 붙어 있는 경우, 예를 들어
fooZZZ
ZZZfoo
ZZZfooZZZ
이런 문자열 속의 foo 는 바꾸지 않습니다.



---


###### mv

mv - 파일이동,파일이름 변경
-

파일을 이동하는 명령어로 mv 옵션은 cp 명령어와 비슷하다.

다른 점이 있다면 cp는 파일을 복사하는 것이고 mv는 파일 이동인데 이동 시 원본 파일이 삭제되는 점이다.

그래서 보통 파일이름을 변경할 때도 mv 명령어를 쓴다.

mv [옵션] [원본] [이동경로]

ex)
```
mkdir docket-getting-started
mv docket-getting-started docker-getting-started
```
옵션

-b: 이동 경로에 같은 이름의 파일이나 디렉터리가 존재하면 기존 파일을 백업한 뒤에 이동한다.

-f: 이동 경로에 같은 이름의 파일이나 디렉터리가 존재하면 덮어쓸 때 묻지 않고 바로 덮어쓴다.

-i: 이동 경로에 같은 이름의 파일이나 디렉터리가 존재하면 덮어쓸 때 물어본다.

-v: 이동 상태를 표시한다.

hi.txt 파일을 이동시키는데 hello.txt로 파일이름까지 바꿔보자.

[corej@localhost ~]$ mv hi.txt ./mydir1/hello.txt




