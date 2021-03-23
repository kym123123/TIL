# 반복문 내의 함수

```javascript
function makeArmy() {
  let shooters = [];

  let i = 0;
  while (i < 10) {
    let shooter = function() { // shooter 함수
      console.log( i ); // 몇 번째 shooter인지 출력해줘야 함
    };
    shooters.push(shooter);
    i++;
  }

  return shooters;
}

let army = makeArmy();

army[0](); // 0번째 shooter가 10을 출력함
army[5](); // 5번째 shooter 역시 10을 출력함
// 모든 shooter가 자신의 번호 대신 10을 출력하고 있음
```

위의 코드는 모두 10만을 출력하게 된다. 무슨 문제일까?

-> shooter 함수는 shooters 배열에 저장되고, 함수 makeArmy에 의해서 배열전체가 리턴되어 army배열로 저장된다. 나중에 `army[i]();`로 호출되면, 이미 함수내의 while문은 반복을 마친상태고 makeArmy의 렉시컬환경에 있는 변수i의 값은 10인 상태다. 따라서 console.log(i);를 실행한다면 함수에 의해 출력되는 값은 인덱스에 상관없이 모두 i=10을 참조하게 되어 10이 출력된다. 



## 1. for 문을 사용하여 올바르게 출력하기

```javascript
function makeArmy() {
  let shooters = [];

  for(let i = 0; i < 10; i++) {
    let shooter = function() {
      console.log(i);
    };
    shooters.push(shooter);
  }
  
let army = makeArmy();
army[0](); // 0
army[5](); // 5
```

for 문으로 루프를 생성하면, 변수i는 makeArmy 함수의 렉시컬 환경이 아닌, 현재 루프문(for)의 반복(for block)의 렉시컬 환경이 매 반복마다 생성되어 안에 저장되게 된다. 따라서 외부에서 호출되면 각 반복에 맞는 i를 참조할 수 있게 된다.



## 2. while 문을 사용하여 올바르게 출력하기

```javascript
function makeArmy() {
  let shooters = [];
  let i = 0;
  while(i < 10) {
    let j = i;
    let shooter = function() {
      console.log(j);
    };
    shooters.push(shooter);
    i++;
  }
  return shooters;
}

let army = makeArmy();
army[0](); // 0
army[5](); // 5
```

위처럼 while 문 내부에 변수 j를 새로 선언하여 매 반복마다 i의 값을 가져 오도록 한다면, for 문처럼 새로운 렉시컬 환경을 각 반복 실행마다 만들게 되어 올바른 j값을 받아서 사용할 수 있게 된다.