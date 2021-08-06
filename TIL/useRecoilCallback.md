## useRecoilCallback

useRecoilCallback은 비동기적으로 recoil의 상태를 업데이트 할 수있고, 상태값을 읽어들일수 있다. 따라서 상태를 특정 이벤트 핸들러가 트리거 되었을경우에만 읽기전용으로 읽고 싶은 경우에, 컴포넌트에 상시 구독해 두지 않고 사용할 수 있다. -> 상시 구독을 하지 않기 때문에 불필요한 리렌더링을 최소화 할 수 있다.

atom, selector value를 원하는 시점에만 비동기적으로 읽기 가능.

특히나 비용이 많이 드는 비동기 동작같은 경우 조회를 연기할 수 있다.

렌더링 이전에 데이터를 미리 가져온다.

아래의 예시에서 logParentState라는 함수를 useRecoilCallback으로 생성했다. useRecoilcallback의 첫번째 매개변수는 함수를 반환하는 함수이고, 그 함수 안에 snapshot, set, gotoSnapshot등등 여러가지 메서드 들이 존재한다.

(1) snapshot.getPromise를 사용하면 logParentState함수가 트리거 되는 시점에 구독없이 parentState상태 값을 읽어서 변수에 할당이 가능하다. 이 값을 이용하여 (3) 처럼 조건문 안에서 set을 이용하여 상태의 변화를 줄 수 있다.

(2) snapshot 객체 안에는 map이라는 메서드가 존재하는데, Array.prototype.map과는 다르게 순회를 하는 메서드가 아니다. snapshot.map 메서드는 스냅샷으로 받은 읽기전용 객체를 다른 값으로 수정하여 사용할 수 있도록 하는 메서드 이다. 이 map메서드 안의 set을 이용하여 parentState를 '1234'로 업데이트 하더라도 ((2) 에서...) 실제로 parentState값이 '1234'로 바뀌지 않는다. 읽기전용. 값을 

```javascript
import React from 'react';
import { useRecoilCallback } from 'recoil';
import { parentState } from './parent';
import { atom } from 'recoil';

export const parentState = atom({
  key: 'parentState',
  default: 'default parent',
});

const ChildCompo = () => {
  const logParentState = useRecoilCallback(({ snapshot, set, gotoSnapshot }) => async () => {
    const parent = await snapshot.getPromise(parentState); // (1)
    const newParent = await snapshot.map(({ set }) => set(parentState,  '1234')).getPromise(parentState); // (2)
    console.log(parent, '***');  // (a)
    console.log(newParent); // (b)
    console.log(parent, '***'); // (c)

    // (3)
    if (parent === '1') {
      set(parentState, '1');
      return;
    } else {
      set(parentState, '2');
    }
  });

  return (
    <>
      <div>i am a child.</div>
      <button
        onClick={() => {
          logParentState();
        }}>
        change parent Number
      </button>
    </>
  );
};

export default ChildCompo;

```

위의 함수를 클릭하여 실행되는 결과는 아래와 같다. 조건문(3)에서 parent의 값은 1이 아니므로 함수를 두번째로 트리거 시켰을 경우에는 default value => 2로 변화 된 것을 알수 있다. 또한 (2)에서 읽기전용으로 받은 parentState의 값을 snapshot.map으로 가공하여 1234로 바꾼 것을 (b)에서 로깅하였는데, (2)메서드에 의해서 set이 되었어도 실제 parentState값이 바뀐 것은 아니라는 것이 확인 가능하다.

![image](https://user-images.githubusercontent.com/51959017/128534161-49b9e7f7-d5dd-438d-9668-6fc194231957.png)



snapshot.getPromise대신 async함수가 아닌곳에서 비동기 값을 읽어 들이는 것도 가능하다.

```javascript
import React from 'react';
import { useRecoilCallback } from 'recoil';
import { childSelector, childState } from './child';
import { parentState } from './parent';
import { atom, selector } from 'recoil';

export const childState = atom({
  key: 'childState',
  default: 'child',
});

export const childSelector = selector<Promise<string>>({
  key: 'childSelector',
  get: async ({ get }) => {
    const res = await fetch('https://jsonplaceholder.typicode.com/todos/1');
    const data = await res.json();

    console.log(data);

    return data;
  },
});

const ChildCompo = () => {
  const logParentState = useRecoilCallback(({ snapshot, set, gotoSnapshot }) => async () => {
    const parent = await snapshot.getPromise(parentState);
    const newParent = await snapshot.map(({ set }) => set(parentState, '1234')).getPromise(parentState);
    const child = snapshot.getLoadable(childState); // (4)
    const asyncChild = snapshot.getLoadable(childSelector); // (5)
    
    console.log(parent, '***');
    console.log(newParent);
    console.log(parent, '***');
    console.log(child);
    console.log(asyncChild);

    if (parent === '1') {
      set(parentState, '1');
      return;
    } else {
      set(parentState, '2');
    }
  });

  return (
    <>
      <div>i am a child.</div>
      <button
        onClick={() => {
          logParentState();
        }}>
        change parent Number
      </button>
    </>
  );
};

export default ChildCompo;

```

![image](https://user-images.githubusercontent.com/51959017/128536833-598b3e1d-b914-4745-9744-e2bdbd20f7ee.png)

(4), (5) 처럼 snapshot.getLoadable을 이용하면, useRecoilValueLoadable을 이용할때 받는 lodable객체를 받을 수 있다. 비동기로 작성된 asyncChild Selector는 로그에서 보는 것 처럼 state와 content를 가진 loadable객체를 받을 수 있다. 한번 더 버튼을 클릭하면 state는 hasValue로 변한 asyncChild selector값을 받을 수 있다.

아래 코드는 공식문서에 있는 useRecoilcallback으로 컴포넌트 내에 atom이나 selector를 상시 구독하지 않고 함수가 호출되는 경우에만 값을 읽는 에제이다.

```javascript
import {atom, useRecoilCallback} from 'recoil';

const itemsInCart = atom({
  key: 'itemsInCart',
  default: 0,
});

function CartInfoDebug() {
  const logCartItems = useRecoilCallback(({snapshot}) => async () => {
    const numItemsInCart = await snapshot.getPromise(itemsInCart);
    console.log('Items in cart: ', numItemsInCart);
  });

  return (
    <div>
      <button onClick={logCartItems}>Log Cart Items</button>
    </div>
  );
```

