<nodejs - 책(do it nodejs)>

- [모듈 사용](#8714_1)
- [설치한 패키지 적용](#8714_2)
- [삭제 및 재설치](#8714_3)
- [js 기타](#8714_4)
- [배열](#8714_5)
- [콜백함수](#8714_6)
- [프로토타입 객체 만들기](#8714_7)
- [간단한 웹서버 만들기](#8715_1)
- [EADDRINUSE 에러 - 서버중복실행](#8715_2)
- [Content-Type 헤더값에 들어갈 MIME Type](#8715_3)
- [express 사용](#8715_4)
- [미들웨어와 라우터](#8722_1)
- [미들웨어 사용](#8722_2)
- [라우터 미들웨어 사용](#8722_3)
- [세션과 쿠키](#8722_4)
- [파일 업로드](#8722_5)
- [mysql사용](#8722_6)
                                            
---

기본환경 
이클립스 - marketplace - enide 플러그인 설치

###### 8714_1

모듈 사용
-

모듈을 사용하기 위해서 exports 전역객체를 사용한다.

두가지 방법이 있다.

1.exports 

```js
exports.add=function(a,b){
	return a+b;
};

exports.multiply = function(a,b){
	return a * b;
};
```
2. module.exports

```js

var calc = {};

calc.add = function(a,b){
	return a + b ;
}

module.exports = calc;

```


모듈을 불러올떄는 requre() 메소드 사용.(모듈객체반환)

```js
var cal2 = require('./calc');
console.log('use : %d', cal2.add(10,10));
```

이러한 사용자가 직접 만든 모듈을 외장모듈이라 한다.

반면 내장모듈은 시스템에 접근할 수 있는 모듈이다.




---

###### 8714_2

설치한 패키지 적용
-

<설치한 패키지의 적용에 대해>

매번 npm으로 외부패키지를 설치해야 하는것은 아니다,

공통적으로 적용되는 패키지의 경우 현재 프로젝트의 [node_module]폴더를 프로젝트들의 상위 폴더인 workspace 폴더에 옮기거나, 설치할때 부터 상위폴더에서 설치하면 

공통되는 패키지를 일일이 프로젝트마다 설치할 필요가 없어진다.  

console.dir();
사용하면 내부에 있는 속성들이 모두 출력된다.



---

###### 8714_3

삭제 및 재설치
-

```
//삭제
npm uninstall 패키지명

//재설치
npm install 패키지명 --save

```

패키지 정보가 들어있는 package.json
을 다른 pc에 옮긴후 
```
npm install
```
해주면 다른 pc에서도 그대로 패키지를 한꺼번에 설치해서 쓸수있다.



---

###### 8714_4

js 기타
-

객체 생성방법 및 접근

```js
var Person = {};

1.대괄호 사용
Person["age"] = 20;

2. 점 접근자 사용
Person.mobile = '010-1000-1000';
```

<함수사용>
```js
1.선언적함수

function add(a,b){
	return a + b;
} 

var result = add(10,10);

2.익명 함수

var add = function (a,b){
	return a + b;
}

var result = add(10, 10);
```

차이: 생성하는 시점의 차이가 있다.

1 선언적함수: script 태그 실행전 가장머저 읽힘
                      (여러군데서 여러번 호출될때 )
2.익명함수 : 한줄한줄 실해오디면서 정의된 부분을 만날떄 정의, 함수 호출전에 정의가 되어야 실행가능
(1회성으로 누군가에게 호출될경우 많이씀)



---

###### 8714_5

배열
-

```js
var Users = [{name:'소녀시대', age:20}, {name:'걸스데이',age:22}];


//배열에 새 객체 추가
Users.push({name:'티아라', age:23});

var add = function(a,b){
	return a + b;
};

Users.push(add)

console.log('배열 요소의 수 : %d', Users.length);
console.log('세 번째 요소로 추가된 함수 실행 : %d', Users[2](10, 10));

<forEach>

첫번쨰 인자 : 배열의 각 요소
두번쨰 인자 : 각요소의 인덱스값
```js
Users.forEach(function(item, index){
	console.log('배열 요소 #' + index + ' : %s', item.name);
});
```

<배열에 값 추가 및 삭제>

- push(Object) :배열 끝에 요소추가
- pop() : 배열 끝 요소 삭제
- unshift() : 배열의 가장 앞에 요소 추가
- shift(): 배열 가장 앞에 있는 요소 삭제
- splice(index, removeCount [,object]) : 여러개의 객체를 요소로 추가하거나 삭제
- slice(index, copyCount): 여러 개의 요소를 잘라내어 새로운 배열 객체로 만듬

배열 중간에 있는 요소 삭제시
```
delete Users[1];
```

이렇게 하면 두번쨰 요소가 삭제가능.

그러나 값만 삭제되고 빈공간인 (undefined)로 남기 때문에 splice() 메소드로 삭제 하는것이 좋다.


<splice() 메소드 사용>

-삭제에 사용-
5개의 객체가 있는 Users 배열에서 3,4번째 요소 삭제시

```js
Users.splice(2,2);
```
(2번쨰 인덱스 부터, 2개삭제)

-객체 추가에 사용-
splice를 사용해서 객체 넣는 방법

```js
Users.splice(1,0,{name:'애프터스쿨',age:25});
```

<slice() 메소드 사용>

배열의 일부 요소를 복사하여 새로운 배열 생성

```js
var Users2 = Users.slice(1,3);
```
Users의 객체중 1번쨰 인덱스부터 3개를 복사해서 사용한다




---

###### 8714_6

콜백함수
-

함수를 파라미터로 전달하는 경우 대부분 비동기 프로그래밍 방식으로 코드로 만들때이다.

이떄 파라미터로 전달되는 함수를 콜백함수라고 한다.

이는 함수가 실행되는 중간에 호출되어 상태 정보를 전달하거나 결과값을 처리하는데 사용.


-함수를 호출했을때 또 다른 함수를 파라미터로 전달하는 방법-
```js
function add(a, b, callback){ //3.정의된 callback과 전달된 인자를 바탕으로 수행.
	var result = a + b;
	callback(result); //1.콜백함수 호출   4.정의된 callback익명함수를 기본으로 수행
}

add(10, 10, function(result){ //2. add함수 호출및 인자인 callback에 대한 익명함수 정의 
	console.log('파라미터로 전달된 콜백 함수 호출됨.');
	console.log('더하기 (10,10)의 결과 : %d', result);
});


//callback 원래 저렇게 callback 이렇게 사용하는게 맞고
//add를 호출하는데 있어서 callback에 대한 인자호출시 의 함수를 익명성 함수로 선언 하는것 
//즉 callback인자가 function(result)

===
파라미터로 전달된 콜백 함수 호출됨.
더하기 (10,10)의 결과 : 20

```
add()함수를 호출할 때 익명 함수로 만들어서 파라미터로 바로 전달할 수도 있다.


-함수 안에서 값을 반환할 때 새로운 함수를 만들어 반환하는 방법-

```js
function add(a, b, callback){//3.add함수 호출및 전체수행
	var result = a + b;
	callback(result);//1.콜백 호출  4.정의된 callback익명함수를 기본으로 수행
	
	var history = function(){ 
		return a + ' + ' + b + ' = ' + result;
	};
	return history; //5.위 함수 호출후 저장된 변수 history 반환
}

var add_history = add(10, 10, function(result){ //2.add의 인자인 callback에 대한 익명함수 정의  //6.return된 history저장
	console.log('파라미터로 전달된 콜백 함수 호출됨.');
	console.log('더하기 (10, 10)의 결과 : %d', result);
});

console.log('결과값으로 받은 함수 실행 결과 : ' + add_history())//7.add의 history의 값 출력.add_history()에서 ()빼면 history의 function이 다 출력되어버림(function(){}
===
파라미터로 전달된 콜백 함수 호출됨.
더하기 (10, 10)의 결과 : 20
결과값으로 받은 함수 실행 결과 : 10 + 10 = 20

```

-반환된 함수에서 접근할 수 있는 변수-
```js
function add(a, b, callback){
	var result = a + b;
	callback(result);
	
	var count = 0;
	var history = function(){
		count++;
		return count + ' : ' + a + ' + ' + b + ' = ' + result;
	};
	return history;
}

var add_history = add(10, 10, function(result){
	console.log('파라미터로 전달된 콜백 함수 호출됨.');
	console.log('더하기 (10, 10)의 결과 : %d', result );
});

console.log('결과 값으로 받은 함수 실행 결과 : ' + add_history());
console.log('결과 값으로 받은 함수 실행 결과 : ' + add_history());
console.log('결과 값으로 받은 함수 실행 결과 : ' + add_history());

===
파라미터로 전달된 콜백 함수 호출됨.
더하기 (10, 10)의 결과 : 20
결과 값으로 받은 함수 실행 결과 : 1 : 10 + 10 = 20
결과 값으로 받은 함수 실행 결과 : 2 : 10 + 10 = 20
결과 값으로 받은 함수 실행 결과 : 3 : 10 + 10 = 20

```

반환된 history 함수가 실행될 때에는 이미 add함수가 메모리에서 접근할 수 없는 상태가 된 다음이다.

따라서 history 함수가 실행 될때는 add 함수 안에 만들어진 count 변수도 같이 접근 할 수 없어야한다.

하지만 이와 같이 함수 안에서 새로운 함수를 만들어 반환하는 경우는 예외적으로 접근을 허용한다.
-> '클로저(Closure)' 라고 한다.


---

###### 8714_7

프로토타입 객체 만들기
-

js - 객체생성시 중괄호 사용

자바스크립트 객체는 함수를 이용해서 만들수도 있다. ?> 함수도 객체이기 때문

Person 프로토타입 객체를 만들어본다.

```js
function Person(name, age){
	this.name = name;
	this.age = age;
}

//객체에 함수를 속성으로 추가하기 위해서는 prototype을 사용해야한다.
Person.prototype.walk = function(speed){
	console.log(speed + 'km 속도로 걸어갑니다.');
}

var person01 = new Person('소녀시대', 20);
var person02 = new Person('걸스데이',22);

console.log(person01.name + '객체의 walk(10)을 호출합니다.');
person01.walk(10);
```

Person 객체안의 prototype 속성도 객체로 만든것인데, Pserson 함수가 만들어 질때  자동으로 만들어진다.
prototype 객체는 Person 객체 자신을 가리키도록 되어있다.




###### 8715_1

간단한 웹서버 만들기
-

노드 - 웹 서버를 만들떄 필요한 http 모듈이 있다.

이 모듈로 HTTP 프로토콜로 요청하는 내용과 응답을 모두 처리할 수 있다.

그러나 익스프레스 사용시 더 쉽고 빠르게 웹서버 구성가능.

-http사용-

```js
var http = require('http');

// 웹 서버 객체 생성
var server = http.createServer();

// 웹 서버를 시작하여 3000번 포트에서 대기
var port = 3000;
server.listen(port, function(){
	console.log('웹 서버가 시작되었습니다. : %d', port);
});

// 클라이언트 연결 이벤트 처리
server.on('connection', function(socket){
	var addr = socket.address();
	console.log('클라이언트가 접속했습니다. : %s, %d', addr.address, addr.port);
});

// 클라이언트 요청 이벤트 처리
server.on('request', function(req, res){
	console.log('클라이언트 요청이 들어왔습니다.');
	
	res.writeHead(200, {"Content-Type": "text/html; charset=utf-8"});
	res.write("<html>");
	res.write("	<head>");
	res.write("		<title>응답 페이지</title>");
	res.write("	</head>");
	res.write("	<body>");
	res.write("		<h1>노드제이에스로부터의 응답 페이지</h1>");
	res.write("	</body>");
	res.write("</html>");
	res.end();
});


// 서버 종료 이벤트 처리
server.on('close', function(){
	console.log('서버가 종료되었습니다.');
});
```

http객체에서 server를 만든후 이 객체의 listen() 메소드를 호출하면 웹서버가 실행하여 대기한다.

(server 객체 대표적 메소드 listen(), close())


-서버 객체의 주요 이벤트-

connection : 클라이언트가 접속하여 연결이 만들어질떄 발생 이벤트

request : 클라이언트가 요청시 발생 이벤트

close : 서버종료시 발생 이벤트

---

res 객체의 writeHead(), write(),end()메소드 사용시 클라이언트로 응답을 보낼 수 있다.

end()메소드는 응답을 모두 보냈다는 것을 의미, 일반적으로 end()메소드가 호출될때 클라이언트로 응답을 전송

 ![](https://drive.google.com/uc?export=view&id=1PCbvOHGqaR1mTJybUX2RfALpgIHAQG67)


위의 request 부분을 서버 객체 생성시 바로 입력수도 있다.
```js
var server = http.createServer(function(req,res){
	console.log('클라이언트 요청이 들어왔습니다.');
	
	res.writeHead(200, {"Content-Type": "text/html; charset=utf-8"});
	res.write("<html>");
	res.write("	<head>");
	res.write("		<title>응답 페이지</title>");
	res.write("	</head>");
	res.write("	<body>");
	res.write("		<h1>노드제이에스로부터의 응답 페이지</h1>");
	res.write("	</body>");
	res.write("</html>");
	res.end();
});
```


---

###### 8715_2

EADDRINUSE 에러 - 서버중복실행
-

서버가 중복 실행되어 충돌이 일어난 상황이다.
기존의 실행중인 서버를 종료하고 
실행하려는 서버를 실행해야한다.



---

###### 8715_3

Content-Type 헤더값에 들어갈 MIME Type
-

 ![](https://drive.google.com/uc?export=view&id=1zczBvf19iO-zcL41moXQWCgHtvgd9Q4p)



---


###### 8715_4

express 사용
-

express 모듈 - 간단히 웹서버 기능 구현

express에서 제공하는 미들웨어와 라우터로 각각의 기능을 훨씬 편리하게 구성.

new -> Node.js Express Project로 프로젝트생성

시작점이 되는 파일은 app.js다.

```js
//app.js

var express = require('express') //웹서버를 위해 생성, http 모듈 위에서 동작(http모듈도 함께 써야)
  , routes = require('./routes')//[routes]폴더의 index.js파일을 불러들임
  , user = require('./routes/user') // [routes]폴더안에있는 user.js파일 불러들임
  , http = require('http')
  , path = require('path'); // 파일 패스를 다루는데 사용

var app = express();

// all environments

//속성 설정
app.set('port', process.env.PORT || 3000);
app.set('views', __dirname + '/views');
app.set('view engine', 'jade');
//미들웨어 사용 설정
app.use(express.favicon());
app.use(express.logger('dev'));
app.use(express.bodyParser());
app.use(express.methodOverride());
app.use(app.router);
app.use(express.static(path.join(__dirname, 'public')));

// development only
if ('development' == app.get('env')) {
  app.use(express.errorHandler());
}

//클라이언트 요청 처리
app.get('/', routes.index);
app.get('/users', user.list);

//서버 실행
http.createServer(app).listen(app.get('port'), function(){
  console.log('Express server listening on port ' + app.get('port'));
});
```
createServer()메소드 파라미터인 app은 express()메소드로 만들어진 express 서버객체이다.


express 서버 객체의 주요메소드

- set(name,value) : 서버 설정을 위한 속성을 지정. 
- get(name) : 서버 설정을 위해 지정한 속성을 꺼내 온다.
- use([path,]function[,function...]): 미들웨어 함수 사용
- get([path,]function) : 특정 패스로 요청된 정보처리


set() 메소드로 설정한 속성의 이름이 미리 정해진 이름이라면 웹 서버 환경의 설정에 영향을 미친다.
아래는 미리 정해진 주요 속성 이름

- env : 서버 모드를 설정
- views : 뷰들이 들어 있는 폴더 또는 폴더 배열을 설정
- view engine : 디폴트로 사용할 뷰엔진 설정


-----------------------------------------

###### 8722_1

미들웨어와 라우터
-

app.use()를 사용하면 미들웨어를 설정할수있다.

노드에서는 미들웨어를 사용하여 필요한 기능을 순차적으로 실행할 수 있다.

 ![](https://drive.google.com/uc?export=view&id=124rzbvbruC2j-iGHi15uXfXD_lYUVWTF)

미들웨어나 라우터는 하나의 독립된 기능을 가진 함수이다.

익스프레스에서 웹 요청과 응답에 관한 정보를 사용해 필요한 처리를 진행할 수 있도록 독립된 함수로 분리한다. 

이 분리한 각각이 미들웨어라고 부른다.

로그를 남기는 기능을 함수로 만든후 use() 메소드로 미들웨어로 등록해두면, 모든 클라이언트 요청이 이 미들웨어를 거치면서 로그를 남긴다.

각 미들웨어는 next() 메소드를 호출하여 다음 미들웨어가 처리될 수있도록 순서를 넘긴다.


라우터는 클라이언트의 요청 패스를 보고 요청 정보를 처리할 수 있는 곳으로 기능을 전달해주는 역할을 한다.

이러한 기능을 '라우팅'이라고 부른다.

클라이언트가 /users 패스로 요청하면 이에 대한 응답 처리를 하는 함수를 별도로 분리해서 만든 후 get() 메소드를 호출하여 라우터로 등록할수 있다.

그러면 등록해 둔 라우터 정보로 찾은 함수가 호출되며, 이 함수 안에서 클라이언트로 응답을 보낼 수 있다.



-----------------------------------------

###### 8722_2

미들웨어 사용
-

모든 기능을 직접 미들웨어로 만들기는 힘들기 때문에 미리 만둘어둔 미들웨어가 제공된다.

미들웨어의 종류에 여러가지가 있다.

<static 미들웨어>

특정 폴더의 파일들을 특정 패스로 접근할 수있도록 만들어준다.

ex) public 폴더에 있는 모든 파일을 웹 서버의 루트 패스로 접근 할 수있도록 만들고 싶다면

```
app.use(express.static(path.join(__dirname,'public')));
```

public 하위 의 파일들은 클라이언트에서 바로 접근할 수있게된다.

ex)
ExpressExample/public/index.html
ExpressExample/public/images/house.png

위와 같은 구조의 파일이 있다면

http://localhost:3000/index.html
http://localhost:3000/images/house.png
이렇게 접근 가능 하다는것

이를 웹브라우저에서 보려면 app.js파일에서 다음과 같이 응답을 보내면 된다.

```js
res.end("<img src='/images/house.png' width='50%'");
```

만약 public 폴더를 /public 패스로 접근하도록 하려면

```
app.use(express.static(path.join(__dirname,'public')));
```

이와 같이 하면된다.


<body-parser 미들웨어>

POST 요청시 요청 파라미터를 확인 할수 있게해줌.

외장 모듈이라 설치해야됨

```
npm install body-parser --save
```

req.param('id');
req.param('password');

이런식으로 사용한다.



-----------------------------------------

###### 8722_3

라우터 미들웨어 사용
-

url을 일일이 확인 해야하는 문제 해결을위함.

라우터도 미들웨어 중 하나지만 app 객체의 속성으로 제공되며 요청기능이 무엇인지 패스기준으로 구별해주기 떄문에 중요한 역할담당

get(path,callback) :get 방식으로 특정 패스요청 발생시 사용할 콜백함수 지정

post(path,callback)

put(path,callback)

delete(path,callback)

all(path,callback)




-----------------------------------------

###### 8722_4

세션과 쿠키
-



```
npm install cookie-parser --save
```

```js

var cookieParser = require('cookie-parser');

app.use(express.cookieParser());

//쿠키 정보를 확인함
app.get('/process/showCookie', function(req,res){
		console.log('/process/showCookie 호출됨.');
		
		res.send(req.cookies);
});

//쿠키에 이름 정보를 설정함
app.get('/process/setUserCookie', function(req,res){
	console.log('/process/setUserCookie 호출됨.');
	
	//쿠키 설정
	res.cookie('user', {
		id: 'mike',
		name: '소녀시대',
		authorized: true
	});
	
	res.redirect('/process/showCookie');
});

```

<세션>

```
npm install express-session --save
```


세션을 사용할때는 쿠키도 같이 사용하므로 cookie-parser 모듈도 함꼐 불러들인다.
```js

var cookieParser = require('cookie-parser');
var expressSession = require('express-session');

app.use(cookieParser());
app.use(expressSession({
	secret:'my key',
	resave:true,
	saveUninitialized:true
}));



app.get('/process/product', function(req,res){
	console.log('/process/product 호출됨.');
	
	if(req.session.user){
		res.redirect('/public/product.html');
	}else{
		res.redirect('/public/login2.html');
	}
});
```


```
if(req.session.user){
	//로그인 된 상태
	console.log('로그아웃 합니다.');
	
	req.session.destroy(function(err){
		if(err){throw err;}
		console.log('세션 삭제및 로그아웃.');
		res.redirect('/public/login2.html');
	});
}else{
//로그인 안 된 상태
console.log('아직 로그인되어 있지 않습니다.');

res.redirect('/public/login2.html');
}
});
```

-----------------------------------------

###### 8722_5

파일 업로드
-

외장 모듈을 사용하여 익스프레스에서 multipart 포맷으로 파일 업로드 할 수 있다.

```
npm install multer --save
```

파일 업로드 기능 구현시, body-parser, multer, router 등으 미들웨어 사용. 이렇게 여러개의 미들웨어를 사용할때 사용 순서가 바뀌면 동작하지 않을 수 있다. 순서에 주의

```
var bodyParser = require('body-parser')
var cookieParser = require('cookie-parser')
var  expressSession = require('express-session');

var multer = require('multer');
var fs = require('fs');




app.use(bodyParser.urlencoded({extended: true}));

app.use(cookieParser());
app.use(expressSession({
	secret:'my key',
	resave:true,
	saveUninitialized:true
}));

//multer
app.use(multer({
	dest: 'uploads',
	putSingleFilesInArray: true,
	limits: {
		files: 10,
		fileSize: 1024 * 1024
	},
	rename: function(fieldname, filename){
		return filename+Date.now();
	},
	onFileUploadStart: function (file){
		console.log('파일 업로드 시작 : ' + file.originalname);
	},
	onFileUploadComplete: function(file,req, res){
		console.log('파일  업로드 완료 : ' + file.filename + ' -> ' + file.path);
	},
	onFileSizeLimit : function (file){
		console.log('파일 크기 제한 초과 : %s', file.originalname);
	}
}));


```


-----------------------------------------

###### 8722_6

mysql사용
-

환경:heidisql

```
npm install mysql --save
```
(전체코드)
```js

/**
 * MySQL 데이터베이스 사용하기
 *
 * 웹브라우저에서 아래 주소의 페이지를 열고 웹페이지에서 요청
 * (먼저 사용자 추가 후 로그인해야 함)
 *    http://localhost:3000/public/login2.html
 *    http://localhost:3000/public/adduser2.html
 *
 * @date 2016-11-10
 * @author Mike
 */

// Express 기본 모듈 불러오기
var express = require('express')
  , http = require('http')
  , path = require('path');

// Express의 미들웨어 불러오기
var bodyParser = require('body-parser')
  , cookieParser = require('cookie-parser')
  , static = require('serve-static')
  , errorHandler = require('errorhandler');

// 에러 핸들러 모듈 사용
var expressErrorHandler = require('express-error-handler');

// Session 미들웨어 불러오기
var expressSession = require('express-session');
 

//===== MySQL 데이터베이스를 사용할 수 있도록 하는 mysql 모듈 불러오기 =====//
var mysql = require('mysql');

//===== MySQL 데이터베이스 연결 설정 =====//
var pool      =    mysql.createPool({
    connectionLimit : 10, 
    host     : 'localhost',
    user     : 'root',
    password : 'admin',
    database : 'test',
    debug    :  false
});



// 익스프레스 객체 생성
var app = express();

// 설정 파일에 들어있는 port 정보 사용하여 포트 설정
app.set('port', process.env.PORT || 3000);

// body-parser를 이용해 application/x-www-form-urlencoded 파싱
app.use(bodyParser.urlencoded({ extended: false }))

// body-parser를 이용해 application/json 파싱
app.use(bodyParser.json())

// public 폴더를 static으로 오픈
app.use('/public', static(path.join(__dirname, 'public')));
 
// cookie-parser 설정
app.use(cookieParser());

// 세션 설정
app.use(expressSession({
	secret:'my key',
	resave:true,
	saveUninitialized:true
}));
 



//===== 라우팅 함수 등록 =====//

// 라우터 객체 참조
var router = express.Router();


// 로그인 처리 함수
router.route('/process/login').post(function(req, res) {
	console.log('/process/login 호출됨.');

	// 요청 파라미터 확인
    var paramId = req.body.id || req.query.id;
    var paramPassword = req.body.password || req.query.password;
	
    console.log('요청 파라미터 : ' + paramId + ', ' + paramPassword);
	
    // pool 객체가 초기화된 경우, authUser 함수 호출하여 사용자 인증
	if (pool) {
		authUser(paramId, paramPassword, function(err, rows) {
			// 에러 발생 시, 클라이언트로 에러 전송
			if (err) {
                console.error('사용자 로그인 중 에러 발생 : ' + err.stack);
                
                res.writeHead('200', {'Content-Type':'text/html;charset=utf8'});
				res.write('<h2>사용자 로그인 중 에러 발생</h2>');
                res.write('<p>' + err.stack + '</p>');
				res.end();
                
                return;
            }
			
            // 조회된 레코드가 있으면 성공 응답 전송
			if (rows) {
				console.dir(rows);

                // 조회 결과에서 사용자 이름 확인
				var username = rows[0].name;
				
				res.writeHead('200', {'Content-Type':'text/html;charset=utf8'});
				res.write('<h1>로그인 성공</h1>');
				res.write('<div><p>사용자 아이디 : ' + paramId + '</p></div>');
				res.write('<div><p>사용자 이름 : ' + username + '</p></div>');
				res.write("<br><br><a href='/public/login2.html'>다시 로그인하기</a>");
				res.end();
			
			} else {  // 조회된 레코드가 없는 경우 실패 응답 전송
				res.writeHead('200', {'Content-Type':'text/html;charset=utf8'});
				res.write('<h1>로그인  실패</h1>');
				res.write('<div><p>아이디와 패스워드를 다시 확인하십시오.</p></div>');
				res.write("<br><br><a href='/public/login2.html'>다시 로그인하기</a>");
				res.end();
			}
		});
	} else {  // 데이터베이스 객체가 초기화되지 않은 경우 실패 응답 전송
		res.writeHead('200', {'Content-Type':'text/html;charset=utf8'});
		res.write('<h2>데이터베이스 연결 실패</h2>');
		res.write('<div><p>데이터베이스에 연결하지 못했습니다.</p></div>');
		res.end();
	}
	
});


// 사용자 추가 라우팅 함수
router.route('/process/adduser').post(function(req, res) {
	console.log('/process/adduser 호출됨.');

    var paramId = req.body.id || req.query.id;
    var paramPassword = req.body.password || req.query.password;
    var paramName = req.body.name || req.query.name;
    var paramAge = req.body.age || req.query.age;
	
    console.log('요청 파라미터 : ' + paramId + ', ' + paramPassword + ', ' + paramName + ', ' + paramAge);
    
    // pool 객체가 초기화된 경우, addUser 함수 호출하여 사용자 추가
	if (pool) {
		addUser(paramId, paramName, paramAge, paramPassword, function(err, addedUser) {
			// 동일한 id로 추가하려는 경우 에러 발생 - 클라이언트로 에러 전송
			if (err) {
                console.error('사용자 추가 중 에러 발생 : ' + err.stack);
                
                res.writeHead('200', {'Content-Type':'text/html;charset=utf8'});
				res.write('<h2>사용자 추가 중 에러 발생</h2>');
                res.write('<p>' + err.stack + '</p>');
				res.end();
                
                return;
            }
			
            // 결과 객체 있으면 성공 응답 전송
			if (addedUser) {
				console.dir(addedUser);

				console.log('inserted ' + result.affectedRows + ' rows');
	        	
	        	var insertId = result.insertId;
	        	console.log('추가한 레코드의 아이디 : ' + insertId);
	        	
				res.writeHead('200', {'Content-Type':'text/html;charset=utf8'});
				res.write('<h2>사용자 추가 성공</h2>');
				res.end();
			} else {
				res.writeHead('200', {'Content-Type':'text/html;charset=utf8'});
				res.write('<h2>사용자 추가  실패</h2>');
				res.end();
			}
		});
	} else {  // 데이터베이스 객체가 초기화되지 않은 경우 실패 응답 전송
		res.writeHead('200', {'Content-Type':'text/html;charset=utf8'});
		res.write('<h2>데이터베이스 연결 실패</h2>');
		res.end();
	}
	
});



// 라우터 객체 등록
app.use('/', router);


// 사용자를 인증하는 함수
var authUser = function(id, password, callback) {
	console.log('authUser 호출됨 : ' + id + ', ' + password);
	
	// 커넥션 풀에서 연결 객체를 가져옴
	pool.getConnection(function(err, conn) {
        if (err) {
        	if (conn) {
                conn.release();  // 반드시 해제해야 함
            }
            callback(err, null);
            return;
        }   
        console.log('데이터베이스 연결 스레드 아이디 : ' + conn.threadId);
          
        var columns = ['id', 'name', 'age'];
        var tablename = 'users';
 
        // SQL 문을 실행합니다.
        var exec = conn.query("select ?? from ?? where id = ? and password = ?", [columns, tablename, id, password], function(err, rows) {
            conn.release();  // 반드시 해제해야 함
            console.log('실행 대상 SQL : ' + exec.sql);
            
            if (rows.length > 0) {
    	    	console.log('아이디 [%s], 패스워드 [%s] 가 일치하는 사용자 찾음.', id, password);
    	    	callback(null, rows);
            } else {
            	console.log("일치하는 사용자를 찾지 못함.");
    	    	callback(null, null);
            }
        });

        conn.on('error', function(err) {      
            console.log('데이터베이스 연결 시 에러 발생함.');
            console.dir(err);
            
            callback(err, null);
      });
    });
	
}

//사용자를 등록하는 함수
var addUser = function(id, name, age, password, callback) {
	console.log('addUser 호출됨 : ' + id + ', ' + password + ', ' + name + ', ' + age);
	
	// 커넥션 풀에서 연결 객체를 가져옴
	pool.getConnection(function(err, conn) {
        if (err) {
        	if (conn) {
                conn.release();  // 반드시 해제해야 함
            }
            
            callback(err, null);
            return;
        }   
        console.log('데이터베이스 연결 스레드 아이디 : ' + conn.threadId);

    	// 데이터를 객체로 만듦
    	var data = {id:id, name:name, age:age, password:password};
    	
        // SQL 문을 실행함
        var exec = conn.query('insert into users set ?', data, function(err, result) {
        	conn.release();  // 반드시 해제해야 함
        	console.log('실행 대상 SQL : ' + exec.sql);
        	
        	if (err) {
        		console.log('SQL 실행 시 에러 발생함.');
        		console.dir(err);
        		
        		callback(err, null);
        		
        		return;
        	}
        	
        	callback(null, result);
        	
        });
        
        conn.on('error', function(err) {      
              console.log('데이터베이스 연결 시 에러 발생함.');
              console.dir(err);
              
              callback(err, null);
        });
    });
	
}




// 404 에러 페이지 처리
var errorHandler = expressErrorHandler({
 static: {
   '404': './public/404.html'
 }
});

app.use( expressErrorHandler.httpError(404) );
app.use( errorHandler );


//===== 서버 시작 =====//

// 프로세스 종료 시에 데이터베이스 연결 해제
process.on('SIGTERM', function () {
    console.log("프로세스가 종료됩니다.");
});

app.on('close', function () {
	console.log("Express 서버 객체가 종료됩니다.");
});

// Express 서버 시작
http.createServer(app).listen(app.get('port'), function(){
  console.log('서버가 시작되었습니다. 포트 : ' + app.get('port'));
});
 
```



