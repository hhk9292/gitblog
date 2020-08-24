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
  - dotenv
  - qs
- [Vue.js](https://vuejs.org/)
  - Vue router
  - axios



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
    <script src="https://developers.kakao.com/sdk/js/kakao.js"></script>
  </head>
  <body>
    <noscript>
    <div id="app"></div>
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

##### 3-1 Front

[로그인 데모](https://developers.kakao.com/tool/demo/login/login)

위 문서에 있는 코드를 적절히 수정하여 옮겨 쓴다.

- 로그인 버튼

  ```html
  <a id="custom-login-btn" @click="loginWithKakao" >
        <img
         src="//k.kakaocdn.net/14/dn/btqCn0WEmI3/nijroPfbpCa4at5EIsjyf0/o.jpg"
         width="222"
        />
  </a>
  ```

- 로그인 함수

  ```javascript
  const redirectUri = 'http://localhost:8080/kakaologin'
  methods: {
    loginWithKakao() {
        this.$Kakao.Auth.authorize({
           redirectUri
        })
     },
  },
  ```

  

여기까지 하면 아래와 같은 화면이 나타난다.

![vue_1](/vue/images/vue_1.png)

여기서 로그인 버튼을 누르면

![vue_2](/vue/images/vue_2.png)

이렇게 주소창에 쿼리문으로 코드가 온다. 이제 이 코드를 받아서 백으로 넘기고 백에서 이 코드를 이용해 토큰을 받고 프론트로 넘겨주면 로그인이 완료된다.

- 코드를 받는 함수

  ```javascript
  computed: {
     getCode() {
        return this.$route.query.code
     }
  },
  ```

- 코드 확인

  ```html
  <p>{{ getCode }}</p>
  ```

이제 로그인 버튼을 누르면 아래와 같은 화면이 나온다.

![vue_3](/vue/images/vue_3.png)

코드가 잘 도착하는 것을 확인했으므로 백으로 넘겨주는 함수를 만든다.

- 코드를 백으로 넘겨주는 함수

  ```javascript
  // KakaoLogin.vue 
  import axios from 'axios'
  const BASE_URL = 'http://localhost:3000'
  
  methods: {
    login() {
      const LOGIN_URL = BASE_URL + '/login'
      const data = {
        code: this.getCode
      }
      const config = { data }
      axios.post(LOGIN_URL, config)
      .then(res => console.log(res))
      .catch(err => console.log(err))
    }
  }
  
  mounted() {
    if (this.getCode) {
      this.login()
    }
  }
  ```

  `mounted()`에 함수를 넣어주는 이유는 코드를 받아오는 과정에서 새로고침이 발생하기 때문이다. 즉 `watch`에 `getCode`를 걸어서 사용하면 아무런 반응이 없다.



##### 3-2 Back

`back` 폴더를 만들고 `index.js` 파일을 생성한 다음 `$ npm init`을 입력하여 프로젝트를 시작한다.

```bash
$ npm i express
$ npm i axios
$ npm i cors
$ npm i dotenv
$ npm i qs
```

```javascript
// index.js

const express = require('express');
const axios = require('axios');
const cors = require('cors');
const dotenv = require('dotenv');
const qs = require('qs');
const app = express();



// cors
app.use(cors());

// body-parser
app.use(express.json()); 

// env
dotenv.config();

const PORT = 3000;

const welcome = function(req, res) {
    res.send('hello')

const portOn = function() {
    console.log('listening on port 3000')
}

app.get('/', welcome)
app.listen(PORT, portOn)
```

간단하게 백을 구성하고 터미널에 `$ node index.js` 입력하여 서버가 열린 것을 확인한다.

![vue_4](/vue/images/vue_4.png)

또한 `KakaoLogin.vue`에 다음과 같은 코드를 입력하여 백과 연결되었는지도 확인한다.

```Vue
created() {
  const WELCOME_URL = BASE_URL + '/'
  axios.get(WELCOME_URL)
  .then(res => console.log(res.data))
  .catch(err => console.log(err))
}
```

콘솔창에 `hello`가 출력되면 프론트와 백이 연결된 것이다.

`index.js`에 `app.user(express.json())` 코드를 추가하고

```javascript
// index.js

// '/login'
const getCode = function(req, res) {
  console.log(req.body.data.code)
}

app.post('/login', getCode)
```

위와 같은 함수를 추가하고 콘솔창에 코드가 나오는지 확인한다.

![vue_5](/vue/images/vue_5.png)

![vue_6](/vue/images/vue_6.png)

이제 이 코드를 카카오 서버로 보내고 토큰값을 받아 프론트로 넘기는 함수를 만들어준다.

```java
// index.js

// '/login'
const getCode = function(req, res) {
    console.log(req.body.data.code)
    const TOKEN_URL = "https://kauth.kakao.com/oauth/token"
    const grant_type = "authorization_code"
    const client_id = process.env.REST_KEY
    const redirect_uri = "http://localhost:8080/kakaologin"
    const code = req.body.data.code
    const headers = {
        "Content-type": "application/x-www-form-urlencoded;charset=utf-8"
    }
    const data = qs.stringify({
        grant_type,
        client_id,
        redirect_uri,
        code
    })
    axios.post(
        "https://kauth.kakao.com/oauth/token",
        data,
        headers
    )
    .then(response => {
        console.log(response.data)
        const accessToken = response.data.access_token
        res.send({accessToken})
    })
    .catch(error => console.log(error))
}
```

이렇게 함수를 만들고 프론트에서는 아래와 같이 `login()` 함수를 변경한다.

```javascript
methods: {
  login() {
	const LOGIN_URL = BASE_URL + '/login'
    const data = {
        code: this.getCode
    }
    const config = { data }
    axios.post(LOGIN_URL, config)
    .then(res => {
      console.log(res.data.accessToken)
      const token = res.data.accessToken
      this.$Kakao.Auth.setAccessToken(token)
      const savedToken = this.$Kakao.Auth.getAccessToken()
      console.log(savedToken)
      })
    .catch(err => console.log(err))
    },
  }
}
```

토큰을 잘 받아왔는지 `this.$Kakao.Auth.setAccessToken(),this.$Kakao.Auth.getAccessToken() `을 이용해 확인한다. 이제 다른 페이지에서 `this.$Kakao.Auth.getAccessToken() `을 통해 토큰을 이용할 수 있다.

