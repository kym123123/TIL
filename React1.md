# React Documentation

## JSX

```react
const element = <h1>Hello, world!</h1>
```

: 위의 문법은 JSX이며 javascript를 확장한 문법이다.  JSX는 React 엘리먼트(element)를 생성한다.

* react에서는 이벤트가 처리되는 방식, 시간에 따라 state가 변하는 방식, 화면에 표시하기 위해 데이터가 준비되는 방식 등, 렌더링 로직이 다른 UI로직과 연결된다.

* react는 별도의 파일에 마크업과 로직을 인위적으로 분리하지 않고 둘다 포함하는 **"컴포넌트"**라는 유닛으로 분리한다.

## JSX에 표현식 포함하기

```react
const name = 'Josh Perez';
const element = <h1>Hello, {name}</h1>; // {name} -> jsx의 중괄호

ReactDOM.render(
  element,
  document.getElementById('root');
);
```

* name 이라는 변수를 선언한 후, 중괄호로 감싸 JSX안에서 사용하였다.
* JSX의 중괄호 안에는 유효한 모든 Javascript 표현식을 넣을 수 있다.

```react
function formatName(user) {
  return user.firstName + ' ' + user.lastName;
}
const user = {
  firstName: 'Harper',
  lastName: 'Perez'
};

//  formatName 함수 호출결과를 <h1> 엘리먼트에 포함.
const element = (
  <h1>
    Hello, {format(user)}; 
  </h1>
);

ReactDom.render(
	element,
  document.getElementById('root')
);
```

* formatName 함수의 호출도 유효한 Javscript 표현식이므로 <h1>엘리먼트에 위처럼 표함 가능하다.

* JSX도 표현식이다. 컴파일이 끝나면 JSX 표현식이 정규 Javascript 함수 호출이 되고 , Javascript객체로 인식된다. 따라서 JSX를 if문 및 for문 같은 제어문 안에서 사용도 가능하고, 변수에 할당할 수도 있고, 인자로 받아들이고, 함수의 반환값으로 사용할 수 있다. -> 표현식이니까...

  ```react
  function getGreeting(user) {
    if (user) {
      return <h1>Hello, {formatName(user)}!</h1>;
    }
    return <h1>Hello, Stranger</h1>;
  }
  ```

* JSX 속성 정의

  : 속성에 따옴표를 이용하여 문자열 리터럴을 정의할 수 있다.

  또한 중괄호를 사용하여 어트리뷰트에 JavaScript 표현식을 삽입할 수도 있다.

  ```react
  const element = <div tabIndex="0"></div>;
  const element = <img src={user.avatarUrl}></img>;
  ```

  어트리뷰트에 Javascript 표현식을 삽입할 때 중괄호 주변에 따옴표 사용하지 않는다.

  따옴표(문자열 값에 사용) , 중괄호(표현식에 사용)중 하나만 사용한다. 

  `동일한 어트리뷰트에 두 가지를 동시에 사용하면 안된다.`

  JSX는 HTML 보다 Javascript에 가깝기 때문에 ReactDOM은 HTML 어트리뷰트 이름 대신 

  camelCase 작명법을 사용한다.

  Ex) JSX에서 class는 className, tabindex는 tabIndex로 사용한다.

  빈태그 라면 <img .../>을 이용해 닫아주어야 한다.

* JSX 태그는 자식 태그를 포함할 수 있다.

  ```react
  const element = (
    <div>
      <h1>Hello!</h1>
      <h2>Good to see you here.</h2>>
    </div>
  );
  ```

* JSX를 이용해 html태그를 삽입하는 것은 XSS(cross-site-scripting) 공격을 방지한다. React DOM 은 JSX에 삽입된 모든 값을 렌더링 하기 전에 문자열로 변환하기 때문이다. -> 명시적으로 애플리케이션에서 작성하지 않은 내용은 injection 되지 않는다.

* JSX는 객체를 표현한다. Babel은 JSX를 React.createElement( )호출로 컴파일 한다. 따라서 아래의 두 예시는 동일하다

  ```react
  const element = (
    <h1 className="greeting">
      hello, world!
    </h1>
  )
  
  const element = React.createElement(
    'h1',
    {className: 'greeting'},
    'hello, world!'
  );
  ```

  위의 코드는 React.createElement()에 의해서 아래와 같은 객체를 생성하고 몇가지 검사를 수행하며 작성된다. -> 버그가 없는 코드를 작성하도록 해준다.

  ```react
  // 주의: 다음 구조는 단순화 되었습니다.
  const element = {
    type: 'h1',
    props: {
      className: 'greeting',
      children: 'Hello, world!'
    }
  };
  // 이러한 객체를 React 엘리먼트라고 한다. -> 화면에 표시하려는 항목에 대한 설명
  ```

