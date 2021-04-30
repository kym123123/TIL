# 클래스와 상속

## javascript ES6 클래스와의 차이점

: javascript ES6의 클래스는 아래의 코드처럼 프로퍼티는 생성자 안에 선언해야 한다. 메서드는 생성자 밖에 선언될 수 있지만, 프로퍼티는 그렇지 않다.

```javascript
class Man {
  constructor(name) {
    this.name = name;
  }
  
  sayHi() {
    console.log(`Hi! I'm ${this.name}`);
  }
}
```

위처럼 name 프로퍼티는 반드시 constructor 생성자 안에 선언된다.

그러나 typescript에서는 위 코드는 컴파일 에러가 발생한다.

`Property 'name' does not exist on type 'Man'. ts(2551)`

typescript의 클래스는 클래스 몸체에 프로퍼티를 먼저 선언해 주어야 한다.

```typescript
class Man {
  private name: string; // name 프로퍼티 사전선언
  
  constructor(name: name) {
    this.name = name;
  }
  
  sayHi() {
    console.log(`Hi! I'm ${this.name}`);
  }
}

const man = new Person('Min');
man.sayHi(); 
//Hi! I'm Min
```



## 접근 한정자(접근 제한자)

1. public: 어디에서나 접근 가능하다.  접근 한정자를 설정하지 않을경우의 접근 수준의 기본값.
2. protected: 이 클래스와 서브클래스의 인스턴스 내에서만 접근이 가능하다.
3. private: 이 클래스의 인스턴스에서만 접근이 가능하다.



* 클래스는 구체 클래스, 추상 클래스로 분류된다. 추상 클래스는 추상 메서드와 추상 프로퍼티를 가질 수 있다.
* 클래스는 인스턴스 프로퍼티도 가질 수 있고, 이 프로퍼티들은 한 가지 한정자를 갖는다. 생성자의 매개변수나, 프로퍼티 초기자에도 한정자를 사용할 수 있다.
* 인스턴스 프로퍼티 선언시 readonly 추가 가능하다. (constructor 내부에서 프로퍼티 선언시에만 사용이 가능하다. ) -> 주로 상수로 사용할 값을 선언시 사용한다.



* 접근 한정자는 생성자 파라미터에 선언할 수도 있다. 접근 제한자가 사용된 생성자 파라미터는 암묵적으로 클래스 프로퍼티로 선언되고, 생성자 내부의 별도의 초기화가 없어도 초기화가 수행된다.

* ```typescript
  class Foo2 {
      constructor(public x: string, protected y: string, private z: string) {}
  }
  ```

  ```typescript
  class Foo2 {
      constructor(x: string, protected) {}
  }
  // 접근 한정자를 선언하지 않으면, 생성자 파라미터는 생성자 내부에서만 유효한 지역변수가 되어, constructor 외부에서는 참조가 불가능하다.
  ```



## 인터페이스

클래스는 인터페이스를 통해 사용하는 경우가 많다.

인터페이스도 타입별칭처럼, 타입에 이름을 지어주는 것이다. 이를 사용하면 타입을 깔끔하게 정의가 가능하다. 인터페이스는 타입별칭을 사용한 모든곳에 대체가 가능하며, 타입별칭, 인터페이스 둘 다 형태(Shape)를 정의한다. 두 형태 정의는 서로 할당이 가능하다. -> 인터페이스는 형태를 정의하느 수단.

* 인터페이스와 타입별칭의 차이점

  1. 타입 별칭은 오른쪽에 타입표현식, 유니온, 인터섹션과 같은 타입연산자를 포함한 타입이 나올수 있다. 
  2. 인터페이스는 타입연산자를 사용할수 없고 클래스처럼 extends 키워드를 사용하여 상속 받을 인터페이스를 지정해 줄수 있다.

  ```typescript
  type Food = {
      calories: number
      tasty: boolean
  }
  
  type Sushi = Food & {
      salty: boolean
  }
  
  type Cake = Food & {
      sweet: boolean
  }
  // 타입 연산자 사용 가능. 오른편에는 타입 연산자, 타입의 형태가 나온다.
  ```

  ```typescript
  interface Food {
      calories: number
      tasty: boolean
  }
  
  interface Sushi extends Food {
    	salty: boolean
  }
  
  interface Cake extends Food {
    sweet: boolean
  }
  
  // 타입연산자 사용x, extends 키워드로 다른 인터페이스와의 상속관계 지정 가능
  ```
  * 인터페이스는 똑같은 이름을 가진 두 가지가 있으면, ts가 자동으로 둘을 하나의  interface로 합친다. 다만, 같은 프로퍼티명을 가진 프로퍼티가 다른 타입을 가지며 충돌하면 안된다.


## implements

클래스 선언시 implements 키워드를 이용하여, 특정 인터페이스를 만족 시키도록 할 수 있다. 

```typescript
interface Animal {
    readonly name: string;
    eat(food: string): void;
    sleep(hours: number): void; 
}

class Cat implements Animal {
    name = 'mmm';

    eat(food: string) {
        console.info('Ate some', food, 'Mmm!');
    }
    
    sleep(hours: number) {
        console.info('Slept for', hours, 'hours');
    }
}
// 인터페이스로 프로퍼티를 정의할 수 있지만, 가시성 한정자와 static 키워드는 사용 불가.
// readonly 키워드는 사용가능
```

클래스는 하나의 인터페이스만을 구현해야 하는 것은 아니며, 필요하면 여러 인터페이스를 구현할 수 있다. , 를 이용하여 인터페이스 여러개를 연결한다.

```typescript
interface Animal {
    readonly name: string;
    eat(food: string): void;
    sleep(hours: number): void; 
}

interface Feline {
    meow(): void
}

class Cat implements Animal, Feline { // Animal, Feline 인터페이스 구현
    name = 'mmm';

    eat(food: string) {
        console.info('Ate some', food, 'Mmm!');
    }

    sleep(hours: number) {
        console.info('Slept for', hours, 'hours');
    }

    meow() {
        console.info('Meow')
    }
}
```



## 추상클래스 상속 vs 인터페이스 구현

* 인터페이스: 형태를 정의하는 수단,  -> 값 수준으로 객체, 배열, 함수 , 클래스, 클래스 인스턴스를 정의할 수 있음. 인터페이스는 아무런 자바스크립트 코드를 만들지 않고, <u>컴파일 타임에만 존재한다.</u>
* 추상 클래스: 오직 클래스만 정의 가능. 추상 클래스는 런타임에 자바스크립트 클래스 코드를 만든다. <u>생성자와 기본 구현을 모두 가질 수 있다. 프로퍼티와 메서드에 접근 한정자를 지정 할 수있다.</u>

사용 상황: 여러 클래스에서 공유하는 구현 -> 추상 클래스 이용, 단순히 이 클래스는 어떤것이다 를 정의하는 것이 목적이면 인터페이스를 사용한다.





## 클래스, 구조 기반 타입 지원

ts는 클래스를 비교할 때, 다른 타입과 달리 이름이 아니라 구조를 기준으로 삼는다. -> 자신과 똑같은 프로퍼티와 메서드를 정의하는 기존의 일반 객체를 포함해 클래스의 형태를 공유하는 다른 모든 타입과 호환된다.

단 가시성 한정자를 이용하여 클래스 필드를 갖는다면 주의해야 한다. 클래스에  private 또는 protected 필드가 있으면, 할당하려는 클래스 혹은 서브클래스의 인스턴스가 아니면 할당이 불가능하다.

```typescript
class A {
    private x = 1
}

class B extends A {}

function f(a: A) {}

f(new A()) // 할당 가능
f(new B()) // 할당 가능

class C {
    private x = 1;
}

f(new C()) // 할당 불가능 에러발생
// Argument of type 'C' is not assignable to parameter of type 'A'.
//Types have separate declarations of a private property 'x'.
```



## 클래스는 값과 타입을 모두 선언한다

ts의 거의 모든 것은 값 아니면 타입이다.

```typescript
if (a + 1 > 3) // a는 값으로 추론 됨
let x: a = 3; // a는 타입으로 추론 됨
```

위처럼 같은 식별자 명을 가지고 있어도, ts가 해당식별자가 어떻게 사용되었는지를 판별하여 알아서 타입 혹은 값으로 추론하고 각 다른 네임스페이스에 생성한다. (타입 네임스페이스 or 값 네임스페이스)

클래스와 열거형은 타입 네임스페이스에 타입을, 값 네임스페이스에 값을 동시에 생성한다.

```typescript
class C { }
let c: C = new C(); // c:C는 타입 C, new C()는 값 C로 판별

enum E { F, G }; // E는 열거형의 타입
let e: E = E.F // 문맥상 E.F의 E는 값 E로 판별
```

ts가 위의 코드를 문맥에 따라서 알아서 타입 / 값으로 판별한다. 







