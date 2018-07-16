## Markdown

- [image](#image)
- [테이블사용](#table)
- [이미지 링크](#imagelink)
- [링크](#link)
- [테이블](#table)
- [체크박스](#checkbox)

### image
> 실제 구글에서 공유되는 url과 깃허브 마크다운에서 포맷이 다르다는것에 유의

```html
<img src="https://drive.google.com/uc?export=view&id=1k9uZ45bnK0rtH41iI8BlDeGRip5lUXQe" width="400" height="300" />

![split](https://drive.google.com/uc?export=view&id=1k9uZ45bnK0rtH41iI8BlDeGRip5lUXQe)
```



### table
```
|   |   |
|---|---|
|   |   |
|   |   |
|   |   |
|   |   |
|   |   |
```

```
|   |   |   |
|---|---|---|
|   |   |   |
|   |   |   |
|   |   |   |
```
[테이블생성사이트](http://www.tablesgenerator.com/markdown_tables)

>'<코드 (O)  <코드> (X) '



---

### mdcomment

**md주석처리에대해**

마크다운의 경우 그냥 주석으로 처리하면 브라우저(깃허브)에서 볼때 주석이 숨김처리 되기때문에 감안하고
html의 주석이 아닌 // 이런식으로 하던지, 마크다운 문법에 대한 주석의 경우 코드뷰(``` ``` )안에 넣는게 좋다


### table

[table생성]](http://www.tablesgenerator.com/markdown_tables)

---

## checkbox

```
> - [x] AdminPage
> - [ ] 질문정리
```

> - [x] AdminPage
> - [ ] 질문정리


Atom

.less 파일에 대해..

css와 같은 개념이지만, css의 불편점을 좀더 개선한 파일이다. 서로 호환이되어서 크게 신경쓸 필요는 x

---

emmet 코드 생산성 향상

단축키 ctrl + e , tab(안됬었는데,저장하고 인코딩을 html로 바꿔주니 된다.)

! + 단축키 : html 기본 세팅

https://docs.emmet.io/abbreviations/syntax/ (레퍼런스)

---

### Link

```
[Text](URI)     
[Text](URI){:target="_blank"}
```
