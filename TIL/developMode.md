# development mode

: 개발모드는 개발과정을 위해 지원되는 환경, 개발하는 과정에서는 요구사항 분석 및 필요기능 구현을 반복한는데, 개발모드는 이를 위한 편의성을 제공해준다.(개발 생산성을 향상 시키는 옵션들)

개발모드에서는 개발 서버를 제공해준다.

```
// 웹팩 dev 서버 모듈 설치
$ npm i -D webpack-dev-server
```

서버를 실행시키기 위해서는 모듈에서 지원하는 명령어를 실행시켜주어야 한다.

```
// 모듈 안에서 제공되는 실행파일을 통해 실행하기 위해서는 node_modules안에 있는 .bin 폴더에 접근해야한다.

$ ./node_modules/.bin/webpack-dev-server
```

이 명령어를 사용하면 아래처럼 에러가 발생한다. 

> `internal/modules/cjs/loader.js:883
  throw err;
  ^

> Error: Cannot find module 'webpack-cli/bin/config-yargs'
> Require stack:

> /Users/yongminkim/Desktop/playground/eliie/webpackPractice/node_modules/webpack-dev-server/bin/webpack-dev-server.js
    at Function.Module._resolveFilename (internal/modules/cjs/loader.js:880:15)
    at Function.Module._load (internal/modules/cjs/loader.js:725:27)
    at Module.require (internal/modules/cjs/loader.js:952:19)
    at require (internal/modules/cjs/helpers.js:88:18)
    at Object.<anonymous> (/Users/yongminkim/Desktop/playground/eliie/webpackPractice/node_modules/webpack-dev-server/bin/webpack-dev-server.js:65:1)
    at Module._compile (internal/modules/cjs/loader.js:1063:30)
    at Object.Module._extensions..js (internal/modules/cjs/loader.js:1092:10)
    at Module.load (internal/modules/cjs/loader.js:928:32)
    at Function.Module._load (internal/modules/cjs/loader.js:769:14)
    at Function.executeUserEntryPoint [as runMain] (internal/modules/run_main.js:72:12) {
  code: 'MODULE_NOT_FOUND',
  requireStack: [
    '/Users/yongminkim/Desktop/playground/eliie/webpackPractice/node_modules/webpack-dev-server/bin/webpack-dev-server.js'
  ]
    }`

그 이유는 package.json에서 npm run dev, npm run build 같이 기존 웹팩 명령어 수행시 어떠한 환경 모드로 실행할지 설정해주었다. 

웹팩 dev 서버 또한 --config 플래그를 이용하여 실행시켜야한다.

```
$ ./node_modules/.bin/webpack-dev-server --config webpack.dev.js
```

> 지금 이 명령어를 사용해도 같은 에러가 발생하는데, webpack, webpack-cli와 web pack-dev-server의 버전 호환이 제대로 되지 않은경우 발생하는 에러라고 한다. 

`npm uninstall -D webpack webpack-cli` 명령을 실행한 후, `npm i -D webpack webpack-cli` 를 실행시키면 된다고 하는데도 안된다. 이런경우에는 `npx webpack serve` 명령어를 이용하여 서버를 실행시키면 동작한다..

```json
"scripts": {
    "dev": "NODE_ENV=DEVELOPMENT webpack --config webpack.dev.js",
    "build": "NODE_ENV=PRODUCTION webpack --config webpack.prod.js",
    "start": "npx webpack serve --config webpack.dev.js"
  },
//script에 명령어 등록 -> npm start
```

localhost:8080에 접속.

개발모드로 동작시킨 서버에서는 dist 폴더의 내용과는 상관없이 빌드 결과물이 실행된다. 이는 빌드 결과물이 파일로써 쓰여지지 않는 것이다. webpack dev서버에서 동작된 빌드 결과물은 메모리상에 존재하기 때문에 직접 파일을 쓰고 지우는 것보다 훨씬 빠르다. 수정된 값도 메모리상에 있는 값과 비교하여 바로 수정된 사항의 피드백을 빠르게 받을 수 있다.

기존에는 dist/index.html로 결과하였지만, dev 서버를 이용하면 서버가 띄워진 상태라서 접근도 쉽고 cors 설정내용도 api서버를 만들게 되면 확인 가능하고, http url로 접근이 가능하여 실제 웹에서 어떻게 동작할지 가늠할 수 있다.

## history API fallback

특정 라우팅 지정시, 제공하지 않는 라우팅으로 갈 경우 예외처리를 하고나, 특정 라우팅은 특정 html 문서로 이동시키도록 지정 가능, devServer 키 안에서 사용한다. 기본상태에서는 우리가 지정하지 않는 routing에 접근시 ex) localhost:8080/hello 에 접근하면 404 not found가 표시된다. 

```javascript
// 기본 설정
const config = {
  mode: 'development',
  devServer: {
    historyApiFallback: false
  }
}
```

이 설정을 historyApiFallback: true로 바꾸면 기본 index.html을 표시해준다. (기본값이 index.html)

spa같이 html5 history api를 사용하는 경우에 설정하는데, true를 주지않고 객체를 할당하면, 특정경로를 특정 파일로 이동시킬수 있다.

```javascript
devServer: {
    historyApiFallback: {
      // from에는 라우팅 입력된 값을 정규표현식으로 매칭
      // to는 해당 매칭값에 따른 redirect파일을 지정.
      // /subpage로 접근시 subpage.html로 이동시켜주는 설정.
      rewrites: [{ from: /^\/subpage$/, to: 'subpage.html' },
                 // 특정 경로 제외 모든 페이지는 404.html로 이동
                 { from: /./, to : '404.html' }], d       
    },
  },
```

정적 사이트를 만들 경우에 자주 사용한다.

## open option

이 옵션을 사용할 경우, 웹팩 관련 스크립트를 사용하면 기본 브라우저가 자동으로 실행된다.

```javascript
devServer: {
    open: true, // open key 지정. -> 기본 브라우저를 기반으로 새창이 열리면서 브라우저를 따로 열지 않게 해준다.
    historyApiFallback: {
      rewrites: [{ from: /^\/subpage$/, to: 'subpage.html' }],
    },
  },
```



## overlay option

: 에러 발생시, console창이나 터미널로 확인하는것이 아니라 , 에러메시지 자체가 브라우저 화면 자체에 나타난다.

```javascript
devServer: {
	overlay: true,
}
```



## port option

자동으로 설정된 8080포트값을 임의의 포트 값으로 수정이 가능하게 해준다. 기본값 = port:8080


```javascript
devServer: {
	port: 3333,
	// 3333포트로 브라우저 창이 열린다.
}
```

