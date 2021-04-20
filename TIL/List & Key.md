# List & Key

## typescript의 type에서 key

```typescript
  type Key = string | number;

  interface Attributes {
      key?: Key | null;
  }
```

key는 string이거나 number이다. Attributes interface에서 key는 선택적 타입으로 Key 타입 별칭이나 null을 타입으로 한다.

## key

: key는 엘리먼트 리스트를 만들때 사용해야 하는 attribute이다. key는 react가 어떤 항목을 변경, 추가, 삭제할지 식별할 수 있도록 해준다. key는 엘리먼트에 안정적 고유성을 부여하기 위해 사용한다. 따라서 이러한 고유성을 위해서 key로 사용할 것은 ID같은 데이터의 고유한 값을 사용해야 한다. 도저히 사용할 값이 없다면 배열의 index를 key 로 사용하도록 하지만, 배열의 순서가 바뀔수 있는 상황에서는 index를 사용하지 않도록 한다.

Key attributes를 넣어주는 위치는 배열로부터 map 메서드 등에 의해 생성되는 component의 가장 부모 컨테이너에 넣어줘야 한다.

key값은 동일한 배열 안에서만 자신을 식별할수 있는 값이면 된다, 서로 다른 두개의 배열 A,B가 각각 다른 컴포넌트 안에서 map 메서드로 자식 `<li>`들을 렌더링 한다면, 그 key 값은 같아도 괜찮다.

react는 diffing algorithm으로 휴리스틱 알고리즘을 사용하여 시간복잡도를 O(n)으로 최소화 했다. 따라서 BFS로 실제 DOM과 가상 DOM을 탐색하며 차이를 확인하고, state의 변경등에 따른 차이가 발견되면 dirty를 체크한다. 이러한 dirty체킹된 것들을 batch에 쌓아 두고 한번에 마무리 한 다음, 실제 DOM을 그에맞게 변경한다.



## virtual DOM

* Javascript object(or JSON) is a representation of the browser DOM
* Extremely fast compared to the browser DOM
* Can produce 200,000 virtual DOM nodes a second
* Created Completely from scratch on every setState or dispatch



## React Diffing Algorithm

React implements a heuristic algorithm based on two assumptions: 

1. Two elements of different types will produce different tree.
2. The developer can hint at which child elements may be stable across different renders with a key prop. In practice, these assumptions are valid for almost all practical use cases.

when comparing two React DOM elements of the same Type, React looks at the attributes of both, keeps the same underlying DOM node, and only updates the changed attributes. Works the same way for the style tag.

If a node is found to be different(different type or component) it will re-render the entire subtree.







