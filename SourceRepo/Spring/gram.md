## SpringGrammars

- [@RequestParam이 Null일떄](#requestparam)

### @RequestParam
> @RequestParam은 Null이 될떄 아래와 같은 에러가 발생한다.
```html
org.springframework.web.bind.MissingServletRequestParameterException: Required int parameter 'board_seq' is not present
```
> 파라미터의 값이 필수가 아니라면 required 속성의 값을 설정해주도록 하면 된다. 기본값은 true 이다.
```html
@RequestParam(value="board_seq",required=false)int board_seq
```
