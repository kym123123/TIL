# SOLID

코드를 오래 지속할수 있도록 하기 위해서 사용, 

## Single-responsibility principle

한개의 컴포넌트는 한가지 일만 하도록 작성. 



## Open-closed principle

변형을 피하기 위해서 컴포넌트는 닫혀있어야 하고, (self-closed), 확장성을 위해서는 열려있어야 한다.(Open). self-closed 태그를 이용하여 컴포넌트를 구성하고 몇가지 props로 내려받아 컴포넌트 내부를 구성하게 되면, 새로운 기능을 위하여 확장하기 너무 불편해진다. props를 추가로 내려주어야 하며, 그것을 받아서 내부에서 구성해야 한다. 한두가지가 추가되면 좋겠지만, 20가지 이상이 추가된다면, props의 타입을 정의해주고 복잡한 작업을 위해서 prop을 다루기 매우 까다로워진다.

```react
<Modal
  modalTitle="Register"
  modalLabelText="Registration form"
  openButtonText="Register"
  dismissable={true}
  contents={
    <LoginForm
      onSubmit={register}
      submitButton={<Button variant="secondary">Register</Button>}
    />
  }
/>
```

```react
<Modal>
  <ModalOpenButton>
    <Button variant="secondary">Register</Button>
  </ModalOpenButton>
  <ModalContents aria-label="Registration form">
    <ModalDismissButton>
      <CircleButton>
        <VisuallyHidden>Close</VisuallyHidden>
        <span aria-hidden>×</span>
      </CircleButton>
    </ModalDismissButton>
    <ModalTitle>Register</ModalTitle>
    <LoginForm
      onSubmit={register}
      submitButton={<Button variant="secondary">Register</Button>}
    />
  </ModalContents>
</Modal>
```

첫번째 컴포넌트가 더 깔끔해보이지만, 확장성에는 좋지않다. 두번째 컴포넌트는 미래에 기능을 추가하기 더 좋으며 props로 고생할 일이 줄어든다.

## **Interface segregation principle**





-----

아래의 SOLID원칙은 따로따로 골라서 사용하는 코드작성원칙이 아니다.

## S: Single Responsibility Principle (단일 책임 원칙)

한개의 함수나 ,클래스는 한가지 기능만 해야 한다. 그렇게 하면 한 기능을 디버깅하거나, 살짝 변경하기 위해서 코드를 수정하거나 할 경우에 여러 함수와 클래스를 옮겨가며 코드를 봐야할 일이 없어지고, 그렇기 때문에 스파게티 코드를 쓰지 않도록 해준다.

* Formal Definition: Every module or class should have responsibility over a single part of the functionality provided by the software, and that responsibility should be entirely encapsulated by the class, module or function. All its services should be narrowly aligned with that responsibility
* Informal Definition:  A class should have only one reason to change - robert C Martin
* react Components: Sole responsibility is to display the state of the app
* redux reducers: Sole responsibility is to update the state of the application
* Api Services: Sole responsibility is to fetch state from the backend of the application
* Hooks: single function that is reponsible for accomplishing one task(useState is responsible for maintaining local state(only))

---

## O: Open-Closed Principle (개방 폐쇠 원칙): 

​	the open portion: we have the ability to add new things, without having to manke any changes in a class or module...

새로운 것을 추가할때, 기존에 존재하는 함수나 클래스를고쳐야지 새로운것이 같이 돌아가도록 짜는 것이 아니라, 기존에 존재하는 함수나 클래스가 새로 추가한 코드를 문제없이 작동 시킬 수 있도록 코드를 짜는것.

따라서 코드를 추가하는 것이 가능하며, 그 추가된 코드가 작동되기 위해서 기존의 코드가 변화가 없어야 한다는 것이다. (switch문 혹은 if문을 크게 사용한 코드는 항상 이 원칙을 위배한다. 새로운 코드를 추가하면 그 case에 맞는 switch문, 혹은  if문을 추가해야하기 때문이다.)

* Formal Definition: software entities (classes, modules, functions...etc) should be open for extension, but closed for modification.
* Informal Definition: You should be able to add new funtionality to existing entities without having to introduce the risk of changing existing behavior.
* class Inheritance : If I have a TimeoutService class that is provided to me by a librarym and I want it to return promises. Instead of wrapping it, I can extend it and add my functionality in a TimeoutPromiseService
* Coding to Interfaces: If my React component has a dependency on a service that gets data from the backend, I should abstract an interface (ex, IHttpService instead of just HttpService) so that if (when) requirements change, my component is Open to changes in how the IHttpService is implemented, but Closed to changes in the specification.
* The Children Prop: Used to inject different functionality within the same component without a need to change the component itself

---

## L: Liskov Substitution Principle (리스코프 치환 원칙): 모든 서브클래스는 슈퍼클래스가 들어가는 위치에(인자로 전달되는 위치) 대체로 사용될 수 있어야 한다.

* Formal Definition: If <u>S</u> is a subtype of <u>T</u>, then objects of type <u>T</u> may be replaced with objects of type <u>S</u> without altering any of the desirable properties of the program (correctness, task, performed)
* Informal Definition: When extending a class or implementing an interface, my new class must be able to be used everywhere the parent class or interface is used without unexpected consequences / side effects
* Example) Imagine I have a class HttpService
  * New requirements come in and say that we are making too many requests and the backend can't handle it.
  * Targeted solution: I will just cache some responses to data that we know won't be changing very often.
  * Now we have some components that need to be able to always get up to date information, and some that are ok to receive cached Information.
  * All of our components are using the Httpservice currently
    * Think about our past design flaws and how we should have abstracted an interface instead of relying on concrete implementation.
  * Create a new HttpCachingService which extends Httpservice
  * Override the methods that we want to add caching to
  * Provide HttpCachingService to the components that require caching and are currently using HttpService

---

## I: Interface Segregation Principle (인터페이스 분리 원칙)

엄청 큰 클래스가 있을 경우, 그 안에 많은 메서드, 프로퍼티가 포함되어 있다. 이 클래스를 상속받아서 생성된 서브 클래스가 만약 그 클래스 안의 어떠한 메서드나 프로퍼티를 사용하지 않으면서도 그것을 포함하고 있다면 인터페이스 분리 원칙을 위배하는 것이다.

* Formal Definition: Many Client-specific interfaces are better than one general-purpose interface
* Informal Definition: A client should only have knowledge of the methods which is actually going to need.
  * If I want a banana, Don't give me a gorilla holding a banana. just give me the banana.
* 90's and Xerox has a problem
  * All of their software for printers uses a job class, that is responsible for every type of job a printer can do.
  * Changes to the software were becoming impossible to manage as the job class continued to grow with every new feature. 
  * Robert C.Martin finds a solution
  * New interfaces are written for each type of job that the printer can perform.
  * The wrapper classes call into the old job class(which now implements all of the smaller interfaces)
  * New functionality for each job is written in the smaller classes.
  * Finally, all of the clients for these jobs can use only the interfaces that they require - no more gorilla, just the banana
* Redux Reducers: Making use of combineReducers allows us to segregate each reducer to only act on actions and state for a single logical unit.
* React vs React DOM : the separation of React and ReactDOM into different packages is a large-scale implementation of this principle, react is responsible for the the virtual DOM and translating JSXm while ReactDOM does the heavy lifting of DOM manipulations.

---

## D: Dependency Inversion Principle (의존성 역전 원칙)

 한 store에 있는 정보를 갖고, 어떤 api를 통해서 결제를 하는 (ex. paypal) 메서드를 작성하는 경우. paypal api에 해당 store가 의존하게 된다. 그러면 코드를 paypal.pay(), paypal.refund() 이런식으로 직접 의존하게 작성하게 될텐데, 그렇게 의존시키지 않고 paypal과 직접 연결되어 결제하는 중간의 api를 작성하여, 그 중간 api에 store가 의존하도록 시킨다. 이것이 의존성 역전 원칙이다.

* Formal Definition: High-level modules should not depend on low-level modules. both should depend on abstractions (e.g. interfaces)
* Informal Definition: Use interfaces for dependencies - not concrete classes.
  * An interface's method signatures should not have details related to specific implementations.(No bark method in IAnimal. Cause It's only for Dog)
  * ![image-20210630025301690](/Users/yongminkim/Library/Application Support/typora-user-images/image-20210630025301690.png)

* react: No component or function should care about how a particular thing is done.
* 리액트에서는 쉽게 생각 했을경우, s원칙과 비슷하다. 컴포넌트가 한가지 작은 모듈에 의존을 한다고 해서 (ex. axios) 그 모듈을 해당컴포넌트 내에서 직접적으로 의존해서 사용하면, 그 컴포넌트가 하는 일 외에도 axios.get()...등의 코드를 보여주게 된다. 이는 컴포넌트가 하는 일외에 너무나 많은 다른 일을 보여주는 것이므로 직접 의존하여 코드의 수가 길어지면 리팩토링 하기 복잡해진다. 따라서  axios로 데이터를 가져오는 custom hook으로 분리해 내어 컴포넌트가 하는일에 필요한 data만 hook에서 얻어오도록 수정시며 주면 된다. 그렇게 하면 이 custom hook이 위의 그림에서의 interface A가 되는 것이다. intermediate thing..(컴포넌트의 data의존성이 axios 모듈로부터 custom hooks로 바뀌고, 그 axios모듈의 상세적인 하는일은 관심을 안줘도 된다.)