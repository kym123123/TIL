# 타입 캐스팅

: 타입스크립트에서 <u>어떤 변수를 한 타입에서 다른 타입으로 변경시키도록 해주는 것</u>. 자바스크립트는 동적 타이핑 언어이기 때문에 타입캐스팅에 대한 개념이 없지만, ts는 있다.

* as 키워드 또는 <> operator를 사용하여 타입 캐스팅을 한다.

### as 키워드 사용한 타입 캐스팅

```typescript
let input = document.querySelector('input["type="text"]');
```

document.querySelector() 메서드의 반환값의 타입은 Element type이므로, 아래의 코드는 컴파일러 에러가 발생한다.

```typescript
console.log(input.value); 
```

value 프로퍼티는 HTMLInputElement 타입의 엘리먼트에만 존재하기 때문이다. 이문제를 해결하려면, 타입캐스팅을 통해서 Element타입을 HTMLInputElement타입으로 바꿔주면 된다.

```typescript
let input = document.querySelector('input[type="text"]') as HTMLInputElement;
console.log(input.value);
```

이렇게 as 키워드를 사용해 타입캐스팅을 해주면, input 변수는 HTMLInputElement 타입이 된다. 따라서 input이 가진 property에 접근하는 것이 어떠한 에러도 발생시키지 않게 된다.

as 키워드를 아래처럼 타입 캐스팅 할 수 있다.

```typescript
let input = document.querySelector('input[type="text"]');

let enteredText = (input as HTMLInputElement).value;

console.log(enteredText); 
```

HTMLInputElement 타입은 HTMLElement타입을 확장한 것이고, HTMLElement 타입은 Element타입을 확장한 것이다. 따라서 HTMLElement 타입을 HTMLInputElement타입으로 캐스팅 하는것은 down casting 이라고도 한다.



다운 캐스팅의 예시

```typescript
let el: HTMLElement;
el = new HTMLInputElement();
```

el은 HTMLElement 타입의 변수, el에 HTMLInputElement 타입을 할당할 수 있다. HTMLInputElement는 HTMLElement 타입의 서브 클래스이기 때문이다.



### <> 연산자를 사용한 타입캐스팅

```typescript
let input = <HTMLInputElement>document.querySelector('input[type="text"]');

console.log(input.value);
```

```typescript
let a: typeA;
let b = <typeB>a;
```

