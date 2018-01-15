- [400에러 - The request sent by the client was syntactically incorrect.](#400request)


## 400request

```
 The request sent by the client was syntactically incorrect
 ```
  request - 서버로의 전달중 input 의 name-value 값이 잘못되거나 null이거나 타입이 안맞는 등일때 발생된다.

  나같은 경우 ProductVO에 image라는 String 변수가 있어서 자동으로 파라미터로 매핑되는 과정에서

  html의 file타입이 name을 file로 지정해야했는데 image로 지정하면서 file이 String값이 아니기 때문에 에러가 생김

---
