# todos 과제 코드

```javascript
// states
let todos = [];
let navState = 'all';

// DOMs
const $todos = document.querySelector('.todos');
const $inputTodo = document.querySelector('.input-todo');
const $nav = document.querySelector('.nav');
const $completeAll = document.querySelector('.checkbox');
const $clearBtn = document.querySelector('.btn');
const $span = document.querySelector('.completed-todos');
const $strong = document.querySelector('.active-todos');

// functions
const fetchTodos = () => [
  { id: 1, content: 'HTML', completed: false },
  { id: 2, content: 'CSS', completed: false },
  { id: 3, content: 'Javascript', completed: false }
];

const render = () => {
  let todosBynavState = [...todos];
  if (navState === 'active') {
    todosBynavState = todos.filter(todo => todo.completed === false);
  } else if (navState === 'completed') {
    todosBynavState = todos.filter(todo => todo.completed === true);
  }
  $todos.innerHTML = todosBynavState.map(todo => `<li id="${todo.id}" class="todo-item">
      <input id="ck-${todo.id}" class="checkbox" type="checkbox" ${todo.completed ? 'checked' : ''}>
      <label for="ck-${todo.id}">${todo.content}</label>
      <i class="remove-todo far fa-times-circle"></i>
      </li>`).join();
};

const generateNewId = () => {
  let newArr = [];
  newArr = todos.map(todo => [todo.id, ...newArr]);
  return newArr.length ? Math.max(...newArr) + 1 : 0;
};

const addNewTodo = content => {
  todos = [{ id: generateNewId(), content, completed: false }, ...todos];
};

const updateCompleted = target => {
  todos.forEach(todo => {
    if (todo.id === +target.parentNode.id) todo.completed = !todo.completed;
  });
};

const countCompletedItems = () => todos.filter(todo => todo.completed === true).length;

const displayOnClearBtn = () => {
  $span.textContent = countCompletedItems();
  $strong.textContent = todos.length - (+$span.textContent);
};
// Event
document.addEventListener('DOMContentLoaded', () => {
  todos = fetchTodos();
  displayOnClearBtn();
  render();
});

$inputTodo.onkeyup = e => {
  if (e.key !== 'Enter' || !$inputTodo.value) return;

  addNewTodo($inputTodo.value);
  $inputTodo.value = '';
  $inputTodo.focus();
  displayOnClearBtn();
  render();
};

$nav.onclick = e => {
  if (!e.target.matches('.nav > li')) return;

  navState = e.target.id;
  [...$nav.children].forEach(li => {
    li.classList.toggle('active', li.id === navState);
  });
  render();
};

$todos.oninput = e => {
  updateCompleted(e.target);
  render();
  displayOnClearBtn();
};

$todos.onclick = e => {
  if (!e.target.matches('.remove-todo')) return;

  todos = todos.filter(todo => +e.target.parentNode.id !== todo.id);
  $todos.removeChild(e.target.parentNode);
  displayOnClearBtn();
};

$completeAll.onchange = () => {
  const checkCompleted = todos.every(todo => todo.completed === true);
  if (checkCompleted) todos.forEach(todo => todo.completed = !todo.completed);
  else if (!checkCompleted) todos.forEach(todo => todo.completed = true);
  displayOnClearBtn();
  render();
};

$clearBtn.onclick = () => {
  todos = todos.filter(todo => todo.completed !== true);
  displayOnClearBtn();
  render();
};
```

