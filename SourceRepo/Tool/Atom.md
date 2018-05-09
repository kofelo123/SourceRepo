- [아톰자동완성설정](#autocomplete)
- [자동줄바꿈관련설정](#softwrap)
- [md주석처리에대해](#mdcomment)
- [gitplus](#gitplus)
- [gitclone](#gitclone)
- [git단축키](#git단축키)
- [split](#split)
- [key binding](#keybinding)


### AutoComplete

packages - autocomplete-plus 중지시켜놨다.

---

### ImageLink

```
 ![](https://drive.google.com/uc?export=view&id=)
```
![](https://drive.google.com/uc?export=view&id=1gTBg4CMFx2qbECzwWPHe4KxkDWPoHEyu)

>(크롬웹스토어-구글드라이브 앱 쓰는중../ 구글드라이브의 폴더에 이미지를 저장시키는데, 이것을 직접 이미지마다 공유를 눌러서 시켜야 하는 번거로움이 있어서, 시도끝에 폴더를 공유시키니 알아서 이미지들도 바로공유된다. 그렇게 해놓은 상태.)

---

---
### softwrap

**자동 줄바꿈 관련 설정**

>화면 중앙에서 자동 줄바꿈 되는 현상때문에 설정수정

setting > editor > soft wrap at preffred line length 체크해제

---



---

### script
> 에디터내에서 빌드인을 보여주는 패키지

**단축키**:shift+ctrl+b

---

### github


### gitplus
> 깃 전반적인 기능이 있다는데, 잘안되었다. 이메일 설정하는 부분이 안보여서 시도하다가 말았다.

---

###	gitclone
> git plus 내에 clone에 대한 기능이 없어서 설치하는 패키지인데,
나같은 경우 이걸 쓰면 될것 같다.

---

### git단축키

```html
ctrl + h (여기서 나오는 파레트가 작동이 잘 안되었다.(내 설정의 문제인지..)+run기능을 쓰면 직접 bash처럼 명령어를 입력할 수 있는것 같다.)   

ctrl + p(이걸쓰니까 잘된다.)
```

---


### git-plus설치

주의점: gitpath에 경로가 아니라 git.exe까지 명시 해야됨. (에러주의(꺠진글자가 나와서 해결 난해함))
![gitpath](https://drive.google.com/uc?export=view&id=1yLs8fJiex3OGn-v4kmGZ26RQ4Snf8E-c)

---

## Atom 사용법

> Atom은 메뉴바를 제외하고 웹개발 기반으로 만들어졌기 떄문에, 바로 크롬 개발자도구를 사용할수있다.(view-developer-toggle developper tools (Crtl+Shift+i))  
플러그인 기반이라서 커스터마이징에 따라 여러방법으로 사용할 수도 있다.

---



### split
>split right,left,up,down 을 통해서 분할창을 사용할 수 있다.

<img src="https://drive.google.com/uc?export=view&id=1k9uZ45bnK0rtH41iI8BlDeGRip5lUXQe" width="400" height="300" />

![split](https://drive.google.com/uc?export=view&id=1tUq7_bKgVwqhWGdS8UO70ymCajSIcUgR)

---


###### keybinding

아톰 - key binding
-

아톰 - key binding

설정 - 메뉴에서 install 밑에 Open Config Folder가 있고 그중에서 keymap.cson을 연다.

설정 - Keybindings에 설치한 패키지에 해당하는 Selector와 Command 값을 가지고 keymap.cson에서 수정해주면된다.

error Masage : [stdin]:17:1: error: missing indentation

-> keymap.cson에서 값입력할때 앞에 한칸 띄우고 쓰면된다.

혹은 Keybindings에 들어가지않고 패키지설치할때
패키지 여백공간에 클릭했을떄 뜨는 패키지관련 정보중에 패키지에서 쓰이는 Keymap을 모아놓은게 있어서 그 정보를 사용해도 된다.
