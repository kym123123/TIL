# Error를 없애기 위한 코드작성



## hasOwnProperty

* hasOwnProperty의 직접적인 사용 금지

  ```javascript
  const foo = { bar: 1 };
  console.log(foo.hasOwnProperty('bar')); // true
  ```

  위의 상황에서는 foo가 객체로 존재하기 때문에 foo.hasOwnProperty 메서드의 동작에 이상이 없이 true가 출력된다. hasOwnProperty는 마침표 앞이 객체라는 전제하에서 동작하기 때문에 foo가 undefined나 null처럼 객체가 아니게 되는 순간 에러를 출력하여 프로그램의 동작이 멈추게 된다.

  `foo가 객체라는 전제가 깨지는 순간 앱이 정지된다.`

  ```javascript
  let foo = { bar: 1 };
  foo = undefined;
  console.log(foo.hasOwnProperty('bar'));
  // TypeError: Cannot read property 'hasOwnProperty' of undefined
  ```

  ```javascript
  let foo = { bar: 1 };
  foo = null;
  console.log(foo.hasOwnProperty('bar'));
  // TypeError: Cannot read property 'hasOwnProperty' of null
  ```

* 위의 메서드를 사용하고 싶은경우는 아래와 같이 코드를 작성하면 오류를 발생시키지 않고 안전성을 높일수 있다.(약간의 가독성은 떨어지지만 장점이 훨씬 크다. 오류 발생이 없으니까)

  ```javascript
  const foo = { bar: 1 };
  console.log(Object.prototype.hasOwnProperty.call(foo, 'bar')); 
  // true
  ```

  call 메서드를 사용하여 호출한다. (call 메서드 없이 Object.prototype.hasOwnProperty로 호출하게되면  this가 Object.prototype을 가리키게 된다.) 이러한 방식으로 코드를 작성하면 hasOwnProperty 마침표 앞이 항상 객체라는 것이 보장되기 때문에 에러가 발생하지 않아 앱이 동작을 멈추는 상황이 없다. (Object.prototype은 언제나 객체니까....)



## `__proto__`와 Object.getPrototypeOf, Object.setPrototypeOf

*  `__proto__`접근자 프로퍼티를 사용하는 것보다 Object.getPrototypeOf 메서드와 Object.setPrototypeOf 메서드를 이용하는 것이 좋다. 

  * 1) `__proto__`는 체인상의 최상단에 위치할 경우 상속받아 사용할 수 없다.

    객체가 프로토타입의 최상단에 위치하는 경우 ( [[Prototype]] = null 인 경우) `__proto__`접근자 프로퍼티로 접근하게 되면 에러가 발생한다. 이때 Object.getPrototypeOf 메서드를 사용하면 에러가 발생하지 않는다.

    ```javascript
    const me = Object.create(null); // __proto__가 null을 가리키는 객체 생성
    const parent = { // me객체의 프로토타입으로 교체할 객체 parent
      sayHello () {
        console.log(`Hi! My name is ${this.name}`);
      }
    };
    me.__proto__ = parent; // me 객체의 프로토타입 parent로 교체
    me.sayHello(); // TypeError: me.sayHello is not a function
    // me 객체는 체인의 종점에 위치하기 때문에 __proto__접근자 프로퍼티를 상속받을수 없다.
    ```

    * 대안

    ```javascript
    const me = Object.create(null);
    const parent ={
      sayHello () {
        console.log(`Hi! My name is ${this.name}`);
      }
    };
    Object.setPrototypeOf(me, parent);
    me.sayHello(); // Hi! My name is undefined
    ```

  * 2) `__proto__`보다 위의 두가지 메소드가 가독성이 좋다.

  * 3) `__proto__`는 최근까지 비표준이었다.