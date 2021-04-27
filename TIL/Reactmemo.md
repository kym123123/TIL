# React.memo

: react.memo는 HOC이다. 아래의 코드로 보자.

```javascript
const MyComponent = React.memo(function ({ number1 }) {
  console.log('hello');
  
  return <div>{number1}</div>;
});

const App = () => {
  const [number, setNumber] = useState(0);
  const [number1, setNumber1] = useState(0);

  const onClickHandler = () => {
    setNumber(s => s + 1);
  };

  return (
    <div>
      <button onClick={onClickHandler}>click me</button>
      <span>{number}</span>
      <MyComponent number1={number1} />
    </div>
  );
};
```

MyComponent는 함수형 컴포넌트로 App 컴포넌트로부터 상태 number1을 props로 전달받아서, 렌더링한다. App component에 있는 number1, number 상태가 바뀔때 모두 MyComponent가 재 렌더링 되므로, 직접 사용하는 props인 number1이 변경될때만 재 렌더링 되도록 React.memo로 컴포넌트를 감싸면, props가 변경 될 때만 재 렌더링 되도록 할 수 있다.

React.memo는 props를 비교할 때, 얕은 비교를 한다. 깊은 비교 방식같은 방법을 사용하고 싶으면, React.memo()의 두번째 인수로 비교 함수를 넘겨줄 수 있다.

```javascript
const MyComponent = React.memo(function ({ number1 }) {
  console.log('hello');
  return <div>{number1}</div>;
}, areEqual);

// ...

function areEqual(prevProps, nextProps) {
  return prevProps.something === nextProps.something && prevProps.something2 === nextProps.something2;
}

```

areEqual의 반환값이 true라면 재 렌더링을 위한 컴포넌트 재 호출을 거치지 않고, false를 반환하면 재 호출을 통해 재 렌더링이 된다.

## React.memo를 사용하면 좋은 경우

1. 대부분 같은 props를 이용하여 렌더링 하는 함수형 컴포넌트인 경우, props가 변할일이 없는 경우가 대부분 이라면 이때는 memoization의 이점을 얻을 수 있다.

   ```javascript
   function MovieViewsRealtime({ title, releaseDate, views }) {
     return (
       <div>
         <Movie title={title} releaseDate={releaseDate} />
         Movie views: {views}
       </div>
     );
   }
   
   // Initial render
   <MovieViewsRealtime
     views={0}
     title="Forrest Gump"
     releaseDate="June 23, 1994"
   />
   
   // After 1 second, views is 10
   <MovieViewsRealtime
     views={10}
     title="Forrest Gump"
     releaseDate="June 23, 1994"
   />
   
   // After 2 seconds, views is 25
   <MovieViewsRealtime
     views={25}
     title="Forrest Gump"
     releaseDate="June 23, 1994"
   />
   
   // etc
   ```

   위처럼 매초 views props에 전달되는 값을 서버에 요청하여 갱신하는 상황이 있다면, 같은 title과 releaseDate를 렌더링하는 Moive 컴포넌트를 메모이제이션 해서 아래처럼 사용하면 성능 개선에 효과적이다.

   ```javascript
   function MovieViewsRealtime({ title, releaseDate, views }) {
     return (
       <div>
         <MemoizedMovie title={title} releaseDate={releaseDate} />
         Movie views: {views}
       </div>
     );
   }
   ```

   

## React.memo를 사용하지 말아야 할 경우

1. 일단 클래스 기반의 컴포넌트에는 사용하지 않는것이 좋다. 클래스 컴포넌트에 memoization이 필요하다면, PureComponent를 extends하거나, shouldComponentUpdate()를 이용하여 구현한다.

2. props가 지속적으로 변할수 밖에 없는 컴포넌트를 react.memo로 감싸면, 메모이제이션의 의미도 없으며, 이점을 얻기 힘들다.



## React.memo와 함수, 객체의 참조 동일성

```javascript
const MyComponent = React.memo(function ({ number1, ff }) {
  console.log('hello');
  ff();
  return <div>{number1}</div>;
});

const App = () => {
  const [number, setNumber] = useState(0);
  const [number1, setNumber1] = useState(0);

  const onClickHandler = () => {
    setNumber(s => s + 1);
  };

  return (
    <div>
      <button onClick={onClickHandler}>click me</button>
      <span>{number}</span>
      <MyComponent
        number1={number1}
        ff={() => {
          console.log('hello2');
        }}
      />
    </div>
  );
};
```

위와 같은 경우 MyComponent는 react.memo에 의해서  memoization 되어 있다. 하지만 전달해주는 props중에 ff는 인수 'hello2'를 전달해야 하는 console.log 메서드에 의해서 무명 화살표 함수를 매번 렌더링 마다 다시 생성하여 전달해준다. 같은 형태의 함수여도 함수 리터럴 방식으로 생성되므로 참조값이 달라서 react.memo는 이 전달되는 ff props를 다른것으로 판단하여 사용자의 의도와는 다르게 MyComponent를 다시 호출하게 된다. 따라서 이러한 방법을 막기위해서는 두가지 방법을 사용할 수 있다.

1. useCallback으로 () => {console.log('hello2')}를 감싸서 동일한 참조값을 유지시켜 준다.

   ```javascript
   const App = () => {
     const [number, setNumber] = useState(0);
     const [number1, setNumber1] = useState(0);
   
     const onClickHandler = () => {
       setNumber(s => s + 1);
     };
   
     const sameff = useCallback(() => {
       console.log('hello2');
     }, []);
   
     return (
       <div>
         <button onClick={onClickHandler}>click me</button>
         <span>{number}</span>
         <MyComponent number1={number1} ff={sameff} />
       </div>
     );
   };
   ```

   sameff를 usecallback에 의해서 메모이제이션된 함수 참조값을 저장하도록 하고, ff에 sameff에 저장된 참조값을 props로 전달한다면 동일 참조성에 의해서 react.memo는 같은 props로 인식하게 된다.

2. areEqual(react.memo의 두번째 매개변수)를 이용하여 조건을 커스터마이징 해준다.

   ```javascript
   function areEqual(prevProps, nextProps) {
     return prevProps.number1 === nextProps.number1;
   }
   ```

   이러한 조건을 준다면, props중에서  ff의 참조값의 변화와는 상관없이 number1 props의 값에 의해서만 재 렌더링 여부를 결정할 수 있다.