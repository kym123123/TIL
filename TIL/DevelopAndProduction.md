# Develop Mode and Production Mode

* 프로젝트 환경설정시 개발자가 개발을 효율적으로 하기위해, 개발과정에서 도움되는 여러 설정을 세팅한다.
* 사용자를 위해서는 기능이 정확히 동작되면서도 빠르게 데이터가 전달되어야 하기 때문에 최적화도 필요
* mode설정시 개발모드와 프로덕션 모드 설정이 필요하다.
* mangling같은 최적화는 사용자를 위한 것이다. -> production mode
* 어플리케이션 기능이 제대로 동작하는지 확인하기 위해서 코드 수정시 마다 빌드를 다시 진행하였는데, 빌드시간이 길어질수록 개발 효율이 떨어진다.
* 최적화 관련 설정을 프로덕션 모드에서만 진행되도록 분리시키면 개발 효율을 올릴 수 있다. -> 빌드시간단축, 수정사항 변화 빠르게 확인 가능



### Production 환경

새로운 webpack 환경설정 파일이 필요하다. (개발모드 설정파일, 프로덕션모드 설정파일, 환경설정 공통부분 담당 파일)

각 설정파일들의 mode값을 설정후 진행한다.

```
$ npm i webpack-merge -D
```



```javascript
// webpack.common.js
// 공통적 요소만 남긴다.
// optimization key와 최적화 관련 plugin 전부 삭제

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
      minify: {
        collapseWhitespace: true,
        useShortDoctype: true,
        removeScriptTypeAttributes: true, // script태그의 타입을 없애준다. 기본값이 js를 읽는것인데, 이 설정없이 빌드되면 script태그에 type이 설정되어 있다.
      },
    }),
    new CleanWebpackPlugin(),
  ],
};

```

```javascript
// webpack.dev.js

const { merge } = require('webpack-merge');
const common = require('./webpack.common');

const config = {
  mode: 'development',
};

module.exports = merge(common, config);



```javascript
//  webpack.prod.js
// optimization과 plugin들만 적용된 파일 

const { merge } = require('webpack-merge');
const common = require('./webpack.common');

const HtmlWebpackPlugin = require('html-webpack-plugin');
const TerserWebpackPlugin = require('terser-webpack-plugin');

const config = {
  plugins: [
    new HtmlWebpackPlugin({
      title: 'webpack',
      template: './template.hbs', // template키: 자동으로 생성되는 html 문서가 특정파일을 기준으로 만들어지도록 지정. -> dist에 template.html 파일이 생성된다.
      meta: {
        viewport: 'width=device-width, initial-scale=1',
      },
      minify: {
        collapseWhitespace: true,
        useShortDoctype: true,
        removeScriptTypeAttributes: true, // script태그의 타입을 없애준다. 기본값이 js를 읽는것인데, 이 설정없이 빌드되면 script태그에 type이 설정되어 있다.
      },
    }),
  ],

  optimization: {
    runtimeChunk: {
      name: 'runtime',
    },
    splitChunks: {
      cacheGroups: {
        commons: {
          test: /[\\/]node_modules[\\/]/, // node_modules 폴더 내에 있는 파일들이 대상
          name: 'venders',
          chunks: 'all',
        },
      },
    },
    minimize: true,
    minimizer: [new TerserWebpackPlugin({})],
  },
  mode: 'production',
};

module.exports = merge(common, config); 
```

각 빌드모드마다 다른 파일을 불러올수 있도록 package.json을 수정한다.

```json
// package.json config플래그를 사용하여 구분짓는다.
  "scripts": {
    "dev": "webpack --config webpack.dev.js",
    "build": "webpack --config webpack.prod.js"
  },
```



### define plugin

: 웹팩이 빌드진행시 특정 상수값을 만들어 모듈들이 이 상수값을 어디서든 사용할수 있도록 확인 가능하다. 웹팩 모듈 내부 플러그인임으로 추가 설치 필요 없음.

```javascript
// webpack.common.js
const webpack = require('webpack');

// ...


 plugins: [
   // ...
    new webpack.DefinePlugin({
      // 모듈 전역에서 제공되는 상수값을 지정할 수 있다
      IS_PRODUCTION: true, // is_production 상수값 지정
    }),
  ],
```

이렇게 지정해두면 현재 IS_PRODUCTION이 true이면 프로덕션모드, 아니면 아닌것 확인용도로 선언하였고 전역에서 확인이 가능하다

```javascript
//index.js

console.log('IS_PRODUCTION MODE?', IS_PRODUCTION); // true
// js파일에서 조회가 가능
```



모드별로 해당 상수값이 변하도록 하기위해 package.json 을 수정한다.

```json
  "scripts": {
    "dev": "NODE_ENV=DEVELOPMENT webpack --config webpack.dev.js",
    "build": "NODE_ENV=PRODUCTION webpack --config webpack.prod.js"
  },
```

노드환경에서 process.ENV객체에 에 담긴 node_env에 접근 가능해진다. 이 식별자에 담긴 값을 가지고 설정을 유동적으로 변경시킬 수 있도록 webpack.common.js에서 코드를 변경해준다.

여기서는 plugins key안의 HtmlWebpackPlugin 설정 안의 minify값을 NODE_ENV변수 값에 따라 객체로 줄지 false로 줄지를 고려하는 코드

```javascript
// webpack.common.js

const path = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');
const { CleanWebpackPlugin } = require('clean-webpack-plugin');
const MiniCssExtractPlugin = require('mini-css-extract-plugin');
const webpack = require('webpack');
// NODE_ENV값 조회
const isProduction = process.env.NODE_ENV === 'PRODUCTION'; 

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
      // isProduction 값에 따라서 minify에 전달되는 값이 다르다.
      minify: isProduction
        ? {
            collapseWhitespace: true,
            useShortDoctype: true,
            removeScriptTypeAttributes: true, // script태그의 타입을 없애준다. 기본값이 js를 읽는것인데, 이 설정없이 빌드되면 script태그에 type이 설정되어 있다.
          }
        : false,
    }),
    new CleanWebpackPlugin(),
    new webpack.DefinePlugin({
      // 모듈 전역에서 제공되는 상수값을 지정할 수 있다
      IS_PRODUCTION: true,
    }),
  ],
};
```

npm run dev 진행시 압축되지않은 index.html 생성, npm run build 사용시 압축된 index.html생성 확인