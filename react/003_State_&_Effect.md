# 3. **State & Effects**

- `useState` with lazy init and updater function
- `useReducer` for complex logic
- `useEffect` cleanup, dependency arrays, custom hooks
- `useLayoutEffect` vs. `useEffect`

### \#\# High-Importance Questions 🌟

---

#### **1. Explain how the `useState` hook works. What is lazy initialization and why would you use it?**

The **`useState`** hook is a function that lets you add state to a functional component. When you call `useState`, you are telling React that your component needs to remember something.

It works like this:

- You call `useState` with an **initial value**.
- It returns an array containing two elements: the **current state value** and a **setter function** to update that value.
- When you call the setter function with a new value, React will re-render the component and all of its children with the updated state.

**Lazy initialization** is a performance optimization. If creating the initial state is computationally expensive (e.g., reading from `localStorage` or performing a complex calculation), you don't want to do it on every single render.

To use lazy initialization, you pass a **function** to `useState` instead of a value. React will only call this function **once**, during the initial render, to get the initial state. On all subsequent re-renders, the function is ignored.

**Code Example:**

```jsx
import React, { useState } from "react";

// This expensive function will only run ONCE.
function getInitialState() {
  console.log("Calculating initial state...");
  // Imagine this is a heavy computation
  return 0;
}

function Counter() {
  // Using lazy initialization by passing a function
  const [count, setCount] = useState(getInitialState);

  return <button onClick={() => setCount(count + 1)}>Count: {count}</button>;
}
```

---

#### **2. What is the updater function form in `useState`? When and why is it useful?**

The **updater function** is an alternative way to set state. Instead of passing a new value directly to the setter function, you pass a **function** that receives the previous state as its argument and returns the new state.

`setCount(prevCount => prevCount + 1);`

**Why it's useful:**
This is essential when your new state depends on the previous state. React batches state updates for performance. If you have multiple state updates in the same event handler, the `count` value from the component's scope might be "stale" and not the most up-to-date version.

The updater function guarantees that you are always working with the most recent state value, preventing bugs related to stale state in asynchronous or batched updates.

**Code Example:**

```jsx
function Counter() {
  const [count, setCount] = useState(0);

  function handleTripleClick() {
    // 👎 This is buggy. All three calls use the same 'count' value (0).
    // The final result will be 1, not 3.
    // setCount(count + 1);
    // setCount(count + 1);
    // setCount(count + 1);

    // 👍 This is correct. Each function receives the latest state.
    setCount((prevCount) => prevCount + 1); // prevCount is 0, returns 1
    setCount((prevCount) => prevCount + 1); // prevCount is 1, returns 2
    setCount((prevCount) => prevCount + 1); // prevCount is 2, returns 3
  }

  return <button onClick={handleTripleClick}>Triple Increment</button>;
}
```

---

#### **3. Describe scenarios where `useReducer` is preferred over `useState`. How does `useReducer` work?**

**`useReducer`** is an alternative to `useState` for managing state. You typically prefer `useReducer` in these scenarios:

- **Complex State Logic**: When you have state that involves multiple sub-values or when the next state depends on complex logic based on the previous one.
- **Interrelated State**: When changes to one piece of state often trigger changes in another.
- **Performance Optimization**: It allows you to pass a `dispatch` function down to child components instead of callbacks. This can prevent unnecessary re-renders of those children if the callbacks were being recreated on every render.

**How it works:**
`useReducer` is inspired by Redux.

1.  You define a **reducer function** that takes the current `state` and an `action` object as arguments, and it returns the new state.
2.  You call `useReducer(reducer, initialState)`.
3.  It returns an array with the **current state** and a **`dispatch` function**.
4.  To update the state, you call `dispatch({ type: 'SOME_ACTION', payload: ... })`. React then calls your reducer with the current state and this action to calculate the new state.

**Code Example:**

```jsx
import React, { useReducer } from "react";

const initialState = { count: 0 };

function reducer(state, action) {
  switch (action.type) {
    case "increment":
      return { count: state.count + 1 };
    case "decrement":
      return { count: state.count - 1 };
    default:
      throw new Error();
  }
}

function Counter() {
  const [state, dispatch] = useReducer(reducer, initialState);

  return (
    <>
      Count: {state.count}
      <button onClick={() => dispatch({ type: "decrement" })}>-</button>
      <button onClick={() => dispatch({ type: "increment" })}>+</button>
    </>
  );
}
```

---

#### **4. Explain the dependency array in `useEffect`. What happens if it's empty, omitted, or has certain dependencies?**

The **dependency array** is the second argument to `useEffect`. It controls when the effect is re-run.

- **Dependency Array is Omitted**: If you don't provide the array at all, the effect will run **after every single render**. This is often inefficient and can lead to infinite loops if the effect itself triggers a state update.
  ```jsx
  useEffect(() => {
    /* Runs on every render */
  });
  ```
- **Dependency Array is Empty (`[]`)**: The effect will run **only once**, after the initial render. This is the perfect replacement for `componentDidMount`.
  ```jsx
  useEffect(() => {
    /* Runs once on mount */
  }, []);
  ```
- **Dependency Array has Dependencies (`[prop, state]`)**: The effect will run **on the initial render** and then **only if any of the values in the dependency array have changed** between renders. This is the replacement for `componentDidUpdate`.
  ```jsx
  useEffect(() => {
    /* Runs on mount and when `value` changes */
  }, [value]);
  ```

---

#### **5. How do you implement a cleanup function in `useEffect`? Give an example use case.**

You implement a cleanup function by **returning a function** from within your `useEffect` callback. React will execute this cleanup function when the component is about to unmount, or before the effect is re-run due to a dependency change.

This is the replacement for `componentWillUnmount` and is essential for preventing memory leaks.

**Use Case:**
A classic use case is setting up and tearing down a subscription or an event listener. You add the listener when the component mounts and you must remove it when the component unmounts to avoid errors and memory leaks.

**Code Example:**

```jsx
import React, { useState, useEffect } from "react";

function MousePosition() {
  const [position, setPosition] = useState({ x: 0, y: 0 });

  useEffect(() => {
    // This is the effect setup
    console.log("Effect is running: adding event listener.");
    const handleMouseMove = (e) => {
      setPosition({ x: e.clientX, y: e.clientY });
    };

    window.addEventListener("mousemove", handleMouseMove);

    // This is the cleanup function
    return () => {
      console.log("Cleanup is running: removing event listener.");
      window.removeEventListener("mousemove", handleMouseMove);
    };
  }, []); // Empty array means this runs only on mount and cleans up on unmount

  return (
    <p>
      X: {position.x}, Y: {position.y}
    </p>
  );
}
```

---

#### **6. What are custom hooks? How do you write a custom hook that uses other hooks internally?**

A **custom hook** is a reusable JavaScript function whose name starts with "use" (e.g., `useFetch`, `useLocalStorage`) and that can call other hooks. It's a powerful pattern for extracting and sharing stateful logic between components.

You write one just like a regular function, but you follow two rules:

1.  The name must start with `use`.
2.  You can call other hooks (like `useState`, `useEffect`) inside it.

The custom hook can then return values (like state and updater functions) that components can use.

**Code Example (A custom hook for tracking window width):**

```jsx
import { useState, useEffect } from "react";

// 1. Create a function with a name starting with 'use'
function useWindowWidth() {
  // 2. Use built-in hooks inside it
  const [width, setWidth] = useState(window.innerWidth);

  useEffect(() => {
    const handleResize = () => setWidth(window.innerWidth);
    window.addEventListener("resize", handleResize);

    // Cleanup the event listener
    return () => window.removeEventListener("resize", handleResize);
  }, []); // Run only once

  // 3. Return the value you want to share
  return width;
}

// Now, any component can easily get the window width
function MyComponent() {
  const width = useWindowWidth();
  return <div>Window width is: {width}px</div>;
}
```

---

#### **7. Compare `useEffect` and `useLayoutEffect`. When should you use one over the other?**

Both hooks have the same signature and functionality, but they differ in **when they are fired**.

- **`useEffect`**: Fires **asynchronously** after the render has been committed to the screen. This means the browser has a chance to paint the updated DOM _before_ the effect runs. This is the correct choice for the vast majority of side effects (like data fetching) because it doesn't block the browser from painting.

- **`useLayoutEffect`**: Fires **synchronously** after all DOM mutations but **before** the browser has painted the result to the screen.

**When to use `useLayoutEffect`:**
You should only use `useLayoutEffect` when your effect needs to **measure the DOM** (e.g., get the height or scroll position of an element) and then **synchronously re-render** to change the DOM based on that measurement. Using `useEffect` for this can cause a flicker, where the user briefly sees the initial rendered state before the effect runs and triggers a re-render.

**Rule of Thumb**: Always start with `useEffect`. If you notice a visual flicker, try switching to `useLayoutEffect`.

---

### \#\# Medium-Importance Questions 🤔

---

#### **8. How does React batch state updates in hooks? Does batching happen synchronously or asynchronously?**

React **batches** state updates to improve performance. This means that if you call multiple state setter functions in a single event handler, React will group them together and perform only **one single re-render** at the end.

Historically (before React 18), this batching was only consistent inside React event handlers (like `onClick`). Updates inside promises or `setTimeout` were not batched.

With **Automatic Batching** in React 18, this behavior is now consistent everywhere. React will automatically batch all state updates that occur in a single synchronous block of code, regardless of whether it's inside an event handler, a promise, or a timeout. The re-render happens **asynchronously** after the event handler or synchronous code block has finished executing.

---

#### **9. Explain how closure and stale state issues arise in hooks. How can you fix them?**

A **stale state** issue occurs when a closure (like an event listener or a `setTimeout` callback) captures a state variable from a particular render. If the state updates later, the closure still holds on to the "stale" value from the render it was created in.

**Example Scenario:**
Imagine a counter component. You click a button that sets a timeout to log the count after 3 seconds. In those 3 seconds, you click the increment button several times. The log will show the count from when the timeout was _created_, not the current count.

**How to fix it:**

1.  **Updater Function**: Use the updater form of the state setter (`setCount(prevCount => prevCount + 1)`). This guarantees you always have the most recent state.
2.  **`useRef`**: Store the value in a ref. A ref is a mutable object that persists across renders, so it always holds the current value.
3.  **Dependency Array in `useEffect`**: If the closure is inside a `useEffect`, including the state variable in the dependency array will ensure the effect is re-created (with a new closure over the new state) whenever the state changes.

---

#### **11. How do you optimize performance when using `useEffect` to avoid unnecessary runs?**

The key to optimizing `useEffect` is the **dependency array**.

1.  **Provide a Dependency Array**: Always provide the second argument. If you omit it, the effect runs after every single render, which is almost never what you want.
2.  **Be Specific**: Include only the values from the component's scope (props and state) that the effect actually depends on. If the effect uses a prop `userId` but not `userName`, only include `userId` in the array.
3.  **Use Empty Array `[]` for "on mount" effects**: If the effect should only run once when the component mounts (e.g., to fetch initial data or add a global listener), use an empty dependency array.
4.  **Memoize Functions**: If you need to include a function in the dependency array, wrap it in `useCallback` to ensure its reference doesn't change on every render, which would cause the effect to run unnecessarily.

---

#### **12. How can you handle asynchronous logic inside `useEffect`? Why can’t you make the effect function itself `async`?**

You cannot make the `useEffect` callback function itself `async` because it would implicitly return a `Promise`. However, `useEffect` must either return **nothing** (`undefined`) or a **cleanup function**. A promise does not fit this signature and would cause unexpected behavior.

The correct way to handle asynchronous logic is to define a separate `async` function **inside** the effect and then call it immediately.

**Code Example:**

```jsx
import { useEffect, useState } from "react";

function UserProfile({ userId }) {
  const [user, setUser] = useState(null);

  useEffect(() => {
    // 1. Define an async function inside the effect
    const fetchData = async () => {
      const response = await fetch(`/api/users/${userId}`);
      const data = await response.json();
      setUser(data);
    };

    // 2. Call the async function
    fetchData();

    // The effect itself does not return a promise
  }, [userId]); // Re-fetch when userId changes

  if (!user) return <div>Loading...</div>;
  return <h1>{user.name}</h1>;
}
```

---

#### **14. How do you share stateful logic between components with hooks? Compare custom hooks vs higher-order components.**

- **Custom Hooks**: This is the modern and preferred way to share stateful logic. A custom hook is a function (e.g., `useFetch`) that encapsulates logic and state (using `useState`, `useEffect`, etc.) and can be called from any functional component.

  - **Pros**: Simple, explicit, no component nesting, logic is easy to trace.
  - **Cons**: Can only be used in functional components.

- **Higher-Order Components (HOCs)**: An older pattern where a HOC is a function that takes a component as an argument and returns a new, enhanced component with extra props.

  - **Pros**: Works with both class and functional components.
  - **Cons**: Can lead to "wrapper hell" (deeply nested components in the dev tools), prop name collisions, and it's less obvious where props are coming from ("implicit composition").

In almost all new development, **custom hooks are superior** to HOCs for sharing logic due to their simplicity and clarity.

#### **10. What are the rules of hooks? Why must hooks be called at the top level of the component or custom hook?**

There are two fundamental rules of hooks that must be followed:

1.  **Only Call Hooks at the Top Level**: Do not call hooks inside loops, conditions, or nested functions.
2.  **Only Call Hooks from React Functions**: Call hooks from React functional components or from custom hooks. Don't call them from regular JavaScript functions.

**Why these rules exist:**
React relies on the **call order** of hooks to associate state with the correct component. On every render, React expects the hooks to be called in the exact same sequence. If you put a hook inside a condition, that hook might not be called on a subsequent render, which would shift the order of all the hooks that come after it. This would cause React to mix up the state between different `useState` calls, leading to unpredictable and buggy behavior. By enforcing that hooks are only called at the top level, React can guarantee a consistent call order on every render.

---

#### **13. Explain the behavior of hooks inside conditional statements or loops. Why is it prohibited?**

As stated in the rules of hooks, calling a hook inside a conditional or a loop is prohibited because it **breaks the stable call order** that React depends on.

Let's imagine you did this (which is not allowed):

```jsx
// 🚨 THIS IS INVALID CODE
if (someCondition) {
  const [name, setName] = useState(""); // Hook #1
}
const [age, setAge] = useState(0); // Hook #2
```

- **On the first render**, if `someCondition` is true, React sees `useState('')` as Hook \#1 and `useState(0)` as Hook \#2.
- **On a second render**, if `someCondition` is now false, the first `useState` is skipped. React now sees `useState(0)` as Hook \#1. It will mistakenly return the state for `name` ('') instead of the state for `age`, leading to a complete breakdown of your component's state.

Because the call order must be identical on every single render, hooks are not allowed inside any logic that could change their call order.

---

#### **15. Describe pitfalls with accumulating effects or infinite loops in `useEffect` due to missing or incorrect dependencies.**

This is one of the most common bugs when working with `useEffect`.

**Infinite Loops:**
An infinite loop occurs if you update a state variable inside a `useEffect` that also depends on that same state variable, but you haven't structured your dependencies correctly.

- **Missing Dependency**: If your effect updates state but you forget to include a dependency array, the effect will run on every render. It updates the state, which causes a re-render, which causes the effect to run again, and so on, forever.
- **Incorrect Dependency (Objects/Arrays)**: If you depend on an object or array that is re-created on every render, the effect will run every time. For example, if you pass `style={{ color: 'red' }}` as a prop, that object is new on every render, and an effect depending on it will run every time.

**Accumulating Effects:**
This happens when you forget the **cleanup function**. For example, if you have an effect that adds an event listener but doesn't remove it, and the effect runs multiple times, you will end up with multiple, duplicate event listeners attached, which can lead to bugs and memory leaks.

---

#### **16. What happens if you update state inside `useEffect` without a dependency array? How does it affect rendering?**

If you update state inside a `useEffect` and **omit the dependency array**, you will create an **infinite render loop**.

Here's the sequence of events:

1.  The component renders for the first time.
2.  The `useEffect` hook runs because the dependency array was omitted.
3.  Inside the effect, you call a state setter (e.g., `setCount(1)`).
4.  This state update triggers a re-render of the component.
5.  After the re-render, the `useEffect` hook runs again (because there's no dependency array to stop it).
6.  The state is updated again, which triggers another re-render.
7.  This cycle repeats indefinitely, likely crashing your browser tab.

### \#\# Low-Importance Questions 📚

---

#### **17. How does React handle component unmounts with regard to `useEffect` cleanup?**

When a component is about to be removed from the DOM (unmounted), React runs the **cleanup function** for every `useEffect` hook in that component.

The cleanup function is the function that you **return** from the `useEffect` callback. This process ensures that any subscriptions, event listeners, or timers that were set up when the component mounted are properly torn down, preventing memory leaks and errors.

**Code Example:**

```jsx
useEffect(() => {
  // Effect setup runs on mount
  const timerId = setInterval(() => console.log("tick"), 1000);

  // Cleanup function runs on unmount
  return () => {
    clearInterval(timerId);
  };
}, []); // Empty dependency array ensures this is a mount/unmount effect
```

---

#### **18. Explain nested or chained `useEffect` calls and potential issues.**

"Chained" `useEffect` calls refer to a pattern where one effect triggers a state change, which in turn triggers another effect.

**Scenario:**

1.  `useEffect` \#1 runs and fetches data, then calls `setData(result)`.
2.  `useEffect` \#2 has `data` in its dependency array, so it runs in response to the state change from the first effect.

**Potential Issues**:

- **Complexity**: This can make the data flow hard to trace and debug.
- **Infinite Loops**: If the second effect also updates the state that the first effect depends on, you can easily create an infinite loop of re-renders.
- **Performance**: It can lead to multiple, cascading re-renders where a single re-render would have been sufficient.

**Best Practice**: It's generally better to combine related logic into a **single, more comprehensive `useEffect` hook** if possible, or to use a `useReducer` to handle more complex, multi-step state transitions.

---

#### **19. Can you use multiple `useReducer` hooks in the same component? When would this be useful?**

Yes, you can absolutely use multiple `useReducer` hooks in a single component, just like you can use multiple `useState` hooks.

This is useful when you have **multiple pieces of complex state that are not related to each other**. By using a separate reducer for each piece of state, you can keep the state logic organized and decoupled. This follows the principle of separation of concerns and can make your component easier to understand and maintain than having one massive reducer that handles all state transitions.

**Code Example:**

```jsx
function UserDashboard() {
  const [userState, userDispatch] = useReducer(userReducer, initialUserState);
  const [postsState, postsDispatch] = useReducer(
    postsReducer,
    initialPostsState
  );

  // ... logic to manage user and posts state independently
}
```

---

#### **20. What is the difference between React state (`useState`) and mutable refs (`useRef`)? When would you use refs to hold state?**

This is a key distinction in React.

- **`useState`**:

  - **Purpose**: To manage data that is used for **rendering**.
  - **Behavior**: When you update state with the setter function, it triggers a **re-render** of the component.
  - **Value**: The state variable is a snapshot from a specific render.

- **`useRef`**:

  - **Purpose**: To create a mutable "box" that can hold any value and **persists across renders**. It's also used to get a direct reference to a DOM element.
  - **Behavior**: **Updating a ref does NOT trigger a re-render**. The change to `.current` is immediate and synchronous.
  - **Value**: The `.current` property is mutable and always holds the latest value.

**When to use a ref to hold "state-like" values:**
You should use a ref to store a value when you need to keep track of it across renders, but **changing it should not cause the component to re-render**.

**Common Use Case**: Storing a timer ID from `setTimeout` or `setInterval`. You need to access the ID later to clear it, but you don't want the component to re-render every time the ID is set.
