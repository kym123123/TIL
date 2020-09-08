# 제어문 과제1

## 1. 변수 x 가 10 보다 크고 20보다 작을 때, 변수 x 를 출력하는 조건식

```javascript
var x = 21;
if( x>10 && x<20 ) {
  console.log(x);
}
```



## 2. for 문을 사용하여 0 부터 10 미만의 정수중에서 짝수만을 작은 수부터 출력

```javascript
for(var i = 0 ; i<10 ;i++){
  if(i % 2 === 0) {
    console.log(i);
  }
}
```



## 3. for 문을 사용하여 0 부터 10 미만의 정수중에서 짝수만을 작은 수부터 문자열로 출력

```javascript
var sumStr = '';
for(var i =0 ; i < 10 ; i++) {
  if(i % 2 === 0){
    sumStr = sumStr + i;
  }
}
console.log(sumStr);
```



## 4. for 문을 사용하여 0부터 10 미만의 정수 중에서 홀수만을 큰 수부터 출력

```javascript
for(var i = 9; i >= 0; i--) {
  if(i % 2){
    console.log(i);
  }
}
```



## 5. while 문을 사용하여 0 부터 10 미만의 정수중에서 짝수만 작은 수부터 출력

```javascript
var num = 0;
while(num < 10) {
  if(num % 2 === 0) {
    console.log(num);
  }
  num++;
}
```



## 6. while 문을 사용하여 0 부터 10 미만의 정수중에서 홀수만을 큰수부터 출력

```javascript
var num = 9;
while(num >= 0){
  if( num % 2 !== 0 ) console.log(num);
  num--;
}
```



## 7. for 문을 사용하여 0부터 10 미만의 정수의 합을 출력

```javascript
var sum = 0;
for(var i = 0; i < 10; i++) {
  sum = sum + i;
}
console.log(sum);
```



## 8. 1부터 20 미만의 정수 중에서 2 또는 3의 배수가 아닌 수의 총합

```javascript
var sum = 0;
for(var i = 1; i < 20; i++) {
  if( i % 2 !== 0 && i % 3 !== 0){
    sum = sum + i;
  }
}
console.log(sum);
```



## 9. 1부터 20 미만의 정수 중에서 2 또는 3의 배수인 수의 총합

```javascript
var sum = 0;
for(var i = 1; i < 20; i++) {
  if( i % 2 === 0 || i % 3 === 0) {
    sum = sum + i;
  }
}
console.log(sum);
```



## 10. 두 개의 주사위를 던졌을 때, 눈의 합이 6이 되는 모든 경우의수 출력하기

```javascript
for(var diceValue1 = 1; diceValue1 <= 6; diceValue1++) {
  for (var diceValue2 = 1; diceValue2 <= 6; diceValue2++){
    if(diceValue1 + diceValue2 === 6){
      console.log(`[ ${diceValue1}, ${diceValue2} ]`);
    }
  }
}
```



## 11. 삼각형 출력하기 1

```javascript
var str = '';
for(var line = 1; line <= 5; line++){
  for(var star = 1; star <= line; star++){
    str = str + '*';
  }
  str = str + '\n';
}
console.log(str);
```



## 12. 삼각형 출력하기 2

```javascript
var str = '';
for(var line = 1; line<=5; line++) {
  for(var blank = 0; blank < line-1; blank++) {
    str = str + ' ';
  }
  for(var star = line; star <= 5; star++){
    str = str + '*';
  }
  str = str + '\n';
}
console.log(str);
```



## 13. 삼각형 출력하기 3

```javascript
var str = '';
for(var line = 0; line < 5; line++){
  for(var star = 0; star < 5 - line; star++){
    str = str + '*';
  }
  str = str + '\n';
}
console.log(str);
```



## 14.  삼각형 출력하기 4

```javascript
var str = '';
for(var line = 1; line <= 5; line++) {
  for(var blank = 0; blank < 5 - line; blank++) {
    str = str + ' ';
  }
  for(var star = 1; star <= line; star++) {
    str = str + '*';
  }
  str = str + '\n';
}
console.log(str);
```



## 15. 정삼각형 출력하기

```javascript
var str = '';
for(var line = 1; line <= 5; line++) {
  for(var blank = 0; blank < 5 - line; blank++){
    str = str + ' ';
  }
  for(var star = 1; star <= 2 * line -1; star++){
    str = str + '*';
  }
  str = str + '\n';
}
console.log(str);
```



## 16.  역정삼각형 출력하기

```javascript
var str = '';
for(var line = 0; line < 5; line++){
  for(var blank = 0; blank < line; blank++){
    str = str + ' ';
  }
  for(var star = 0; star < 9 - 2 * line; star++) {
    str = str + '*';
  }
  str = str + '\n';
}
console.log(str);
```

 