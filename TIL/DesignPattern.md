# Design Pattern

## 1. null object pattern

반환되는 값이 null이 될 가능성이 있는 경우, 객체 내부의 프로퍼티에 접근하는 경우 에러를 막기위해 null인지 아닌지 체크하고 case분류를 한다. 

객체의 프로퍼티에 접근하는 코드를 작성할 때 마다 null 체킹을 해주는 것은 번거롭고, 실수로 하지않아서 에러를 발생시킬 확률도 높다. 이때 null Object pattern을 사용한다.

* null 이 반환될 가능성이 있어서 null체킹을 해주는 코드

```javascript
class User {
  constructor(id, name) {
    this.id = id;
    this.name = name;
  }

  hasAccess() {
    return this.name === 'Bob';
  }
}

const users = [new User(1, 'Bob'), new User(2, 'John')];

function getUser(id) {
  return users.find(user => user.id === id);
}

function printUser(id) {
  const user = getUser(id);

  let name = 'Guest';
  if (user !== null && user.name !== null) name = user.name;
  console.log('Hello' + name);

  if (user !== null && user.hasAccess !== null && user.hasAccess()) {
    console.log('You have access');
  } else {
    console.log('You are not allowed here');
  }
}
```

* null object pattern으로 수정한 코드

```javascript
class User {
  constructor(id, name) {
    this.id = id;
    this.name = name;
  }

  hasAccess() {
    return this.name === 'Bob';
  }
}

// null을 접두사로 붙혀서, user가 null인 경우를 나타냄.
class NullUser {
  constructor() {
    // 기본 값들을 사용,
    this.id = -1;
    this.name = 'Guest';
  }

  hasAccess() {
    return false;
  }
}

const users = [new User(1, 'Bob'), new User(2, 'John')];

function getUser(id) {
  const user = users.find(user => user.id === id);
  if (user === null) {
    return new NullUser();
  }
  return user;
}

function printUser(id) {
  const user = getUser(id);

  console.log('Hello' + user.name);

  if (user.hasAccess()) {
    console.log('You have access');
  } else {
    console.log('You are not allowed here');
  }
}
```

class 한가지를 더 추가하므로써, 반복되는 복잡한 null 체크 코드를 사용할 필요가 없게 된다.



## 2. builder pattern

```javascript
class Address {
  constructor(zip, street) {
    this.zip = zip;
    this.street = street;
  }
}

class User {
  constructor(name, age, phone, address) {
    this.name = name;
    this.age = age;
    this.phone = phone;
    this.address = address;
  }
}

// user의 정보에 이름과 주소만 넣고 싶은 경우에는 2,3번 인자로 undefined를 전달해줘야 한다.
const user = new User('Bob', undefined, undefined, new Address(1, 'Main'));

console.log(user);
```

위처럼 원하는 정보만을 넣어주고 싶은 경우, undefined를 일일히 넣어야 하여, 코드의 가독성도 떨어지고 실수할 위험도 증가.

* builder 패턴을 적용한 코드1 (builder 클래스 이용)

```javascript
class Address {
  constructor(zip, street) {
    this.zip = zip;
    this.street = street;
  }
}

class User {
  constructor(name) {
    this.name = name;
  }
}

class UserBuilder {
  constructor(name) {
    this.user = new User(name);
  }
  setAge(age) {
    this.user.age = age;
    return this;
  }
  setPhone(phone) {
    this.user.phone = phone;
    return this;
  }
  setAddress(address) {
    this.user.address = address;
    return this;
  }
  build() {
    return this.user;
  }
}

let user = new UserBuilder('Bob').setAge(10).build();
console.log(user); // {name: 'Bob', age: 10}

let user2 = new UserBuilder('John').setPhone(11111111).build();
console.log(user2); // {name: 'John', phone: 11111111}
```

이 방식도 builder를 작성하는 것이 간단하지 않다.

* 빈객체를 이용한 방식

```javascript
class Address {
  constructor(zip, street) {
    this.zip = zip;
    this.street = street;
  }
}

// 빈 객체를 기본값으로하여, 객체를 전달 하도록 한다.
class User {
  constructor(name, { age, phone, address } = {}) {
    this.name = name;
    this.age = age;
    this.phone = phone;
    this.address = address;
  }
}

let user = new User('Bob', { age: 10, address: new Address('1', 'main') });

console.log(user);
// { name: 'Bob',
//  age: 10,
//  phone: undefined,
//  address: Address { zip: '1', street: 'main }
```

이 방식을 이용하면 두번째 인자로 객체를 전달하고 객체가 전달되지 않을경우 빈 객체를 기본값으로 전달한다.

## 3. singleTon pattern

어플리케이션에서 하나의 instance를 공유하는 방식이다. 전역변수를 만들어야 한다는 문제점이 있다. 이 하나의  instance가 전역에서 공유되기 때문이다. 따라서 테스트하기 힘들어지고, 여러 부분에 있어서 공통적으로 instance에 의존하게 되기 때문에 디버깅 하기가 어려워서 사용하지 말라는 의견도 있으나, 잘 사용하면 singleton을 사용하지 않을 경우보다 유용한 부분이 있다. 

```javascript
// fancyLogger.js
export default class FancyLogger {
  constructor() {
    this.logs = [];
  }

  log(message) {
    this.logs.push(message);
    console.log(`Fancy: ${message}`);
  }

  printLogCount() {
    console.log(`${this.logs.length} Logs`);
  }
}
```
```javascript
// first.js
import FancyLogger from './fancyLogger.js';

const logger = new FancyLogger();

export default function logFirstImplementation() {
  logger.printLogCount();
  logger.log('first file');
  logger.printLogCount();
}
```
```javascript
// second.js
import FancyLogger from './fancyLogger.js';

const logger = new FancyLogger();

export default function logSecondImplementation() {
  logger.printLogCount();
  logger.log('second file');
  logger.printLogCount();
}
```

```javascript
// index.js
import logFirstImplementation from './first.js';
import logSecondImplementation from './second.js';

logFirstImplementation(); 
// 0 Logs
// Fancy: first file
// 1 Logs
logSecondImplementation();
// 0 Logs
// Fancy: second file
// 1 Logs
```

app전체의 log를 기록하기 위해서 만든 fancyLogger에서 생성된 인스턴스 였지만, first, second 모듈마다 새로운 인스턴스를 생성해서 로그의 개수가 공유가 안되고 있는 모습이다. 이를 수정하기 위해서 아래와 같이 singleTon 패턴으로 인스턴스 하나를 공유하도록 한다.



```javascript
// fancyLogger.js

// 클래스를 export하지 않는다.
class FancyLogger {
  constructor() {
    // static 변수 instance를 이용하여 이미 인스턴스 생성이 된경우, 새로 생성x
    if (FancyLogger.instance === null) {
      this.logs = [];
      FancyLogger.instance = this;
    }
    return FancyLogger.instance;
  }

  log(message) {
    this.logs.push(message);
    console.log(`Fancy: ${message}`);
  }

  printLogCount() {
    console.log(`${this.logs.length} Logs`);
  }
}

const logger = new FancyLogger();
Object.freeze(logger); // 새로운 프로퍼티나 메서드를 가질수 없게 freeze
export default logger;
// freeze한 logger 인스턴스를 export, 이제부터 이 인스턴스를 공유한다.
```

이렇게 수정한 코드를 실행 시키면 아래와 같다.

```javascript
// index.js
import logFirstImplementation from './first.js';
import logSecondImplementation from './second.js';

logFirstImplementation(); 
// 0 Logs
// Fancy: first file
// 1 Logs
logSecondImplementation();
// 1 Logs
// Fancy: second file
// 2 Logs
```

instance가 공유 되기 때문에 log의 개수가 어플리케이션 전역에서 카운트 되고 원하는대로 동작하게 된다.



## 4. Facade Pattern

: 이론적으로는 하나의 함수는 하나의 일만 하는 것이 좋지만, 이렇게 코드를 작성할 경우, 함수의 갯수가 너무나도 많아 질 수 있다. 그리고 한가지 일을 하기 위한 과정이 꽤 복잡한 함수들이라면, 코드의 가독성도 좋지 않게된다. 여러 함수들을 단순화하고 통합한 외관을 만들어 준다면, 하나의 일을 하기위해 함수를 작성하는 방식을 간략화 할 수 있다. 이러한 방식이 퍼사드 패턴인데, 퍼사드 패턴은 미래에 코드를 수정하기 쉽도록 하는방식이다. 브라우저의 fetch api가 좋은 예시. 

```javascript
function getUsers() {
  return fetch('https://jsonplaceholder.typicode.com/users', {
    method: 'GET',
    headers: { 'Content-Type': 'application/json' },
  }).then(res => res.json());
}

function getUserPosts(userId) {
  return fetch(`https://jsonplaceholder.typicode.com/posts?userId=${userId}`, {
    method: 'GET',
    headers: { 'Content-Type': 'application/json' },
  }).then(res => res.json());
}

getUsers().then(users => {
  users.forEach(user => {
    getUserPosts(user.id).then(posts => {
      console.log(user.name);
      console.log(posts.length);
    });
  });
});

```

Facade 패턴을 이용하여 getUsers와 getUserPosts를 간단하게 만든 코드

```javascript
function getUsers() {
  return getFetch('https://jsonplaceholder.typicode.com/users');
}

function getUserPosts(userId) {
  return getFetch(`https://jsonplaceholder.typicode.com/posts`, {
    userId: userId,
  });
}

getUsers().then(users => {
  users.forEach(user => {
    getUserPosts(user.id).then(posts => {
      console.log(user.name);
      console.log(posts.length);
    });
  });
});

//[[userId, 1]] 형태의 params를 문자열로...
function getFetch(url, params = {}) {
  const queryString = Object.entries(params)
    .map(param => {
      return `${param[0]}=${param[1]}`;
    })
    .join('&');

  return fetch(`${url}?${queryString}`, {
    method: 'GET',
    headers: { 'Content-Type': 'application/json' },
  }).then(res => res.json());
}
```

getFetch라는 하나의 외관을 만들어 나머지 함수가 간단해졌다. 이 패턴을 이용하면 나중에 fetch대신  axios를 쓰게 되어 코드를 수정해야 한다면, getFetch함수 한 부분만 교체해주면 되는 것이다. 만약 이패턴을 쓰지않고 코드를 수정한다면 훨씬 복잡해지고 여러 함수들을 일일히 수정해줘야 하는것이다.
