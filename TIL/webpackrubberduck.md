* 웹팩: 프론트엔드에서 가장 많이 사용되는 모듈 번들러.
* 모듈 번들러란 웹 앱을 구성하는 자원(HTML, CSS, JS, images 등)을 모두 각각의 모듈로 보고, 이를 조합해서 하나의 병합된 결과물을 만드는 도구이다.
* 모듈: 특정 기능을 갖는 작은 코드 단위. 성격이 비슷한 기능들을 하나의 의미있는 파일로 관리하면 모듈이 된다.
* 자바스크립트의 변수 유효 범위는 기본적으로 전역이다. 최대한 넓은 변수 범위를 갖기 때문에 어디서든 접근하기 편리하지만 이러한 점이 실제 웹앱을 개발할 때는 문제점이다. 변수의 이름을 모두 기억하기도 힘들고, 같은 이름을 가진 함수명 변수명들이 생기기 쉬운데 이러한 것이 중복 선언되면서 의도치 않은 버그가 발생한다.
* 예전의 프론트엔드 개발에서는 IDE에서 코드를 수정하고 브라우저를 새로고침 해주어야 변경된 내용을 볼 수 있었다. 또한 웹 서비스를 개발하고 웹서버에 배포할 때, html,css,js압축, 이미지 압축, css전처리기 변환등의 작업을 해줘야 했었다. 이러한 작업을 자동화해주는 grunt, gulp등이 등장했다.

## 웹팩으로 해결하려는 문제

1. 자바스크립트 변수 유효범위: 웹팩은 변수 유효범위의 문제점을 es6의 modules문법과 웹팩의 모듈 번들링으로 해결한다.

2. 브라우저별 http요청 숫자의 제약: 브라우저별로 한번에 서버로 보낼 수 있는 http 요청 숫자는 제약되어 있다. (모던 브라우저 최대 연결횟수 6) 따라서 http 요청 숫자를 줄이면 웹 앱의 성능을 높여줄 수 있고, 사용자가 사이트를 조작하게 되는 시간을 앞당겨 줄수 있다. 웹팩을 이용해 여러 파일을 하나로 합치면 http 요청 숫자 제약에서 유리해 질수 있다.

3. 사용하지 않는 코드의 관리

4. dynamic loading & lazy loading 미지원

   3, 4는 이전에는 라이브러리를 쓰지 않으면, 원하는 시점에 모듈을 로딩하는 것이 불가능 했다. 웹팩의 code splitting 기능을 이용하면 원하는 모듈을 원하는 타이밍에 로드할 수있다.

## 웹팩 실행

```json
// package.json

"scripts": {
  "build": "webpack"
}
```



```js
// webpack.config.js
// package.json의 webpack명령어가 이 config 파일을 기본값으로 사용한다.
const path = require('path');

module.exports = {
  mode: 'none',
  entry: './src/index.js',
  output: {
    filename: 'main.js',
    path: path.resolve(__dirname, 'dist')
  }
}
```

## 웹팩의 4가지 주요 속성

웹팩의 빌드(파일 변환) 과정을 이해하기 위해서 아래 4가지 주요 속성에 대해 알고 있어야 한다.

1. entry: 웹팩이 웹 자원을 변환하기 위해 필요한 최초 진입점이자 js 파일 경로. entry 파일에는 웹 어플리케이션의 전반적 구조와 내용이 담겨져 있어야 한다. 웹팩이 해당 파일을 읽어서 사용되는 모듈들의 연관관계를 이해하고 분석하기 때문이다.(모듈간의 의존관계가 생기는 구조-> dependency graph)

   ```js
   module.exports = {
     entry: './src/index.js'
   }
   // 웹팩 실행시 src폴더의 index.js를 대상으로 웹팩이 빌드를 수행한다.
   ```

   

2. Output: 웹팩이 빌드하고 난 결과물의 파일 경로이다. 객체 형태로 option을 추가해준다.

   ```javascript
   const path = require('path');
   
   module.exports = {
     output: {
       filename: 'bundle.js', // 웹팩으로 빌드한 파일의 이름
       path: path.resolve(__dirname, './dist') // 파일의 경로
     }
   }
   ```

3. Loader: 로더는 웹팩이 웹 어플리케이션을 해석할 때, js 파일이 아닌 웹 자원(HTML, CSS, images, 폰트)등을 변환할 수 있도록 도와주는 속성

   module이라는 이름을 사용한다.

   ```js
   module.exports = {
     module: {
       rules: [
         {
           test: /\.css$/, // 로더를 적용할 파일 유형 (정규표현식 사용)
           use: ['css-loader'] // 해당 파일에 적용할 로더의 이름,
           exclude: /node_modules/, // nodemodules폴더 제외
         },
         {
           test: /\.ts$/,
           use: 'ts-loader'
         }
       ]
     }
   }
   ```

   rules 배열안에 객체로 css로더를 추가한다. css 로더를 위처럼 설정해주고 나면 js 코드에서 css를 import 해올수 있다.

   ```javascript
   import './app.css';
   // 에러가 발생하지 않음.
   ```

   css loader, bable loader, sass loader, file loder, ts loader등이 자주사용된다. 

   특정 파일에 대해서 여러개의 로더를 사용하는 경우, 순서에 주의해야 한다. 로더는 기본적으로 배열의 오른쪽에서 왼쪽 순서로 적용된다.

   ```js
   module: {
     rules: [
       {
         test: /\.scss$/,
         use: ['style-loader', 'css-loader', 'sass-loader']
       }
     ]
   }
   // scss 파일에 대해서 sass 로더로 먼저 전처리(scss -> css로 변환)을 한 다음 웹팩에서  css를 인식할 수 있도록 css 로더를 적용. 그리고 css파일이 웹 어플리케이션에 인라인 스타일 태그로 추가되도록 style-loader를 추가.
   
   // 아래처럼 옵션을 포함하는 형태로 객체를 전달할 수도 있다.
   
   rules: [
     {
       test: /\.scss$/,
       use: [
         {loader: 'style-loader'},
         {
           loader: 'css-loader',
           options: {modules: true}
         },
         {
           loader: 'sass-loader'
         }
       ]
     }
   ]
   ```

4. Plugin: 플러그인은 웹팩의 기본적인 동작에 추가적인 기능을 제공하는 속성, 로더랑 비교하면 로더는 파일을 해석하고 변환하는 과정에 관여하는 반면, 플러그인은 해당 결과물의 형태를 바꾸는 역할에 관여한다. 웹팩 변환 과정 전반에 대한 제어권을 가지고 있다. 플러그인도 배열에 설정을 추가하는데, 배열에는 생성자 함수로 생성한 객체 인스턴스만 추가 될 수 있다.

   ```js
   // webpack.config.js
   
   const webpack = require('webpack');
   const HtmlWebpakPlugin = require('html-webpack-plugin');
   
   module.exports = {
     plugins: [
       new HtmlWebpackPlugin(), // 웹팩으로 빌드한 결과물로 html파일을 생성해주는 플러그인
       new webpack.ProgressPlugin() // 웹팩의 진행률을 표시해주는 플러그인
     ]
   }
   ```

   

## 웹팩 실행 모드 - mode

웹팩 버전 4 부터 mode 라는 개념이 추가되었다.

```js
module.exports = {
  mode: 'none', 
  // ...
}
// none = 모드 설정 안함
// development = 개발 모드
// production = 배포 모드
```

각 실행 모드에 따라 웹팩의 결과물 모습이 달라진다. 개발모드인 경우 개발자들을 위해서 웹팩 로그나 결과물이 보기편하게 주어지며, 배포모드인 경우 성능 최적화를 위해 파일 압축등의 빌드 과정이 추가된다.

웹팩을 실행할 때, env 환경변수 인자를 넘겨줄 수 있다.

```javascript
// webpack.config.js
module.exports = env => {
  let entryPath = env.mode === 'production'
  ? './public/index.js' : './src/index.js';
  
  return {
    entry: entryPath,
  	output: // ...
  }
}
```

module.exports를 하는 것이 객체가 아니고 함수 형식으로 바꾸면 env인자를 받아서 사용이 가능하다. env 인자는 webpack 빌드 명령어를 통해서 넘겨줄 수 있다.

```json
{
  "build": "webpack",
  "development": "npm run build -- --env.mode=development", // env.mode설정
  "production": "npm run build -- --env.mode=production"
}
```

또는 명령어를 webpack --env.mode=production 으로 실행하면 env.mode를 config파일에서 사용이 가능하다.

