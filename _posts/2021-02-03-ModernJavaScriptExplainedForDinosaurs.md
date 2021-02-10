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

<br>

> [참고] <br>
[https://medium.com/the-node-js-collection/modern-javascript-explained-for-dinosaurs-f695e9747b70](https://medium.com/the-node-js-collection/modern-javascript-explained-for-dinosaurs-f695e9747b70)