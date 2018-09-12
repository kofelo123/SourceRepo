- [opacity - 투명도](#8712_084)
- [css 글자 자르기](#180811_4)
- [filedset과 legend (폼에서 테둘리묶음)](#180819_3)
- [border-collapse 속성 : 테이블의 선을 단선으로](#180819_9)
- [overflow - 요소의 박스보다 내용이 더 길떄](#180819_4)
- [z-index](#180819_5)


---


###### 8712_084

opacity - 투명도
-

엘리먼트의 투명도를 조절한다.

0.0 ~1.0 까지 값

기본값 1(100%불투명)

```css
div {
    opacity: 0.5;
}
```


-----------------------------------------

###### 180811_4

css 글자 자르기
-

제목등이 너무 길때 글자 자르는것을 css단에서 할 수있다.



http://webdir.tistory.com/483



-----------------------------------------

###### 180819_3

filedset과 legend (폼에서 테둘리묶음)
-

![](https://drive.google.com/uc?export=view&id=135os8EkSXB_RRcT-yxcZqQkS1Qg4cYlw)


-----------------------------------------

###### 180819_4

overflow - 요소의 박스보다 내용이 더 길떄 
-

```
visible: 기본값으로 내용이 더 길어도 그대로 보입니다.
           (내용이 흘러넘침)
 
hidden : 내용이 넘치면 자릅니다. 자른 부분은 보이지 않습니다.
 
scroll : 내용이 넘치지 않아도 항상 스크롤바가 보입니다.
 
auto : 내용이 잘릴 때만 스크롤바가 보입니다.

```

```css
#visible
{
overflow: visible;
}
#hidden
{
overflow: hidden;
}
#scroll
{
overflow: scroll;
}
#auto
{
overflow: auto;
}

```

![](https://drive.google.com/uc?export=view&id=1q688ud0W_gR21G6j7XjgIKHf8Nk_rfI8)


-----------------------------------------

###### 180819_5

z-index
-

position 속성을 absolute 등으로 변경하면 태그들이 겹치게 될 수 있다.

일반적으로 더 나중에 선언한 태그가 위에 배치되나,

style 속성의 z-index 를 이용하면 더 위에 배치시키거나 더 아래에 배치시킬 수 있다.

z-index 값이 높을수록 위, 작을수록 아래에 배치되며 정수 혹은 자연수 값을 사용한다.

```html
<html>
<head>
<style>
    .box{ width:150px; height:50px; position:absolute; }
    #box1{ z-index:30; background-color:cyan; left:0px; top:0px; }
    #box2{ z-index:70; background-color:blue; left:50px; top:30px; }
    #box3{ z-index:10; background-color:magenta; left:100px; top:60px; }
</style>
</head>

<body>
    <div id="box1" class="box"></div>
    <div id="box2" class="box"></div>
    <div id="box3" class="box"></div>
</body>
</html>

```





-----------------------------------------

###### 180819_9

border-collapse 속성 : 테이블의 선을 단선으로
-

![](https://drive.google.com/uc?export=view&id=1GNcAD7sSwPZrMn5C-TIpzE53o3eW0fls)


