# 과제

## 1)  1. 1 ~ 10,000의 숫자 중 8이 등장하는 횟수 구하기 (Google)
1부터 10,000까지 8이라는 숫자가 총 몇번 나오는가? 이를 구하는 함수를 완성하라.
단, 8이 포함되어 있는 숫자의 갯수를 카운팅 하는 것이 아니라 8이라는 숫자를 모두 카운팅 해야 한다. 예를 들어 8808은 3, 8888은 4로 카운팅 해야 한다.
(hint) 문자열 중 n번째에 있는 문자 : str.charAt(n) or str[n]

```javascript
function getCount8() {
  var string = '';
  var count = 0;
  for(var i =1; i <= 10000; i++) {
    string += i; //1~10000의 숫자를 모두 한문자열에 저장
  }
  for(var i = 0; i < string.length; i++) {
    if(string[i] === '8'){//문자열 전체를 탐색하면서 8과 일치할경우 count증가
      count++;
    }
  }
  return count++;
}
console.log(getCount8());
```





## 2) 이상한 문자 만들기
toWeirdCase함수는 문자열을 인수로 전달받는다. 문자열 s에 각 단어의 짝수번째 인덱스 문자는 대문자로, 홀수번째 인덱스 문자는 소문자로 바꾼 문자열을 리턴하도록 함수를 완성하라.
예를 들어 s가 ‘hello world’라면 첫 번째 단어는 ‘HeLlO’, 두 번째 단어는 ‘WoRlD’로 바꿔 ‘HeLlO WoRlD’를 리턴한다.
주의) 문자열 전체의 짝/홀수 인덱스가 아니라 단어(공백을 기준)별로 짝/홀수 인덱스를 판단한다.

```javascript
function toWeirdCase(s) {
  var newString = '';
  var splitString = s.split(' ');
  var changedString = []; // split된 단어를 대소문자 변환후 저장할 배열
  for(var i = 0; i < splitString.length; i++) {
    changedString[i] = '';//배열값 초기화, undefined 출력 방지
    for(var j = 0; j < splitString[i].length; j++) {
      if(j % 2 === 0) {
        changedString[i] += splitString[i][j].toUpperCase();
      } else {
        changedString[i] += splitString[i][j].toLowerCase();
      }
    }
  }
  newString = changedString.join(' ');
  return newString;
}
console.log(toWeirdCase('hello world'));    // 'HeLlO WoRlD'
console.log(toWeirdCase('my name is lee')); // 'My NaMe Is LeE'
console.log(toWeirdCase('hihih hi hihihi hihi'));
```

