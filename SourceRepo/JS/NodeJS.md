
---
- [모듈](#module)

###### module
모듈

-

require();

모듈을 리턴하는 함수이다.

```
module.js(test)

var o = require('os');
console.log(o.platform());

>node modue.js

결과: win32
```

npm = node package manager

[](https://www.npmjs.com)

** uglify-js 설치 *

```
npm install uglify-js -g

// -g는 옵션인데 글로벌(전역)적으로  실행 ,쓰겟다는것(독립)
빼면 현재의 디렉토리해당 프로젝트의 부품으로 사용하겟다는뜻..(뭔소린지..)

```

사용
```
uglifyjs pretty.js

uglifyjs pretty.js -m //지역변수까지 변경

uglifyjs pretty.js -o uglified.js -m//지역변수까지 변경+ 해당이름으로(uglified.js) 저장

```
부트스트랩의 min.js 처럼 공백,지역변수등을 한줄로 최적화하여 용량이나 성능 등의 향상 시킨다.



다른사람이 만든 패키지를 가져오기 위해
npm init 해줘야한다.

(패지키 디렉터리를 지정)

-> 내 디렉터리에 package.json이 생김

underscore 설치
```
> npm install underscore

```
설치후 로그에 extraneous 라는 단어가 초록색글씨로 나타나는데, 온전하지 않다는 뜻이다.
```
> npm install underscore --save
```

save를 넣어주면 package.json에 dependenies 의존성 부분이 명시적으로 추가된다.

이렇게 설정이되면 자신의 개인 프로젝트에 언제든지 package.json만 있으면 언제든지 포함시킬수 있다는것.. 기본적으로 --save 적도록 한다.

underscore는 기본적인 js의 빈약한 배열기능을 채워주는 기능이 있다.
