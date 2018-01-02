- [아톰자동완성설정](#autocomplete)
- [마크다운]
  - [이미지 링크](#imagelink)
  - [링크](#link)
- [자동줄바꿈관련설정](#softwrap)
- [md주석처리에대해](#mdcomment)
### AutoComplete

packages - autocomplete-plus 중지시켜놨다.

---

### ImageLink

```
<!-- ![](https://drive.google.com/uc?export=view&id=) -->
```
![](https://drive.google.com/uc?export=view&id=1gTBg4CMFx2qbECzwWPHe4KxkDWPoHEyu)

>(크롬웹스토어-구글드라이브 앱 쓰는중../ 구글드라이브의 폴더에 이미지를 저장시키는데, 이것을 직접 이미지마다 공유를 눌러서 시켜야 하는 번거로움이 있어서, 시도끝에 폴더를 공유시키니 알아서 이미지들도 바로공유된다. 그렇게 해놓은 상태.)

---

### Link

```
[Text](URI)
```

---
### softwrap

**자동 줄바꿈 관련 설정**

>화면 중앙에서 자동 줄바꿈 되는 현상때문에 설정수정

setting > editor > soft wrap at preffred line length 체크해제

---

### mdcomment

**md주석처리에대해**

마크다운의 경우 그냥 주석으로 처리하면 브라우저(깃허브)에서 볼때 주석이 숨김처리 되기때문에 감안하고
html의 주석이 아닌 // 이런식으로 하던지, 마크다운 문법에 대한 주석의 경우 코드뷰(``` ``` )안에 넣는게 좋다
