# Promise

: 프로미스는 자바스크립트에서 제공하는 비동기를 다루는 방식으로 두가지 상태 결과가 존재한다. fulfilled, rejected

```javascript
let promise = new Promise((resolve, reject) => {
  let a = 1 + 1;
  console.log('hello'); // (1) 프로미스 객체가 생성되면서 객체내의 정의된 내용이 실행된다.
  if (a == 2) {
      resolve('Success');
    } else {
      reject('Failed');
    }
});
```

promise는 생성할때 콜백함수를 전달 받고, 콜백함수의 첫번째 인자는 resolve(성공시 콜백), 두번째 인자는 reject(실패시 콜백)이다.

성공시에는 resolve가 호출되고, 실패시에는 reject가 호출되도록 코드를 작성한다. 위의 식은 a = 2가 정답이므로 항상 resolve('Success')가 호출된다. 위의 코드에서는 프로미스 객체가 생성되고 console에 'hello'가 출력된다. 프로미스 객체는 생성되면 내부의 내용이 실행된다. 위의 코드에서 a 는 항상 2가 되는 값이므로 언제나 resolve('Success')가 실행된다. promise 객체내에서 reject, resolve된 경우에 따라 promise객체의 내부 슬롯 [[PromiseState]]는 'pending'상태에서 'settled(fulfilled 혹은 rejected)'상태가 된다. 이 상태가 되면 promise객체 뒤에 then, catch, finally 메서드를 사용하여 reject, resolve함수가 반환한 값을 이용하여 후속 작업을 해줄 수 있다.

```javascript
promise.then((message) => {
  console.log(message) // 'Success'
}).catch((message) => {
  console.log(message) // 'Failed'
}).finally(() => {
 	console.log('the end') // 'the end'
});
```

promise객체는 settled(fulfilled/rejected) 상태가 되면 resolve 또는 reject함수가 인수로 전달받은 값을 가진 promise객체를 반환하며, 이 객체를 then, catch, finally 메서드로 사용할 수 있다. 위의 코드에서는 resolve('Success')를 건네주었기 때문에, then 메서드가 실행되며, 문자열 Success가 콘솔창에 출력된다. 만약 reject('Failed')가 되었다면, 문자열 Failed가 출력 될 것이다. then과 catch 모두 첫번째 인자로 프로미스 객체 내에서 resolve또는 reject가 전달받은 값을 인자로 넣어주게 된다. finally 객체는 resolve되거나 reject되거나 상관없이 항상 실행되는 메서드 이다. 따라서 위의 상황에서는 console에 출력되는 것은 'Success' 그리고 'the end'가 된다. 



## 프로미스 객체는 생성되면 실행된다.

```javascript
let promise = new Promise((resolve, reject) => {
  let a = 1 + 1;
  console.log('hello'); // (1) 프로미스 객체가 생성되면서 객체내의 정의된 내용이 실행된다.
  if (a == 2) {
      resolve('Success');
    } else {
      reject('Failed');
    }
});
```



이 경우에서 promise객체가 생성되면서 (Promise 생성자 함수에 의해) 객체 내부의 console.log('hello')가 바로 실행되며 출력된다. 따라서 원하는 시기에 프로미스 객체를 생성하여 실행되도록 하는 것이 중요하다. 이를 위해서 함수를 만들고 이 함수가 프로미스 객체를 반환하도록 하면 된다. 그렇게 한다면 원하는 시기에 프로미스 객체를 반환하도록 해당 함수를 호출 할 수 있다.

```javascript
function getPromise() {
  return new Promise((resolve, reject) => {
    console.log('hello');
    if (userLeft) {
      reject({
        name: 'user1',
        message:'where are you ? :('
      });
    } else if (userWatchingMovie) {
      reject({
        name: 'user1',
        message: "stop watching it!"
      });
    } else {
      resolve('good job!');
    }
  });
}

// 함수만 선언해놓은 상태이므로 getPromise함수가 생성되었다고 해서 Promise객체가 생성되지 않으므로 console에 'hello가 출력되지 않는다.'
```



위의 함수를 호출한다면 그제서야 Promise객체가 생성 후 반환되고, 내부의 코드들이 실행된다. 그리고 이제 프로미스 후속처리 메서드를 사용하여 반환된 프로미스 객체를 이용한 작업을 수행할 수 있게 된다.

```javascript
const userLeft = false;
const userWatcingMovie = false;

const result = getPromise(); // getPromise함수가 promise객체를 반환하여 내부 코드가 실행되고 result는 promise객체를 할당받는다.

result.then((message) => {
  console.log(message) // 'good job!'
}).catch((object) => {
  const {name, message} = object;
  console.log(`${name}! ${message}`); 
}).finally(() => {
  console.log('bye!')
});
```



## 프로미스 메서드를 이용한 병렬 비동기 작업 수행

프로미스의 정적 메서드를 이용하여 비동기 작업을 수행하는 몇개의 프로미스 객체를 동시에 실행 시킬 수 있다.

1. Promise.all 메서드

   ```javascript
   const promise1 = new Promise(resolve => resolve('promise1 done!'));
   const promise2 = new Promise(resolve => resolve('promise2 done!'));
   const promise3 = new Promise(resolve => resolve('promise3 done!'));
   
   Promise.all([promise1, promise2, promise3]).then((res) => console.log(res)); 
   // ['promise1 done!', 'promise2 done!', 'promise3 done!']
   ```

프로미스 객체 1, 2, 3을 Promise.all에 배열형태로 전달해준다. 그리고 then을 이용하여 res값을 확인하면  res는 각 프로미스객체가 resolve한 문자열 값을 배열에 담아서 반환해준다. 3가지 프로미스 객체를 병렬적으로 실행하여 결과값을 모두 반환해 주는 정적메서드이다. 이와는 다르게 비슷한 문법을 이용하지만 가장 먼저 실행 완료된 promise객체의 값만을 반환시켜주는 메서드도 있다.

2. Promise.race 메서드

   ```javascript
   const promise1 = new Promise(resolve => resolve('promise1 done!'));
   const promise2 = new Promise(resolve => resolve('promise2 done!'));
   const promise3 = new Promise(resolve => resolve('promise3 done!'));
   
   Promise.race([promise1, promise2, promise3]).then((res) => console.log(res)); // promise1 done!
   ```

이 메서드는 1,2,3 객체중에 가장 먼저 작업이 종료된 객체의 resolve된 값을 반환해준다. 따라서 promise1 done! 문자열 값이 res에 반환된다.