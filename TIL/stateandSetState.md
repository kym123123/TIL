# state, setState()

## 1) constructor 메서드 내에서의 state

* Constructor 메서드 내에서 state를 초기화 하는 작업이 없다면, 해당 React 컴포넌트에서 생성자를 구현하지 않아도 된다. 

* 초기화를 위해서는 아래와 같이 작성한다

  ```javascript
   class App extends React.Component {
    constructor(props) {
      super(props); // 작성하지 않으면 에러 발생!
      this.state = {
        count: 0 // state객체안의 상태프로퍼티 키 count와 그 값.
      };
    }
  }
  ```

* 또는 멤버변수(클래스 필드)를 사용하여 아래와 같이 작성할 수도 있다.

  ```javascript
  class App extends React.Component {
    state = {
      count: 0
    };
  }
  ```
  
* constructor 메서드 내부에서는 state의 초기화를 위해서 setState( ) 메서드를 사용해서는 안된다. state의 초기화를 위해서는 반드시 위와 같이 멤버변수 혹은 this.state를 이용한다.

* state의 초기값을 할당해줄때, state에 props값을 복사해서 전달하면 안된다. 이는 props의 값이 변하더라도 state에 반영되지 않기 때문에 버그를 발생시키기 쉬운 안티패턴이다. 처음 들어온 props의 값만을 이용하고자 할 경우에만 사용한다. 그럴 경우에는 props의 이름을 count 보다는 initialCount, defaultCount등과 같이 명시적으로 목적이나 역할을 표기해 주는것이 좋다.

  ```javascript
  class App extends React.Component {
    constructor(props) {
      super(props);
      this.state = {
        count: props.number; // anti-pattern. 이렇게 하지 않도록 한다.
      };
    }
  }
  ```

## 2) state의 값을 변경하기 위한 setState() 메서드

* constructor 메서드 이외의 공간에서 state의 값을 변경하기 위해서 아래처럼 this.state.key 와 같이 직접적으로 접근하여 값을 할당하지 않는다. property key에 접근하여 직접 변경하는 것은  constructor에서 초기화를 해주는 경우에만 해당한다.

  ```javascript
    // App component 내부의 click() 메서드에서의 state를 잘못 변경하는 경우.
    click () {
      this.state.count = 1; // anti-pattern. 
    }
  ```

* state를 변경해주기 위해서는 setState메서드를 사용하여 새로운 값을 할당해 주도록 한다.

  ```javascript
  click() {
    this.setState({ count: 3 }); // count의 값이 초기값 0 에서 3으로 업데이트 되도록 요청을 한다.
  }
  // 새로운 객체를 리터럴로 생성하여 state 객체에 존재하는 count 프로퍼티 키와 값을 새로 전달한다.
  // 이렇게 하면 기존에 존재하는 count 키에 새로운 값이 오버라이딩 된다.
  ```

* 그렇다면 이 setState를 이용하여 렌더링된 count의 값을 변경시키는 컴포넌트를 만들어보자.

* ```javascript
  class ClassComponent extends React.Component {
    state = {
      count: 0,
    };
  
    add = number => {
      // 전달받은 인수 number만큼 state를 증가시키는 함수.
      this.setState({ count: this.state.count + number }); // setState 호출!
      console.log(this.state.count);
    };
  
    click = () => {
      this.add(1); // count 1 증가 console.log = 1 출력예상
      this.add(2); // count 2 증가 console.log = 2 출력예상
      this.add(3); // count 3 증가 console.log = 3 출력예상
      // 1 + 2 + 3 = 6
      // 총 6이 증가하게 되는 행동을 예상
    };
  
    render() {
      return (
        <div>
          {this.state.count} <button onClick={this.click}>count 증가</button>
        </div>
      );
    }
  }
  
  ReactDOM.render(<ClassComponent />, document.getElementById('root'));
  ```

* button 태그에 click 인스턴스 메서드를 바인딩 해주었고, 그 add인스턴스 메서드안에서는 증가시키고싶은 크기인 number를 인수로 전달하면서 add 인스턴스 메서드를 세번 호출한다. 각각 1, 2, 3 을 전달하 +1, +2, +3 만큼 state를 업데이트 하도록 하기에, 예상되는 결과는 총 6을 증가시킨 { count:  6 }이다.

* ![image](https://user-images.githubusercontent.com/51959017/102106447-edfeec00-3e73-11eb-8e22-bdbd0917b8c5.png) 초기 상태가 렌더링 된 모습.

* count 증가 버튼을 눌렀을 때의 렌더링 결과와 console.log 결과는 아래와 같다.

* ![image](https://user-images.githubusercontent.com/51959017/102106619-23a3d500-3e74-11eb-9028-dce601f4dbe6.png)

* ![image](https://user-images.githubusercontent.com/51959017/102114092-2b1bac00-3e7d-11eb-89ac-0894eb843499.png) <= 왜 6이 아닌 3만큼 증가하였으며, click 메서드 안에서 세차례 add 메서드가 호출되면서 찍힌 console.log의 결과는 모두 0이 나왔을까?

* setState 메서드는 비동기로 동작하기 때문이다. **setState를 이벤트 핸들러 안에서 호출한다면. 호출되는 setState에 의해서 업데이트가 요청되는 state의 count의 값은 호출 이후 즉각적으로 반영되지 않는다. setState는 이벤트 핸들러 안에서 현재 state의 값에 대한 변화를 요청하기만 하는 것이고, 그 요청사항은 이벤트 핸들러가 종료되고 react에 의해서 효율적으로 상태가 갱신된다.**

  ```javascript
   click = () => {
      this.add(1); // count를 1 증가시켜줘 라고 react에게 요청한 상태
      this.add(2); // count를 2 증가시켜줘 라고 react에게 요청한 상태
      this.add(3); // count를 3 증가시켜줘 라고 react에게 요청한 상태
      // ... 혹시 있을지모를 다른 행동을 모두 마친후
      // 이벤트 핸들러가 종료되기 직전인 이곳에서 3가지 setState요청사항을 한번에 효율적으로 처리한다. 이후 렌더링 전에 update를 한다.
    };
  ```

  효율적으로 한번에 처리한다고 하는 것은, state내의 하나의 key에 대해서 요청사항을 모두 즉각 반영하여 갱신값을 한 이벤트 핸들러 안에서 누적하여 처리하지 않는다는 것이다. 같은 key에 대해서는 마지막에 요청되는 변경 사항이 오버라이딩 되는것이다. 마치 Javascript의 스프레드 문법처럼 처리된다. 위의 요청사항을 아래와 같이 풀어서 쓸 수 있다.

  ```javascript
  click = () => {
    {...this.state,
      ...{count: this.state.count + 1},
      ...{count: this.state.count + 2},
      ...{count: this.state.count + 3}
    }
    // 결과는 {...this.state, ...{count: this.state.count + 3}}와 같다. 
    // 따라서 state내의 count 프로퍼티의 최종 업데이트 요구 값은 "현재 값 에서 3만큼 증가시켜라"가 되는 것이다.
  }
  ```

  위처럼 하나의 키에 대해서는(위의 예에서는 count) 업데이트 요구사항을 리액트가 모두 적용하여

   count: 0 -> count: 0 + 1 //  count: 1 -> count: 1 + 2 // count: 3 -> count: 3 + 3 처럼 업데이트 시키지 않는다는 것이다.

* <u>왜 react는 위처럼 업데이트 요구사항을 매 setState호출마다 반영하지 않고 종합하여 한번에 처리할까? 바로 여러번의 불필요한 렌더링을 막기 위해서 이다.</u> render 메서드는 state또는 props가 변경될 때만(state의 변경사항은 setState를 호출하면 state를 변경하는 것으로 react가 인지하고 있다.) 호출되어 변경사항을 반영한 렌더링을 하게 된다. 이 때, 다시 렌더를 하는 것은 비용이 많이 드는 행동이다. 하나의 이벤트 핸들러 안에서 세 번이나 setState를 호출하게 된다면, 렌더링을 세번이나 하게되는, 막대한 비용이 드는 요청이기 때문에 이벤트 핸들러가 닫히는 시점에 요구사항을 효율적으로 한번에 처리하는 것이다. (state값의 업데이트는 렌더링 되기 전에 한다.) (같은 state의 프로퍼티 키에 대한 요청사항 세번이므로, 이를 차례대로 갱신한다고 하면, 결국 사용자의 눈에 보이게 되는 것은 마지막 갱신 요구사항이다.)

* 따라서 같은 이벤트 핸들러 안에서는 어떠한 행동을 하더라도 내가 업데이트를 요구한 state 값을 적용받아서 원하는 행동을 할 수 없는 것이다. 그로 인하여 위에서도 이전의 state의 count의 값인 0 만이 console창에 찍힌 것이다.

* ```javascript
   click = () => {
      this.add(1); // count를 1 증가 요청, 요청접수 완료, 그러나 count는 아직 요청 반영 전의 값인 0 이다.
      console.log(this.state.count) // 0
      
      this.add(2); // count를 2 증가 요청, 요청접수 완료, 그러나 count는 아직 요청 반영 전의 값인 0 이다.
      console.log(this.state.count) // 0
    		
      this.add(3); // count를 3 증가 요청, 요청접수 완료, 그러나 count는 아직 요청 반영 전의 값인 0 이다.  
      console.log(this.state.count) // 0
     
      ...
      
      // 이제 요청사항들을 종합한다. (batching)
      // 렌더링 전에 state를 요청에 맞게 변경하고 이 변경된 state 값으로 render를 한다. render 호출 ( shouldComponentUpdate가 true이면 )
      // 따라서 render() 메서드 안에서는 state값이 업데이트 된 값이고, 이 값으로 렌더링을 하기 때문에 우리 눈에는 우리가 요청한 state 값이 보이게 되는것.
   };
   ```




### 그렇다면 내가 요청한 state의 변경값을 이용하여 같은 이벤트 핸들러 or 메서드 안에서 뭔가를 수행하고자 한다면 어떻게 해야 할까? 세 가지 방법이 있다.

1 .  ComponentDidUpdate() 안에서 작업을 수행한다. 이 생명주기 메서드는 state 변경 요청사항을 토대로 변경한 값을 가지고 렌더링 한 이후에 호출되는 메서드이기 때문에 state는 변경 이후의 값이다. (이 메서드는 최초 렌더링 = 마운팅시 최초 렌더링)에서는 호출되지 않는다.

```javascript
componentDidUpdate(prevProps, prevState, snapshot) {
  conosole.log(this.state.count) // 반영 된 값인 3이 출력된다.
}
```



2 .  setState(updater, callback) 메서드에서 updater 인자에 콜백 함수를 전달하여 이용한다. 콜백함수의 인자인 state와 props는 갱신 이후의 값이 보장된다. 따라서 갱신된 값을 가지고 작업을 하고싶다면 this.state.count가 아닌  state.count로 참조하면 갱신된 값을 조회받을 수 있다.


```javascript
 add = number => {
    // 전달받은 인수 number만큼 state를 증가시키는 함수.
    this.setState((state, props) => {
      console.log('this.state.count',this.state.count); // 갱신 이전 값
      console.log('state.count',state.count); // 갱신 이후 값
      return { count: this.state.count + number }; // 리턴 값은 기존의 this.state객체에 얇게 병합된다.
    });
  };

  click = () => {
    this.add(1); // count 1 증가, return 문 이전의 console.log = 0 
    this.add(2); // count 2 증가, return 문 이전의 console.log = 1 
    this.add(3); // count 3 증가, return 문 이전의 console.log = 2 
  };
```
코드 실행 결과 console(this.state.count로 조회한 값은 갱신 이전의 값. 매개변수 state.count로 조회한 값은 갱신 이후의 값인 것을 알 수 있다.) 같은 이벤트 핸들러 내부에서 갱신요구사항이 반영된 것을 볼 수 있다.

![image](https://user-images.githubusercontent.com/51959017/102111384-a8452200-3e79-11eb-8780-e0e10b2a6e22.png)

이 방법은 setState요청사항이 차례대로 어딘가의 대기열에 들어가게 되는것 같이 동작한다. return 문의 요청사항이 차례대로 state업데이트 대기열에 쌓이고, 다음의 add메서드에서 console.log(state.count)를 출력하면, 대기열에 있는 직전의 setState메서드 요청사항인 {count: 1}을 반환받을수 있다는 것이 확인된다. 이러한 요청사항들은 이벤트 핸들러가 종료되고 종합되어 render되기 전에 업데이트 된다. **<u>(shouldComponentUpdate)메서드 안에서 state를 확인해 보면 여전히 update되기 전인 값임을 알수 있다.</u>**



3. setState(updater, ( ) => {...}) 메서드에서, updater인자에 요구사항을 반영한 state 객체를 새로 전달하고, 두번째 인자인 콜백함수 안에서 this.state.count를 참조하여 이용한다. 이 두 번째 인자인 콜백함수 안에서의  this.state는 첫번째 매개변수에 의해 전달된 갱신요청된 값이 적용된 state임이 보장된다. <u>(단 갱신 적용된 this.state.count의 값은 이벤트 핸들러 내의 모든 코드가 실행되고 난 이후에 출력된다.)</u>

```javascript
 add = number => {
    // 전달받은 인수 number만큼 state를 증가시키는 함수.
    this.setState({ count: this.state.count + number }, () => {
      console.log(this.state.count); // number만큼 증가시켜달라는 요구사항이 종합하여 최종 반영된 state가 출력 됨.
      // add(3)이 이벤트 핸들러 내의 가장 마지막 setState호출 이므로 count 키의 최종 값은 3이다. 따라서 3출력
    });
  };

  click = () => {
    this.add(1); // count 1 증가요청, 그러나 최종값은 3이므로 3 출력
    this.add(2); // count 2 증가요청, 그러나 최종값은 3이므로 3 출력
    this.add(3); // count 3 증가요청, 그러나 최종값은 3이므로 3 출력
  };
```

![image](https://user-images.githubusercontent.com/51959017/102113184-12f75d00-3e7c-11eb-842f-ec0ee212ecab.png) 콘솔에 찍힌 결과.

아래의 예로 이벤트핸들러 안에서 setState(updater, callback) 방식의 메서드 실행순서를 자세히 알아 보자.

```javascript
class ClassComponent extends React.Component {
  state = {
    count: 0,
  };

  add = number => {
    // 전달받은 인수 number만큼 state를 증가시키는 함수.
    this.setState({ count: this.state.count + number }, () => {
      console.log('inside', this.state.count); // inside
    }); // setState 호출!
    console.log('outside', this.state.count);//outside
  };

  click = () => {
    this.add(1); // count 1 증가 요청
    this.add(2); // count 2 증가 요청
    this.add(3); // count 3 증가 요청
  };

  render() {
    return (
      <div>
        {this.state.count} <button onClick={this.click}>count 증가</button>
      </div>
    );
  }
}
```

console.log 실행결과

![image](https://user-images.githubusercontent.com/51959017/102693441-f8e3c300-425d-11eb-81c3-1bee0e595e2b.png)

이를 보면 setState({state 업데이트 요청사항}, 콜백함수) 형식으로 setState 메서드를 호출 했을 경우 콜백함수 안에서는 this.state의 값이 왜 요청대로 업데이트 된 값이 나오는지 확인할 수 있다. (콜백함수 내부에서의 console.log를 확인하기위해 inside를 붙힌) console.log는 이벤트 핸들러가 실행되고, setState의 값을 업데이트 하고 난 이후 실행 종료 전 가장 마지막에 처리되는 것을 알수있다. 따라서 최종 요청값인 count:3이 출력 되는 것이다.

**<u>단 이러한 방법을 사용하더라도 setState의 업데이트 요청사항은 이벤트핸들러가 종료되고 요청사항을 종합하여 shouldComponentUpdate 메서드가 종료되고 render 메서드가 실행되기 직전에 처리 된다.</u>**

아래의 예로 확인해 볼 수 있다.

```javascript
class ClassComponent extends React.Component {
  state = {
    count: 0,
  };

  add = number => {
    // 전달받은 인수 number만큼 state를 증가시키는 함수.
    this.setState({ count: this.state.count + number }, () => {
      console.log('inside', this.state.count);
    }); // setState 호출!
    console.log('outside', this.state.count);
  };

  click = () => {
    this.add(1); // count 1 증가 요청
    this.add(2); // count 2 증가 요청
    this.add(3); // count 3 증가 요청
  };
  shouldComponentUpdate() {
    console.log('should Update?', this.state);
    return true;
  }

  render() {
    console.log('now render', this.state);
    return (
      <div>
        {this.state.count} <button onClick={this.click}>count 증가</button>
      </div>
    );
  }
  componentDidUpdate() {
    console.log('did updated', this.state);
  }
}
```

![image](https://user-images.githubusercontent.com/51959017/102693754-419c7b80-4260-11eb-9eb7-0f340c8248b7.png)

shouldComponentUpdate에서도 여전히 count: 0 임을 확인가능.

render 메서드가 실행되기 직전에 업데이트 되므로 , render메서드 실행 이후부터는 모두 count: 3이다.

**<u>특이한 점은 add메서드 안의 setState메서드 안에서 호출한 console.log('inside' + this.state.count)는 전체 생명주기 메서드가 모두 실행 된 이후에 출력된다는 점이다.</u>**

