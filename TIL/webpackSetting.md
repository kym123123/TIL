## webpack 설정

### HBS

: handlebars의 약자, handlebars가 사용하는 확장자.

템플릿 엔진을 활용할수 있다. Model(문서에 노출시킬 data), template(모델이 가지고 있는 데이터가 어디에서 어떻게 표현될지 작성되어있음) -> 템플릿 엔진의 컴파일러가 모델과 템플릿을 이용하여 모델 데이터들이 템플릿을 통해 들어갈 자리를확인하고 데이터를 넣어주는 과정을 실행 -> 템플릿 내에 데이터가 함께 들어가있는 완성된 데이터인 view를 표현해준다.

mustache를 활용하여 표현한다.

```
$ npm i handlebars -D
// handlebars 모듈 설치
```

HBS 파일을 읽기위해 handlebars-loader설치

```
$ npm i handlebars-loader -D
```

```javascript
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
      {
        test: /\.hbs$/,
        use: ['handlebars-loader'],
      },
    ],
  },
  plugins: [
    new HtmlWebpackPlugin({
      title: 'webpack', // template.hbs에서 사용할 데이터
      template: './template.hbs', // template키: 자동으로 생성되는 html 문서가 특정파일을 기준으로 만들어지도록 지정. -> dist에 template.html 파일이 생성된다.
      meta: {
        viewport: 'width=device-width, initial-scale=1',
      }, // html 의 meta값도 설정이 가능
    }),
  ],
```

template.html을 template.hbs로 변경하고 내부에 템플릿을 설정해준다.

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge" />
    /*
    viewport 관련 meta태그가 알아서 추가된다.
    */
    /* htmlwebpackplugin의 title 데이터 사용 */
    <title>Webpack {{htmlWebpackPlugin.options.title}}</title>
  </head>
  <body></body>
</html>

```



## caching & webpack

Caching: 사용자가 서비스를 이용하는데 소모되는 비용을 최소화하는 것은 매우중요, 이를 위한 한 방법이 캐싱이다. 리소스의 내용이 변하지 않는다면, 같은 내용을 다시 서버에 요청하지 않고, 리소스의 복사본을 서버보다 가깝게 두고 클라이언트가 사용할 수 있도록 한다.

* 서버와의 통신이 감소하여 서버의 부하를 덜어줌
* 서버보다 가까운곳에서 데이터를 가져다 주기때문에 사용자가 더 짧은 시간을 소모하여 정보 습득이 가능
* web app을 이용할 경우, 브라우저에 담긴 캐시가 local cache이다. 요청을 통해 받는 모든 리소스가 해당된다.
* 뒤로가기, 앞으로가기 , 재방문, 리로드와 같은 동작을 할 때 캐시가 있다면 캐시를 이용한다.

웹팩을 통한 캐싱 이용법: 웹팩이 모듈을 bundle파일을 만들고 나서 브라우저는 bundle파일을 동작시키는데, 이때 설정파일을 기준으로 bundle파일을 만들게 되면, 브라우저는 같은 이름으로 bundle파일 호출

브라우저가 캐싱을 구분하는 기준은 url, 로드 리소스 이름이 같을경우 캐싱을 이용하기 떄문에, 이전파일이 호출된 결과가 보여진다. 파일이 수정되어도 수정이 안되는 파일을 보여주기 쉽다. 이를 방지하기 위해서 bundle파일뒤에 hash값을 넣어 bundle파일에 넣어 준다. 파일이 bundling될 때만 해시값을 넣어준다.(수정 사항이 있을 경우만) -> 수정 사항이 있을경우만 새 파일을 로드하는것이 가능하다.

웹팩에서 사용가능한 해시값

1. 해시 : 빌드 할 때마다 새로운 번들.해시 파일이 생성되기 때문에 이를 해결해주기 위한 것이 필요하다.

   ```javascript
    output: {
      // [hash]를 추가해준다.
       filename: 'bundle.[hash].js',
       path: path.resolve(__dirname, 'dist'),
     },
       // 빌드 결과 파일명
       // bundle.3l2j3hdksj3kwlksd.js
   ```

   ```
   $ npm i clean-webpack-plugin -D
   // 새 파일이 계속 생성되는것 해결을 위한 패키지
   ```

   

   ```javascript
   const path = require('path');
   const HtmlWebpackPlugin = require('html-webpack-plugin');
   
   // cleanWebpackPlugin 추가
   const { CleanWebpackPlugin } = require('clean-webpack-plugin');
   
   module.exports = {
     entry: './index.js',
     output: {
       filename: 'bundle.[hash].js',
       path: path.resolve(__dirname, 'dist'),
     },
   // ... 중략
   plugins: [
       new HtmlWebpackPlugin({
         title: 'webpack',
         template: './template.hbs', // template키: 자동으로 생성되는 html 문서가 특정파일을 기준으로 만들어지도록 지정. -> dist에 template.html 파일이 생성된다.
         meta: {
           viewport: 'width=device-width, initial-scale=1',
         },
       }),
       new CleanWebpackPlugin(),
     ],
   ```

   이렇게 설정을 해주면 bundle파일이 새로 빌드될 때마다 알아서 정리된다.

   

2. 컨텐츠 해시

   css 파일이 style태그로 주입되게 하지 않고 css파일을 별도로 설치하여 link태그로 불러오도록 해준다.

   ```
   $ npm i mini-css-extract-plugin -D 
   // 먼저 설치
   // style-loader와 하는일이 비슷해 충돌날 수 있으므로 style-loader와 같이 사용하지 않는다.
   ```

   ```javascript
   //webpack.config.js
   const MiniCssExtractPlugin = require('mini-css-extract-plugin');
   //추가
   
   
   module: {
       rules: [
         {
           test: /\.css$/i, // 어떤파일들이 로더의 대상이 되는지 정규표현식 사용
           use: [
          // style-loader 잠시 삭제하고 minicssExtractPlugin 사용
             {
               loader: MiniCssExtractPlugin.loader,
             },
             {
               loader: 'css-loader',
               options: {
                 modules: true, // css 파일들의 모듈화가 가능해짐. css파일별로 클래스 이름이 같아도 겹치지 않는다
               },
             },
           ],
         },
         {
           test: /\.hbs$/,
           use: ['handlebars-loader'],
         },
       ],
     },
       
       // plugin에도 추가
       plugins: [
       new MiniCssExtractPlugin(),
       new HtmlWebpackPlugin({
         title: 'webpack',
         template: './template.hbs', // template키: 자동으로 생성되는 html 문서가 특정파일을 기준으로 만들어지도록 지정. -> dist에 template.html 파일이 생성된다.
         meta: {
           viewport: 'width=device-width, initial-scale=1',
         },
       }),
       new CleanWebpackPlugin(),
     ],
   ```

   이렇게 해주면 고정된 파일명으로만 css파일이 생성되어서 해시값을 적용하지 않으면 문제가 생긴다. 

   ```javascript
    plugins: [
       new MiniCssExtractPlugin({
         filename: '[hash].css'
       }),
      //....
      ]
   ```

   위처럼 filename 속성을 적용해준다.

   `빌드 결과: 279387492dv2o8u38.css`파일생성

   이 방법도 문제가 있다. js와 css파일의 생성 시점이 달라서, js 파일에 변화가 있고 css파일에는 변화가 없어도, css가 불필요하게 다시 생성된다. -> css파일이 제대로 캐싱되지 않는 문제가 발생.(해쉬값이 바뀌었기 때문에 새로운 리소스로 판단.) -> content Hash 필요(컨텐츠의 내용에따라 해쉬값을 부여 따라서 내용이 같으면 해쉬값이 유지됨.)

   ```javascript
   new MiniCssExtractPlugin({
         filename: '[contenthash].css',
       }),
   ```

   이렇게 설정후 빌드하면 변화가 있는 js파일만 새로 빌드되고 css는 해쉬값이 유지된다. 캐싱이 가능해짐.

   

3. 청크 해시

   : 어플리케이션을 만들다보면 점점 파일의 사이즈가 커져서 bundle.js의 크기가 너무 커지게 된다. 서버에요청하는 파일의 수와 빈도가 줄어드는 장점도 있지만, 파일이 도착하는 시간이 지연된다. 따라서 몇가지 기준으로 bundle파일을 분리하는데, 이러한 분리된 bundle을 chunk라고 한다.

   * chunk를 나누는 기준: runtime / bender으로 나눈다.

   1. runtime chunk: 웹팩이 모듈을 해석해 의존성 그래프를 완성하고 하나의 번들파일에 모듈을 모두 모아 번들을 완성시키는데, 이를 실행시키면 모듈을 순서대로 읽을수 있도록 마련된 초기화에 해당하는 코드가 존재한다. 어플리 케이션이 메모리를 할당받고 실행되는 환경을 런타임이라고 하는데, 이러한 런타임 환경에서 모듈을 안전하게 실행가능토록 마련된 초기화 코드는 모듈이 몇개가 관여되던 변화가 없다. 런타임떄 사용되는 코드만 chunk로 분리하면, 나머지 내용은 module에 관한 내용만 남게된다. 런타임 코드는 변화가 없기 때문에 캐시가 적용되어 어플리케이션이 좀더 빠르게 동작가능
   2. vender chunk: 외부 패키지에 해당하는 모듈들을 의미. 우리가 외부 패키지를 버전업 해주지 않기때문에 외부에서 버전업 해주지 않으면 그 파일은 변화가 없다. 이러한 외부 패키지들을 모아둔 vender chunk는 이러한 특징으로 캐싱이 가능하다.

   * Optimization key: 웹팩의 번들파일을 최적의 상태로 만들어 주도록 한다.

     ```javascript
     const path = require('path');
     const HtmlWebpackPlugin = require('html-webpack-plugin');
     const { CleanWebpackPlugin } = require('clean-webpack-plugin');
     const MiniCssExtractPlugin = require('mini-css-extract-plugin');
     
     module.exports = {
       entry: './index.js',
       output: {
         filename: '[name].[chunkhash].js',
         path: path.resolve(__dirname, 'dist'),
       },
       module: {
         rules: [
           {
             test: /\.css$/i, // 어떤파일들이 로더의 대상이 되는지 정규표현식 사용
             use: [
               // 사용하는 로더들을 배열에 전달, 객체 전달시 loader 키(사용하는 로더 지정)와, option 키(로더의 동작 변경 가능) 사용 가능
               // { // css 정보를 읽어들여, html 문서 내에 style태그를 생성하고 주입하는 로더
               //   loader: 'style-loader',
               //   options: {
               //     injectType: 'singletonStyleTag',
               //   },
               // },
               {
                 loader: MiniCssExtractPlugin.loader,
               },
               {
                 loader: 'css-loader',
                 options: {
                   modules: true, // css 파일들의 모듈화가 가능해짐. css파일별로 클래스 이름이 같아도 겹치지 않는다
                 },
               },
             ],
           },
           {
             test: /\.hbs$/,
             use: ['handlebars-loader'],
           },
         ],
       },
       plugins: [
         new MiniCssExtractPlugin({
           filename: '[contenthash].css',
         }),
         new HtmlWebpackPlugin({
           title: 'webpack',
           template: './template.hbs', // template키: 자동으로 생성되는 html 문서가 특정파일을 기준으로 만들어지도록 지정. -> dist에 template.html 파일이 생성된다.
           meta: {
             viewport: 'width=device-width, initial-scale=1',
           },
         }),
         new CleanWebpackPlugin(),
       ],
       // 여기에 선언한 name이 
       // filename: [name]의 값으로 들어간다.
       optimization: {
         runtimeChunk: {
           name: 'runtime',
         },
       },
       mode: 'none',
     };
     
     ```

     output의 filename키를 위와같이 변경해주고

     optimization설정을 입력해준다.

     `빌드 결과`: bundle파일이 두가지 생성된다.

     1. main.lkjs3ls39232923.js
     2. runtime.b3143239483848fec7.js

     

     다음은 vender Chunk를 해보자.

     jquery를 설치하여 예제를 만든다. 

     ```
     $ npm i jquery
     ```

     

     ```javascript
     // index.js
     import $ from 'jquery';
     
     // .....
     
     // jquery 코드 입력
     console.log($(`.${styles.helloWebpack}`).length);
     ```

     webpack.config.js도 수정해준다.

     ```javascript
     optimization: {
         runtimeChunk: {
           name: 'runtime',
         },
         splitChunks: {
           cacheGroups: {
             commons: {
               // node_modules 폴더 내에 있는 파일들이 대상
               test: /[\\/]node_modules[\\/]/,
               name: 'venders',
               chunks: 'all',
             },
           },
         },
       },
     ```

     빌드 결과: main.hash.js, runtime.hash.js, venders.hash.js생성

     Venders.hash.js파일안에는 추가해준 jquery의 내용이 담겨있다.