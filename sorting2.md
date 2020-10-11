## 2.3 선택 정렬

- 선택 정렬(selection sort)은 배열의 최소값을 검색하여 배열의 왼쪽부터 순차적으로 정렬을 반복하는 정렬 알고리즘이다.
- 배열이 미정렬 상태이므로 최소값 검색에는 이진 검색이 아닌 선형 검색 알고리즘을 사용한다.
- 선택 정렬은 버블 정렬보다 빠르다.
- 시간 복잡도: O(n2)

![img](https://poiemaweb.com/assets/fs-images/selection-sort.png)

선택 정렬

선택 정렬을 통해 주어진 배열(array)을 정렬하는 함수를 구현하라. 단, 어떠한 빌트인 함수도 사용하지 않고 for 문을 사용하여 구현하여야 한다

```javascript
function selectionSort(array) {
  let min;
  let indexOfMin;
  for(let i = 0; i < array.length-1; i++) {
    indexOfMin = i;
    min = array[i];
    for (let j = i; j < array.length; j++) {
      if(min < array[j]) continue;
      else{
        min = array[j];
        indexOfMin = j;
      }
    }
    [array[i], array[indexOfMin]] = [array[indexOfMin], array[i]];
  }
  return array;
}

console.log(selectionSort([3, 1, 0, -1, 4, 2])); // [-1, 0, 1, 2, 3, 4]
console.log(selectionSort([2, 4, 5, 1, 3]));     // [1, 2, 3, 4, 5]
console.log(selectionSort([5, 2, 1, 3, 4, 6]));  // [1, 2, 3, 4, 5, 6]
console.log(selectionSort([5, -2, 1, -3, 4, 6,1,2,3]));  // [1, 2, 3, 4, 5, 6]
```



## 2.4 삽입 정렬

- 삽입 정렬(insertion sort)은 인덱스 1부터 왼쪽과 비교하면서 순차적으로 정렬을 반복하는 정렬 알고리즘이다.
- 정렬이 진행됨에 따라 왼쪽에는 정렬이 종료된 값이 모이게 되고, 오른쪽에는 아직 정렬되지 않은 값이 남게 된다.
- 선택 정렬은 최소값 검색이 필요하지만 삽입 정렬은 필요 없다.
- 삽입 정렬은 평균 시나리오에서 선택 정렬과 유사하고(데이터 정렬 유형에 따라 차이가 있다) 버블 정렬보다 빠르다.
- 시간 복잡도: O(n2)

![img](https://poiemaweb.com/assets/fs-images/insertion-sort.png)

삽입 정렬

삽입 정렬을 통해 주어진 배열(array)을 정렬하는 함수를 구현하라. 단, 어떠한 빌트인 함수도 사용하지 않고 for 문을 사용하여 구현하여야 한다.

* 방법1)

```javascript
function insertionSort(array) {
  for(let i = 1; i < array.length; i++) {
    for(let j = i; j > 0; j--) {
      if(array[j] < array[j - 1]){
        [array[j - 1], array[j]] = [array[j], array[j - 1]];
      }
    }
    //console.log(array);// 바뀌는 과정 확인..
  }
  return array;
  
}

console.log(insertionSort([3, 1, 0, -1, 4, 2])); // [-1, 0, 1, 2, 3, 4]
console.log(insertionSort([2, 4, 5, 1, 3]));     // [1, 2, 3, 4, 5]
console.log(insertionSort([5, 2, 1, 3, 4, 6]));  // [1, 2, 3, 4, 5, 6]
```

* 방법2) 

  앞의 배열요소가 더 작으면 for문 break

```javascript
function insertionSort(array) {
  for (let i = 1; i < array.length; i++) {
    for (let j = i; j > 0; j--) {
      if (array[j] > array[j - 1]) break;
      else {
        [array[j - 1], array[j]] = [array[j], array[j - 1]];
      }
    }
    //console.log(array);// 바뀌는 과정 확인..
  }
  return array;
}

console.log(insertionSort([3, 1, 0, -1, 4, 2])); // [-1, 0, 1, 2, 3, 4]
console.log(insertionSort([2, 4, 5, 1, 3]));     // [1, 2, 3, 4, 5]
console.log(insertionSort([5, 2, 1, 3, 4, 6]));  // [1, 2, 3, 4, 5, 6]
```

