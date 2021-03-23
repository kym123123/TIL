# React Documentation

## 엘리먼트 렌더링

```react
const element = <h1>Hello, world!</h1>;
```

: 엘리먼트는 React 앱의 가장 작은 단위이다. (컴포넌트의 구성요소이다.)

브라우저 DOM 엘리먼트와 달리 React 엘리먼트는 일반 객체이며 쉽게 생성 가능하다. ReactDOM은 React 엘리먼트와 일치하도록 DOM을 업데이트 한다. 

* 루트(root) DOM 노드

  ```react
  <div id ="root"></div>
  ```

  이 안에 들어가는 모든 엘리먼트를 React DOM 에서 관리한다. 따라서 루트(root) DOM 노드라고 부른다. <u>**React로 구현된 애플리케이션은 일반적으로 하나의 루트 DOM 노드가 있다.**</u> React를 기존 앱에 통합하려는 경우 원하는 만큼 많은 수의 독립된 루트 DOM 노드가 있을 수 있다.

  React 엘리먼트를 루트 DOM 노드에 렌더링하면, ReactDOM.render()로 전달하면 된다.

  ```react
  const element = <h1>Hello, World</h1>;
  
  ReactDOM.render(element, 
  document.getElementById('root'));
  ```



## 렌더링 된 엘리먼트 업데이트하기

: **React 엘리먼트는 불변객체이다.** 엘리먼트를 생성한 이후에는 해당 엘리먼트의 자식이나 속성을 변경할 수 없습니다. 엘리먼트는 특정 시점의 UI를 보여준다.

따라서 UI를 업데이트 하는 유일한 방법은, 새로운 엘리먼트를 생성하고 ReactDOM.render()로 전달하는 것이다. 실제로 대부분의 React 앱은, ReactDOM.render()를 한번만 호출한다. 

<u>ReactDOM은 해당 엘리먼트와 자식 엘리먼트를 이전의 엘리먼트와 비교하고, DOM을 원하는 상태로 만드는데 필요한 경우에만 DOM을 업데이트 한다.</u>



