## 순수 react

* 리액트를 브라우저에서 다루기 위해서, React, ReactDOM라이브러리를 불러와야 한다.
* React: 뷰를 만들기 위한 라이브러리
* ReactDOM은 UI를 실제 브라우저에 렌더링 할 떄 사용하는 라이브러리
* ReactDOM이 UI를 렌더링 하기위해 사용할 HTML 엘리먼트도 필요하다.

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Document</title>
  </head>

  <body>
    <div class="react-container"></div>

    /* react와 reactDOM cdn*/
    <script src="https://unpkg.com/react@17/umd/react.development.js" crossorigin></script>
    <script src="https://unpkg.com/react-dom@17/umd/react-dom.development.js" crossorigin></script>

    <script>
      // 순수 react와 js코드
    </script>
  </body>
</html>
```

위의 html이 브라우저에서 react를 사용하기 위한 최소한의 요구사항.

## 가상 DOM

* html은 브라우저가 문서 객체 모델(DOM)을 구성하기 위해 따라야 하는 절차이다. HTML문서를 이루는 엘리먼트는 브라우저가 HTML문서를 읽어들이면, DOM엘리먼트가 되고, DOM API가 화면에 사용자 인터페이스를 표시한다.
* 전통적 웹은 독립적인 HTML페이지들로 만들어 졌다. 사용자가 페이지 사이를 내비게이션 하면 브라우저가 서버에 매번 다른 html문서를 요청해서 로딩했다. ajax가 생기고 spa가 등장했다. 브라우저가 ajax를 사용해서 작은 data만을 요청해서 가져올 수 있게 되었고, 전체 웹 애플리케이션이 한 html내에서 실행되면서 js에 의존하여 사용자 ui를 갱신할 수 있도록 되었다.
* spa에서 처음에 브라우저는 html문서를 하나 갖게된다. 사용자는 웹의 네비게이션을 돌아다니는것 같지만 (각 다른 html사이를 옮겨다니는것 같지만,) 사실은 같은 html안에 머물게 되는 것이다. js는 사용자와 어플리케이션의 인터렉션에 맞춰서 표시되어있는 ui를 요청된 새 ui로 바꿔서 보여준다. js에 의존도가 높아짐

### DOM API

* 브라우저의 DOM을 변경하기 위해 js가 사용할 수 있는 객체 모음이다. (Document.createElement, document.appendChild 등등...)
* 화면에 표시된 DOM element를 js로 갱신하거나 변경하는 것은 쉽다. 하지만 새 element를 추가하려면 시간이 오래걸린다. 따라서 웹 개발자가 ui를 변경하는 방법을 세밀하게 신경을 써야 한다.
* 인터렉션이 많은 요새 spa들은 이 점 때문에 성능을 향상시킬 무언가가 필요했다.
* js를 사용해서 DOM 변경을 효율적으로 처리하는 것은 아주 복잡하다. 
* 이미 존재하는 모든 element를 삭제하고 다시 넣는 것은 쉽지만, 필요한 element만 교체하는 일은 어렵다.
* 따라서 리액트가 나왔다.

### React

* react는 브라우저 DOM을 갱신하기 위해 만들어진 라이브러리다. 리액트가 모든 처리를 대신 해 주기 때문에, spa를 더 효율적으로 만들기 위해 복잡한 내용을 직접 만들거나 신경쓰지 않아도 된다.
* 프로그래머가 직접 DOM API를 이용해 DOM을 조작하지 않는다. 가상 DOM을 다루거나 리액트가 UI를 생성하고, 브라우저와 상호작용하기 위한 몇 가지에만 관여한다.
* 가상 DOM은 React Element로 이루어진다. React Element는 HTML엘리먼트와 비슷하지만 실제로는 js객체다. DOM API를 직접 다루는 것 보다, js 객체인 가상 DOM을 직접 다루는 것이 훨씬 빠르다.
* 우리가 가상 DOM을 변경하면 리액트가 DOM API를 이용하여 변경사항을 가장 효율적으로 렌더링 해준다.

### React Element

* 브라우저 DOM은 DOM엘리먼트로 이루어진다. React DOM은 React Element로 이루어진다.

* DOM 엘리먼트와 react 엘리먼트는 비슷해 보이지만 사실 많이 다르다.

* React 엘리먼트는 대응되는 DOM 엘리먼트가 어떻게 생겨야 하는지 기술한다.

* 즉, React Element는 브라우저 DOM을 만드는 방법을 알려주는 명령이다.

* ```javascript
  React.createElement('h1', null, '헤딩1'); // 인자1: 만들려는 DOM element 타입, 인자2: 프로퍼티(DOM element의 attribute), 인자3: 자식노드
  
  // 위 코드는 렌더링 과정에서 아래의 실제 DOM 엘리먼트로 변환된다.
  <h1>헤딩1</h1>
  ```

* DOM 엘리먼트에 있는 attribute를 리액트 엘리먼트의 프로퍼티로 표현가능 하다.

  ```js
  React.createElement('h1', 
                      {id: "recipe-0", data-type: "title"},
                     	"헤딩1");
  
  // 위 코드는 렌더링 과정에서 아래의 실제 DOM 엘리먼트로 변환된다.
  <h1 data-reactroot id="recipe-0" data-type="title">헤딩1</h1>
  ```

  Data-reactroot: 개발자가 만든 react 컴포넌트의 루트 엘리먼트에 나타난다. React 15버전에는 개발자가 정의한 컴포넌트의 모든 노드에 reactID가 부여되었고, 그 id를 이용하여 렌더링 시에 갱신이 필요한 엘리먼트를 추적했다. 이제는 root element에만 attribute가 추가되고, 렌더링 시에 엘리먼트 사이의 계층 관계에 따라서 갱신 대상을 추적한다.

* 리액트 엘리먼트는 리액트가 DOM 엘리먼트를 구성하는 방법을 알려주는 js 리터럴이다.

* ```js
  {
    $$typeof: Symbol(React.element),
    "type": "h1",
    "key": null,
    "ref": null,
    "props": {"children": "헤딩1"},
    "_owner": null,
    "_store": {}
  }
  // React.creatElement() 메서드에 의해서 만들어진 react element객체
  ```

* $$typeof, _owner, _store같은 필드는 리액트가 사용한다. type프로퍼티는 만들려는 HTML혹은 SVG 엘리먼트의 타입을 지정

* props 프로퍼티는 DOM 엘리먼트를 만들기 위해 필요한 데이터나 자식 element를 표현한다.

* children 프로퍼티는 텍스트형태로 표시할 다른 내부 엘리먼트. 텍스트가 아닌 다른 리액트 엘리먼트를 자식으로 렌더링할 수도 있다. -> 엘리먼트 트리 생성(컴포넌트 트리)

### ReactDOM

* reactDOM에는 리액트 엘리먼트를 브라우저에 렌더링하는데 필요한 모든 것이 들어있다. 

* render메서드, 서버에서 사용하는 renderToString, renderToStaticMarkup 메서드도 있다.

* 가상 DOM에서 HTML을 생성하는데 필요한 모든 것이 이 라이브러리 안에 있다.

* 리액트 엘리먼트와, 모든 자식 엘리먼트를 함께 렌더링 하기 위해서 ReactDOM.render를 사용한다. 이 함수의 첫번째 인자는 렌더링할 리액트 엘리먼트, 두번째 인자는 렌더링이 일어날 대상 DOM 노드이다.

* 네이티브 앱을 만들 때도, 리액트를 사용할 수 있기 때문에 모든 DOM 렌더링 기능이 ReactDOM으로 옮겨졌다. 브라우저는 리액트를 사용할 수 있는 대상 플랫폼 중 한가지 이다.

* ```html
  <body>
      <div id="react-container"></div>
  
      <script src="https://unpkg.com/react@17/umd/react.development.js" crossorigin></script>
      <script src="https://unpkg.com/react-dom@17/umd/react-dom.development.js" crossorigin></script>
  
      <script>
        // 순수 react와 js코드
        const heading = React.createElement('h1', null, '헤딩1');
  
        // id가 react-container인 div의 자식에 h1엘리먼트 추가
        ReactDOM.render(heading, document.getElementById('react-container'));
      </script>
    </body>
  ```

### 자식

* 리액트는 props.children을 사용해 자식 엘리먼트를 렌더링 한다. 텍스트가 아닌 다른 리액트 엘리먼트를 자식으로 렌더링 할 수 있고, 그렇게 엘리먼트 트리가 생성되며, <u>컴포넌트 트리</u>라고 한다.

* ```html
  <ul class="caps">
    <li>black cap</li>
    <li>yellow cap</li>
    <li>red cap</li>
    <li>blue cap</li>
    <li>white cap</li>
  </ul>
  ```

* ```js
  React.createElement('ul', {className: 'caps'}, 
                     React.createElement('li', null, 'black cap'),
                     React.createElement('li', null, 'yellow cap'),
                     React.createElement('li', null, 'red cap'),
                     React.createElement('li', null, 'blue cap'),
                     React.createElement('li', null, 'white cap')
                     );
  // 4번째 인자부터는 쭉 자식 엘리먼트로 취급된다.
  ```

* 위의 React.createElement의 호출결과는 아래와 같다. js 객체

* ```js
  {
      type: 'ul',
    	props: {
        children: [
          {type: 'li', props: {children: 'black cap'}...},
          {type: 'li', props: {children: 'yellow cap'}...},
          {type: 'li', props: {children: 'red cap'}...},
          {type: 'li', props: {children: 'blue cap'}...},
          {type: 'li', props: {children: 'white cap'}...},
        ]
      }
  }
  ```

* 순수 리액트는 브라우저에서 실행되며, 가상 DOM은 단일 루트로부터 뻗어나온 리액트 엘리먼트의 트리이다.

* 리액트 엘리먼트는 리액트가 브라우저에서 UI를 어떻게 구성할 지 지시하는 명령이다.

### 데이터를 엘리먼트로 만들기

* 리액트 사용의 가장 큰 장점이 UI엘리먼트와 데이터를 분리 할 수 있는것이다. 

* 리액트는 js이기 때문에 리액트 컴포넌트 트리 구성시에 js로직을 얼마든지 추가 가능하다.

* Ex: 배열에 있는 text를 매핑하여 리액트 엘리먼트 구성하기. (js의 Array.prototype.map 메서드를 사용.)

* ```html
      <script>
        // 순수 react와 js코드
        const caps = ['black', 'yellow', 'red', 'blue', 'white'];
  
        const $ul = React.createElement(
          'ul',
          { className: 'caps' },
          caps.map(cap => React.createElement('li', null, cap))
        );
        const $heading = React.createElement('h1', null, '헤딩1', $ul);
  
        ReactDOM.render($heading, document.getElementById('react-container'));
      </script>
  ```

* `Warning: Each child in a list should have a unique "key" prop.

  Check the top-level render call using <ul>. See https://reactjs.org/link/warning-keys for more information.
      at li` 에러가 발생한다. 배열을 이터레이션 해서 자식 엘리먼트의 리스트를 만드는 경우, 리액트에서는 각 자식 엘리먼트에  key 프로퍼티를 넣을 것을 권장한다.

* 리액트에서는 key를 사용하여 DOM을 더 효율적으로 갱신한다. 따라서 고유 key를 부여한다.

* ```html
  <script>
        // 순수 react와 js코드
        const caps = ['black', 'yellow', 'red', 'blue', 'white'];
  
        const $ul = React.createElement(
          'ul',
          { className: 'caps' },
          caps.map(cap => React.createElement('li', { key: cap }, cap)) // key 부여로 경고 제거
        );
        const $heading = React.createElement('h1', null, '헤딩1', $ul);
  
        ReactDOM.render($heading, document.getElementById('react-container'));
  </script>
  ```

### DOM 렌더링

DOM에 엘리먼트를 삽입하는 것이 DOM API중에 가장 비싼 연산이다. (가장 느린 연산) 이미 제 위치에 있는 DOM 엘리먼트를 갱신하면 새로운 엘리먼트를 삽입하는 것 보다 훨씬 빠르다. 따라서 ReactDOM.render는 현재 DOM을 그대로 두고 갱신이 필요한 DOM 엘리먼트만 변경한다.

새로운 DOM엘리먼트를 삽입해야 한다면 ReactDOM은 그렇게 한다. 하지만 ReactDOM은 가능한 DOM 삽입을 최소화 한다. app의 상태가 자주 바뀌기 때문에 빠른시간에 제대로 작동하려면 이렇게 효율적으로 렌더링을 해야한다.

JSX

* 가상 DOM은 리액트가 사용자 UI를 생성하고 갱신하는 방법을 알려주는 명령이다. 이 명령은 React Element라는 자바스크립트 객체로 이루어 진다.
* 리액트 엘리먼트를 만들기 위해서 React.createElement를 사용하기만 하면 너무 번거롭다.
* 대안으로  JSX문법이 있다. 자바스크립트의 확장으로 HTML과 비슷한 구문을 사용해 리액트 엘리먼트를 정의할 수 있게 해준다.
* attribute가 붙은 복잡한  DOM 트리를 작성할 수 있는 간편한 문법을 제공해준다. 태그의  attribute는 프로퍼티를 표현한다.
* 어떠한 브라우저도 JSX를 읽을 수 없기 때문에(지원x) JSX를 브라우저가 읽을 수 있는 코드로 변환해줄 수단이 필요하다. (이 과정을 트랜스파일링 이라고 하며, 바벨이 해준다.) 
* 예전에는 facebook이 만든 JSX변환기가 표준 jsx처리 방식이었으나, 바벨이 jsx 처리 표준으로 바뀌었다.



### babel

* 바벨 프리셋: 바벨6는 처리할 수 있는 변환의 종류를 preset이라는 모듈로 나눴다. 개발자는 사용할 프리셋을 지정하여 바벨이 처리할 변환의 종류를 명확히 정의할 수 있다.
* 프리셋의 목표는 바벨을 모듈화함으로써, 개발자가 어떤 문법을 변환해야 할지 결정할 수 있도록 만든다. 
  * Babel-preset-es2015: es2015를  es5로 컴파일
  * Babel-preset-es2016: es2016을  es2015로 컴파일
  * Babel-preset-es2017: es2017을 es2016으로 컴파일
  * Babel-preset-env: es2015,2016,2017을 es5로 컴파일. 위의 세 프리셋을 하나로 합친것.
  * Babel-preset-react: JSX를 React.createElemen 호출로 변경.
* ECMAscript 명세에 새 기능을 추가하는 제안이 들어오면 0단계 ~ 3단계의 과정을 거쳐서 표준으로 받아들여진다. 이러한 단계에 대한 프리셋도 제공된다. 
  * babel-preset-state-0: Strawman(허수아비)
  * babel-preset-state-1: Proposal(제안)
  * babel-preset-state-2: Draft(초안)
  * babel-preset-state-3: Candidate(후보)

### 웹팩

* 리액트를 프로덕션에 사용하려면 고려할 사항이 많다. (JSX와 ES6 이상의 트랜스파일링 처리 등... 프로젝트의 의존관계 관리, 이미지, css 최적화...)
* 웹팩은 모듈 번들러로써 여러 파일(js, sass, css, jsx, es6)을 하나의 파일로 묶어준다.
* 모듈을 하나로 묶음으로써, 모듈성(modularity)과 네트워크 성능(network performance)의 이점을 갖는다.
* 모듈성: 소스코드를 작업하기 쉽게 여러 부분 또는 모듈로 나눠서 다룰수 있게 해준다. 
* 의존 관계가 있는 여러 파일을 묶은 번들을 브라우저가 한 번만 읽어 들이기 때문에 네트워크 성능이 좋아진다. 각 script태그는 http요청을 만들어 내며, http 요청마다 약간의 대기시간이 발생한다. 번들은 하나만 있으면 되기 때문에 http요청이 한번만 발생하여 추가 대기시간을 방지한다.
* 웹팩은 아래의 일도 처리가 가능하다.
  * 코드 분리: 여러 덩어리로 코드를 나눠서 필요할 때 각각 로딩 가능, (rollup 혹은 layer 라고 부른다.) 코드 분리는 여러 다른 페이지나 디바이스에서 필요한 자원을 따로 나눠 효율적으로 처리하기 위함이다.
  * 코드 축소(minifying)
  * 특징 켜고 끄기: 코드의 기능 테스트를 위한 경우 각각의 환경에 맞춰서 보내준다(feature flagging)
  * HMR(hot module replacement): 소스 코드가 바뀌는지 감지해서 변경된 모듈만 즉시 갱신.
* 웹팩 로더: 앱이 브라우저가 이해할 수 없는 다른 언어를 사용하는 경우 webpack.config.js에 필요한 로더를 지정하여 앱의 코드를 브라우저가 이해할 수 있도록 변환시킨다.
* Babel-loader를 포함시키면 es6나 react코드를 트랜스 파일링 가능하다.
* 웹팩과 바벨을 사용하면 개발에 최신 js문법을 활용할 수 있다.