```javascript
// States
let todos = [];
let navState = 'all';

//DOM
const $nav = document.querySelector('.nav');
const $inputTodo = document.querySelector('.input-todo');
const $todos = document.querySelector('.todos');
const $completedTodos = document.querySelector('.completed-todos');
const $activeTodos = document.querySelector('.active-todos');
const $completeAll = document.querySelector('.complete-all');
const $clearBtn = document.querySelector('.btn');

// Functions
const render = () => {
  console.log(todos);
  let html = '';
  let _todos = [...todos];

  _todos = todos.filter(todo => (navState === 'completed' ? todo.completed : navState === 'active' ? !todo.completed : true));
  _todos.forEach(({ id, content, completed }) => {
    html += `<li id="${id}" class="todo-item">
      <input id="ck-${id}" class="checkbox" type="checkbox" ${completed ? 'checked' : ''}>
      <label for="ck-${id}">${content}</label>
      <i class="remove-todo far fa-times-circle"></i>
    </li>`;
  });
  $todos.innerHTML = html;
  $completedTodos.textContent = todos.filter(todo => todo.completed).length;
  $activeTodos.textContent = todos.filter(todo => !todo.completed).length;
};

const request = {
  get(url) {
    return fetch(url);
  },
  post(url, payload) {
    return fetch(url, {
      method: 'POST',
      headers: { 'content-Type': 'application/json' },
      body: JSON.stringify(payload)
    });
  },
  patch(url, payload) {
    return fetch(url, {
      method: 'PATCH',
      headers: { 'content-Type': 'application/json' },
      body: JSON.stringify(payload)
    });
  },
  delete(url) {
    return fetch(url, { method: 'DELETE' });
  }
};

const fetchTodos = () => {
  request.get('/todos')
    .then(res => res.json()) // 응답값중에서 json으로 작성된 데이터만 가져옴.
    .then(_todos => { todos = _todos; }) // todos배열에 가져온 값을 저장한다.
    .then(render) // 가져온값을 화면에 표시
    .catch(console.error);
};

const generateNextId = () => (todos.length ? Math.max(...todos.map(todo => todo.id)) + 1 : 1);

// EVENTS
window.onload = fetchTodos;

$inputTodo.onkeyup = e => {
  if (e.key !== 'Enter' || !$inputTodo.value) return;

  const newTodo = { id: generateNextId(), content: e.target.value, completed: false };
  $inputTodo.value = '';
  request.post('/todos', newTodo)
    .then(res => res.json()) // 응답값중에서 json으로 작성된 데이터만 가져옴.
    .then(_todos => { todos = _todos; }) // todos배열에 가져온 값을 저장한다.
    .then(render) // 가져온값을 화면에 표시
    .catch(console.error);
};

$nav.onclick = e => {
  if (!e.target.matches('.nav > li')) return;

  navState = e.target.id;
  document.querySelector('.active').classList.remove('active');
  e.target.classList.add('active');
  render();
};

$todos.onchange = e => {
  const { id } = e.target.parentNode;
  const { checked } = e.target;

  request.patch(`/todos/${id}`, { completed: checked })
    .then(res => res.json()) // 응답값중에서 json으로 작성된 데이터만 가져옴.
    .then(_todos => { todos = _todos; }) // todos배열에 가져온 값을 저장한다.
    .then(render) // 가져온값을 화면에 표시
    .catch(console.error);
};

// 삭제하는 버튼 만들기
$todos.onclick = e => {
  if (!e.target.matches('.remove-todo')) return;
  const idToRemove = e.target.parentNode.id;
  request.delete(`/todos/${idToRemove}`)
    .then(res => res.json()) // 응답값중에서 json으로 작성된 데이터만 가져옴.
    .then(_todos => { todos = _todos; }) // todos배열에 가져온 값을 저장한다.
    .then(render) // 가져온값을 화면에 표시
    .catch(console.error);
};

$completeAll.oninput = e => {
  if (!e.target.matches('.complete-all > input')) return;
  const { checked } = e.target;
  request.patch('/todos', { completed: checked })
    .then(res => res.json()) // 응답값중에서 json으로 작성된 데이터만 가져옴.
    .then(_todos => { todos = _todos; }) // todos배열에 가져온 값을 저장한다.
    .then(render) // 가져온값을 화면에 표시
    .catch(console.error);
};

$clearBtn.onclick = () => {
  request.delete('/todos/completed')
    .then(res => res.json()) // 응답값중에서 json으로 작성된 데이터만 가져옴.
    .then(_todos => { todos = _todos; }) // todos배열에 가져온 값을 저장한다.
    .then(render) // 가져온값을 화면에 표시
    .catch(console.error);
};
```

