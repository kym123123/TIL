# Type Assertion 타입 어셔션

: TSC에게 어떠한 변수를 하나의 특정한 타입으로 대하도록 지시하는 것. as 키워드를 사용한다.

```typescript
expression as targetType
```

타입 어셔션은 타입 좁히기라고도 알려져있다. 어떠한 유니온 타입으로 부터 하나의 타입으로 narrowing 할 수 있도록 해준다. 

```typescript
function getNetPrice(price: number, discount: number, format: boolean): number | string {
    let netPrice = price * (1 - discount);
    return format ? `$${netPrice}` : netPrice;
}

let netPrice = getNetPrice(100, 0.05, true) as string;
console.log(netPrice); // $95

let netPrice = getNetPrice(100, 0.05, false) as number;
console.log(netPrice); // 95
```

getNetPrice 함수의 매개변수 중에서 마지막 매개변수인 format에 따라서 함수의 반환값이 number 타입이거나, string 타입으로 결정된다. (반환타입이 number | string =>. union 타입으로 지정되어 있다.) 

위처럼 as 키워드를 사용하여 string 혹은 number 키워드로 변수의 타입을 다루라고 TSC에게 지시하도록 사용한다. (number | string 유니온 타입을 하나의 타입 number 혹은 string으로 대하도록 지정)

타입 어셔션은 타입캐스팅과 매우 유사해 보이지만, 단지 TSC 에게 어떠한 타입을 적용하요 타입 체킹을 할 것인지에 대한 목적으로만 사용하는 것이고, 타입 캐스팅을 하는 것은 절대 아니다.

### <> 연산자 사용한 타입 어셔션

```typescript
let netPrice = <number>getNetPrice(100, 0.05, false);
```

위처럼 <>를 이용하여 타입 어셔션을 해줄수 있지만, 리액트의 jsx 문법과 겹쳐서 리액트에서는 사용할 수 없다. 따라서 as 키워드를 사용한 어셔션을 하자.