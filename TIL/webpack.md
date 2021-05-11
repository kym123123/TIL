# webpack

```
$ npm init -y

$ npm i webpack webpack-cli --save-dev
```

Web pack-cli : 웹팩 실행 가능 명령어 지원해주는 패키지

`npx 명령어`: 설치된 패키지를 기준으로 node_modules안에서 실행파일을 찾아 실행시켜준다.

node_modules 폴더 안의 .bin 폴더 : 패키지들을 실행시켜주는 역할을 해주는 파일을 담고있다.

## bundle

```
$ npx webpack
```

위의 명령어를 실행하기 전에 configuration 해줄 것이 필요하다. (entry와 output 설정은 따로 하지 않아도 번들링이 됨.)

src와 dist 폴더를 만들고. index.js와 index.js가 사용하는 모듈을 모두 src 폴더에 옮기고 해당 명령어 실행

번들링이 완료되면 dist 폴더에 main.js 파일 생성

```npx
$ npx webpack --target=node

// webpack 실행되는 환경이 node라는 환경임을 인식시켜주기 위해 target key설정
```



## webpack 설정

1. webpack.config.js 파일을 폴더내에 생성

2. ```javascript
   // __dirname, path module
   const path = require('path');
   
   module.exports = {
     entry: './src/index.js', // 시작 파일
     output: {
       path: path.resolve(__dirname, 'dist'),
       filename: 'bundle.js',
     }, // 번들파일 이름, 생성되는 파일 경로 (절대경로로 설정해야한다.) -> __dirname 변수, path 모듈 사용한다.(노드에서 제공됨)
     target: 'node', // 실행환경이 노드임을 전달
   };
   ```

3. ```
   $ npx webpack
   ```

4. 생성된 bundle.js 파일 확인



## webpack의 기본구조

* Mode : 
  * package.json의 외부 모듈 기록 과정
    1. 어플리케이션 내부에 직접 포함되는 모듈 -> jquery(DOM 선택을 위한 모듈)  등등... 실제 실행에 필요 = dependencies(--save)
    2. 개발 과정에 필요한 모듈 -> 개발 효율, 코드 컨벤션 검사, 코드 품질에 도움을 주는 모듈 = devDependencies(--save-dev)
  
* Loader: 웹팩의 의존성 그래프를 완성시키는 과정에서, 의존성을 갖는 다양한 모듈들을 입력받아 처리하는 역할. 웹팩이 기본적으 인식하는 모듈 형태는 js파일, json 파일 이기 때문에, 다른 파일은 개별적으로  loader를 준비해서 연결시켜줘야 한다.

  * ```javascript
    module.exports = {
      module: {
        rules: [laader1, loader2] // 필요한 로더들 설정하는 공간. 
       // 배열타입으로 전달한다. -> 간단하게 문자열로 전달
       // 로더의 설정을 자세하게 전달하려면 객체로 전달하는 방법도 있다.
      }
    }
    ```

  * ```javascript
    const path = require('path');
    
    module.exports = {
      entry: './index.js',
      output: {
        filename: 'bundle.js',
        path: path.resolve(__dirname, 'dist'),
      },
      mode: 'none', // browser에서 실행되도록 모드 none
    };
    ```

  * ```json
    "scripts": {
        "test": "echo \"Error: no test specified\" && exit 1",
        "build": "webpack"
      },
    // package.json에 scripts의 build 명령어 설정
    ```

  * npm run build

* CSS loader, style loader를 이용하여 css 파일을 모듈로 읽어오기, css파일 내용을 style태그 내에 주입하기

  * ```
    npm i style-loader css-loader --save-dev
    ```

  * ```javascript
    // webpack.config.js
    const path = require('path');
    
    module.exports = {
      entry: './index.js',
      output: {
        filename: 'bundle.js',
        path: path.resolve(__dirname, 'dist'),
      },
      module: {
        rules: [
          {
            test: /\.css$/i, // 어떤파일들이 로더의 대상이 되는지 정규표현식 사용 -> .css로 끝나는 파일들 매칭
            use: [
              // 사용하는 로더들을 배열에 전달, 객체 전달시 loader 키와, option 키 사용 가능
              'style-loader',
              {
                loader: 'css-loader',
              },
            ],
          },
        ],
      },
      mode: 'none',
    };
    ```

  * ```
    npm i normalize.css --save
    // normalize.css 설치 및 import 하여 사용
    import 'normalize.css'; (js파일에서 )
    ```

  * 이후에 index.css 파일을 생성하여 스타일셋을 지정해주고 index.js에서 import 'index.css'를 하여 임포트 가능하다. -> head태그 안에 style태그가 주입된것을 확인할 수 있다.

  * https://github.com/webpack-contrib/css-loader -> css-loader 공식문서를 참고하면 options 객체 내에 키값 설정에 대한설명을 볼 수 있다.

  * 아래의 설정은 modules:true로 지정하여 css파일들의 모듈화를 가능하게 해준다.

  * ```javascript
    module: {
        rules: [
          {
            test: /\.css$/i, // 어떤파일들이 로더의 대상이 되는지 정규표현식 사용
            use: [
              // 사용하는 로더들을 배열에 전달, 객체 전달시 loader 키(사용하는 로더 지정)와, option 키(로더의 동작 변경 가능) 사용 가능
              'style-loader',
              {
                loader: 'css-loader',
                options: {
                  modules: true, // css 파일들의 모듈화가 가능해짐. css파일별로 클래스 이름이 같아도 겹치지 않는다
                },
              },
            ],
          },
        ],
      },
    ```

  * 스타일로더 옵션 설정(스타일 로더는 처리하는 파일별로 style태그를 생성해준다.) 이러한 방식에서, 스타일 태그 하나에 전부 스타일셋을 읽어오도록 옵션설정을 해 줄수 있다.

  * ```javascript
    use: [
              // 사용하는 로더들을 배열에 전달, 객체 전달시 loader 키(사용하는 로더 지정)와, option 키(로더의 동작 변경 가능) 사용 가능
              {
                // style-loader로 객체형태로 options설정
                loader: 'style-loader',
                options: { // 하나의 스타일 태그를 생성하도록 지정
                  injectType: 'singletonStyleTag',
                },
              },
              {
                loader: 'css-loader',
                options: {
                  modules: true, // css 파일들의 모듈화가 가능해짐. css파일별로 클래스 이름이 같아도 겹치지 않는다
                },
              },
            ],
    ```

  * 이렇게 지정하고 npm run build를 하게되면 하나의 style태그로 합쳐진 것을 head 태그 안에서 확인이 가능하다.

* Plugin: 웹팩의 동작 과정에 개입이 가능하여 여러가지 역할을 해준다. (번들파일에 변화, 개발모드에서 개발편의성제공, 프로덕션 모드에서 코드 최적화 등...)

  * plugins키로 지정이 가능하다.

  * html webpack plugin -> 번들러를 위한  html 파일을 자동으로 생성, 설정 해준다.

  * ```
    npm i html-webpack-plugin -D
    ```

  * 플러그인 모듈 적용

    ```javascript
    const path = require('path');
    const HtmlWebpackPlugin = require('html-webpack-plugin');
    
    module.exports = {
      entry: './index.js',
      output: {
        filename: 'bundle.js',
        path: path.resolve(__dirname, 'dist'),
      },
      module: {
        rules: [
          {
            test: /\.css$/i, // 어떤파일들이 로더의 대상이 되는지 정규표현식 사용
            use: [
              // 사용하는 로더들을 배열에 전달, 객체 전달시 loader 키(사용하는 로더 지정)와, option 키(로더의 동작 변경 가능) 사용 가능
              {
                loader: 'style-loader',
                options: {
                  injectType: 'singletonStyleTag',
                },
              },
              {
                loader: 'css-loader',
                options: {
                  modules: true, // css 파일들의 모듈화가 가능해짐. css파일별로 클래스 이름이 같아도 겹치지 않는다
                },
              },
            ],
          },
        ],
      },
      plugins: [
        new HtmlWebpackPlugin({
            template: './template.html', // template키: 자동으로 생성되는 html 문서가 특정파일을 기준으로 만들어지도록 지정. -> dist에 index.html 파일이 생성된다. -> 자동으로 script 태그나 link태그를 지정해주므로, 사전에 우리가 먼저 설정해줄 필요가 없다.
        }),
      ],
      mode: 'none'
    };
    
    ```

    

