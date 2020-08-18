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

여기까지가 로그인 과정이다. 여기서 1, 2번 과정은 프론트(`Vue.js`)만으로도 구현할 수 있지만 3, 4번 과정은 서버와 서버 사이에서 요청과 응답이 이뤄지기 때문에 `Node.js`와 `express`를 이용해 로컬 서버를 만들 것이다. 4번 과정까지 거치면 다른 API 요청을 할 때 사용하는 토큰이 발급되고 이를 다시 프론트로 넘겨주는 과정까지 이번 글에서 구현할 예정이다.

`Vue.js`에서 카카오 서버로 요청을 보낼 때에는 JavaScript 키를 이용할 것이고, 서버에서 카카오 서버로 요청을 보낼 때에는 REST API 키를 이용할 것이다.



### 2. 구현

#### 0. 사용 기술

- [Node.js](https://nodejs.org)
  - express
  - axios
  - cors
- [Vue.js](https://vuejs.org/)
  - Vue router
  - axios
  - qs



#### 1. 프로젝트 시작

먼저 새로운 프로젝트를 시작하기 위해 다음 명령어를 입력한다.

```bash
$ vue create front
$ cd front
$ npm run serve
```

[http://localhost:8080](http://localhost:8080)에 들어갔을 때 다음과 같은 화면이 나오면 프로젝트가 잘 시작된 것이다.

![vue_0](/vue/images/vue_0.png)

서버를 닫고 필요한 모듈을 설치한다.

```bash
$ vue add router
$ npm i axios
$ mpm i qs
```

`Vue router`의 경우 history 모드를 선택하는 화면에서 Y를 입력하여 설치한다. 필요 없는 파일들을 수정 및 삭제하면 기본 세팅은 끝이 난다.

`@/views`경로에 `KakaoLogin.vue` 파일을 생성한다.

```vue
<!-- KakaoLogin.vue -->
<template>
  <div>
    <h1>Kakao Login</h1>
  </div>
</template>

<script>
export default {
  name: 'KakaoLogin',
}
</script>

<style>

</style>
```

`@/router/index.js`파일에 다음과 같이 추가한다.

```javascript
// router/index.js

import KakaoLogin from '../views/KakaoLogin.vue'

const routes = [
  ...
  {
    path: '/kakaologin',
    name: 'KakaoLogin',
    component: KakaoLogin
  },
  ...
]
```



#### 2. Kakao SDK for JavaScript 사용

[Kakao SDK for JavaScript](https://developers.kakao.com/docs/latest/ko/getting-started/sdk-js)

`public/index.html`에 `<script src="https://developers.kakao.com/sdk/js/kakao.js"></script>` 코드를 삽입하면 `Kakao SDK for JavaScript`를 사용할 수 있다.

```html
<!-- public/index.html-->
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width,initial-scale=1.0">
    <link rel="icon" href="<%= BASE_URL %>favicon.ico">
    <title><%= htmlWebpackPlugin.options.title %></title>
    <script src="https://developers.kakao.com/sdk/js/kakao.js"></script>
  </head>
  <body>
    <noscript>
      <strong>We're sorry but <%= htmlWebpackPlugin.options.title %> doesn't work properly without JavaScript enabled. Please enable it to continue.</strong>
    </noscript>
    <div id="app"></div>
    <!-- built files will be auto injected -->
  </body>
</html>
```



```javascript
// SDK를 초기화 합니다. 사용할 앱의 JavaScript 키를 설정해 주세요.
Kakao.init('JAVASCRIPT_KEY');

// SDK 초기화 여부를 판단합니다.
console.log(Kakao.isInitialized());
```

Javascript 키를 숨기기 위해 위 코드를 `index.html`이 아닌 `App.vue`의 `created()`부분에 넣었다.

```vue
<!-- App.vue -->
<script>
const JS_KEY = process.env.VUE_APP_JS_KEY
export default {
  name: 'App',
  created() {
    Kakao.init(JS_KEY)
    console.log(Kakao.isInitialized())
  }
}
</script>
```

 위 코드를 `public/index.html`에 넣어서 실행하면 에러가 나지 않지만 `App.vue`에서는 `'Kakao' is not defined`라는 에러가 나면서 실행이 되지 않을 것이다. 이를 해결하려면 `Kakao`를 전역변수로 등록해야한다.

```javascript
// main.js
Vue.prototype.$Kakao = window.Kakao
```

위 코드를 넣어주고 

```vue
<!-- App.vue -->
<script>
const JS_KEY = process.env.VUE_APP_JS_KEY
export default {
  name: 'App',
  created() {
    this.$Kakao.init(JS_KEY)
    console.log(this.$Kakao.isInitialized())
  }
}
</script>
```

이렇게 변경해주면 잘 작동한다. 

`KakaoLogin.vue`가 아닌 `App.vue`에 넣는 이유는 `KakaoLogin.vue`에 넣으면 수정사항이 있을 때마다 `this.$Kakao.init(JS_KEY)` 함수가 실행되어 `"KakaoError: Kakao.init: Already initialized."`오류가 나기 때문이다. 여기 까지 하면 ` Kakao SDK for JavaScript`를 사용할 준비가 끝난 것이다. 마지막으로  [내 애플리케이션](https://developers.kakao.com/console/app/)의 카카오 로그인 항목에서 `Redirect URI`를 `http://localhost:8080/kakaologin`으로 설정한다.



#### 3. 카카오 로그인 연동

