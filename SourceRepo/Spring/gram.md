## SpringGrammars

- [@RequestParam이 Null일떄](#requestparam)
- [@ModelAttibue()](#modelattribute)
- [logger](#logger)

### @RequestParam

> @RequestParam은 Null이 될떄 아래와 같은 에러가 발생한다.

```java
org.springframework.web.bind.MissingServletRequestParameterException: Required int parameter 'board_seq' is not present
```

> 파라미터의 값이 필수가 아니라면 required 속성의 값을 설정해주도록 하면 된다. 기본값은 true 이다.

```java
@RequestParam(value="board_seq",required=false)int board_seq
```

---

### ModelAttribute
``
>컨트롤러 파라미터에 @ModelAttribute 를 사용할 경우, 직접 메소드내에 model.addAttribute 하지않아도 자동으로 view단으로 전달된다.

```java
//BoardController
public String listPage(@ModelAttribute("cri") SearchCriteria cri, ...) throws Exception {
  ...
}

```html
// list.jap
          <option value="t"
							<c:out value="${cri.searchType eq 't'?'selected':''}"/>>
							제목</option>
```

---

### logger

```java
private static final Logger logger = LoggerFactory.getLogger(현재클래스.class);
```

---

## htmlappend
```html
$('#').html() will completely replace the content inside the tag that the selector corresponds to. $('#').append() will just append to the end of the content inside the tag.

ex.

$('#foo').html("New content here!");

before: <span id="foo">Old content here!</span>
after: <span id="foo">New content here!</span>
and with append

 $('#foo').append("New content here!");

before: <span id="foo">Old content here!</span>
after: <span id="foo">Old content here!New content here!</span>
```
