## Atom Families

같은 atom의 형태를 여러 컴포넌트에 각기 매핑시켜야 하는 상황일 경우(ex: list로 렌더링되는 `<List>`컴포넌트가 있고, 그 안에 각각 개별적으로 상태를 갖는 atom을 저장해야 하는 상황), atom 한개에 리스트의 갯수만큼  상태를 보유하고 있게되면, 10개의 리스트중에 단 하나만 상태를 변경하여도, 상태는 모두 한개의 공통 atom에 존재하기 때문에, 리스트 컴포넌트 모두가 리렌더링 된다.

이는 성능상으로 매우 좋지 않다. 리스트의 개수가 늘어날수록 엄청난 성능 저하를 보인다. 그러나 atomFamily를 이용하면, 각 컴포넌트에 같은 형태의 개별적인 atom을 매핑 시켜줄 수 있어서, 상태가 바뀐 그 컴포넌트만 리렌더링 시킬 수 있다. 이를 가능하게 해주는 것이 atomFamily다.

![Screen Shot 2021-05-29 at 1 50 40 AM](https://user-images.githubusercontent.com/51959017/120060996-f780b700-c095-11eb-8000-25823594db60.png)

```javascript
export type ElementStyle = {
    position: {top: number; left: number};
    size: {width: number; height: number};
};

export type Element = {style: ElementStyle};

const elementState = atomFamily<Element, number>({
    key: 'element',
    default: {
        style: {
            position: {top: 0, left: 0},
            size: {width: 50, height: 50},
        },
    },
});
// 각 atom의 type은 Element이고, atomFamily가 호출될때 전달받는 값(ex: id값)은 number타입이라는 것을 제네릭에 전달한다.


const testState = atomFamily<number>({
    key: 'test',
    default: () => Math.random(),
});
// 이렇게 쓰면 아톰이 생성될 때마다 Math.random()이 호출되어 atomFamily가 호출될때 전달받은 값(ex. id) 에 따라서 각기 다른 default를 갖게된다.
  
const testState = atomFamily<number>({
    key: 'test',
    default: Math.random()  
});
//  이렇게 쓰면 모든 atom의 기본값이 하나로 고정된 상태로 atom이 생성된다. 따라서 atomFamily가 호출될때마다 같은 default를 갖게된다.
```

atomFamily는 값을 구독하거나 setter를 받을때, 각 컴포넌트 별로 개별적으로 구독하는 atom을 구분짓기 위하여 id값과 같은 컴포넌트별로 고유한 인자를 넣어 호출시켜준다. (atom의 값을 구독 할 때는 호출연산자를 사용하지 않지만, atomFamily는 호출연산자 안에 고유한 값을 인자로 전달하여, 그 인자에 해당하는 즉, 내가 원하는 atom의 값만 구독할 수 있게 한다.)

```javascript
export const Rectangle = ({id}: {id: number}) => {
    const [selectedElement, setSelectedElement] = useRecoilState(selectedElementState);
  
    const [element, setElement] = useRecoilState(elementState(id)); 
  
  	// Rectangle 컴포넌트마다 전달받는 고유한 props인 id로 구분된 atom을 호출
    const test = useRecoilValue(testState(id));
  
 // ....
}
```