## itertools

1. 순열 (permutations)

반복 가능한 객체에 대해서 중복을 허용하지 않고 r개를 뽑아서 나열한다.

뽑힌 순서대로 나열하여 순서에 의미가 있다.

```python
from itertools import permutations

for i in permutations([1, 2, 3, 4], 2):
  print(i, end=' ')
  
# (1, 2)...(4, 3)
```



2. 조합(combinations)

반복 가능한 객체에 대해서 중복을 허용하지 않고 r 개를 뽑는다. 순서는 고려하지않는다

```python
from itertools import combinations

for i in combinations([1, 2, 3, 4], 2):
  print(i, end=' ')
  
  # (1, 2) (1, 3) (1, 4) (2, 3) (2, 4) (3, 4)
```



3. 중복 순열(product)

```python
from itertools import product

for i in product([1, 2, 3], 'ab'):
  print(i, end='')
# (1, 'a'), (1, 'b'), (2, 'a'), (2,'b'), (3, 'a'), (3, 'b')
```

```python
for i in product(range(3), range(3), range(3)):
  print(i, end='')
  
  # (0, 0, 0) (0, 0, 1) (0, 0, 2) (0, 1, 0) (0, 1, 1) (0, 1, 2) (0, 2, 0) (0, 2, 1) (0, 2, 2) (1, 0, 0) (1, 0, 1) (1, 0, 2) (1, 1, 0) (1, 1, 1) (1, 1, 2) (1, 2, 0) (1, 2, 1) (1, 2, 2) (2, 0, 0) (2, 0, 1) (2, 0, 2) (2, 1, 0) (2, 1, 1) (2, 1, 2) (2, 2, 0) (2, 2, 1) (2, 2, 2)
```



4. 중복 조합

combinations_with_replacement(반복 가능 객체,  r)

```python
from itertools import combinations_with_replacement

for cwr in combinations_with_replacement([1, 2, 3, 4], 2):
  print(cwr,end=' ')
 
# (1, 1) (1, 2) (1, 3) (1, 4) (2, 2) (2, 3) (2, 4) (3, 3) (3, 4) (4, 4)

```

