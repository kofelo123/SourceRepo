- [기타](#else)
- [설치(apt-get)](#aptget)
- [vim 에디터종료](#vimediterend)
- [디렉터리 권한](#directorypermission)
- [디렉터리 생성](#directorycreate)
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
