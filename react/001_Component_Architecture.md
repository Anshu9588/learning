# 1. **Component Architecture**

- Functional vs. class components: lifecycle methods vs. hooks
- Composition patterns: children props, slots, compound components

### \#\# Part 1: High-Importance Questions 🌟

---

#### **1. Compare functional components with class components. What are the main differences in syntax, lifecycle, and performance?**

**Functional components** (with hooks) are the modern standard for writing React components. **Class components** are the original way and are still supported but are less common in new codebases.

Here are the main differences:

| Feature            | Functional Components                                                                                                                                                            | Class Components                                                                                                 |
| :----------------- | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | :--------------------------------------------------------------------------------------------------------------- |
| **Syntax**         | Plain JavaScript functions. They are simpler and more concise.                                                                                                                   | ES6 classes that extend `React.Component`. They require more boilerplate (`constructor`, `render` method, etc.). |
| **State**          | Managed with the `useState` hook. State logic can be easily separated and reused.                                                                                                | Managed with `this.state` and updated with `this.setState()`.                                                    |
| **Lifecycle**      | Lifecycle logic is handled with the `useEffect` hook, which combines the concepts of mounting, updating, and unmounting into a single API.                                       | Uses explicit lifecycle methods like `componentDidMount`, `componentDidUpdate`, and `componentWillUnmount`.      |
| **`this` Keyword** | There is no `this`. Props and state are accessed directly from the function's scope, which avoids binding issues.                                                                | Heavily relies on the `this` keyword, which often requires manual binding in the constructor for event handlers. |
| **Performance**    | Generally have a slight performance advantage. They are simpler to optimize for React, have less overhead, and hooks allow for better logic collocation and easier optimization. | Can be slightly less performant due to the overhead of class instantiation and `this` binding.                   |

---

#### **2. How do React hooks (e.g., `useState`, `useEffect`) replace class component lifecycle methods?**

React hooks provide a more direct and functional way to manage state and side effects, effectively replacing the need for most class lifecycle methods.

- **`useState`**: Replaces the `constructor` for state initialization and `this.setState` for state updates.

- **`useEffect`**: This is the most versatile hook and covers the main lifecycle events:

  - **`componentDidMount`**: An `useEffect` with an empty dependency array `[]` runs only once, after the initial render, mimicking `componentDidMount`.
    ```jsx
    useEffect(() => {
      // Runs once on mount
    }, []);
    ```
  - **`componentDidUpdate`**: An `useEffect` with a dependency array `[someValue]` will run on mount _and_ whenever `someValue` changes, which is similar to `componentDidUpdate`.
    ```jsx
    useEffect(() => {
      // Runs on mount and whenever someValue changes
    }, [someValue]);
    ```
  - **`componentWillUnmount`**: The **cleanup function** returned from an `useEffect` hook runs when the component unmounts, replacing `componentWillUnmount`. This is perfect for removing event listeners or clearing timers.
    ```jsx
    useEffect(() => {
      // ... some logic
      return () => {
        // This is the cleanup, runs on unmount
      };
    }, []);
    ```

---

#### **3. Explain the rules of hooks. Why must hooks be called at the top level and only in React function components?**

There are two fundamental rules of hooks that must be followed:

1.  **Only Call Hooks at the Top Level**: Do not call hooks inside loops, conditions, or nested functions.
2.  **Only Call Hooks from React Functions**: Call hooks from React functional components or from custom hooks. Don't call them from regular JavaScript functions.

**Why these rules exist:**
React relies on the **call order** of hooks to associate state with the correct component. On every render, React expects the hooks to be called in the exact same sequence. If you put a hook inside a condition, that hook might not be called on a subsequent render, which would shift the order of all the hooks that come after it. This would cause React to mix up the state between different `useState` calls, leading to unpredictable and buggy behavior. By enforcing that hooks are only called at the top level, React can guarantee a consistent call order on every render.

---

#### **4. What are custom hooks? How do you create one, and when would you use it?**

A **custom hook** is a reusable JavaScript function whose name starts with "use" (e.g., `useFetch`, `useLocalStorage`) and that can call other hooks. It's a powerful pattern for extracting and sharing stateful logic between components.

**How to create one:**
You simply create a function with a name starting with `use`, and inside it, you can use built-in hooks like `useState` and `useEffect`. The function can then return values (like state and updater functions) that components can use.

**When to use one:**
You should create a custom hook whenever you find yourself writing the same stateful logic in multiple components. Instead of duplicating the logic, you can extract it into a custom hook. This makes your components cleaner and your logic reusable and testable in isolation.

**Code Example:**

```jsx
import { useState, useEffect } from "react";

// Custom hook to fetch data from an API
function useFetch(url) {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    setLoading(true);
    fetch(url)
      .then((res) => res.json())
      .then((data) => setData(data))
      .catch((err) => setError(err))
      .finally(() => setLoading(false));
  }, [url]); // Re-run effect if the URL changes

  return { data, loading, error };
}

// Now, any component can use this hook to fetch data
function UserProfile({ userId }) {
  const { data: user, loading, error } = useFetch(`/api/users/${userId}`);

  if (loading) return <div>Loading...</div>;
  if (error) return <div>Error!</div>;

  return <h1>{user.name}</h1>;
}
```

---

#### **5. Describe how you would convert a class component that uses state and lifecycle methods into a functional component using hooks.**

Let's convert a typical data-fetching class component. The process involves mapping the class concepts to their hook equivalents:

1.  Convert the `class` into a `function`.
2.  Replace the `constructor` and `this.state` with `useState` hooks.
3.  Replace the lifecycle methods (`componentDidMount`, `componentWillUnmount`) with a `useEffect` hook.
4.  Remove the `render` method and just return the JSX directly from the function.
5.  Remove all references to `this`.

**Before: Class Component**

```jsx
import React from "react";

class UserGreeter extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      name: "Guest",
    };
  }

  componentDidMount() {
    // Fetch user data on mount
    fetch(`/api/users/${this.props.userId}`)
      .then((res) => res.json())
      .then((user) => this.setState({ name: user.name }));
  }

  render() {
    return <h1>Hello, {this.state.name}</h1>;
  }
}
```

**After: Functional Component with Hooks**

```jsx
import React, { useState, useEffect } from "react";

function UserGreeter({ userId }) {
  // 1. Replace state with useState
  const [name, setName] = useState("Guest");

  // 2. Replace componentDidMount with useEffect
  useEffect(() => {
    fetch(`/api/users/${userId}`)
      .then((res) => res.json())
      .then((user) => setName(user.name));
  }, [userId]); // Dependency array ensures it re-runs if userId changes

  // 3. Return JSX directly
  return <h1>Hello, {name}</h1>;
}
```

---

#### **6. What are compound components? Show how they enable flexible parent-child APIs using React context or props.**

**Compound components** are a pattern where you use multiple components together to create a single, cohesive UI element. They manage a shared, implicit state between a parent component and its children, giving the user maximum flexibility over the rendering of the child elements.

Think of the `<select>` and `<option>` elements in HTML. They work together. A `<select>` without `<option>`s is useless, and `<option>`s only make sense inside a `<select>`.

You can implement this in React using **React Context**. The parent component acts as a `Provider` for some shared state (like which tab is active), and the child components act as `Consumers` of that state.

**Code Example (Simplified Tabs):**

```jsx
import React, { useState, useContext, createContext } from "react";

// 1. Create a context to share the active tab state
const TabsContext = createContext();

// 2. Parent component provides the context
function Tabs({ children }) {
  const [activeTab, setActiveTab] = useState(0);
  return (
    <TabsContext.Provider value={{ activeTab, setActiveTab }}>
      {children}
    </TabsContext.Provider>
  );
}

// 3. Child components consume the context
function Tab({ index, children }) {
  const { activeTab, setActiveTab } = useContext(TabsContext);
  const isActive = activeTab === index;
  return <button onClick={() => setActiveTab(index)}>{children}</button>;
}

function TabPanel({ index, children }) {
  const { activeTab } = useContext(TabsContext);
  return activeTab === index ? <div>{children}</div> : null;
}

// Usage gives the developer full control over layout
function App() {
  return (
    <Tabs>
      <div>
        <Tab index={0}>Tab 1</Tab>
        <Tab index={1}>Tab 2</Tab>
      </div>
      <div>
        <TabPanel index={0}>Content for Tab 1</TabPanel>
        <TabPanel index={1}>Content for Tab 2</TabPanel>
      </div>
    </Tabs>
  );
}
```

---

#### **7. How does the `children` prop work? What patterns can you use to pass or manipulate children in React?**

The **`children` prop** is a special prop that allows you to pass components or JSX directly between the opening and closing tags of a component. It's the primary way React achieves **composition**.

**How it works**: Whatever you put inside `<MyComponent>...</MyComponent>` gets passed to `MyComponent` as a prop named `children`.

**Common Patterns**:

1.  **Wrapper Components (Generic Boxes)**: The most common use is for creating layout or wrapper components, like a `Card` or `Modal`, that can contain any content.

    ```jsx
    function Card({ children }) {
      return <div className="card">{children}</div>;
    }
    // Usage: <Card><h1>Title</h1><p>Content</p></Card>
    ```

2.  **Passing Functions as Children (Render Props)**: You can pass a function as a child, which the component can then call with some internal state. This is a powerful pattern for sharing logic.

    ```jsx
    function MouseTracker({ children }) {
      const [position, setPosition] = useState({ x: 0, y: 0 });
      // ... logic to update position
      return children(position);
    }
    // Usage: <MouseTracker>{pos => <div>Coords: {pos.x}, {pos.y}</div>}</MouseTracker>
    ```

3.  **Manipulating Children with `React.Children`**: React provides a utility API, `React.Children`, to work with the `children` prop. You can use methods like `React.Children.map` or `React.Children.forEach` to iterate over the children, and `React.cloneElement` to add or modify their props. This is useful for more advanced patterns like compound components.

---

### \#\# Part 2: Medium-Importance Questions 🤔

These questions explore more advanced composition patterns that are used to create highly reusable and flexible components.

---

#### **8. Explain the concept of render props. How do they differ from compound components and HOCs?**

The **render props** pattern is a technique for sharing code between components using a prop whose value is a function. A component with a render prop takes a function and calls it with some of its internal state, effectively "rendering" whatever the function returns. The most common implementation is to use the `children` prop for this, making it a "function as a child" pattern.

**How it differs:**

- **Compound Components**: Compound components manage implicit state between a parent and specific children (like `Tabs` and `Tab`). Render props are more generic; they provide state to _any_ component or JSX that you pass in as the function child.
- **Higher-Order Components (HOCs)**: HOCs are functions that wrap a component to add functionality. This creates a new component, which can lead to "wrapper hell" and makes it less clear where props are coming from. Render props are more explicit; you can see exactly where the logic is being shared directly in your JSX.

**Code Example:**

```jsx
import { useState } from "react";

// This component uses a render prop to share its 'on' state
function Toggle({ children }) {
  const [on, setOn] = useState(false);
  const toggle = () => setOn(!on);

  // It calls the children function with its internal state and logic
  return children({ on, toggle });
}

// Usage
function App() {
  return (
    <Toggle>
      {/* The child is a function that receives the state */}
      {({ on, toggle }) => (
        <div>
          <button onClick={toggle}>{on ? "ON" : "OFF"}</button>
          {on && <h2>The light is on!</h2>}
        </div>
      )}
    </Toggle>
  );
}
```

---

#### **9. What are “slots” or “named slots” patterns in React? How can you implement them?**

The **slots pattern** allows a parent component to receive and render multiple, distinct sets of child content in different, predefined places ("slots"). This is useful for creating complex layout components like a modal or a sidebar where you want to define areas for a header, a body, and a footer.

You can implement this by passing specific components or JSX as props. Instead of a single generic `children` prop, you pass multiple props that represent the different slots.

**Code Example:**

```jsx
// A layout component with named slots for 'header' and 'footer'
function PageLayout({ header, footer, children }) {
  return (
    <div className="page">
      <header>{header}</header>
      <main>{children}</main> {/* 'children' acts as the default slot */}
      <footer>{footer}</footer>
    </div>
  );
}

// Usage
function App() {
  return (
    <PageLayout header={<h1>My Page Title</h1>} footer={<p>© 2025 My App</p>}>
      <p>This is the main content of the page.</p>
    </PageLayout>
  );
}
```

---

#### **10. How do you prevent prop drilling? Compare using context API vs. composition patterns.**

**Prop drilling** is the process of passing props down through multiple layers of nested components, even if the intermediate components don't need the props themselves. It can make code hard to maintain.

There are two primary ways to prevent it:

1.  **Context API**: This is the standard solution for sharing "global" state that many components at different levels need, such as the current user, theme, or language. You wrap your component tree in a `Context.Provider` and any nested component can access the state using the `useContext` hook without it being passed down explicitly through props.

    - **Best for**: Truly global state that is not specific to one part of the component tree.

2.  **Component Composition**: This is often a simpler and more performant solution for avoiding prop drilling in specific component trees. Instead of passing props down, you can pass the component that needs the props _down to_ the component that has the state, often using the `children` prop. This "inverts" the control.

    - **Best for**: State that is local to a specific part of your application. It keeps the component relationships clearer.

**Code Example (Composition):**

```jsx
// 👎 Prop Drilling
function App() {
  const [user, setUser] = useState({ name: "Alice" });
  return <Layout user={user} />;
}
const Layout = ({ user }) => <Header user={user} />;
const Header = ({ user }) => <h1>Welcome, {user.name}</h1>;

// 👍 Composition (No Prop Drilling)
function App() {
  const [user, setUser] = useState({ name: "Alice" });
  const userHeader = <h1>Welcome, {user.name}</h1>;
  // We create the component that needs the prop here
  // and pass it down as children. Layout doesn't need to know about 'user'.
  return <Layout header={userHeader} />;
}
const Layout = ({ header }) => <div>{header}</div>;
```

---

#### **12. How do you build a reusable modal or tab component using composition? Sketch the API and explain implementation.**

Building a reusable `Modal` with composition and the slots pattern makes it extremely flexible.

**API Sketch:**
The goal is to have a `Modal` component that manages the open/close state and provides slots for the content.

```jsx
<Modal>
  <Modal.Header>My Modal Title</Modal.Header>
  <Modal.Body>
    <p>This is the content of the modal.</p>
  </Modal.Body>
  <Modal.Footer>
    <button>Close</button>
  </Modal.Footer>
</Modal>
```

**Implementation Explanation:**
This can be implemented as a **compound component**.

1.  Create a `Modal` parent component that manages the `isOpen` state. It will use React Context to share this state and the `onClose` function.
2.  Create sub-components like `Modal.Header`, `Modal.Body`, and `Modal.Footer`.
3.  These sub-components don't need any props. They simply render their `children` inside the appropriate layout elements (e.g., `<header>`, `<footer>`).
4.  You would also include a `Modal.Trigger` component that consumes `onOpen` from the context to open the modal and a close button inside the `Modal.Footer` that consumes `onClose`. This keeps all state logic encapsulated within the `Modal` component.

---

### ## Part 3: Low-Importance Questions 📚

These are deep-dive questions. Answering them correctly can definitely set you apart and signal a senior level of understanding.

---

#### **13. When would you choose class components over functional components with hooks?**

In modern React, there are very few reasons to choose a class component for new code. Functional components with hooks can cover virtually all use cases with a simpler and more composable API.

The only significant reason you might still need a class component is for **Error Boundaries**. As of now, the lifecycle methods `getDerivedStateFromError` and `componentDidCatch` that are required to implement an error boundary do not have a direct hook equivalent. Therefore, if you need to create an error boundary to gracefully handle rendering errors in your component tree, you must use a class component.

---

#### **15. What are the trade-offs between HOCs, render props, and hooks for logic reuse?**

This is a great question about the evolution of React patterns.

- **Higher-Order Components (HOCs)**:

  - **Pros**: A powerful and flexible pattern for wrapping components.
  - **Cons**: Can lead to "wrapper hell" (deeply nested components in DevTools), making debugging difficult. Prop naming collisions can occur if the HOC injects a prop with the same name as an existing one. It's also less explicit where props are coming from ("implicit composition").

- **Render Props**:

  - **Pros**: More explicit than HOCs. You can see exactly where the shared logic is being used directly in your render method. Avoids prop naming collisions.
  - **Cons**: Can lead to a similar nesting issue inside your JSX ("pyramid of doom") if you use multiple render props.

- **Hooks**:
  - **Pros**: The modern and preferred solution. They are the simplest and cleanest way to reuse stateful logic. They don't introduce any extra component nesting. They allow you to collocate related logic (e.g., setting up and tearing down a subscription in one `useEffect`).
  - **Cons**: They can only be used in functional components and have their own set of rules that must be followed.

In summary, **hooks have largely superseded HOCs and render props** for most logic-sharing use cases in new development.

---

#### **18. What are potential pitfalls when using the `children` prop for complex layouts?**

While the `children` prop is incredibly powerful for composition, it can have pitfalls in complex scenarios:

1.  **Implicit API**: When you just pass `children`, the parent component has little control or knowledge of what it's rendering. This can be a problem if the parent needs to inject props or coordinate between its children. This is where patterns like compound components (which have a more explicit API) are better.
2.  **Performance**: If the parent component re-renders, React will also re-render the `children`. If the children are expensive to render, this can cause performance issues. This can be mitigated with `React.memo`, but it's something to be aware of.
3.  **Single Child Expectation**: Some components might be designed to only work with a single child element. Using `React.Children.only` can enforce this, but it can be a source of runtime errors if the user of the component is not aware of the constraint.

---

#### **19. How do you test components that use render props or compound patterns with React Testing Library?**

The good news is that **React Testing Library** is designed to test components from a user's perspective, so you don't need special techniques for these patterns. You test them just like any other component by interacting with the rendered output.

- **For Render Props**: You simply render the component with its function-as-a-child and then query for the elements that the function is expected to render. You can then simulate events to test the logic that the render prop provides.
- **For Compound Components**: You render the entire component hierarchy (`<Tabs>`, `<Tab>`, `<TabPanel>`) just as you would in your application. Then, you can find elements by their roles or text (e.g., find a tab by its name, click it, and then assert that the correct panel content is now visible).

The key is to **not test the implementation details**. You don't need to check what props are passed or what context value is set. You just verify that the final rendered HTML behaves as a user would expect.

---

#### **20. Describe a scenario where composition patterns lead to performance issues. How would you mitigate them?**

A common scenario where composition can lead to performance issues is when a top-level provider component re-renders frequently, causing all of its children to re-render, even if they don't depend on the changed data.

**Scenario**: Imagine you have a single large `AppContext` that provides both the `theme` and the `currentUser` to the entire application. Now, you have a component deep in the tree that only uses the `theme`. If the `currentUser` object changes (e.g., the user logs in), the entire `AppContext.Provider` will re-render. Because of this, your theme-consuming component will _also_ re-render, even though the `theme` value it cares about hasn't changed at all.

**Mitigation Strategies**:

1.  **Split Contexts**: The best solution is often to split a large context into smaller, more granular contexts. In this case, you would create a `ThemeContext` and a `UserContext`. This way, components only subscribe to the specific context they need, and they won't re-render when unrelated context values change.
2.  **Memoization with `React.memo`**: You can wrap the child components in `React.memo`. This will prevent them from re-rendering if their props have not changed. This is effective but can sometimes feel like a patch rather than solving the root problem of why the component is being told to re-render in the first place.
