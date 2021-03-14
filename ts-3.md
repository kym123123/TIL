

## 타입

1. any : 모든 값의 집합. 따라서 any로 타이핑 하는 것은 되도록 피해야 한다. 숫자타입과 문자타입을 더해서 새로운 결과값을 내야만 하는 상황일 경우는 tsc가 에러를 내지 않도록 any로 타입을 지정해 주는 것이 필요하다. (tsconfig.json에서 noImplicitAny를 활성화 시키면, any 타입이 발생할 경우 에러를 발생시킨다.)

2. unknown: 타입을 미리 알 수 없는 경우에 사용해야 하는 타입. unknown으로 타이핑 된 값은 개발자가 명시적으로 다른 타입으로 지정해주기 전에는 혹은 타입을 검사하여 어떠한 타입으로 refine되기 전까지는 사용할 수 없다. 사용하려고 한다면 에러가 발생한다.

   `TSError: ⨯ Unable to compile TypeScript:
   src/index.ts:4:9 - error TS2571: Object is of type 'unknown'.`

   unknown은 비교연산(==, ===, ||, &&, ?)과 반전(!) 및 자바스크립트의 다른 연산자 (typeof, instanceof)로 refine한 후 사용 가능하다.

   ```typescript
   let a: any = 6;
   let b: any = ['d'];
   let c: unknown = 7;
   
   let d = c + 7; // 아직 c값의 refine이 발생하기 전 -> c값 사용 불가. 에러 발생
   
   if (typeof c === 'number') {
     let d = c + 7; // typeof 연산자와 일치연산자로 refine이 발생한 후. 따라서 c값을 연산에 사용할 수 있다.
     console.log(d);
   }
   ```

3. Boolean: 불리언 타입은 true/false의 값을 갖는다. 불리언 타입은 타입 리터럴(오직 하나의 값을 나타내는 타입) 기능을 이용할 수 있다.

   ```typescript 
   const hi = true; // 불리언 타입이지만 const 키워드를 사용하여 true 값을할당하였으므로 값의 변경이 일어나지 않고 오직 boolean 타입의 true만을 갖게되는 것. -> const를 사용하면 ts가 자동으로 변수의 타입을 그 식별자가 가질수 있는 가장 좁읍 값으로 추론하고 boolean타입중 true로 타입을 추론한다.
   
   let isOutside: true = true;
   // isOutside 식별자는 boolean의 true를 타입으로 갖는다는 명시적 타입선언
   ```

4. Number: 모든 숫자의 집합(정수, 소수, 양/음수, Infinity, NaN...)으로써, 숫자 관련 연산이 지원된다.(+, -, %, <, >, *, ...등등)

5. 객체: ts의 객체는 구조기반타입을 지향한다. (구조 기반 타입화: 객체 이름과 무관하게 어떠한 프로퍼티를 가지고있는지를 고려. similar to duck typing)

   ```typescript
   // ts 객체 서술시 타입 이용 방식
   
   // 1. object타입 명시적 지정 -> 어떤 프로퍼티를 갖는지 관심x, 그냥 객체가 필요할 경우에 사용
   let obj: object = {
     p: 'x'
   };
   console.log(obj.p);
   //Property 'p' does not exist on type 'object'.ts(2339) 
   
   // 2. 객체 리터럴처럼 타입을 리터럴형식으로 지정
   let obj: {p: number} = {
     p: 7
   }
   ```

   2번 방식처럼 변수 obj를 {p:number}처럼 초기화 한 후에는 obj에 할당되는 객체의 형태가 위와 같아야 한다. p 이외의 다른 프로퍼티가 있거나, p가 없으면 에러가 발생한다. 초기화 된 프로퍼티 이외에 추가되거나 없을 수 있을 경우에 선언하는 방식은 아래와 같다.

   ```typescript
   let obj: {
     b: number, // 숫자타입의 b는 항상 존재해야 한다.
     c?: string, // 문자열타입의 c는 존재할 수도 있다.
     [key:number]: boolean // boolean 타입의 프로퍼티를 추가로 여러개 포함할 수 있다.
   }
   ```

   * 인덱스 시그니처 (index signature)

   위처럼 [key:number]: boolean형태의 문법을 인덱스 시그니처라 부른다. 객체가 여러 키를 가질수 있음을 알려준다. (모든 number타입의 key는 boolean형태의 value를 갖는다.)

   ```typescript
   let popularSubjectNames: {
     [subjectName: string]: string;
   } = {
     firstSub: 'Math',
     secondSub: 'HTML',
     thirdSub: 'CSS',
   };
   
   console.log(popularSubjectNames);
   // {firstSub: 'Math', secondSub: 'HTML', thirdSub: 'CSS' }
   ```

   * readonly 한정자를 사용한 특정 프로퍼티 읽기전용 선언

     : 이를 사용하여 선언하면 초기값을 할당한 후에는 바꿀수 없다. (const 사용한 효과)

     ```typescript
     let readOnlyPropObj: {
       readonly prop: string;
       hey: number;
     } = {
       prop: 'kim',
       hey: 1,
     };
     console.log(readOnlyPropObj);
     // { prop: 'kim', hey: 1 }
     
     readOnlyPropObj = {
       prop: 'jey',
       hey: 3,
     };
     
     console.log(readOnlyPropObj);
     // { prop: 'jey', hey: 3 }
     
     readOnlyPropObj.prop = 'change';
     //Cannot assign to 'prop' because it is a read-only property.ts(2540) 에러발생
     ```

     위의 코드처럼 readOnlyPropObj에서 readonly prop: string;으로 prop이라는 프로퍼티 키를 읽기전용으로 초기화 했지만 readOnlyPropObj전체를 다른 객체로 할당시켜 버리면 prop 값을 바꿀 수 있다.(이것은 readOnlyPropObj의 참조값 자체를 바꿔서 할당한 행위이므로, 결국 동일 참조값의 객체의 prop프로퍼티를 교체하는 것은 아니다.) 하지만 readOnlyPropObj.prop 에 직접 다른 값을 할당하려고 시도하면 에러가 발생한다.

     
   
   * 빈 객체 타입 : null과 undefined를 제외한 모든 타입을 빈 객체 타입에 할당 할 수 있다. -> 사용하기 까다로움. 사용하지 않는것이 좋다.
   
     ```typescript
     let emptyObj: {};
     
     emptyObj = { a: 1 };
     
     console.log(emptyObj); // { a: 1 }
     
     emptyObj = 1;
     
     console.log(emptyObj); // 1
     //에러가 발생하지 않는다
     ```
   
     

## 타입 수준에서 수행할 수 있는 동작들

* 타입 별칭: 타입 별칭으로 타입을 가리킬 수 있다.

  ```typescript
  type Age = number;
  type Person = {
    name: string;
    age: Age; // age: number
  };
  
  console.log(Person); // 'Person' only refers to a type, but is being used as a value here.ts(2693)
  // 타입은 타입으로써만 참조될수 있지만, 여기선 값처럼 사용되고 있다는 에러
  ```

  ```typescript
  type Person = {
    name: string;
    age: Age; // age: number
  };
  let driver: Person = {
    name: 'James May',
    age,
  };
  
  type Person = 'red'; // Duplicate identifier 'Person'.ts(2300)
  // 동일한 이름으로 type을 두 번 정의할 수 없다.
  ```

  type 별칭도 블록 영역에 적용된다. 아래의 else문 스코프에서는 Color 타입이 정의되지 않아서 오류가 발생하는 모습

  ```typescript
  if (x) {
    type Color = 'blue';
    let b: Color = 'blue';
    console.log(b);
  } else {
    let c: Color = 'red'; // Cannot find name 'Color'.ts(2304)
    console.log(c);
  }
  ```

  type 별칭으로 type을 선언하여 사용하면 어떤 목적으로 변수가 사용되었는지 이해하기 쉽다. 

  `주의: let, const 로 선언한 value를 type처럼 사용할 수는 없다.`

  ```typescript
  let test = 1;
  
  let a:test = 1; // 'test' refers to a value, but is being used as a type here. Did you mean 'typeof test'?ts(2749)
  // test는 값으로써 참조되어야 하는데, type처럼 참조되고 있다는 오류메시지
  ```

* Union( | )과 Intersection( & ) :  타입스크립트에서 제공하는 type 에 적용할 수 있는 연산자. 집합처럼 연산을 수행할 수 있다.

  * Union으로 합친 두 타입을 어떤 식별자의 타입으로 명시하면, 그 식별자는 합쳐진 두 타입중 한가지의 타입이 되어야 한다. (두 타입중 한타입의 조건을 만족시키거나, 두타입의 교집합의 결과인 모든 프로퍼티를 중복없이 가지고 있으면 OK)
  * Intersection으로 합친 두 타입을 어떤 식별자의 타입으로 명시하면 , 그 식별자는 두 타입의 교집합 결과인 중복없이 모든 프로퍼티를 갖는 결과를 타입으로 갖게된다.

  ```typescript
  type Cat = { name: string; purrs: boolean };
  type Dog = { name: string; barks: boolean; wags: boolean };
  type CatOrDogOrBoth = Cat | Dog;
  type CatAndDog = Cat & Dog;
  
  let a1: CatOrDogOrBoth = { name: 'Bonkers', barks: false, wags: true }; // Dog 타입의 프로퍼티를 가지거나
  let a2: CatOrDogOrBoth = { name: 'Boyington', purrs: true }; // Cat 타입의 프로퍼티를 가지거나
  let a3: CatOrDogOrBoth = { name: 'Salah', barks: false, wags: true, purrs: false }; // Dog 타입과 Cat 타입안에 선언된 프로퍼티가 모두 존재하면 OK
  
  let b: CatAndDog = { name: 'Cathy', purrs: true, barks: true, wags: true };
  // Cat과 Dog 타입안에 선언된 프로퍼티가 모두 존재해야한다.
  
  console.log(a1); // { name: 'Bonkers', barks: false, wags: true }
  console.log(a2); // { name: 'Boyington', purrs: true }
  console.log(a3); // { name: 'Salah', barks: false, wags: true, purrs: false }
  
  console.log(b); // { name: 'Cathy', purrs: true, barks: true, wags: true }
  ```

6. 배열 : 타입스크립트의 배열도 자바스크립트처럼 특별한 객체이다.

   ```typescript
   let a = [1, 2, 3]; // number []
   var b = ['a', 'b']; // string []
   let c: string[] = ['a']; // string []
   let d = [1, 'a']; // (string | number) []
   const e = [2, 'b']; // (string | number) []
   
   let f = ['red']; //  string []
   f.push('blue'); // string []
   f.push(true); // error! true타입 인수를 string 타입 매개변수에 할당 불가
   
   let g = []; // any []
   g.push(1); // number []
   g.push('red'); // (string | number) []
   
   let h: number[] = []; // number []
   h.push(1); // number []
   h.push('red'); // error! 'red'타입 인수를 'number'타입 매개변수에 할당할 수 없음.
   ```

   : c와  h 를 제외한 모든 변수의 타입은 ts가 정의한다. b, d, e, f 는 배열을 선언할때, 초기화하면서 선언해주었다. 따라서 배열의 타입이 추론되었고, 이 타입에 맞는 값만을 요소로 가질 수 있다. e는 const 키워드로 선언되었지만, 객체와 마찬가지로 ts는 타입을 더 좁게 추론하지 않고 (string | number)로 추론한다. 

   반면 g는 배열을 선언할 때, 초기화로 어떠한 요소도 전달하지 않았다. 이러한 배열은 any []로 ts에 의해 정의된다. 이러한 any 타입의 배열은 정의된 환경을 벗어나면 더이상 타입의 확장이 불가능하다. 하지만 정의된 환경내에서는 원하는 만큼 타입의 확장이 가능하다.

   ```typescript
   let arrB = [1, 'a']; // (string | number) []
   arrB.push(true); 
   // Argument of type 'boolean' is not assignable to parameter of type 'string | number'.ts(2345)
   // arrb는 선언과 함께 1과 'a'로 초기화 되었으므로 (string | number) []타입으로 정의된다. 타입의 확장이 불가능하다.
   
   
   let arrA = []; // any []
   arrA.push(1); // number []
   arrA.push('a'); // (string | number) []
   arrA.push(true); // (string | number | boolean) []
   //현재 스코프에서 선언된 arrA는 선언된 영역인 지금 위치에서 지속적으로 타입의 확장이 가능하다.
   ```

   ```typescript
   const generateArray = () => {
     const newArray = []; // any []
     newArray.push(1); // number []
     newArray.push('string'); // (string | number) []
   
     return newArray;
   };
   
   const newArr = generateArray();
   newArr.push(1);
   newArr.push(true); // Argument of type 'boolean' is not assignable to parameter of type 'string | number'.ts(2345)
   ```

   generateArray  스코프 내에 선언된 newArray는 any []타입으로 정의되었고 newArray.push(1)과 newArray.push('string')에 의하여 number[ ] -> (string | number) [ ] 타입으로 확장되었다. 그리고 이후에 return 되어 정의된 영역을 벗어나고, 이렇게 return된 newArray를 전달받은 식별자newArr는 newArr.push(1)은 오류가 없지만, newArr.push(true);를 호출하게되면, 이미 (string | number) [ ]로 최종 타입 할당된 배열에 추가로 boolean 타입의 값을 요소로 추가할 수 없다는 에러가 발생한다.

   

7. 튜플(tuple) : 배열의 서브타입, 길이가 고정되어있고, 각 인덱스의 타입이 알려져있는 배열의 일종이다. 튜플은 선언시에 타입을 명시해주어야 한다.

   ```typescript
   let a:[number] = [1]; 
   let b:[string, string, number] = ['Min', 'Kim', 29];
   
   b = ['queen', 'king', 30, 'not allowed']; // Type '[string, string, number, string]' is not assignable to type '[string, string, number]'.
    // Source has 4 element(s) but target allows only 3.ts(2322)
   // 3개의 요소만 정의된 튜플 b에 4개의 요소를 허락하지 않는 오류
   ```

   * 튜플의 선택형 요소:  객체 타입과 마찬가지로 ? 를 사용하여 선택형(옵셔널)을 표시한다.

     ```typescript
     const getOptionalElementsArr = (number1: number, number2: number, isOptional: boolean) => {
       type noOptional = [number, number];
       type Optional = [number, number?]; // 선택형 요소 사용
     
       // optional한 배열은 2번째 요소를 갖지 않는다.
       if (isOptional) {
         const newOptionalArray: Optional = [number1];
         return newOptionalArray;
       }
     
       // optional 하지 않은 배열은 2번째 요소를 갖는다.
       const notOptionalArray: noOptional = [number1, number2];
       return notOptionalArray;
     };
     
     const array1 = getOptionalElementsArr(1, 2, true); // 1과 2를 전달하고, optional한 배열을 받는다.
     const array2 = getOptionalElementsArr(3, 4, false);
     
     console.log(array1, array2); // [ 1 ] [ 3, 4 ]
     ```

   * 나머지요소(...)를 사용한 튜플
   
   * ```typescript
     let friends: [string, ...string[]] = ['Sara', ...['Tali', 'Min', 'Loren', 'Kali']];
     console.log(friends); // [ 'Sara', 'Tali', 'Min', 'Loren', 'Kali' ]
     
     let friends2: [string, ...string[]] = ['Sara', 'Tali', 'Min', 'Loren', 'Kali'];
     console.log(friends2); // [ 'Sara', 'Tali', 'Min', 'Loren', 'Kali' ]
     ```
   
   * 읽기전용 배열과 튜플 : readonly 배열 타입을 지원하므로, 이를 이용하여 불변 배열을 만들 수 있다. 불변 배열(읽기전용 배열)은 일반배열과 같지만, 갱신할 수 없다. 명시적 타입 어노테이션으로 만든다.
   
   * ```typescript
     let as: readonly number[] = [1, 2, 3]; // readonly number []
     let bs: readonly number[] = as.concat(4); // readonly number []
     let cs = [1, 2, 3];
     
     cs[2] = 5;
     as[1] = 1; //Index signature in type 'readonly number[]' only permits reading.ts(2542) 오직 읽기만 허용한다.
     
     as = [2, 2, 3]; // 재할당은 가능.
     ```
   
   * ts는 읽기전용 배열과 튜플을 만드는 긴 형태의 선언방식도 지원. 개발자의 기호에 맞게 사용하면 된다.
   
   * ```typescript
     type A = readonly string[]; // readonly string []
     type B = ReadonlyArray<string>; // readonly string[]
     type C = Readonly<string[]>; // readonly string[]
     
     type D = readonly [number, string]; // readonly [number, string]
     type E = Readonly<[number, string]>; // readonly [number, string]
     ```
   
8. null, undefined, void, never

:없음을 표현하는 방식으로 자바스크립트에서는 null(없음을 개발자가 명시적으로 지정해주는 경우에 할당한다.)과 undefined(아직 아무 값도 할당되지 않았음을 표현하거나, 혹은 실제로 그 상태인경우)로 표현한다. 타입스크립트에서는 void와 never도 지원한다. void는 어떠한 함수가 return문이 없을 경우(아무런 값을 반환하지 않는 함수인 경우)에 사용되는 반환타입이다. (대표적으로 console.log 메서드)

```typescript
const returnNothingFunc = () => {
  const a = 1;
  const b = 2;
  console.log(a + b);
};
// 어떠한 값도 반환하지 않는 함수. void를 반환하는 함수이다.
```

never 타입은 절대 반환하지 않는다는 뜻을 지닌 함수의 타입이다. (에러를 던지거나, 무한루프를 도는 함수)

```typescript
const throwErrorFunc = () => {
  throw TypeError('you always get an Error with me.');
}; // never를 반환하는 함수

const startInfiniteLoop = () => {
  while(true) {
    // 무한루프를 돌며 영원히 실행된다. never를 반환하는 함수
  }
};
```



9. 열거형(enum): 해당 타입으로 사용할 수 있는 값을 열거한다. 키에 값을 할당하는 순서가 없는 자료구조. (키가 컴파일 타임에 고정된 객체)

   ```typescript
   enum Country {
     England,
     Spain,
     Argentina
   }
   // 열거형의 이름은 단수명사, 첫문자는 대문자로 사용한다. 키도 앞글자를 대문자로 표시한다.
   // 위처럼 key에 value를 할당하지 않으면 TS가 자동으로 각 멤버에 적절한 숫자를 추론하여 할당한다.
   
   //다음처럼 
   // {
   //   '0': 'England',
   //   '1': 'Spain',
   //   '2': 'Argentina',
   //   England: 0,
   //   Spain: 1,
   //   Argentina: 2
   // }
   ```

   명시적으로 열겨형의 각 멤버에 숫자를 할당할 수도 있다.

   ```typescript
   enum Country {
     England = 1,
     Spain = 33,
     Argentina = 23,
   }
   
   // {
   //  '1': 'England',
   //  '23': 'Argentina',
   //  '33': 'Spain',
   //  England: 1,
   //  Spain: 33,
   //  Argentina: 23
   // }
   ```

   열거형은 점 또는 괄호 표기법으로 열거형 값에 접근이 가능하다.

   ```typescript
   enum Country {
     England = 1,
     Spain = 33,
     Argentina = 23,
   }
   
   console.log(Country[1]); // England
   console.log(Country[33]); // Spain
   console.log(Country[23]); // Argentina
   console.log(Country['England']); // 1
   console.log(Country['Korea']); // Element implicitly has an 'any' type because index expression is not of type 'number'.ts(7015) Korea는 선언되지 않았으므로 string대괄호 표기법으로 접근이 불가.
   console.log(Country.Spain); // 33
   
   console.log(Country[4]); // undefined
   //Country안에 선언되지 않은 Country[4]도 TS는 접근을 허용한다. -> 더 안전한 타입인 const enum을 이용하면 막을 수 있다.
   ```

   ```typescript
   const enum Country {
     England = 1,
     Spain = 33,
     Argentina = 23,
   }
   
   console.log(Country[1]); // A const enum member can only be accessed using a string literal.ts(2476)
   console.log(Country['England']);
   console.log(Country[4]); // A const enum member can only be accessed using a string literal.ts(2476)
   ```

const enum을 사용하면 number를 이용하여 enum 의 값에 접근하는것이 불가능해지며, 이로인하여 선언된적이 없는 Country[4]에 접근이 허용되는 것을 막을 수 있다. const enum member는 오직 문자열 리터럴 방식으로만 접근이 가능하다.

열거형은 안전하게 사용하기 까다롭기 때문에 열거형을 사용하는 것은 매우 지양한다.