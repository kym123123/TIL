## SelectorFamily

어떠한 값을 매개변수에서 가져와서 사용할수 있는 selector이다. selector는 다른 atom의 상태나 selector의 getter에서 상태값을 가져와서 어떠한 연산을 할수 있는다. 그러나 데이터 몇가지의 변화만 있고 같은 연산을 하는 경우의 selector를 작성하려면 selectorFamily를 사용해야 한다.

```javascript
import _ from 'lodash';
import produce from 'immer';

const editPropertyState = selectorFamily<number | null, string>({
    // 첫번째: get이 반환하는 값: number, 두번째: path인자의 타입

    key: 'editProperty',
    get:
        (path) =>
        ({get}) => {
            const selectedElement = get(selectedElementState);
            if (selectedElement === null) return null;

            const element = get(elementState(selectedElement));

            return _.get(element, path); // element.path를 반환해준다.
        },
    set:
        (path) =>
        ({get, set}, newValue) => {
            const selectedElement = get(selectedElementState);

            if (selectedElement === null) return null;

            const element = get(elementState(selectedElement));

            // 이렇게 하면 새로운 객체를 만들어서 프로퍼티를 바꾼다. 따라서 객체의 참조값이 다르다.
            const newElement = produce(element, (draft) => {
                _.set(draft, path, newValue);
            });
            // 새로 만든 newElement객체를 set함수에 전달하여 새로운 상태로 갱신시킨다. -> 불변성 유지(react, redux와 같은 개념)
            set(elementState(selectedElement), newElement);

            // recoil의 상태객체도 값을 변경시키면, 새로운 객체를 만들어서 반환해야한다. 리액트, 리덕스처럼. 이렇게하면 프로퍼티만 바꾸고 객체의 참조값은 그대로이다. 따라서 이렇게 하면 렌더링이 다시 안됌.
            // set(elementState(selectedElement), _.set(element, path, newValue)); // element.path = newValue로 만들어주는 메서드
        },
});
```

selectorFamily의 get은 다른 함수를 반환하는 함수이며, 첫번쨰 함수의 매개변수는 외부에서 넘겨받을 data이고, 두번째는 selector처럼 get, set을 불러와서 사용한다.



selectorFamily도 atomFamily처럼 함수 호출문에 인자를 넘겨주면서 호출한다.

```javascript
    const [value, setValue] = useRecoilState(editPropertyState(path));
// editPropertyState에 path라는 인자를 넘겨서 호출한다. useRecoilState안에서. 
// 이렇게 하면 사전에 지정한 setter와 getter에 의해서 setValue, value를 얻을 수 있다.
```



