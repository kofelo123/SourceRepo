<nodejs - 책>

- [내부모듈과 외부모듈](#module)
- [미들웨어](#middleware)

---


###### module

내부모듈과 외부모듈
-

cmd 에서 명령어 node 로 실행해서 콘솔등 찍어볼수 있는것을 'REPL(read eval print loop)'이라 함

기능 확장시 '모듈'이라는 개념사용

기본 내장모듈= '내부 모듈'

[](https://nodejs.org/api)

os 모듈 사용

```js
//모듈 가져오기
var os = require('os');

console.log(os.hostname());
console.log(os.type());
console.log(os.platform());
console.log(os.arch());
console.log(os.release());
console.log(os.uptime());
console.log(os.loadavg());
console.log(os.totalmem());
console.log(os.freemem());
console.log(os.cpus());
console.log(os.getNetworkInterfaces());


```

외부모듈을 위한 npm
[](https://npmjs.org/)

```
> npm install 모듈명
```

request 모듈은 특정 웹페이지를 긁을떄 사용.

```js
> npm install request
```

```
//JavaScript.js

// 모듈 추출
var request = require('request');

//웹페이지 긁기
request('http://www.google.com', function(error,response,body){
    console.log(body);
});
```

```
//cmd

node JavaScript.js
```




---


###### middleware

미들웨어
-


app.use() 메서드에 입력하는 콜백 함수는 request 이벤트 리스너이고 다음과 같은 형태로 서버에 접속하면 자동으로 실행됨

```
var express = require('express');

var app = express();

app.use(function(request,response,next){
    request.test='request.test';
    response.test='response.test';
    next();
});

app.use(function(request,response,next){
    response.send('<h1>'+request.test+'::'+response.test+'</h1>');
});

app.listen(52273, function(){
    console.log('Server Run at http://127.0.0.1:52273');
})
```

이떄 매개변수 next는 다음 콜백함수를 의미한다.

사용자의 요청을 처리하면서 지나가는 app.use() 메서드의 콜백 함수를 미들웨어 라고 부른다.

미들웨어는 위에서 아래로 실행된다.

logger,body parser,cookie parser,session,static,router등 이미 개발된 미들웨어가 있고 활용할수있다.

```
var app = express();
app.use(express.logger());
app.use(express.bodyParser());
app.use(express.cookieParser());
app.use(express.session());
app.use(express.static('public'));
```
