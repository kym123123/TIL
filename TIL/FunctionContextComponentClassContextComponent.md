## Context

context -> 다양한 레벨로 네스팅된 많은 컴포넌트에게 데이터를 전달할 수 있도록 하는것. 기존의 props 전달 방식과 다르게 여러 상위컴포넌트를 통해 그 상위컴포넌트들이 사용하지도 않는 props들을 전달해주는 수고가 없어진다. (단 context를 사용한 컴포넌트는 재사용이 힘들어지므로 꼭 필요한 경우에만 사용하도록 한다.)

### createContext

```react
const myContext = createContext(defaultValue);
// context 객체 생성 
// 이 객체를 구독하는 컴포넌트를 렌더링할 때, react는 트리 상위에서 가장 가까이 있는 Provider로 부터 현재값을 읽는다.
```

매개변수 defaultValue는 트리 내에서 적절한 Provider를 찾지 못했을 경우에만 쓰이는 값. 



### Provider

```react
<MyContext.Provider value={/*어떤 값*/}></MyContext.Provider>
```

provider: context 오브젝트에 포함된 react 컴포넌트. <u>context를 구독하는 컴포넌트들에게 context의 변화를 알린다.</u>

value라는 prop을 받아서 , 이 값을 하위에 있는 컴포넌트에게 전달한다. Provider 하위에 또 다른 Provider를 배치하는 것도 가능하다.

구독하는 컴포넌트로부터 가장 가까운 Provider의 value가 우선시 된다.

Provider의 하위에서 context를 구독하는 모든 컴포넌트는 Provider의 value Props가 바뀔때마다 다시 렌더링 된다.(Provider로 부터 하위 consumer(.contextType과 useContext를 포함)로의 전파는 shouldComponentUpdate 메서드가 적용되지 않는다. 상위 컴포넌트가 업데이트를 건너 뛰어도, consumer가 업데이트 된다.) -> context값의 변경 여부는 Object.is 알고리즘을 사용하여 이전값 새로운값을 비교

> `**Object.is()**` 메서드는 두 값이 [같은 값](https://developer.mozilla.org/ko/docs/Web/JavaScript/Equality_comparisons_and_sameness)인지 결정합니다.
>
> ```javascript
> Object.is(value1, value2);
> ```

### Class.contextType

```react
// App.js

import './App.css';
import MyClass from './MyClass';
import { MyContext } from './MyContext';

function App() {
  console.log(MyContext);

  return (
    <MyContext.Provider value={'dark'}>
      <div className='App'>
        hi
        <button>click</button>
        <MyClass />
      </div>
    </MyContext.Provider>
  );
}

export default App;
```

```react
// MyClass.js

import React, { Component } from 'react';
import { MyContext } from './MyContext';

class MyClass extends Component {
  render() {
    return <div>{this.context}</div>;
  }
}

MyClass.contextType = MyContext;
export default MyClass;
```

```react
// MyContext.js

import { createContext } from 'react';

export const MyContext = createContext('light'); // 기본값을 인자로 넣어줄 수 있다.
```

createContext()로 생성한 Context객체를 원하는 클래스의 contextType 프로퍼티로 지정할 수 있다. 이 프로퍼티를 활용하여, 클래스 안에서 this.context를 이용해 클래스 안에서 this.context를 이용해 가장 가까운 context의 provider를 찾아서 값을 읽을 수 있고, 이 값은 컴포넌트의 모든 생명주기 메서드에서 사용 가능하다. -> 단 이 contextType을 사용하면, 하나의 context만 구독이 가능하다.

### Context.Consumer

```react
<MyContext.Consumer>
  {value => /* context 값을 이용한 렌더링 */}
</MyContext.Consumer>
```

: context의 변화를 구독하는 react 컴포넌트. 이 컴포넌트를 사용하면 함수 컴포넌트 안에서 context를 구독할 수 있다. context.consumer의 자식은 위처럼 함수여야 한다. 이 함수는 context의 현재 값을 받고 react 노드를 반환한다. 이 함수가 받는 value 매개변수 값은 해당 context의 가장 가까운 provider에서 value prop을 받아온 값이다. 상위에 provider가 없다면, value 매개변수 값은 createContext()의 매겨변수인 defaultValue의 값이 된다.



1. context를 담을 파일을 만든다. MyContext.js 생성

2. createContext()를 이용하여 context를 생성한다.

3. context에 value로 전달될 state와 reducer를 위해서 reducer함수를 만든다.

4. useReducer를 사용하여 커스텀 provider안에서 reducer를 생성, state, dispatch함수를 모두 만들어내고 value라는 객체를 생성하여 담아준다.

5. 커스텀 provider컴포넌트의 return을 아까 만들어둔 context를 반환해주도록 하고, value값과 children 값을 설정해준다.

6. useXXX라는 커스텀 훅을 생성하여 context에 담아둔 state와 dispatch를 사용할 수 있도록 해준다.

7. ```react
   // MyContext.js
   
   import { createContext, useContext, useReducer } from 'react';
   
   const MyContext = createContext(); // 기본값을 인자로 넣어줄 수 있다.
   
   function countReducer(state, action) {
     switch (action.type) {
       case 'increment':
         return { count: state.count + 1 };
       case 'decrement':
         return { count: state.count - 1 };
       default:
         throw new Error(`unhandled action type: ${action.type}`);
     }
   }
   
   function CountProvider({ children }) {
     const [state, dispatch] = useReducer(countReducer, { count: 0 });
   
     const value = { state, dispatch };
     return <MyContext.Provider value={value}>{children}</MyContext.Provider>;
   }
   
   function useCount() {
     const context = useContext(MyContext);
     if (context === undefined) throw new Error('useCount must be used within a CountProvider');
   
     return context;
   }
   
   export { CountProvider, useCount };
   
   ```

8. ```react
   // Count.js
   
   import React from 'react';
   import { useCount } from './MyContext';
   
   function CountDisplay() {
     const { state } = useCount();
     console.log('hello display');
     return <div>{state.count}</div>;
   }
   
   function Counter() {
     const { dispatch } = useCount();
   
     const incrementor = () => dispatch({ type: 'increment' });
     const decrementor = () => dispatch({ type: 'decrement' });
     console.log('hello counter');
     return (
       <div>
         <button onClick={incrementor}>Increase</button>
         <button onClick={decrementor}>Decrease</button>
       </div>
     );
   }
   
   export { CountDisplay, Counter };
   
   ```

   ```react
   // App.js
   
   import './App.css';
   import { CountDisplay, Counter } from './Count';
   import FComponent from './FComponent';
   import { CountProvider } from './MyContext';
   
   function App() {
     return (
       <CountProvider>
         <CountDisplay />
         <Counter />
       </CountProvider>
     );
   }
   
   export default App;
   
   ```

9. 편하게 사용한다.

* 주의사항

  : Context.Provider 하위의 컴포넌트를 다시 렌더링할지 여부를 정할 때, value의 참조값을 확인한다. 따라서 Provider의 부모가 렌더링 될때, 하위 컴포넌트들이 불필요하게 렌더링 될수 있는데, 이러한 경우를 방지하기 위하여 value에 전달되는 객체값을 상태와 같이 일정한 참조값을 저장해 둘수 있는 곳에 저장을 하고 그 상태를 value로 전달하면 불필요한 렌더링을 막을 수 있다.

  ```react
  class App extends React.Component {
    render() {
      return (
        <MyContext.Provider value={{something: 'something'}}>
          <Toolbar />
        </MyContext.Provider>
      );
    }
  }
  // value에 객체 리터럴로 전달된 값은 App이 리렌더링 될 때마다 새로 생성되므로 하위 컴포넌트들도 모두 렌더링된다.
  ```

  ```react
  class App extends React.Component {
    constructor(props) {
      super(props);
      this.state = {
        value: {something: 'something'},
      };
    }
  
    render() {
      return (
        <MyContext.Provider value={this.state.value}>
          <Toolbar />
        </MyContext.Provider>
      );
    }
  }
  
  //state값을 전달하게 된다면 이러한 불필요한 렌더링을 막을 수있다.
  ```

  