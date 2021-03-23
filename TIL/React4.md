# React Documentation

## state와 생명주기

: state는 props와 유사하지만, 비공개이며 컴포넌트에 의해 완전히 제어된다.

### 함수 -> 클래스 변환

1. React.Component를 확장하는 동일한 이름의 ES6 class를 생성한다.
2. render( ) 라고 불리는 빈 메서드를 추가한다.
3. 함수의 내용을 render( ) 메서드 안으로 옮긴다.
4. render( ) 내용 안에 있는 props를 this.props로 변경한다.
5. 남아있는 빈 함수 선언을 삭제한다.

```react
class Clock extends React.Component {
  render() {
    return (
    	<div>
      	<h1>Hello, world!</h1>
        <h2>It is {this.props.date.toLocaleTimeString()}.</h2>
      </div>
    );
  }
} // 클래스로 정의된 Clock
```

render 메서드는 업데이트가 발생할 때마다 호출되지만, 같은 DOM 노드로 <Clock />를 렌더링하는 경우 Clock 클래스의 단일 인스턴스만 사용된다. -> 로컬 state와 생명주기 메서드가 같은 부가적인 기능을 사용할 수 있도록 해준다.

### 클래스에 로컬 state 추가하기

* date를 props에서 state로 이동하기

  1. render( ) 메서드 안에 있는  this.props.date를 this.state.date로 변경한다.

  ```react
  class Clock extends React.Component {
    render() {
      return (
      	<div>
        	<h1>Hello, world!</h1>
          <h2>It is {this.state.date.toLocaleTimeString()}.</h2>
        </div>
      );
    }
  }
  ```

  2. 초기 this.state를 지정하는 class constructor를 추가한다.

  ```react
  class Clock extends React.Component {
    constructor(props) {
      super(props); // 기본 props를 constructor에 전달
      this.state = {date: new Date()};
    }
    
    render() {
      return (
      	<div>
        	<h1>Hello, world!</h1>
          <h2>It is {this.state.date.toLocaleTimeString()}.</h2>
        </div>
      );
    }
  }
  ```

  클래스 컴포넌트는 항상 props로 기본 constructor를 호출해야 한다.

  

  3. <Clock /> 요소에서 date prop을 삭제한다.

  ```react
  class Clock extends React.Component {
    constructor(props) {
      super(props);
      this.state = {date: new Date()};
    }
    
    render() {
      return (
      	<div>
        	<h1>Hello, world!</h1>
          <h2>It is {this.state.date.toLocaleTimeString()}.</h2>
        </div>
      );
    }
  }
  
  
  ReactDOM.render(
  	<Clock />,
    document.getElementById('root')
  );
  ```

### 생명주기 메서드를 클래스에 추가하기

: 많은 컴포넌트가 있는 애플리케이션에서 컴포넌트가 삭제될 때, 해당 컴포넌트가 사용 중이던 리소스를 확보하는 것이 중요하다.

마운팅:  컴포넌트가 처음 DOM에 렌더링 될 때마다 호출. ->componentDidMount 메서드

언마운팅: 컴포넌트에 의해 생성된 DOM이 삭제될 때마다 호출. -> componentWillUnmount 메서드

컴포넌트 클래스에서 특별한 메서드를 선언하여 컴포넌트가 마운트 되거나 언마운트 될 때 일부 코드를 작동할 수 있다.

```react
class Clock extends React.Component {
  constructor(props) {
    super(props);
    this.state = {date: new Date()};
  }
// 생명주기 메서드 componentDidMount(), componentWillUnmount()
  componentDidMount() {
  }

  componentWillUnmount() {
  }

  render() {
    return (
      <div>
        <h1>Hello, world!</h1>
        <h2>It is {this.state.date.toLocaleTimeString()}.</h2>
      </div>
    );
  }
}
```

componentDidMount() 메서드는 컴포넌트 출력물이 DOM 에 렌더링 된 후에 실행된다. 따라서 이 메서드 안에서 타이머 설정하면 좋다. 

```react
componentDidMount() {
  this.timerID = setInterval(
  	() => this.tick(),
    1000
  );
}
```



 componentWillUnmount() 메서드는 렌더링된 DOM이 삭제될때 실행된다. 이 메서드 안에서 타이머를 해제시키면 좋다

```react
componentWillUnmount() {
  clearInterval(this.timerID);
}
//타이머 해제
```

```react
class Clock extends React.Component {
  constructor(props) {
    super(props);
    this.state = {date: new Date()};
  }

  componentDidMount() {
    this.timerID = setInterval(
      () => this.tick(),
      1000
    );
  }

  componentWillUnmount() {
    clearInterval(this.timerID);
  }

  tick() {
    this.setState({
      date: new Date()
    });
  }

  render() {
    return (
      <div>
        <h1>Hello, world!</h1>
        <h2>It is {this.state.date.toLocaleTimeString()}.</h2>
      </div>
    );
  }
}

ReactDOM.render(
  <Clock />,
  document.getElementById('root')
);
```

위의 코드를 통해서 메서드가 어떻게 호출되는지 보기.

1. <Clock />이 ReactDOM.render()로 전달되면, React는 Clock 컴포넌트의 constructor를 호출한다. Clock이 현재 시각을 표시해야 하기 때문에 현재 시각이 포함된 객체로 this.state를 초기화한다. 
2. React는 Clock 컴포넌트의 render()메서드를 호출한다. 이를 통해 React는 화면에 표시되어야 할 내용을 알게된다. React는 Clock 렌더링 출력값을 일치시키기 위해 DOM을 업데이트한다.
3. Clock 출력값이 DOM에 삽입되면 React는 componentDidMount( ) 생명주기 메서드를 호출한다. ->그 안에서 Clock 컴포넌트는 매초 컴포넌트의  tick( ) 메서드를 호출하기 위한 타이머를 설정하도록 브라우저에 요청한다.
4.  매초 브라우저가 tick( ) 메서드를 호출한다. 그 안에서 Clock 컴포넌트는 setState( )에 현재 시각을 포함하는 객체를 호출하면서 UI 업데이트를 진행한다. setState( ) 호출 덕분에 React는 state가 변경된 것을 인지하고 화면에 표시될 내용을 알아내기 위해 render( ) 메서드를 다시 호출한다. 이 때, render ( ) 메서드 안의  this.state.date가 달라지고, 렌더링 출력값은 업데이트된 시각을 포함한다. React는 이에 따라 DOM을 업데이트 한다.
5. Clock 컴포넌트가 DOM으로부터 한번이라도 삭제된 적이 있다면 React는 타이머를 멈추기 위해 componentWillUnmount( ) 생명주기 메서드를 호출한다.

### State 사용법

1. <u>직접 State를 수정하지 않는다. 대신 setState()를 사용한다. this.state를 지정할 수 있는 유일한 공간은 constructor다.</u>

   ```react
   // 잘못된 코드 케이스
   this.state.comment = 'Hello';
   ```

   ```react
   // 올바른 코드 케이스
   this.setState({comment: 'Hello'});
   ```

2. State 업데이트는 비동기적일 수도 있다. -> React는 성능을 위해 여러 setState( ) 호출을 단일 업데이트로 한꺼번에 처리할 수 있다. this.props와 this.state가 비동기적으로 업데이트될 수 있기 때문에 다음 state를 계산할 때 해당 값에 의존해서는 안 된다.

   ```react
   // 잘못된 코드 케이스
   this.setState({
     counter: this.state.counter +
    this.props.increment
   });
   ```

   객체보다는 함수를 인자로 사용하는 다른 형태의 setState( )를 사용해야 한다. 그 함수는 이전의 state를 첫 번째 인자로 받아들이고, 업데이트가 적용된 시점의 props를 두 번째 인자로 받아들인다.

   ```react
   // 올바른 코드 케이스
   // 화살표 함수 이용
   this.setState((state, props) => ({
     counter: state.counter + props.increment
   }));
   
   //일반 함수 이용
   this.setState(function(state, props) {
     return {
       counter: state.counter + props.increment
     };
   });
   ```

3. State 업데이트는 병합된다. 

   setState( )를 호출할 때, React는 제공한 객체를 현재 state로 병합한다.

   state는 다양한 독립적인 변수를 포함할 수 있다.

   ```react
   constructor(props) {
     super(pros);
     this.state ={
       posts: [],
       comments: []
     };
   }
   ```

   별도의 setState( ) 호출로 이러한 변수를 독립적으로 업데이트할 수 있다.

   ```react
   componentDidMount() {
     fetchPosts().then(response => {
       this.setState({
         posts: response.posts
       });
     });
     
     fetchComments().then(response => {
       this.setState({
         comments: response.comments
       });
     });
   }
   ```

   병합은 얕게 이루어진다. 따라서  this.setState({comments})는 this.state.posts에 영향을 주지 않지만 this.state.comments는 완전히 대체된다.

   