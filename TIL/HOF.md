## 1. html 생성

아래 배열을 사용하여 html을 생성하는 함수를 작성하라.

```javascript
// 풀이1) map 메서드 써서
const todos = [
  { id: 3, content: 'HTML', completed: false },
  { id: 2, content: 'CSS', completed: true },
  { id: 1, content: 'Javascript', completed: false }
];

function render() {
  let html = '';
  // todo의 내용을 문자열화한 것들을 배열에 담는다.
  return todos.map(todo => {
    let isChecked = (todo.completed === true ? 'checked' : '');
    return `<li id="${todo.id}">
    <label><input type="checkbox" ${isChecked}>${todo.content}</label>
    </li>`;
  }).join();
}
console.log(render());


// 풀이 2) forEach 메서드 써서
const todos = [
  { id: 3, content: 'HTML', completed: false },
  { id: 2, content: 'CSS', completed: true },
  { id: 1, content: 'Javascript', completed: false }
];

function render() {
  let html = '';

  todos.forEach(todo => {
    let isChecked = (todo.completed === true) ? 'checked' : '';
    html = html + `<li id="${todo.id}">
    <label><input type="checkbox" ${isChecked}>${todo.content}</label>
  </li>`;
  });

  return html;
}

console.log(render());
```



## 2. 특정 프로퍼티 값 추출

```javascript
// 풀이1) map사용해서
const todos = [
  { id: 3, content: 'HTML', completed: false },
  { id: 2, content: 'CSS', completed: true },
  { id: 1, content: 'Javascript', completed: false }
];

function getValues(key) {
  return todos.map(todo => todo[key]);
}

console.log(getValues('id')); // [3, 2, 1]
console.log(getValues('content')); // ['HTML', 'CSS', 'Javascript']
console.log(getValues('completed')); // [false, true, false]


//풀이2) forEach 사용해서
const todos = [
  { id: 3, content: 'HTML', completed: false },
  { id: 2, content: 'CSS', completed: true },
  { id: 1, content: 'Javascript', completed: false }
];

function getValues(key) {
  const result = [];
  todos.forEach(todo => result.push(todo[key]));
  return result;
}

console.log(getValues('id')); // [3, 2, 1]
console.log(getValues('content')); // ['HTML', 'CSS', 'Javascript']
console.log(getValues('completed')); // [false, true, false]
```



## 3. 프로퍼티 정렬

```javascript
const todos = [
  { id: 3, content: 'HTML', completed: false },
  { id: 2, content: 'CSS', completed: true },
  { id: 1, content: 'Javascript', completed: false }
];

function sortBy(key) {
  let copiedTodos = [...todos];
  return copiedTodos.sort((todo1, todo2) => {
    return todo1[key] > todo2[key] ? 1 : (todo1[key] < todo2[key] ? -1 : 0)
  });
}

console.log(sortBy('id'));
console.log(sortBy('content'));
console.log(sortBy('completed'));
```



## 4. 새로운 요소 추가

```javascript
let todos = [
  { id: 3, content: 'HTML', completed: false },
  { id: 2, content: 'CSS', completed: true },
  { id: 1, content: 'Javascript', completed: false }
];

function addTodo(newTodo) {
  todos = [newTodo, ...todos];
}

addTodo({ id: 4, content: 'Test', completed: false });
console.log(todos);
```



## 5. 특정 요소 삭제

```javascript
//풀이1) forEach 사용해서
let todos = [
  { id: 3, content: 'HTML', completed: false },
  { id: 2, content: 'CSS', completed: true },
  { id: 1, content: 'Javascript', completed: false }
];

function removeTodo(id) {
  const newArr = [];
  todos.forEach(todo => {
    if(todo.id !== id) newArr.push(todo);
  });
  todos = newArr;
}
removeTodo(2);
console.log(todos);

//풀이2) filter 사용해서
let todos = [
  { id: 3, content: 'HTML', completed: false },
  { id: 2, content: 'CSS', completed: true },
  { id: 1, content: 'Javascript', completed: false }
];

function removeTodo(id) {
  todos = todos.filter(todo => 
    {return todo.id !== id;}
  );
}

removeTodo(2);

console.log(todos);
```

## 8. completed 프로퍼티 값이 true인 요소의 갯수 구하기

```javascript
// 풀이1) filter사용해서
let todos = [
  { id: 3, content: 'HTML', completed: false },
  { id: 2, content: 'CSS', completed: true },
  { id: 1, content: 'Javascript', completed: false }
];

function countCompletedTodos() {
  return todos.filter(todo => todo.completed === true).length;
}

console.log(countCompletedTodos()); // 1

//풀이2) forEach 사용해서
let todos = [
  { id: 3, content: 'HTML', completed: false },
  { id: 2, content: 'CSS', completed: true },
  { id: 1, content: 'Javascript', completed: false }
];

function countCompletedTodos() {
  let count = 0;
  todos.forEach(todo => {
    if(todo.completed === true) count++;
  });
  return count;
}
console.log(countCompletedTodos()); // 1
```

## 9. id 프로퍼티의 값 중에서 최대값 구하기

```javascript
//풀이1) forEach 사용x , map과 Math.max사용
let todos = [
  { id: 3, content: 'HTML', completed: false },
  { id: 2, content: 'CSS', completed: true },
  { id: 1, content: 'Javascript', completed: false }
];

function getMaxId() {
  //todos중에서 id값만 받아서 새로 배열을 만든다.
  //그 배열의 최대값을 반환한다.
  return Math.max(...todos.map(todo => todo.id));
}

console.log(getMaxId()); // 3

//풀이2) reduce 사용
let todos = [
  { id: 3, content: 'HTML', completed: false },
  { id: 2, content: 'CSS', completed: true },
  { id: 1, content: 'Javascript', completed: false }
];

function getMaxId() {
  return todos.reduce((acc, cur) => {
    return (acc > cur.id) ? acc : cur.id;
  }, 0);
}

console.log(getMaxId()); // 3
```

