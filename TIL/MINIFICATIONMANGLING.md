# Minification & Mangling

### minify: 코드의 형태를 압축시키는 과정

1. 어플리케이션 동작에 관여하지 않는 코드 제거 (ex. console.log제거) 
2. 압축하는 과정: 표현을 간결하게 한다 -> 들여쓰기, 띄어쓰기 삭제, 삼항연산자같은 짧은식으로 표현하기

### Mangle

1. 표현의난독화: uglify or mangle, 변수명, 함수명등을 알파벳 한글자로 치환시켜버린다. 용량도 줄이고, 외부에서 코드를 볼 경우 코드 분석도 어렵게 만든다.



### HTML마크업 최적화

htmlWebpackPlugin의 minify키를 활용한다. 공식문서를 보면 minification에 적용할 수 있는 다양한 설정들이 있다.

```javascript
 plugins: [
    new HtmlWebpackPlugin({
      title: 'webpack',
      template: './template.hbs', // template키: 자동으로 생성되는 html 문서가 특정파일을 기준으로 만들어지도록 지정. -> dist에 template.html 파일이 생성된다.
      meta: {
        viewport: 'width=device-width, initial-scale=1',
      },
      minify: {
        collapseWhitespace: true, // 공백삭제
        useShortDoctype: true, // doctype줄이기
        removeScriptTypeAttributes: true, // script태그의 타입을 없애준다. 기본값이 js를 읽는것인데, 이 설정없이 빌드되면 script태그에 type이 설정되어 있다.
      },
    }),
  ],
```

이 설정으로 build하면 dist/index.html이 공백이 삭제된 채로 한줄로 빌드되어 있는것을 확인 가능.



### css 최적화

css nano compressor이용하여 최적화한다. 실수로 같은 스탕리속성을 두번 정의해놓거나, 주석처리해둔 것들을 모두 제거해줘서 최소한으로 처리된 css코드를 제공해준다.

```
$ npm i cssnano
```

cssnano가 동작할수 있도록 plugin설치 필요 -> 공식문서에 가면 사용법이 자세하게 나와있다.

```
$ npm i --save-dev optimize-css-assets-webpack-plugin
```



```javascript
// webpack.config.js
// require로 import
const OptimizeCssAssetsPlugin = require('optimize-css-assets-webpack-plugin');

plugins: [
   new OptimizeCssAssetsPlugin({
      assetNameRegExp: /\.optimize\.css$/g,
      cssProcessor: require('cssnano'),
      cssProcessorPluginOptions: {
        preset: ['default', { discardComments: { removeAll: true } }],
      },
      canPrint: true
    })
  ]
```



### js파일 최적화 -> 난독화 , mangling

compressor가 필요하다. uglifyJS, babelMinify, terser(대표적, 웹팩 설치시 함께 설치된다.)

```
$ npm i terser-webpack-plugin -D
// terser를 사용할 수 있게하는 플러그인만 설치 해준다.
```

```javascript
const TerserWebpackPlugin = require('terser-webpack-plugin');

// 이 플러그인은 optimization 키 안에 설정한다.
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
    minimize: true, // 이렇게 설정하면 압축이 되는데, minimizer를 설정해주면 웹팩 내부에서 terser를 실행시켜 //압축을 진행, terserwebpackPlugin 적용
    minimizer: [new TerserWebpackPlugin({
      cache: true
    })]
  },
```

minimizer안에서 terser의 옵션설정도 가능하다. 빠르게 빌드가 진행되도록 cache키를 사용 -> 빌드시에 이전과 파일의 변화가 없으면 캐싱된 파일을 사용하여 빌드 시간을 줄여준다. -> 현재 cache는 유효하지 않은 key값이라고 사용이 안됨.

