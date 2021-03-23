# React interactivity:  Events and state

## Handling event

In React, we write event handlers directly on the elements in JSX expressions

```react
<button type="button" onClick={() => alert("hi!")}>Say Hi!</button>
```

The value of onClick attribute is a function that triggers a simple alerts. In JSX, these event attributes,such as onclick, on mouse, have to be written in camel-Case. Otherwise JSX will not recognize onclick as a standard onclick handler. All browser events follow this format in JSX.

In React applications, interactivity is rarely confined to just one component. Events that happen in one component will affect other parts of the app. 



### callback prop

sometimes we might be needed to pass some information from child to parent component. But <u>we can't pass information (or Data) from child to parent in the same way as we pass data from parent to child using standard props.</u> Instead, <u>we can write a function in parent component that will expect some data as an input, then pass it to child component as a prop.</u> Once we have a callback prop, we can call it inside the child component to send the right data to parent component.

```react
function addTask(name) {
  alert(name);
}

<Form addTask={addTask} /> // put addTask function as a prop of child Components "Form"
// The prop can have whatever name I want, but it would be better for me to name it "addTask". //Because it matches the name of the function as well as what the function will do.
```

Another common convention we might well come across in React code is to prefix callback prop names with "on", followed by the name of the event that will cause them to be run. 



## State and the useState hook

Props come from the parent of a component. <u>But it's not possible to update the props that a component receives(only to read them)</u>. We can ask a component to track some of its own data for us. Data such as this, which a component itself owns, is called state. state is another powerful tool for React because components not only own state, but can update it later.

<u>React provides a variety of special functions that allow us to provide new capabilities to components, like state. These functions are called **hooks**,</u> <u>and the **useState hook**, as its name implies,is precisely the one we need in order to give our component some state.</u> 

To use a React hook, we need to import it from the react module. as follows.

```react
import React, { useState } from "react";
```

This allows us to import the useState( ) function by itself, and utilize it anywhere in the file where it's written. useState( ) creates a piece of state for a component. and its only parameter determines the initial value of that state.

It returns two things. 1) the state and 2) a function that can be used to update the state later.

```react
const [name, setName] = useState('Use hooks!');
```

What's going on here?

1. Setting the initial `name` value as "Use hooks!"
2. Defining a function whose job is to modify `name` called setName( ).
3. `useState()` returns these two things, so we are using array destructuring to capture them both in separate variables.

### Reading state

```react
import React, { useState } from 'react';

function Form(props) {
  const [name, setName] = useState('Use hooks!');

  function handleSubmit(e) {
    e.preventDefault();
    props.addTask("say hello!");
  }

  return (
    <form onSubmit={handleSubmit}>
        <h2 className="label-wrapper">
          <label htmlFor="new-todo-input" className="label__lg">
            What needs to be done?
          </label>
        </h2>
        <input
          type="text"
          id="new-todo-input"
          className="input input__lg"
          name="text"
          autoComplete="off"
          value={name} // {name} having "Use hooks!" from useState()
        />
        <button type="submit" className="btn btn__primary btn__lg">
          Add
        </button>
      </form>
  );
}

export default Form;
```

The browser will render 'Use hooks!' inside the input above. 

```react
const [name, setName] = useState('');
//change use hooks to an empty string. this is what we want for our initial state.
```

To change name's value, we use setName function here.

```react
console.log('name') // ''

setName('try!'); // name's value changed by calling setName function with an argument 'try!'
console.log(name); // try!. updated with 'try!'
```



