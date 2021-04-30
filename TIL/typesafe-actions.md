# typesafe-actions 

* action: 간단한 액션 객체 생성 api, 액션 helper 사용 불가

  ```typescript
  action(type, payload?, meta?, error?)
  
  const increment = () => action('INCREMENT');
  // { type: 'INCREMENT'; }
  
  const createUser = (id: number, name: string) =>
    action('CREATE_USER', { id, name });
  // { type: 'CREATE_USER'; payload: { id: number; name: string }; }
  
  const getUsers = (params?: string) =>
    action('GET_USERS', undefined, params);
  // { type: 'GET_USERS'; meta: string | undefined; }
  ```

  

* createAction: 액션 객체 생성 API

  ```typescript
  createAction(type)
  createAction(type, actionCallback => {
    return (namedArg1, namedArg2, ...namedArgN) => actionCallback(payload?, meta?)
  })
  
  import { createAction } from 'typesafe-actions';
  
  // payload가 없이 type만 인자로 받는 액션 생성자
  const increment = createAction('INCREMENT');
  
  dispatch(increment()); // { type: 'INCREMENT' }
  
  // type + payload를 받는 액션 생성자
  const add = createAction('ADD', action => {
    return (amount: number) => action(amount);
  });
  dispatch(add(10)); // { type: 'ADD', payload: number }
    
  const getTodos = createAction('GET_TODOS', action => {
    return (params: params) => action(undefined, params);
  })
  dispatch(getTodos('some_meta')); // { type: 'GET_TODOS', meta: params }
  ```

  https://github.com/piotrwitek/typesafe-actions/blob/master/src/create-action.spec.ts 

  간단한 사용예시

  ```typescript
  {
    const withTypeOnly = createAction('CREATE_ACTION')();
    withTypeOnly(); // => { type: 'CREATE_ACTION' }
  }
  
  {
    const withPayload = createAction('CREATE_ACTION')<string | null | number>();
    withPayload('foo'); // => { type: 'CREATE_ACTION', payload: 'foo' }
    withPayload(null); // => { type: 'CREATE_ACTION', payload: null }
    withPayload(3); // => { type: 'CREATE_ACTION', payload: 3 }
  }
  
  {
    const withPayload = createAction('CREATE_ACTION')<any>();
    withPayload(10); // => { type: 'CREATE_ACTION', payload: 10 }
  }
  
  // createAction API를 사용하면, 반환하는 액션 객체가 커스터 마이징이 필요한 아래와 같은 경우에 유용하게 사용도 가능하다.
  export const addTodo = createAction(ADD_TODO, action => (text: string) =>
    action({
      id: nextId++,
      text
    })
  );
  ```

* createAsyncAction: 비동기를 좀더 간단하게 다룰수 있도록 해주는 액션 생성자 API

  ```typescript
  createAsyncAction(
    requestType, successType, failureType, cancelType?
  )<TRequestPayload, TSuccessPayload, TFailurePayload, TCancelPayload?>()
    // 제네릭에는 각 액션타입에 맞는 payload의 타입을 넣어준다. 4번째 제네릭은 선택사항
    type AsyncActionCreator<
    [TRequestType, TRequestPayload],
    [TSuccessType, TSuccessPayload],
    [TFailureType, TFailurePayload],
    [TCancelType, TCancelPayload]?
  > = {
    request: StandardActionCreator<TRequestType, TRequestPayload>,
    success: StandardActionCreator<TSuccessType, TSuccessPayload>,
    failure: StandardActionCreator<TFailureType, TFailurePayload>,
    cancel?: StandardActionCreator<TCancelType, TCancelPayload>,
  }
  ```

  ```typescript
  
  import { createAsyncAction, AsyncActionCreator } from 'typesafe-actions';
  
  const fetchUsersAsync = createAsyncAction(
    'FETCH_USERS_REQUEST',
    'FETCH_USERS_SUCCESS',
    'FETCH_USERS_FAILURE'
  )<string, User[], Error>();
  
  dispatch(fetchUsersAsync.request(params));
  
  dispatch(fetchUsersAsync.success(response));
  
  dispatch(fetchUsersAsync.failure(err));
  
  const fn = (
    a: AsyncActionCreator<
      ['FETCH_USERS_REQUEST', string], // [type, payload의 type]
      ['FETCH_USERS_SUCCESS', User[]],
      ['FETCH_USERS_FAILURE', Error]
    >
  ) => a;
  fn(fetchUsersAsync);
  
  // There is 4th optional argument to declare cancel action
  const fetchUsersAsync = createAsyncAction(
    'FETCH_USERS_REQUEST',
    'FETCH_USERS_SUCCESS',
    'FETCH_USERS_FAILURE'
    'FETCH_USERS_CANCEL'
  )<string, User[], Error, string>();
  
  dispatch(fetchUsersAsync.cancel('reason'));
  
  const fn = (
    a: AsyncActionCreator<
      ['FETCH_USERS_REQUEST', string],
      ['FETCH_USERS_SUCCESS', User[]],
      ['FETCH_USERS_FAILURE', Error],
      ['FETCH_USERS_CANCEL', string]
    >
  ) => a;
  fn(fetchUsersAsync);
  ```

  https://github.com/piotrwitek/typesafe-actions/blob/master/src/create-async-action.spec.ts

  createAsyncAction 사용예제 모음

  ```typescript
  
  import { AsyncActionCreatorBuilder } from './type-helpers';
  import { createAsyncAction } from './create-async-action';
  
  type User = { firstName: string; lastName: string };
  
  {
    const fetchUsersAsync = createAsyncAction(
      'FETCH_USERS_REQUEST',
      'FETCH_USERS_SUCCESS',
      'FETCH_USERS_FAILURE'
    )<undefined, User[], Error>();
  
    fetchUsersAsync.request(); 
    /* => {
      type: 'FETCH_USERS_REQUEST'
    } */
  
    fetchUsersAsync.success([
      { firstName: 'Piotr', lastName: 'Witek' },
    ]); 
    /* => {
      type: 'FETCH_USERS_SUCCESS', payload: [{ firstName: 'Piotr', lastName: 'Witek' }]
    } */
  
    fetchUsersAsync.failure(
      Error('reason')
    ); 
    /* => {
      type: 'FETCH_USERS_FAILURE', payload: Error('reason')
    } */
  
    fetchUsersAsync.cancel; // 취소
  ```

  * ActionType`<typeof something>`

    ```typescript
    const actions = {
      addTodo,
      toggleTodo,
      removeTodo
    };
    
    type TodosAction = ActionType<typeof actions>;
    // actions 객체의 property들의 반환 타입을 union 해서 반환
    ```

* createReducer : 제네릭으로  state와 action을 받고, 인자로 initialState를 받는다. 사용방법은 여러가지가 있다.

  * 1번 사용법: 

  ```typescript
  const todos = createReducer<TodosState, TodosAction>(initialState, {
    [ADD_TODO]: (state, action) =>
      state.concat({
        ...action.payload, // id, text 를 이 안에 넣기
        done: false
      }),
    // 바구조화 할당을 활용하여 payload 값의 이름을 바꿀 수 있음
    [TOGGLE_TODO]: (state, { payload: id }) =>
      state.map(todo => (todo.id === id ? { ...todo, done: !todo.done } : todo)),
    [REMOVE_TODO]: (state, { payload: id }) =>
      state.filter(todo => todo.id !== id)
  });
  ```

* 2번 사용법: handleAction 메서드를 이용한 체이닝, 액션 객체의 타입은 액션 생성함수를 참조하여 유추 할 수 있기 때문에 Generics를 생략해도 무방합니다.

  ```typescript
  const counter = createReducer(initialState)
    .handleAction(increase, state => ({ count: state.count + 1 }))
    .handleAction(decrease, state => ({ count: state.count - 1 }))
    .handleAction(increaseBy, (state, action) => ({
      count: state.count + action.payload
    }));
  ```

  