---
layout: post
title: '구닥다리 공룡을 위한 오늘날의 JavaScript'
date: 2021-02-03
categories: JavaScript
# link: https://steemit.com/javascript/@march23hare/javascript
tags: [JavaScript, NPM, webpack, babel]
description: 'Modern JavaScript Explained For Dinosaurs 번역'
---

# Modern JavaScript Explained For Dinosaurs

![](https://steemitimages.com/DQmTjgFdSuYBi4qqSxKmivun54Y7aLAkz6jChi7DU2Qxrdq/modern_dino_1.png)

오늘날 자바스크립트를 공부하는 것은 힘들다. 특히나 초창기부터 자바스크립트를 접하지 않은 사람이라면 더욱이..!
그정도로 자바스크립트의 생태계는 엄청 빠르게 성장하고 변하기 때문에 다양한 도구로 해결하는 문제 자체가 이해하기 어려워 졌다.

[Browserify](http://browserify.org/)라는 태그라인을 알고있는가?
> "Browserify는 브라우저에서 require('modules')를 사용하여 모든 의존성을 통합할 수 있다." <br>
라는 설명 문장으로 설명되어 있지만 이해하기 어렵다 😅

이 글에서는 website(단순한 HTML, JavaScript 외에 어떤 도구도 사용되지 않는)를 만들어 보고, 차차 구식 frontend 개발 생태계의 문제점들을 해결하는 도구들을 하나씩 소개할 예정이다. 

<br>

# 구식 자바스크립트 

일단, 수동으로 파일들을 다운받고 연결하는 구식 웹사이트로 시작해 본다.

이 웹사이트는 HTML과 자바스크립트를 사용한다. 여기 하나의 자바스크립트 파일이 연결된 간단한 index.html이 있다.

```html
<!-- index.html -->
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <title>자바스크립트 Example</title>
    <script src="index.js"></script>
</head>
<body>
    <h1>Hello from HTML!</h1>
</body>
</html>
```
이 행은 index.html과 같은 디렉토리에 있는 별도의 자바스크립트 파일인 index.js를 참조한다. index.js의 내용은 아래와 같다.

```javascript
console.log("Hello from 자바스크립트!");
```

이게 웹사이트를 만드는데 필요한 전부이다. 이제 여기에 외부 라이브러리를 추가할 예정이다. [moment.js](https://momentjs.com/)는 날짜 형식을 사람이 읽기 쉬운 형식으로 바꿀때 유용한 라이브러리이다. 예를 들어 아래와 같이 moment함수를 사용할 수 있다.

```js
moment().startOf('day').fromNow();  // 20 hours ago
```

그치만 이건 어디까지나 moment.js를 현재 웹사이트에 포함시켰단 가정하에 이야기이다. [moment.js의 홈페이지](https://momentjs.com/)를 방문해 보면 아래와 같은 지시사항을 볼 수 있다.

![monent.js install](/assets/post-img/modern-javascript-explained-for-dinosaur/momentjs_install.png)

오른쪽 인스톨 섹션은 일단 무시하자. index.html이 들어있는 디렉토리에 `moment.min.js`를 다운로드 한다. 그러면 아래의 코드처럼 index.html에 `moment.min.js`를 추가 시킬 수 있다.

```html
<!-- index.html -->
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Example</title>
  <link rel="stylesheet" href="index.css">
  <script src="`moment.min.js`"></script>
  <script src="index.js"></script>
</head>
<body>
  <h1>Hello from HTML!</h1>
</body>
</html>
```

`moment.min.js`가 index.js보다 앞서 로드 되고 있다는걸 주의해야한다. 이말은 index.js안에서 moment함수를 사용할 수 있게 되었다는것을 의미한다.

```js
// index.js
console.log("Hello from 자바스크립트!");
console.log(moment().startOf('day').fromNow());
```

그리고 이게 바로 웹사이트에 자바스크립트 라이브러리를 사용하는 방식이다(완전 구식😜 ). 이해하기 쉽다는게 이 방식의 장점이고, 단점은 라이브러리 제작자가 업데이트를 할 때마다 그것의 새 버전을 매번 찾아야 하고 다운로드하기 번거롭다는 것이다.

<br>

# 자바스크립트 패키지 매니저(npm) 사용하기

2010년 즈음, 중앙 레포지토리로부터 라이브러리 다운로드와 업그레이드를 자동화 해주는, 여러 경쟁적인 자바스크립트 패키지 매니저가 등장했다.
2013년 [Bower](https://bower.io/)는 거의 이껸 없이 최고의 인기 제품이었다 하지만, 2015년 즈음하여 [npm](https://www.npmjs.com/)이 그 인기를 따라잡았다. 2016년 말부부터는 [yarn](https://yarnpkg.com/en/)이 npm 인터페이스의 대안으로 나타났지만 yarn 역시 npm 패키지를 내부적으로 사용하고 있다.

npm은 원래 [node.js](https://nodejs.org/)를 위해 특별히 만들어진 패키지 매니저라는 점을 염두해야 한다. node.js는 프론트엔드가 아닌 서버 위에서 구동되도록 설계된 자바스크립트 실행 환경으로써 브라우저에서 실행되는 JS 라이브러리들을 위한 프론트엔드 자바스크립트 패키지 매니저로 선택했다는 건 꽤 괴팍하다고 할 수 있다. 서버 환경에서 쓰던걸 프론트엔드 환경에서 쓰려는 것이기 때문이다

> 🎈 Tip: 패키지 매니저를 쓴다는 것은 보통 커맨드라인 사용과 연관있다. 이전 프론트엔드 개발 방식(완전 구식)에서는 전혀 필요 없던 부분이다.
하지만 지금은 커맨드라인은 땔 수 없는 존재가 되었다. 커맨드라인을 사용함으로써 다른 개발 영역으로의 다양한 문을 열 수 있을 것이다.

완전 구식 방식의 단점으로 번거롭게 매번 수동으로 다운받는 것을 대신하여 npm을 사용하여 moment.js 패키지를 자동으로 설치하는지 확인 해보자.
npm을 사용하기 위해서는 [node.js](https://nodejs.org/)가 설치되어 있어야한다. LTS 버전을 이용하도록하자. 

> 🔨 버전 설치: 버전을 고를때는 신중해야한다. 너무 최신 버전을 고르는것 보다 두 버전을 낮춰서 설치하는 것을 추천한다. <br>
node.js 같은 경우 LTS 버전이 있는데 여기서는 이것을 선택한다. LTS(Long Term Support)로 오랫동안 이 버전에 대해 서포팅을 한다는 뜻인듯...

커맨드라인으로 index.html이 있는 디렉토리로 이동한 후 다음 명령어를 입력해봐야한다.

```sh
$ npm init
```

이 명령어를 입력하면 몇 가지 질의응답 과정이 이어지고, 응답은 기본 값으로 충분하다(모두 엔터키). 그러면 `package.json`이라는 새로운 파일 하나가 생성된다. 이 파일을 configuration(설정, 구성) 파일로 npm이 이 프로젝트에 대한 모든 정보를 저장하는데 사용된다. 기본적인 `package.json`의 내용은 아래와 같다.

```json
{
    "name": "my-project-name",
    "version": "1.0.0",
    "description": "",
    "main": "index.js",
    "scripts": {
        "test": "echo \"Error: no test specified\" && exit 1"
    },
    "author": "",
    "license": "ISC"
}
```

이제 moment.js 자바스크립트 패키지를 설치하기 위해서 moment.js의 홈페이지에 안내되어 있는 명렁어를 커맨드라인에 입력해야한다.

```sh
$ npm install moment
```

이 명령어를 통해 두가지 일이 수행된다. 첫번째 moment.js 패키지의 모든 코드를 내려받아 `node_modules`라는 폴더에 저장한다. 둘째로 `package.json` 파일이 아래로 수정된다. 이것으로 프로젝트가 의존성을 갖는 moment.js를 지속적으로 추적하도록 한다.

```json
{
    "name": "modern-자바스크립트-example",
    "version": "1.0.0",
    "description": "",
    "main": "index.js",
    "scripts": {
        "test": "echo \"Error: no test specified\" && exit 1"
    },
    "author": "",
    "license": "ISC",
    "dependencies": {
        "moment": "^2.19.1"
    }
}
```

이는 나중에 다른 개발자와 프로젝트를 공유할때 유용하게 사용된다. 보통 용량이 큰 `node_modules` 폴더를 직접 공유하는 대신 `package.json` 파일을 공유함으로써 다른 개발자들이 필요한 패키지들을 자동으로 설치할 수 있도록 한다.  ➡️ `npm install` 명령어를 통해서 

이제 더이상 프로젝트에 수동으로 moment.js를 내려받을 필요가 없어졌다. npm을 이용하여 자동으로 다운로드 받고 업데이트 받을 수 있다. `node_modules` 폴더 안을 살펴보면 `moment.min.js` 파일이 `node_modules/moment/min` 디렉토리 안에 있을것이다. ➡️ npm으로 다운로드 한 `moment.min.js`를 index.html에 연결 할 수 있다는 의미이다!!

```html
<!-- index.html -->
<!DOCTYPE html>
<html lang="ko">
<head>
  <meta charset="UTF-8">
  <title>JavaScript Example</title>
  <script src="node_modules/moment/min/moment.min.js"></script>
  <script src="index.js"></script>
</head>
<body>
  <h1>Hello from HTML!</h1>
</body>
</html>
```

커맨드라인으로 npm을 사용해 우리의 패키지들을 다운로드하고 업데이트 할 수 있다는 장점이 있다.
이 상황에서 나쁜 점은 패키지가 실제로 담겨있는 `node_modules` 폴더의 깊은 곳까지 파고들어야지만 수동으로 HTML에 불러올 수 있다는 것(꽤 나 불편).
그래서 이제는 자동으로 처리해 주는 방법을 살펴볼 예정이다.

![](https://steemitimages.com/DQmfM1oybkxdG8PoLPn1tn1i4huV3Gs3TJ5y4YmFh9RQu6b/modern_dino_2.png)

<br>

# 자바스크립트 모듈 번들러(webpack) 사용하기

대부분의 프로그래밍 언어들이 하나의 코드에서 다른 코드를 불러들이는 방법을 제공한다. 하지만 자바스크립트는 이런 기능이 없었다. 그 이유는 자바스크립트는 오직 브라우저 환경에서만 구동되도록 만들어졌고 보안상의 이유로 컴퓨터의 파일 시스템에 접근 할 수 없기 때문이다. 그래서 오랜 시간동안 다수의 파일로 자바스크립트 코드를 구성하는 일은 각 파일을 전역 변수로 할당하여 공유해야만 했다.

이전의 구식적으로 사용한 행위 역시 위 메커니즘을 이용하여 사용하였따. 예를 들어 전체 `moment.min.js` 파일은 HTML로 로드 된다. 그 안에 정의 되어있는 변수 moment는 전역 변수가 된다. 그 후에 로드 되는 모든 파일에서는 이 전역변수를 사용 할 수 있게된다.

2009년, CommonJS라는 이름의 프로젝트가 시작된다. 이것을 목표는 브라우저 외의 자바스크립트 생태계를 만드는 것이다. CommmonJS의 대부분은 모듈에 대한 명세이다. 이 명세로 마침내 자바스크립트가다른 대부분의 프로그래밍 언어들 처럼 파일을 통해 코드를 들여오고(import) 내보낼(export) 수 있게 된다. 전역 변수에 할당하는 방법 대신에... 이 CommonJS 모듈의 구현체 중 가장 유명한 것이 바로 node.js이다.

![](https://miro.medium.com/max/424/1*xeF1flp1zDLLJ4j7rDQ6-Q.png)

## node.js는 서버에서 구동되도록 설계된 자바스크립트 실행 환경이다.

초기 node.js 모듈 사용 예시를 살펴보자. `moment.min.js`를 HTML script tag로 불러오는 것 대신 자바스크립트 파일을 직접 자바스크립트 안에 불러올 수 있다.

```js
// index.js
var moment = require('moment');
console.log("Hello from 자바스크립트!");
console.log(moment().startOf('day').fromNow());
```

이것이 node.js에서 작동하는 모듈 로딩 방식이다. <br>
node.js가 컴퓨터의 파일 시스템에 접근 가능한 서버 사이드 언어이기에 가능한 것. node.js는 또 각 npm 모듈이 어느 경로에 위치하는지 알고 있다.<br>
이 덕분에 `require('./node_modules/moment/min/moment.min.js')`라고 쓰는 대신 `require('moment')`라고 쓸 수 잇다. 👍

node.js가 이렇게 좋지만, 위 코드를 부라우저 상에서 실행 하려고 하면 `require`가 정의되지 않았다는 에러가 뜬다. 😅 브라우저는 파일 시스템에 접근 권한이 없어서인데 이를 해결하기위한 방법은 매우 까다롭다.. <br>
파일들을 불러오는 것이 (실행속도를 느리게 하는)동기적인 것이든 (타이밍 문제가 있는)비동기적인 것이든 동적으로 수행되어야만 한다.
> `require`와 같은 모듈 로딩 구문은 실행시에 그 구문이 로드한 다른 파일의 코드로 대친된다.

**이러한 부분이 바로 모듈 번들러가 필요해 지는 지점이다.** 자바스크립트 모듈번들러는 파일 시스템에 접근 할 수 있는 빌드 단계에서 위와 같은 문제를 해결하여 브라우저와 호환되는 (파일 시스템에 엑세스 할 필요가 없는) 최종 결과물을 만들어내는 도구이다. 여기서 우리는 모든 `require` 구문을 찾아내어 이를 (실제 요구된 파일)실제 내용으로 대치시켜주는 모듈 번들러가 필요해진다. 이때 최종적인 결과물은 하나로 묶인(bundled) (require 구문이 없는) 단일 자바스크립트 파일인 되느 것이다.!!

가장 인기있었던 모듈 번들러는 [Browserify](http://browserify.org/)로, node.js 방식의 `require`구문을 프론트엔드에 사용하는데 있어 선구자적 역할을 했다. 근본적으로 npm이 프론트엔드 패키지 매니저가 될 수 있게 만들어준셈이다. 그 후 [webpack](https://webpack.github.io/)이 등장하면서 더 보편적으로 사용되는 모듈 번들러로 자리매김한다.
> webpack의 이점을 몽땅 활용 가능한 프론트엔드 ~~프레임워크~~인 React의 인기가 높아짐 <br>
- React는 프레임워크가 아닌 라이브러리.

## webpack 살펴보기

webpack을 써야 `require('moment')` 예제를 브라우저에서 돌릴 수 있다. 젤 처음으로 webpack을 프로젝트에 추가해야된다. webpack 자체는 그저 npm 패키지이고, 따라서 아래 커맨드로 설치 할 수 있다.

```sh
$ npm install webpack webpack-cli --save-dev
```

> 🎈 tip : `--save-dev` 인수로 설치하면 패키지가 개발용 의존으로 저장된다. <br>
- 패키지가 개발 환경에서만 쓰이고 서버에 올라간 제품 상태에선 사용하지 않을 것이란 의미.
- `package.json`에도 자동으로 반영됨. <br>
```json
{
  "name": "modern-자바스크립트-example",
  "버전": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"에러: no test specified\" && exit 1"
  },
  "author": "",
  "license": "ISC",
  "dependencies": {
    "moment": "^2.19.1"
  },
  "devDependencies": {
    "webpack": "^5.21.2",
    "webpack-cli": "^4.5.0"
  }
}
```

webpack이 패키지 중 하나로써 `node_modules` 폴더에 설치가 된다. 커맨드라인에 아래처럼 입력함으로써 webpack을 사용할 수 있다.

```sh
$ ./node_modules/.bin/webpack index.js --mode=development
```

이 명령은 `node_modules`에 설치되어 있는 webpack 도구를 실행시킨다. 실행되는 도구는 `index.js` 파일을 읽기 시작하여 `require` 구문을 찾고 이를 다른 파일의 코드로 대치시킨 최종 결과물로써 `dist/main.js`를 출력한다. 이제 HTML에서 더 이상 (`require` 구문이 있는)`index.js`를 쓸 수 없게 된다. 하지만 `dist/main.js`를 쓰면된다.

```html
<!-- index.html -->
<!DOCTYPE html>
<html lang="ko">
<head>
  <meta charset="UTF-8">
  <title>자바스크립트 Example</title>
  <script src="dist/main.js"></script>
</head>
<body>
  <h1>Hello from HTML!</h1>
</body>
</html>
```

브라우저를 새로고침 해보면 이전과 마찬가지로 모든 것이 정상 작동하는 것을 볼 수 있다.

`index.js`가 수정 될 때마다 webpack 명열을 실행해야 하는데 이것은 상당히 귀찮다. 여기에 webpack의 고급 기능을 사용하면 더 귀찮아진다.<br>
이 점을 해결하기위해서 webpack은 config(설정) 파일로부터 옵션들을 설정할 수 있다. 이 config 파일은 프로젝트의 root 폴더에 위치하며 `webpack.config.js`로 만들면 된다. 

```js
// webpack.config.js
module.exports = {
    mode: 'development',
    entry: './index.js',
    output: {
      filename: 'main.js',
      publicPath: 'dist'
    }
  };
```

위와 같이 설정을 해두면 이제 `index.js`가 변경될 때 마다 아래 명령어를 통해 사용할 수 있다.

```sh
$ ./node_modules/.bin/webpack
```

`webpack.config.js`에 설정한 옵션들을 webpack이 알아서 읽기 때문에 좀 더 편한 방법이다. 그러나 명령어를 매번 입력하는 것도 역시 귀찮다. 이걸 좀더 편하게 만들어 보자.

지금까지 워크 플로우엔 상당한 이점이 있는데 더이상 전역변수로 외부 스크립트를 불러들일 필요가 없는 점이다. 어느 자바스크립트 라이브러리도 HTML 태그 대신 자바스크립트의 `require` 구문을 통해 추가될 것이다. 단일 자바스크립트 묶음 파일을 갖는 것은 성능면에서도 더 좋다. 그리고 이제 또 다른 강력한 기능들을 개발 workflow에 추가 할 수 있는 빌드 단계가 추가된다. 

![](https://steemitimages.com/DQmeBBfUCYJn6NFQCzyBXyeQTNh6CycLpnfTaUuKa4NyU3H/modern_dino_3.png)

<br>

# 언어의 새로운 기능을 사용하기 위한 transpiling (bable)



<br>

> [참고] <br>
[https://medium.com/the-node-js-collection/modern-javascript-explained-for-dinosaurs-f695e9747b70](https://medium.com/the-node-js-collection/modern-javascript-explained-for-dinosaurs-f695e9747b70) <br>
[https://steemit.com/javascript/@march23hare/javascript](https://steemit.com/javascript/@march23hare/javascript)