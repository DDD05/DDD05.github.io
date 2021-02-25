---
layout: post
title: '[Vue.js] 동적 모달 만들기'
date: 2021-02-24
categories: 'Vue.js'
tags: [JavaScript, Vue.js, HTML, CSS, modal]
description: '모달을 만들고, 효율적으로 관리하는 방법 등을 Vue.js를 통해 설명.'
---

# 📖 들어가기

프론트 디자인을 구성하다보면 Modal부분이 거의 필수적으로 사용되기 마련이다. 요즘은 여러 라이브러리에서 이런 모달들을 지원해주기 때문에 따로 내가 만들 필요는 없지만
나만의 커스텀 디자인을 만들고 싶은 경우도 있고, 어떤 구조로 만들어지는 궁금하기도하다. 그래서 오늘은 모달은 만드는 방법과 효율적으로 모달을 관리하는 방법을 설명해보려고한다.

> 전통적인 HTML로 먼저 Modal을 구현하고 이후에 Vue.js에서 컴포넌트 방식으로 모달을 관리하는 방법을 설명.

## 기술 스택

-   HTML
-   CSS
-   Vue.js(JS)

<br>

# HTML에서 Modal 만들기

모달이라고하면 어떤 버튼을 눌렀을때 새로운 작은 창이 생기면서 클릭한 부분의 서브 화면을 노출하는 것을 목적으로 사용합니다.

> **모달, 팝업 차이?** <br>
> 모달과 팝업은 비슷하지만 약간의 차이가 있습니다. 팝업은 새로운 윈도우창에 생겨난다는 점과 모달은 기존 윈도우화면을 덮으며 위로 생겨나는 창을 말합니다.

## HTML 코드

어떤 구조로 만들어 지는지 설명하기 위해서 HTML 코드를 이용해서 태그들이 어떤 방식으로 그려지고 사용되는지 살표 봅니다.

```html
<div id="app">
	<label id="modal__btn" for="modal-status"> 버튼 </label>
	<input type="checkbox" id="modal-status" />
	<div id="modal-wrap">
		<div id="modal--box"></div>
		<label id="modal-bg" for="modal-status" />
	</div>
</div>
```

모달이 현재 열려있는지 상태를 확인하기 위해서 체크박스(`modal-status`)를 만듭니다. 이 체크박스를 통해서 모달이 열려있는지 닫혀있는지를 확인할 것입니다. <br>
그리고 라벨(`modal__btn`)을 이용해 간단한 버튼을 만듭니다. 이 라벨은 체크박스의 상태를 변경하는 역할을 합니다.

이제 모달 화면을 만들 차례입니다. `modal-wrap`을 이용해서 모달 화면을 감싸줄 태그를 만듭니다. 모달안에는 까만 배경과 가운데 하얀 박스 모양으로 만들겁니다. 
`modal--box`를 만들고 그 아래에 `modal-bg`를 만듭니다. `modal-bg`를 라벨로 잡은 이유는 배경을 누르면 모달이 닫히게끔 만들기 위해서입니다.

> **모달과 모달리스 차이점** <br>
> 모달은 백그라운드를 가리고 자신의 작업에 집중할 수 있도록 되어있고 모달리스경우는 뒤에 그려진 화면과 상호작용할 수 있는 차이점이 있습니다.

이제 HTML은 준비가 끝났습니다. 여기에 CSS를 입히면 간단한 모달을 완성할 수 있습니다.

## CSS 코드

```css
html,
body,
#app {
	padding: 0;
	margin: 0;
	height: 100%;
	width: 100%;
}
#modal__btn {
	display: inline-block;
	margin: 10px;
	border-radius: 20px;
	box-shadow: #000 0px 0px 3px;
	padding: 5px 20px;
	cursor: pointer;
	user-select: none;
}
#modal-bg {
	position: fixed;
	top: 0;
	left: 0;
	display: block;
	height: 100%;
	width: 100%;
	background: #ddd;
	opacity: 0.6;
	z-index: 100;
}
#modal--box {
	position: absolute;
	top: 50%;
	left: 50%;
	transform: translate(-50%, -50%);
	min-width: 500px;
	min-height: 500px;
	background: #fff;
	z-index: 101;
}
#modal-status + #modal-wrap {
	display: none;
}
#modal-status:checked + #modal-wrap {
	display: initial;
}
```

위 코드를 보면서 하나하나씩 설명하겠습니다.

- **html, body, #app** : padding과 margin을 0을 줘서 기본적으로 브라우저에서 제공하는 태그의 속성값을 없앴습니다. height, width의 값에 100%를 준 이유는 화면 전체를 사용하기 위해서입니다. 기본적으로 퍼센트는 상위 부모의 값을 기준으로 가져오기 때문에 자식 요소에서 100%를 사용하기 위해서는 부모로부터 그 기준을 선택해줘야합니다.

- **#modal__btn** : 단순 버튼 모양을 만드는 코드라 설명은 넘어가겠습니다.

- **#modal-bg** : 