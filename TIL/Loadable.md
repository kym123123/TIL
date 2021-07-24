## Loadable

: lodable 객체는 atom이나 selector의 가장 최신 상태에 대한 정보를 담고있다. loadable은 크게 아래 두가지의 인터페이스를 갖는다. 비동기 로직을 selector에서 사용하는 경우 suspense를 이용해서 fallback컴포넌트를 지정해 주어야 하는데, 이 방법 대신에 Lodable을 사용할 수 있다.

1. State: atom이나 selector의 최신상태, => 가능 한 값, 'hasValue', 'hasError', 'loading'
2. Contents: state의 값이 'hasValue'라면, contents는 실제 atom이나 selector의 값, 'hasError'이라면, Error객체, 'loading'이라면 toPromise()메서드를 이용하여 Promise를 얻을 수 있다.



* useRecoilValueLoadable(state): 비동기 selector의 값을 읽기위해 사용하는 hook, useRecoilValue와는 다르게 비동기 selector에서 읽어올때 react suspense와 함께 작동하기 위해 Error, Promise를 던지지 않는다. => 값에 대한 Loadable 객체를 리턴한다.
* useRecoilStateLoadable(state): 비동기 selector의 값을 읽기 위해서 사용된다. 위의 useRecoilValueLoadable과 마찬가지로 React suspense와 함께 작동하기 위해서 error혹은 Promise를 반환하지 않고 Loadable 객체를 setter 콜백과 함께 리턴한다.

```javascript
// useRecoilStateLoadable example

function UserInfo({userID}) {
  const [userNameLoadable, setUserName] = useRecoilStateLoadable(userNameQuery(userID));
  // suspense 없이 사용 가능.
  switch (userNameLoadable.state) {
    case 'hasValue':
      return <div>{userNameLoadable.contents}</div>;
    case 'loading':
      return <div>Loading...</div>;
    case 'hasError':
      throw userNameLoadable.contents;
  }
}
```

```javascript
// useRecoilValueLoadable example

function UserInfo({userID}) {
  // Loadable 객체만을 받아서 state, contents 인터페이스를 이용한 ui 렌더링 가능.
  const userNameLoadable = useRecoilValueLoadable(userNameQuery(userID));
  switch (userNameLoadable.state) {
    case 'hasValue':
      return <div>{userNameLoadable.contents}</div>;
    case 'loading':
      return <div>Loading...</div>;
    case 'hasError':
      throw userNameLoadable.contents;
  }
}
```

