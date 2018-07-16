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

                                            
---

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
