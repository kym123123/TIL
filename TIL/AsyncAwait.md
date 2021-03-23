# Async / Await



## 프로미스 체인의 불편함

프로미스 함수를 이용하여 숙소를 예약하는 함수를 만들고, 예약이 성공했는지 실패했는지 내 예약 상태를 확인할수 있는 함수를 아래와 같이 만들어보자

```javascript
// 성인인지 아닌지 여부와, 원하는 방 번호를 전달하는 함수
const bookARoom = (isAdult, roomNumber) => new Promise((resolve, reject) => {
  console.log('hey may I help you?');
  if (isAdult) { // 성인이라면 원하는 방을 예약할 수 있고,
    resolve({
      roomNumber,
      message: 'room booked successfully!'
    })
  } else { // 성인이 아니라면 예약이 거부된다.
    reject('Unable to reserve a room due to your age!');
  }
});

// 내 예약 정보 확인하는 함수. bookARoom 함수로 부터 받은 결과인 state를 매개변수로 받는다.
const checkMyBookingState = state => new Promise((resolve, reject) => {
  console.log('checking my info...');
  resolve(state);
});

```

위의 함수를 이용하여 실제로 내 예약정보를 확인하는 프로미스 체인은 아래와 같다.

```javascript
// 성인, 303호 예약
bookARoom(true, 303).then(state => { 
  console.log(state); // {roomNumber: 303, message: 'room booked successfully!'}
  return checkMyBookingState(state); 
  // 내 방 정보 상태를 확인하기 위하여 bookARoom의 프로미스 결과값 state를 받는 checkMyBookingstate 리턴
}).then(result => console.log(result)) // {roomNumber: 303, message: 'room booked successfully!'}
  .catch(e => console.log(e)); 

// 성인 아님, 303호 예약
bookARoom(false, 303).then(state => {
  console.log(state);
  return checkMyBookingState(state);
}).then(result => console.log(result))
	.catch(error => console.log(error)); // Unable to book a room due to your age!
// 성인이 아니므로 거부되며 error발생
```

겨우 두가지 비동기 함수를 사용하는 것인데 가독성이 딱히 좋지 않고 불편하다. return으로 다른 프로미스 객체를 전달해 주어야 하며 then, catch같은 후속처리 메서드를 이용하여 체인을 형성해서 실행해야 한다. 자바스크립트는 이러한 단점을 보완해 줄 수 있도록 async / await 메서드를 제공한다.



## Usage of Async / Await 

위에서 작성한 프로미스 체인 코드를 프로미스 대신 Async / Await를 이용하여 다시 작성하면 아래와 같다.

```javascript
const book = async () => {
  try {
  const state = await bookARoom(true, 303);
  console.log(state); // {roomNumber: 303, message: 'room booked successfully!'}
  const result = await checkMyBookingState(state); // bookARoom의 결과 state를 전달후 함수 실행
   console.log(result); // checking my Info...
    // {roomNumber: 303, message: 'room booked successfully!'}
  } catch (error) {
    console.log(error)
  }
};

book(); // 비동기 함수 book을 실행
```

위에서 미리 정의해 놓은 프로미스를 반환하는 비동기 함수 bookARoom과

 checkMyBokkingState 함수를 async 키워드를 가진 book 함수 내에서 실행시켜 주기만 하면 된다. 주의할 점은 프로미스를 반환하는 비동기 함수 앞에는 await 키워드를 반드시 붙여야 한다. 만약 붙이지 않는다면 bookARoom 함수의 실행결과값인  state에는 반환된 프로미스의 resolve된 값인 `{roomNumber: 303, message: 'room booked successfully!'}` 가 아닌 프로미스 객체 자체가 반환된다. 이 값을 다음 함수인 checkMyBookingState에 인자로 전달하면 우리가 원하는 예약 상태를 확인하지 못한다.

await키워드를 사용하여 우리가 원하는 값인 resolve된 값을 받을수 있도록 하자.

또한 async 함수안에서는 예외 처리를 위하여 후소거리 메서드 .catch()가 아닌 동기처리에서 사용하는 try..catch문을 사용하여 예외처리가 가능하다.

async함수는 비동기를 처리하고, 비동기는 항상 서버의 상태가 고르지 않을 수 있으며 잘못된 request 값을 전달하여 에러가 발생하기 쉬운 상황이므로 예외처리를 해주어 uncaught error발생으로 인한 앱 작동 중지 상태를 방지해 줘야 한다.