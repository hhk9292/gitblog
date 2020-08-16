---
title: "Vue에서 Kakao SDK for JavaScript를 사용한 카카오 로그인 구현"
date: 2020-08-16T22:57:58+09:00
typora-copy-images-to: ./
typora-root-url: ..
---



### 0. 공식문서

[kakao developers](https://developers.kakao.com/docs/latest/ko/kakaologin/common)



### 1. 카카오 로그인 프로세스

![kakaologin_process](/vue/images/kakaologin_process.png)

로그인 과정은 다음과 같다.

1. 인증 코드 요청
2. 인증 코드 전달
3. 인증 코드로 토큰 요청
4. 토큰 전달

여기까지가 로그인 과정이다. 여기서 1, 2번 과정은 프론트(`Vue.js`)만으로도 구현할 수 있지만 3, 4번 과정은 서버와 서버 사이에서 요청과 응답이 이뤄지기 때문에 `node.js`와 `express`를 이용해 로컬 서버를 만들 것이다. 4번 과정까지 거치면 다른 API요청을 할 때 사용하는 토큰이 발급되고 이를 다시 프론트로 넘겨주는 과정까지 이번 글에서 구현할 예정이다.

`Vue.js`에서 카카오 서버로 요청을 보낼 때에는 JavaScript 키를 이용할 것이고, 서버에서 카카오 서버로 요청을 보낼 때에는 REST API키를 이용할 것이다.

