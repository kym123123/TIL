# React Documentation

## Component와 Props

* 컴포넌트(component): UI를 재사용 가능한 개별적인 여러 조각으로 나누고, 각 조각을 개별적으로 살펴볼 수 있다. 컴포넌트는 JavaScript 함수와 유사하다. "props"라고 하는 임의의 입력을 받고, 화면에 어떻게 표시되는지를 기술하는 React 엘리먼트를 반환한다.

### 함수 컴포넌트와 클래스 컴포넌트

:컴포넌트를 정의하는 가장 간단한 방법 -> JS 함수를 작성하는 것이다.

```react
function Welcome(props) {
  return <h1>Hello, {props.name}</h1>;
}
```

이 함수는 데이터를 가진 하나의 <u>props(속성을 나타내는 데이터) 객체</u>를 인자를 받은 후, <u>React 엘리먼트를 반환</u>하므로 <u>유효한 React 컴포넌트이다.</u> 이러한 컴포넌트는 JS 함수이기 때문에, **함수 컴포넌트** 라고 한다.

: ES6 class를 사용하여 컴포넌트 정의

```react
class Welcome extends React.component {
  render() {
    return <h1>Hello, {this.props.name}</h1>;
  }
}
```

React의 관점에서 위의 두 가지 유형의 컴포넌트는 동일하다. class는 몇가지 추가기능이 있다. 함수 컴포넌트는 간결성과 몇가지 추가 기능이 있다.

### 컴포넌트 렌더링

: React 엘리먼트는 사용자 정의 컴포넌트로도 나타낼 수 있다.

```react
const element = <Welcome name="sara" />;
```

사용자 정의 컴포넌트로 작성한 엘리먼트를 발견하면 <u>JSX 어트리뷰트와 자식을 해당 컴포넌트에 단일 객체로 전달한다. 이 객체를 props 라고 한다.</u>

* 페이지에 Hello, Sara를 렌더링하는 예시

  ```react
  function Welcome(props) {
    return <h1>Hello, {props.name}</h1>;
  }
  
  const element = <Welcome name="Sara" />; // props 객체에 name을 프로퍼티로 저장
  ReactDOM.render(
  	element,
    document.getElementById('root')
  );
  ```

  1) <Welcome name="Sara" /> 엘리먼트로 ReactDOM.render()을 호출한다.

  2) React는 {name: 'Sara'}를 props로 하여 Welcome 컴포넌트를 호출한다.

  3) Welcome 컴포넌트는 결과적으로 <h1>Hello, Sara</h1> 엘리먼트를 반환한다.

  4) React DOM은 <h1>Hello, Sara</h1> 엘리먼트와 일치하도록 DOM을 효율적으로 업데이트 한다.

  `주의: 컴포넌트 이름은 항상 대문자로 시작한다.`

  `React는 소문자로 시작하는 컴포넌트를 DOM 태그로 처리한다. <div />는 html div태그를 나타내지만, <Welcome />은 컴포넌트를 나타내며, Welcome이 있어야 한다.`

### 컴포넌트 합성

컴포넌트는 자신의 출력에 다른 컴포넌트를 참조할 수 있다. React 앱에서는 버튼, 폼, 화면 등의 모든 것들이 흔히 컴포넌트로 표현된다.

* Welcome을 여러번 렌더링 하는 App 컴포넌트 만들기

  ```react
  function Welcome(props) {
    return <h1>Hello, {props.name}</h1>;
  }
  
  function App() {
    return (
    	<div> // JSX 표현식은 반드시 부모 엘리먼트를 가진채로 반환되어야 한다.
      	<Welcome name="Sara" />
        <Welcome name="Cahal" />
      	<Welcome name="Edite" />
      </div>
    );
  }
  
  ReactDOM.render(
  	<App />,
    document.getElementById('root');
  );
  ```

### 컴포넌트 추출

: 컴포넌트를 여러개의 작은 컴포넌트로 나눌수 있다.

Comment 컴포넌트 예제

```react
function Comment(props) {
  return (
  	<div className="Comment">
    	<div className="UserInfo">
      	<img className="Avatar" 
          src={props.author.avatarUrl}
          alt={props.author.name}
          />
        <div className="UserInfo-name">
        	{props.author.name}
        </div>
      </div>
      <div className="Comment-text">
        {props.text}
      </div>
      <div className="Comment-date">
      	{formatDate(props.date)}
      </div>
    </div>
  );
}
```

Comment 컴포넌트는 author(객체), text(문자열) 및 date(날짜)를 props로 받은 후 소셜 미디어 웹 사이트의 코멘트를 나타낸다. 이 컴포넌트는 구성요소들이 모두 중첩구조로 이루어져 있어서 변경하기 어렵고, 각 구성요소를 개별적으로 재사용하기 힘들다.

* 이 컴포넌트에서 몇가지 컴포넌트 추출해보기

  * 1) Avatar 추출하기 : <u>Avatar는 자신이  Comment 내에서 렌더링 된다는 것을 알 필요가 없다. 따라서 props의 이름을 author 에서 더욱 일반화된 user로 변경.</u>

    -> props의 이름은 사용될 문맥이 아닌 컴포넌트 자체의 관점에서 짓는것이 좋다.

    ```react
    function Avatar(props) {
      return (
      	<img className="Avatar"
          src={props.user.avatarUrl}
          alt={props.user.name}
          />
      );
    }
    ```

    ```react
    function Comment(props) {
      return (
      	<div className="Comment">
        	<div className="UserInfo">
          	<Avatar user="{props.author}" />
            <div className="UserInfo-name">
            	{props.author.name}
            </div>
          </div>
          <div className="Comment-text">
            {props.text}
          </div>
          <div className="Comment-date">
          	{formatDate(props.date)}
          </div>
        </div>
      );
    }
    // Avatar 컴포넌트를 이용해서 단순해진 Comment 컴포넌트.
    ```

  * 2) UserInfo 컴포넌트 추출

    ```react
    function UserInfo(props) {
      return (
      	<div className="UserInfo">
        	<Avatar user={props.user} />
          <div className="UserInfo-name">
          	{props.user.name}
          </div>
        </div>
      );
    }
    ```

    ```react
    function Comment(props) {
      return (
      	<div className="Comment">
        	<UserInfo user={props.author} />
          <div className="Comment-text">
          	{props.text}
          </div>
          <div className="Comment-date">
          	{formatDate(props.date)}
          </div>
        </div>
      );
    }
    ```

    Comment 컴포넌트가 더 간단해 졌다. 컴포넌트를 추출해내면, 재사용 가능한 컴포넌트를 만들어 둠으로써, 더 큰 앱에서 작업할 때 유용하다. UI 일부가 여러번 사용되거나, UI 일부가 자체적으로 복잡한 경으, 별도의 컴포넌트로 만드는 게 좋다.

### props는 읽기전용이다.

: 컴포넌트 자체 props를 수정해서는 안된다. 

```react
function sum(a, b) {
  return a + b;
}
// 입력값을 바꾸지 않고 , 동일한 입력에 대해 항상 동일한 값을 반환하는 순수함수
```

```react
function withdraw(account, amount) {
  account.total -= amount;
}
// 자신의 입력값 자체를 변경해 버리기 때문에 순수함수 아니다.
```

**<u>모든 React 컴포넌트는 자신의 props를 다룰때 반드시 순수함수 처럼 동작해야 한다.</u>**

React 컴포넌트는 state를 통해 이 규칙을 위반하지 않고 사용자 액션, 네트워크 응답 및 다른요소에 대한 응답으로 자신의 출력값을 변경할 수 있다.