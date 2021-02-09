---
layout: post
title: 'scoped packages - npm package 앞에 골뱅이란?'
date: 2021-02-09
categories: NPM
tags: [node.js, NPM]
# description: 'npm '
---

npm package를 보다보면 `@vue/cli`와 같은 골뱅이 기호가 앞에 붙는것을 볼 수 있다.

`vue`와 어떤것이 다른지 알아본다.

<br>

이것은 **scoped packages** 라고 불리는 NPM에 새로 추가된 기능이다.

NPM 패키지에 **네임스페이스**를 적용한것

- global modules : 현재 존재하는 컨벤션 네임을 따른 모듈.
- scoped modules : 조직이나 그룹에 "scoped" 된 새로운 모듈. `@조직이름/패키지이름`으로 설치 할 수 있다.