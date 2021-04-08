### 타입 시스템

* 컴파일러에게 사용자의 타입을 명시적으로 지정하는 시스템
* 컴파일러가 자동으로 타입을 추론하는 시스템



* strictNullChecks 옵션을 켜면 , 모든 타입에 자동으로 포함되어있는 null과 undefined를 제거해준다.
* noImplicitReturns 옵션을 켜면,  함수 내에서 모든 코드가 값을 리턴하지 않으면, 컴파일 에러가 발생한다.

* strictFunctionTypes 옵션을 켜면, 함수의 매개변수 타입만 같거나, 슈퍼타입인 경우가 아닌경우, 에러발생

* 매개변수에 object가 들어오는 경우 -> objectliteralType을 이용하여 type 적용 -> 나만의 type을 만들어서 사용한다( interface, type 어노테이션 방식)



## interface와 type alias

* Structural type system - 구조가 같으면 같은 타입. -> 타입스크립트, 구조가 같으면 타입 이름이 달라도 같은타입

* nominal type stystem - 구조가 같아도 이름이 다르면, 다른타입. -> ts도 이러한 system을 흉내낼 수 있다.

```typescript
type PersonID = string & { readonly brand: unique symbol };
// readonly brand에 unique symbol을 이용하여 PersonID 타입을 만들고, 유니크한 string을 만들 수 있다.

function PersonID(id: string): PersonID {
  return id as PersonID; // type assertion을 이용한 PersonID타입 반환
}

function getPersonById(id: PersonID) {}

getPersonById(PersonID('id-aaaaa')); // OK
getPersonById('id-aaaaa'); // error, Argument of type 'string' is not assignable to parameter of type 'PersonID'.
```



* Type alias와 interface에서 function 

  ```typescript
  type EatType = (food: string) => void; // type alias
  
  // interface
  interface IEat {
    (food: string): void;
  }
  ```

* type alias와 interface에서 Array

  ```typescript
  type PersonList = string[];
  
  interface IPersonList {
    [index: number]: string;//index는 number, 요소 type은 string
  }
  ```

* intersection -> 사용 문법은 다르지만 의미적으론 같다.

```typescript
interface ErrorHandling {
  success: boolean;
  error?: { message: string };
}

interface ArtistsData {
  artists: { name: string }[];
}

// type alias, 두가지 타입 합치기
type ArtistsResponseType = ArtistsData & ErrorHandling;

// interface, 두가지 interface 합치기(상속받기)
interface IArtistsResponse extends ArtistsData, ErrorHandling {}

let art: ArtistsResponseType;
let iar: IArtistsResponse;
```

* Union -> a | b 

```typescript
interface Bird {
  fly(): boid;
  layEggs(): void;
}

interface Fish {
  swim(): void;
  layEggs(): void;
}

type PetType = Bird | Fish; // union 조합 타입의 이름은 type alias 사용

interface IPet extends PetType {} // error -> type alias를 interface가 상속 받기 불가능

class Pet Implements PetType {} // error -> type alias를 class가 구현하는것 불가능
```

### contextual typing - 위치에 따라 추론이 다르다

```typescript
const click = e => { // parameter 'e' implicilty has an 'any' type
  e; // any
};

document.addEventListener('click', click);
document.addEventListener('click', (e) => {
  e; //MouseEvent
});
```



## Type Guard로 안전성 확보

1. typeof Type Guard - 보통 primitive 타입일 경우

   ```typescript
   function getNumber(value: number | string): number {
     value; // number | string
     
     if (typeof value === 'number') {
       value; // number
       return value;
     }
     value; // string
     return -1;
   }
   ```

2. instance of Type guard

   ```typescript
   interface IMachine {
     name: string;
   }
   
   class Car implements IMachine {
     name: string;
     wheel: number;
   }
   
   class Boat implements IMachine {
     name: string;
     motor: number;
   }
   
   function getWheelOrMotor (machine: Car | Boat) : number {
   	if (machine instanceof Car) {
       return machine.wheel; // Car
     } else {
       return machine.motor; // Boat
     }
   }
   ```

   instance of Type Guard - Error 객체 구분에 많이 쓰인다. (에러처리)

   ```typescript
   class NegativeNumberError extends Error {} // error를 상속받아서 원하는 에러들을 만들어서 사용한다.
   
   function getNumber(value: number): number | NegativeNumberError {
     if (value < 0) return new NegativeNumberError();
     
     return value;
   }
   
   function main() {
     const num = getNumber(-10);
   	
     if (num instanceof NegativeNumberError) {
       return;
     }
   
     num; // number
   }
   ```

3. In operator Type guard - object의 프로퍼티 유무로처리하는 경우

```typescript
interface Admin {
  id: string;
  role: string;
}

interface User {
  id: string;
  email: string
}

// user is admin 조건을 만들기 불편하다.
function redirect(user: Admin | User) {
  if (/* user is admin*/) {
      routeToAdminPage(user.role);
  } else {
    routeToHomePage(user.email);
  }
}

// in operator를 사용하여 아래와같이 조건분기 해준다.
function redirect(user: Admin | User) {
  if ('role' in user) { // in 연산자 사용
      routeToAdminPage(user.role);
  } else {
    routeToHomePage(user.email);
  }
}
```


4. Literal Type Guard - object의 프로퍼티가 같고, 타입이 다른경우

   ```typescript
   interface IMachine {
     type: string;
   }
   
   class Car implements IMachine {
     type: 'CAR';
     wheel: number;
   }
   
   class Boat implements IMachine {
     type: 'BOAT';
     motor: number;
   }
   
   // redux에서 reducer의 구조와 비슷
   function getWheelOrMotor(machine: Car | Boat): number {
     if (machine.type === 'CAR') {
       return machine.wheel;
     } else {
       return machine.motor;
     }
   }
   ```

5. custom type guard - 라이브러리 상에서 많이 사용한다.

   ```typescript
   function getWheelOrMotor(machine: any): number {
     if (isCar (machine)) {
       return machine.wheel;
     } else if (isBoat(machine)) {
       return machine.motor;
     } else {
       return -1;
     }
   }
   
   function isCar(arg:any): arg is Car { // function 이 true를 반환하면 arg의 타입도 Car라고 보증
     return arg.type === 'CAR';
   }
   
   function isBoat(arg: any): arg is Boat { // function 이 true이면, arg의 타입이 Boat라고 보증
     return arg.type === 'BOAT';
   }
   ```

### class 안전하게 사용하기

1. class 프로퍼티의 타입을 명시적으로 지정해야한다.
   * strictPropertyInitialization 옵션을 켜면, class의 프로퍼티가 생성자 혹은 선언시에 값을 지정하지 않으면, 컴파일 에러를 발생시켜 준다. ( 값없이 타입만 지정해 주었을 경우 )

```typescript
class Square2 {
  area: number; // area프로퍼티가 초기화 되지 않았음에 대한 경고 에러
  sideLength: number; // sideLength프로퍼티가 초기화 되지 않았음에 대한 경고 에러
}

// 사용자가 아래행동을 시도조차 할 수 없도록 만듬
const square2 = new Square2();
console.log(square2.area);
console.log(square2.sideLength);

// 따라서 선언시에 초기화흘 해준다
class Square2 {
  area: number = 0;
  sideLength : number = 0;
}

// 혹은 생성자에서 초기화
class Square2 {
  area: number; 
  sideLength: number;
  
  constructor(sideLength: number) {
    this.sideLength = sideLength;
    this.area = sideLength ** 2;
  }
}
```

typescript 4.0.2버전부터는 변경사항이 있다. Square2의 프로퍼티에 명시적 타입 지정을 해주지 않아도, 생성자내부를보고 프로퍼티 타입을 추론한다.

```typescript
class Square2 {
  area;
  sideLength;
  
  constructor(sideLength: number) {
    this.sideLength = sideLength; // number 추론
    this.area = sideLength ** 2; // number 추론
  }
}
```

그러나 여전히 생성자를 벗어나면 추론이 올바르게 되지 않는다.

```typescript
class Square7 {
  sideLength!: number; // !로 의도를 표현해야한다. -> constructor에서는 초기화가 되지않지만,
// 어디선가는 초기화가 number로 될것이다 라는 의도
// ! 는 주의를 의미하는 기호
  
  constructor(sideLength: number) {
    this.initialize(sideLength);
  }
  
  // 생성자가 아닌 다른 함수에서 초기화를 진행해서 추론이 올바르게 되지 않는다. -> !로 의도전달
  initialize(sideLength: number) {
    this.sideLength = sideLength;
  }
  
  get area() {
    return this.sideLength ** 2;
  }
}
```



### conditional type 활용

```typescript
type Item2<T> = {
  id: T;
  container: T extends string ? StringContainer : NumberContainer;
}

const item2: Item2<string> = {
  id: 'aaaaa',
  container: null, // Type 'null' is not assignable to type 'StringContainer';
}
```



* never는 다른 어느타입에나 넣을순 있지만, 다른 어느타입도 never에는 넣을 수 없다.

>  `Item<T>` 에서 T가 string 이면 StringContainer타입, T가 number면 NumberContainer타입, 둘 중 어느것도 아니면 아예 사용 불가능 하게 만들기 

```typescript
type Item3<T> = {
  id: T extends string | number ? T : never; //T가 string이면 string, number면 number,
  // 둘다 아니면 아무것도 사용할 수 없음(never)
  
  // T가 string -> StringContainer, number면 NumberContainer, 둘다 아니면 never(사용불가)
  container: T extends string
  	? StringContainer
  	: T extends number
  	? NumberContainer
  	: never;
};

// 따라서 boolean을 T에 넣으면 사용이 불가능해 진다.
const item3: Item3<boolean> = {
  id: true, // Type 'boolean' is not assignable to type 'never'
  container: null, // Type 'null' is not assignable to type 'never'
}
```




* `ArrayFilter<T>`: T에 들어온 것 중에, array인 것들만 골라내는 필터 (never 사용)

```typescript
// T 가 array형태라는 것을 T extends any[]로 표현할 수 있다. 
// T가 배열이면, T 반환, 아니면 아무것도 못하게 never 반환
type ArrayFilter<T> = T extends any[] ? T : never;

// ArrayFilter에 유니온 타입 넣음.
type StringsOrNumbers = ArrayFilter<string | number | string [] | number []>;
// 따라서 StringsOrNumbers에 string | number | string[] | number []를 차례대로 T에 넣으면
// never | never | string [] | number [] 가 반환되고, 이것을 정리하면 never가 사라져서 아래처럼 된다.

// string [] | number []
```



* Table or Dino -> 제네릭 안에 제약사항 걸어서 사용 못하게 만드는 방법( never이용하는 것과 다르다 )

  ```typescript
  interface Table {
    id: string;
    chairs: string[];
  }
  
  interface Dino {
    id: number;
    legs: number;
  }
  
  interface World {
    // 제네릭 안에서 제약조건을 string또는 number로 설정
    getItem<T extends string | number> (id: T): T extends String ? Table : Dino;
  }
  
  let world: World = null as any;
  
  const dino = world.getItem(10);
  const what = world.getItem(true); // Error! Argument of type boolean is not assignable to parameter of type 'string | number' . ts(2345)
  // boolean을 T에 넣으면 사용이 불가
  ```



### `Flatten<T>`

```typescript
type Flatten<T> = T extends any []
	? T[number]
	: T extends object
	? T[keyof T]
  : T;

const numbers = [1, 2, 3];
type NumbersArrayFlattend = Flatten<typeof numbers>;
                                    // 1. number[]
                                    // 2. number
                                    
const person = {
	name: 'Mark',
  age: 38
};

type SomeObjectFlattend = Flatten<typeof person>;
// key of T(person) -> 'name' ,' age' (person의 key값들)
// T['name' | 'age'] -> T['age'] | T['name'] -> number | string


const isMale = true;
type SomeBooleanFlattened = Flatten<typeof isMale>; // true
```



### infer

```typescript
// promise의 return값이 배열인 경우, 그 배열요소의 타입, 아니면 any 타입
type UnpackPromise<T> = T extends Promise<infer K>[] ? K : any;
const promises = [Promise.resolve('Mark'), Promise.resolve(38)]; // (string | number) []

type Expected = UnpackPromise<typeof promises>; // string | number
```

`Promise<T>`처럼 Promise의 뒤에 나오는 제네릭의 값은 promise가 반환하는 리턴값의 타입이 들어간다.

* 함수의 리턴타입 알아내기 - MyReturnType

* ```typescript
  function plus1(seed: number): number {
    return seed + 1;
  }
  
  // (...args: any) => any는 함수를 나타낸다.
  // T가 함수이면(제네릭의 제한사항), 이 함수의 Return 타입을 infer(추론;inference)해서, R로 꺼내 반환한다 라는 의미.
  type MyReturnType<T extends (...args: any) => any> = T extends (
  	...args: any
  ) => infer R ? R : any;
  
  // typeof는 런타임에서는 어떠한 것의 타입을 값으로 반환하지만, 컴파일 타임에서는 다르다. 컴파일시에는 plus1의 타입스크립트 타이핑을 얻어내는 역할이다.
  type Id = MyReturnType<typeof plus1>;
  lookupEntity(plus1(10));
  function lookupEntity(id: Id) {
	// query DB for entity by Id 
  }
  ```
  



## 내장 conditional Types1

```typescript
// type Exclude<T, U> = T extends U ? never : T;
type Excluded = Exclude<string | number, string>; // number -> diff

// type Extract<T, U> = T extends U ? T : never;
type Extracted = Extract<string | number, string>; // string -> filter

// type Pick<T, K extends keyof T> = { [P in K]: T[P]; }
type Picked = Pick<{name: string, age: number}, 'name'>; // -> name 만 있는 객체타입

// type Omit<T, K extends string | number | symbol> = { [P in Exclude<keyof T, K>]: T[P]; }
type Omited = Omit<{name: string, age: number}, 'name'>; // -> name 만 없는 객체타입

// type NonNullable<T> = T extends null | undefined ? never : T;
type NonNullabled = NonNullable<string | number | null | undefined>; // string | number
```

never를 사용해서 구현된 내장  conditional Type들. never는 다른 타입과  union하면 없앨 수 있으므로 반환값을 종합하면 사라진다.

따라서  Excluded에서는 number | never가 나오는데 이는 최종적으로  number만 남게된다. 

Mapped Type은  keyof, keyin를 활용하는 Type helper이다.



## 내장  conditional Types2

```typescript
// 함수의 return 타입을 타입으로
type ReturnType<T extends (...args: any) => any> = T extends (...args: any) => infer R ? R : any;

// 함수의 매개변수 타입을 타입으로
type Parameters<T extends (...args: any) => any> = T extends (...args: infer P) => any ? P : never;

type MyParameters = Parameters<(name: string, age: number) => void>; // [name: string, age: number]
// [string, number]
```



## 내장 conditional Types3

```typescript
interface Constructor {
  new (name: string, age: number): string;
}

// 내장 conditional Type인 ConstructorParameters => 생성자의 파라미터 타입을 반환
/*
	type ConstructorParameters<T extends new (...args: any) => any> = T extends new (...args: infer P) => any ? P : never;
*/

type MyConstructorParameters = ConstructorParameters<Constructor>; // [string, number]

/*
	type InstanceType<T extends new (...args: any) => any> = T extends new (...args: any) => infer R ? R : any; 
*/

type MyInstanceType = InstanceType<Constructor>; // string
```



### Function 인 프로퍼티 찾기: 어떠한 인터페이스 안에 들어있는 함수 프로퍼티 찾는법

```typescript
type FunctionPropertyNames<T> = {
  [K in keyof T]: T[K] extends Function ? K : never;
}[keyof T]; // 함수 프로퍼티만 뽑아내기

type FunctionProperties<T> = Pick<T, FunctionPropertynames<T>>; // T에서 함수 프로퍼티만 뽑기

type NonFunctionPropertyNames<T> = {
  [K in keyof T]: T[K] extends Function ? never : k;
}[keyof T];  // 함수 아닌 프로퍼티만 뽑아내기

interface Person {
  id: number;
  name: string;
  hello(message: string): void;
}

type T1 = FunctionPropertyNames<Person>;
type T2 = NonFunctionPropertyNames<Person>;
type T3 = FunctionProperties<Person>;
type T4 = NonFunctionProperties<Person>;
```



## Overloading

* 제네릭과 conditional Type을 활용한 함수

  ```typescript
  function shuffle2<T extends string | any[]>(value: T): T extends string ? string : T;
  function shuffle2(value: any) {
    if (typeof value === 'string')
      return value.split('').sort(() => Math.random() - 0.5).join('');
    
    return value.sort(() => Math.random() - 0.5);
  }
  
  //function shuffle2<'Hello, Mark!'>(value: 'Hello, Mark!'): string
  shuffle2('Hello, Mark!');
  
  //function shuffle2<string[]>(value: string[]): string[]
  shuffle2(['hello', 'mark', 'long', 'time', 'no', 'see']);
  
  //function shuffle2<number []>(value: number[]): number[]
  shuffle2([1, 2, 3, 4, 5]);
  
  // Error! Argument of type 'number' is not assignable to parameter of type 'string | any[]'.
  shuffle2(1);
  ```

* 오버로딩 활용

  ```typescript
  function shuffle3(value: string): string; // (1)
  function shuffle3<T>(value: T[]): T[]; // (2)
  function shuffle3(value: string | any[]): string | any[] { // (3) 구현부
    if (typeof value === 'string') return value.split('').sort(() => Math.random() - 0.5).join('');
   return value.sort(() => Math.random() - 0.5);
  }
  
  shuffle3('Hello, Mark!');
  shuffle3(['Hello', 'Mark', 'long', 'time', 'no', 'see']);
  shuffle3([1,2,3,4,5]);
  ```

  ts의 오버로딩은 런타임에서 사용하는 오버로딩 아니고, ts의 타입처리만 도와주는 오버로딩이다. 따라서 (1), (2)를 제외하고는 나머지는 런타임에 제대로 동작해야 한다. 따라서 런타임에 처리되는 부분은 개발자가 알아서 해야한다.

  (1)과 (2)의 시그니처에 부합하도록 (3)을 작성해야한다. 



* 클래스 메서드의 오버로딩: 작성자

  ```typescript
  class ExportLibraryModal {
    // 타이핑
    // 첫번째 시그니처
    public openComponentsToLibrary(libraryId: string, componentIds: string[]): void;
  	// 두번째 시그니처
    public openComponentsToLibrary(componentsIds: string[]): void;
    // 구현 -> 위의 두 타이핑을 모두 만족하도록 구현
    public openComponentsToLibrary(libraryIdOrComponentIds: string | string[], componentIds?: string[]): void {
      // 이곳에서 문제점, libraryIdOrComponentsIds를 추론해도, 
      // 두번째 인자인 componentIds도 자동추론 해주지 않아서 또 조건을 줘서 추론해줘야한다.
        if (typeof libraryIdOrComponentIds === 'string') {
            if (componentIds !== undefined) {
              // 첫번째 시그니처
                libraryIdOrComponentIds;
                componentIds;
            }
        }
  
        if (componentIds === undefined) {
          // 두번째 시그니처
            libraryIdOrComponentIds;
        }
    }
  }
  ```



* readonly, as const

```typescript
class Layer {
    id!: string;
    name!: string;
    x: number = 0;
    y: number = 0;
    width: number = 0;
    height: number = 0;
}

// 요소의 값을 다시  set 할수 없는 readonly array
const LAYER_DATA_INITIALIZE_INCLUDE_KEYS: ReadonlyArray<keyof Layer> = [
    'x', 'y', 'width', 'height'
];

const x = LAYER_DATA_INITIALIZE_INCLUDE_KEYS[0]; 
// const x: "id" | "name" | "x" | "y" | "width" | "height"


const LAYER_DATA_INITIALIZE_EXCLUDE_KEYS = ['id', 'name'] as const;
const id = LAYER_DATA_INITIALIZE_EXCLUDE_KEYS[0];  // 'id'


//as const 없으면 type이 string으로 넓혀진다.
const LAYER_DATA_INITIALIZE_EXCLUDE_KEYS = ['id', 'name'];
const id = LAYER_DATA_INITIALIZE_EXCLUDE_KEYS[0];  // 'string'

```

ReadonlyArray와  as const 비교 -> 상수형태의 배열에서는  as const 추천

```typescript
const weekdays: ReadonlyArray<string> = [
  'sunday', 'monday', 'tuesday', 'wednesday', 'thursday', 'friday', 'saturday'
];

weekdays[0]; // readonly string[]
weekdays[0] = 'Fancyday' // Error! Index signature in type 'readonly string[]' only permits reading.


const weekdays = [
  'sunday', 'monday', 'tuesday', 'wednesday', 'thursday', 'friday', 'saturday'
] as const;

weekdays[0]; // 'sunday'
weekdays[0] = 'Fancyday' // error! cannot assign to '0' because it is a read-only property.
```



Mapped Types

타입스크립트 내장  Mapped types

```typescript
// 모든 프로퍼티를 옵셔널하게
type Partial<T> = {
  [P in keyof T]?: T[P];
};

// 모든 프로퍼티가 반드시 존재하도록
type Required<T> = {
  [P in keyof T]-?: T[P];
};

// 모든 프로퍼티를 읽기전용으로
type Readonly<T> = {
  readonly [P in keyof T]: T[p];
}

// 내가 고른 프로퍼티 키들만 골라내기
type Pick<T, K extends keyof T> = {
  [P in K]: T[P];
}

type Record<K extends keyof any, T> = {
  [P in K]: T;
}
```

* readonly keyword in return type

  ```typescript
  // array , tuple literal types
  function freturn1():string[] {
    return ['readonly'];
  }
  
  const fr1 = freturn1();
  fr1[0] = 'hello'; // 반환값 변경가능
  
  // 반환타입에  readonly 추가.
  function freturn2(): readonly string[] {
    return ['readonly'];
  }
  
  const fr2 = freturn2();
  fr2[0] = 'hello'; // error! index signature in type 'readonly string[]' only permits reading. -> 반환값 변경 불가능
  
  ```