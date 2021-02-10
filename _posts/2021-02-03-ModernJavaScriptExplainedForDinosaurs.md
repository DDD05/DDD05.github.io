---
layout: post
title: '구닥다리 공룡을 위한 오늘날의 JavaScript'
date: 2021-02-03
categories: JavaScript
link: https://steemit.com/javascript/@march23hare/javascript
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

# 자바스크립트 패키지 매니저(npm) 사용하기

<br>

> [참고] <br>
[https://medium.com/the-node-js-collection/modern-javascript-explained-for-dinosaurs-f695e9747b70](https://medium.com/the-node-js-collection/modern-javascript-explained-for-dinosaurs-f695e9747b70)