# React useReducer Hook simplified

to be honest useReducer hook feels like unga bunga hook if you are using it for the first time, but this is simple uncluttered article to get familiar with useReducer hook.

If you have used `useState` hook for managing the state like simple strings, arrays, objects you are going to have good time here. `useReducer` is used for this exact purpose that is for managing state. but it gives more flexibility for us to control and update the state.

as a matter of fact useState is built on top of `useReducer` you can think of `useState` hook as a simplified version of `useReducer`

**let's see how to use `useReducer` hook first. then we will deep dive into this hook**
// Img

```jsx
const  countReducer = (prevState, action) =>  {
	console.log(action)
	// logs whatever we passed in dispatch(/* smth */)
	// Ex: if we dispatch('bob') in <Counter />, output: 'bob'
	// if we do dispatch(1) in <Counter />, output: 1
	return action // this value is our `newState` that reflects on `count` we get from useReducer
}

function  Counter({initialCount: 0}) {
	const [count, dispatch] = useReducer(countReducer, initialCount)

	return  <button  onClick={() =>  dispatch(count + 1)}>{count}</button>
}
```
Here we just made a counter which we can increment on a button click.
- we will get `[state, dispatch]` array from `useReducer`
- `useReducer` takes *Reducer function*, *initial state* as arguments
- *Reducer function* accepts two arguments `reducerFn(previousState, action)` and returns `newState`

You have to focus on terms **state**,  **dispatch**, **action**, **reducerFunction** here
- **state:** *count* is the state we want to manage here
- **dispatch:** dispatch is a function through which we set the '**action** value'  that we accept through our reducer function.
- **action:** action is a value whatever we passed through function. 
- **reducerFunction:** it is a function through which we take control over and manage our state.

**flow example:** 
1. let's say we clicked the button which calls dispatch(count + 1) 
2. this dispatch value go through countReducer function where the new state will be returned.
3. now `action` value is equal to `count + 1` which is 0 + 1. (`action` = 1)
4. we are returning the `action` value in the reducerFunction 
5. so our <Counter /\> will rerender because of our state is updated.
6. now useReducer gives `[count, dispatch]`  array where, our count value has updated to `1`

![image](https://user-images.githubusercontent.com/91829843/187974612-2d1b0f0e-eedc-4312-998b-c24dfc65c7dd.png)


 **let's add some more things to our counter example with useReducer**
 ```jsx
function countReducer(prevState, action) {
  const {type, step} = action
  switch (type) {
    case 'increment': {
      return {
        count: prevState.count + step,
      }}
    case 'decrement': {
      return {
        count: prevState.count - step,
      }}
    default: {
      throw new Error(`Unsupported action type: ${type}`)
    }}
}

function Counter({initialCount = 0, step = 1}) {
  const [state, dispatch] = React.useReducer(countReducer, {
    count: initialCount,
  })
  
  const {count} = state
  const increment = () => dispatch({type: 'increment', step})
  const decrement = () => dispatch({type: 'decrement', step})
  return {
	  <>
	  <h1>{count}</h1>
	  <button onClick={increment}>increment</button>
	  <button onClick={decrement}>decrement</button>
	  </>
  }
}
```


[Here is the codesandbox](https://codesandbox.io/s/still-firefly-d1jzc0?file=/src/Counter.js)
In this example, we are taking advantage of how `dispatch` and `action` work unlike the previous example.
whatever we give to the dispatch, the same will be reflected on `action` then we used switch case. if `action` is a particular value to do a particular operation. this is how most people use action, to navigate and tell what the reducer function should do to give us a new state.
 
 now, I'm sure you got a good understanding of how useReducer works. Let's see some more examples and scenarios to learn more about this hook.
 
 Let's add an input field to our previous example..
 ```jsx
import "./styles.css";
import React from "react";

function countReducer(prevState, action) {
  const { type, step } = action;
  console.log(prevState);
  switch (type) {
    case "increment": {
      return {
        count: prevState.count + step
      }}
    case "decrement": {
      return {
        count: prevState.count - step
      }}
    case "usernameChange": {
      return {
        username: action.username
      }}
    default: {
      throw new Error(`Unsupported action type: ${type}`);
    }}
}

export default function Counter({ initialCount = 0, step = 1 }) {
  const [state, dispatch] = React.useReducer(countReducer, {
    count: initialCount,
    username: ""
  });

  const { count, username } = state;
  const increment = () => dispatch({ type: "increment", step });
  const decrement = () => dispatch({ type: "decrement", step });
  const handleChange = (event) => {
    dispatch({ type: 'usernameChange', username: event.target.value })
  }
  return (
    <div className="App">
      <h1>{count}</h1>
      <button onClick={increment}>increment</button>
      <button onClick={decrement}>decrement</button>
      <div>
        <label> Username: 
          <input type={"text"} value={username} onChange={handleChange} />
        </label>
      </div>
      <h1> {username} </h1>
    </div>
  );
}

```

here we ahve added `username` to our state. now we can manage our state seamlessly
but hang on.. we have a bug in the above code.
I did it intentionally to reach it in your core memory XD because it is a very common mistake people do.
the bug here is, whenever you change a state, for example take  `increment` case
```jsx
    case "increment": {
      return {
        count: prevState.count + step
      }}
```

we are esentially overriding the whole state with just count in the object. the new state will set to `{cont: prevState.count + step}` we loose our `username` state. 
This is why we should spread the previous state in the object we are returning.
**this is the correct way of doing it**

```jsx
    case "increment": {
      return {
      ...prevState,
        count: prevState.count + step
      }}
```
We should always spread the previous state value in the return object. else we'll end up overriding bunch of other states.

[Here is the codesandbox for this example](https://codesandbox.io/s/still-firefly-d1jzc0?file=/src/Counter2.js)

**tip :**  If you are managing similar state with the `useState` hook you can do like this
```jsx
const  [countState, setCountState] =  React.useState({ count:  0, name:  ""  });
const handleClick = () =>
  setCountState((prevState) => {
    return { ...prevState, count: prevState.count + 1 };
  });
``` 
oh wait a min..
if we can do this whole state thing simply using useState hook what's the point of using this complicated stuff?!!
yeah right, you don't need to use this if useState can get the job done. most people make use of useReducer in codebases for the sake of maintainability, readability and to have better control over the state.

### When to use useReducer: 

it's wise to use useReducer when one element of your state relies on the value of another element of your state in order to update or when the state consists of more than primitive values, like nested object or arrays. if not, you are better off using `useState`

**tip :** do not mix up and add lots of unrelated states into a single reducer. whenever a single state changes you rerender the components which are using this state managed by reducer to get the whole state and UI updated. Imagine one of the component that is so heavy to render and takes lots of resources and time and you re render it unnecessarly. This is why you should only put states which are interrelated together in a single reducer and make separate reducers in order to avoid unnecessary rerenders.
