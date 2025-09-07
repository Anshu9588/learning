# - **Function Patterns**
  - Currying, partial application, composition
  - Hands-on: Compose small math operations
---

### High Importance

#### 1\. What is currying? How does it differ from a regular function? Provide a code example.

**Currying** is the process of transforming a function that takes multiple arguments into a sequence of nested functions, each taking a **single argument**. You "feed" it arguments one by one, and it only executes the final logic once all arguments have been provided.

It differs from a regular function in its **invocation**. A regular function takes all its arguments at once. A curried function is called with one argument and returns a new function, which is then called with the next argument, and so on.

This allows you to create specialized, reusable functions by "locking in" the first few arguments.

```javascript
// Regular function
function regularAdd(a, b, c) {
  return a + b + c;
}
console.log(regularAdd(2, 3, 5)); // 10

// Curried version
function curriedAdd(a) {
  return function (b) {
    return function (c) {
      return a + b + c;
    };
  };
}
console.log(curriedAdd(2)(3)(5)); // 10

// Currying allows for specialization
const addTwo = curriedAdd(2);
const addTwoAndThree = addTwo(3);
console.log(addTwoAndThree(5)); // 10
```

---

#### 2\. What is partial application? How is it different from currying? Provide a code example.

**Partial application** is the process of fixing (or "pre-filling") one or more of a function's arguments, which produces a new function of a smaller _arity_ (number of arguments).

**The key difference**:

- **Currying** is a specific pattern that always produces a chain of single-argument functions. `fn(a, b, c)` becomes `fn(a)(b)(c)`.
- **Partial Application** is more general. You can fix _any number_ of arguments at once. `fn(a, b, c)` could be partially applied to create `fn(a)` which still expects `(b, c)`, or `fn(a, b)` which expects `(c)`.

The most common way to do partial application in JavaScript is with `Function.prototype.bind`.

```javascript
function multiply(a, b, c) {
  return a * b * c;
}

// Partially applying the first argument `a` to create a new function.
const multiplyByFive = multiply.bind(null, 5); // `null` is the `this` context

// The new function only needs the remaining arguments `b` and `c`.
console.log(multiplyByFive(10, 2)); // 100 (5 * 10 * 2)

// Partially applying the first two arguments.
const multiplyByFifty = multiply.bind(null, 5, 10);
console.log(multiplyByFifty(2)); // 100 (5 * 10 * 2)
```

In short: **Currying always returns a unary (one-argument) function. Partial application can return a function of any arity.**

---

#### 3\. Explain function composition. How does it help in building complex functionality? Provide code.

**Function composition** is the creation of a new, complex function by combining multiple, simpler functions. The core idea is that the **output of one function becomes the input of the next**, creating a data processing pipeline.

It helps build complex functionality by promoting **small, reusable, single-purpose functions**. Instead of writing one large, monolithic function, you write several small, easy-to-test functions and then compose them. This makes code more modular, readable, and maintainable.

Here's an example with a `compose` utility:

```javascript
// `compose` takes functions and applies them from right to left.
const compose =
  (...fns) =>
  (initialValue) =>
    fns.reduceRight((value, fn) => fn(value), initialValue);

// Simple, reusable functions
const greet = (name) => `Hello, ${name}!`;
const exclaim = (str) => `${str.toUpperCase()}`;
const repeat = (str) => `${str} ${str}`;

// Create a new, complex function by composing the simple ones.
const createLoudGreeting = compose(
  repeat, // 3. The result from `exclaim` is passed here.
  exclaim, // 2. The result from `greet` is passed here.
  greet // 1. The initial value is passed here first.
);

console.log(createLoudGreeting("Alice")); // "HELLO, ALICE! HELLO, ALICE!"
```

---

#### 4\. Write a curried function for three arguments and demonstrate how to use it.

Certainly. Here is a manually curried function that takes three numbers and returns their sum.

```javascript
const curriedSum = (a) => {
  return (b) => {
    return (c) => {
      return a + b + c;
    };
  };
};

// --- Demonstrations ---

// 1. Calling all at once (in sequence)
const result1 = curriedSum(10)(20)(30);
console.log("Result 1:", result1); // Result 1: 60

// 2. Creating specialized functions through partial application
const add10 = curriedSum(10); // Returns a function that adds 10 to the next two args.
const add10And20 = add10(20); // Returns a function that adds 30 to the next arg.

const result2 = add10And20(30);
console.log("Result 2:", result2); // Result 2: 60

// 3. Reusing a partially applied function
const result3 = add10And20(5);
console.log("Result 3:", result3); // Result 3: 35
```

---

#### 5\. How can you implement a generic `curry` utility that works for any function?

A generic `curry` utility is a higher-order function that takes any function and returns its curried version. It works by recursively collecting arguments until it has enough to match the original function's arity (`fn.length`).

```javascript
/**
 * Takes a function and returns its curried version.
 * @param {Function} fn The function to curry.
 * @returns {Function} The curried function.
 */
function curry(fn) {
  // Return a new function that will start collecting arguments.
  return function curried(...args) {
    // 1. If we have enough arguments, call the original function.
    if (args.length >= fn.length) {
      return fn.apply(this, args);
    } else {
      // 2. Otherwise, return a new function that waits for the rest.
      return function (...nextArgs) {
        // Recursively call `curried`, combining old and new arguments.
        return curried.apply(this, [...args, ...nextArgs]);
      };
    }
  };
}

// --- Usage ---
const sum = (a, b, c) => a + b + c;
const curriedSum = curry(sum);

console.log(curriedSum(1, 2, 3)); // 6
console.log(curriedSum(1)(2, 3)); // 6
console.log(curriedSum(1)(2)(3)); // 6
```

---

#### 6\. Implement a `compose` function that accepts any number of functions and returns a new composed function.

The `compose` function creates a pipeline that executes from **right to left**. This can be implemented elegantly using `Array.prototype.reduceRight`.

```javascript
/**
 * Composes functions from right to left.
 * @param {...Function} fns The functions to compose.
 * @returns {Function} A new function that runs a value through the composed pipeline.
 */
const compose = (...fns) => {
  return (initialValue) => {
    return fns.reduceRight((acc, fn) => fn(acc), initialValue);
  };
};

// --- Usage ---
const add5 = (x) => x + 5;
const multiplyBy2 = (x) => x * 2;

// Executes multiplyBy2 first, then add5.
const composedFunc = compose(add5, multiplyBy2);
console.log(composedFunc(10)); // 25 (10 * 2 = 20, then 20 + 5 = 25)
```

---

#### 7\. Implement a `pipe` function and explain the difference between `pipe` and `compose`.

The `pipe` function is nearly identical to `compose`, but it executes from **left to right**, which many people find more intuitive as it follows the natural reading order. It can be implemented with `Array.prototype.reduce`.

```javascript
/**
 * Composes functions from left to right.
 * @param {...Function} fns The functions to pipe.
 * @returns {Function} A new function that runs a value through the piped pipeline.
 */
const pipe = (...fns) => {
  return (initialValue) => {
    return fns.reduce((acc, fn) => fn(acc), initialValue);
  };
};

// --- Usage ---
const add5 = (x) => x + 5;
const multiplyBy2 = (x) => x * 2;

// Executes add5 first, then multiplyBy2.
const pipedFunc = pipe(add5, multiplyBy2);
console.log(pipedFunc(10)); // 30 (10 + 5 = 15, then 15 * 2 = 30)
```

**The Difference**:

- `compose(f, g, h)` is equivalent to `(...args) => f(g(h(...args)))`. It works **right to left**.
- `pipe(f, g, h)` is equivalent to `(...args) => h(g(f(...args)))`. It works **left to right**.

---

Of course. Let's get into the medium and low-importance questions.

---

### Medium Importance

#### 8\. How do you unwrap arguments when partially applying a function using `Function.prototype.bind` vs. a custom implementation?

The mechanism is different under the hood.

- **`Function.prototype.bind`**: This is a native browser implementation. When you create a bound function like `const add5 = add.bind(null, 5)`, the engine internally stores the `this` context (`null` here) and the pre-filled arguments (`[5]`). When you later call `add5(10)`, the engine executes the original `add` function, prepending the stored arguments to the new ones, effectively calling `add(5, 10)`.

- **Custom Implementation**: A custom partial application function uses a **closure**. The outer function takes the original function and the arguments to pre-fill. It returns a new function that "closes over" these initial arguments. When this new function is called, it takes the new arguments and concatenates them with the closed-over arguments before calling the original function.

  ```javascript
  function partial(fn, ...initialArgs) {
    // This returned function is a closure.
    return function (...laterArgs) {
      // It combines the initial and later arguments.
      return fn.apply(this, [...initialArgs, ...laterArgs]);
    };
  }
  ```

---

#### 9\. Discuss performance considerations when using currying and composition in hot code paths.

While these patterns offer excellent readability and maintainability, there's a minor performance cost. In a **"hot code path"**—a piece of code that runs very frequently, like in a game loop or intensive data processing—this cost can become a factor.

The overhead comes from:

- **Increased Function Calls**: Each step in a composed or curried chain is a separate function call, which adds a frame to the call stack and has a small but non-zero cost.
- **Argument Handling**: Spreading, collecting, and concatenating arguments in generic utilities adds extra work.

For 99% of application code, this overhead is completely negligible and the benefits of clean, modular code are far more important. However, if you've profiled your application and identified a bottleneck in a hot path, replacing a long function chain with a single, monolithic, imperative function might be a valid optimization.

---

#### 10\. How can you use rest parameters with currying to support variable arity?

This is a tricky situation because generic `curry` utilities typically rely on `fn.length` to know when all arguments have been supplied. A function with rest parameters (a variadic function) has a `length` property that only counts the named parameters before the `...rest`. For example, `(a, ...rest) => {}` has a `length` of 1.

This breaks the standard currying logic. To support variadic functions, you'd need a different strategy, such as:

- **Explicit Execution**: A special call, often with no arguments `()`, signals that all arguments have been provided and the function should execute.

  ```javascript
  // A conceptual variadic curry utility
  const variadicAdd = curryWithTerminator(function (...args) {
    return args.reduce((a, b) => a + b, 0);
  });

  const result = variadicAdd(10)(20)(30)(); // The final `()` executes it.
  console.log(result); // 60
  ```

---

#### 11\. Explain how context (`this`) is handled in curried functions and composed functions.

Handling `this` requires care with these patterns.

- **Currying**: The context can be lost if not handled properly. A well-written generic `curry` utility should capture the `this` context from the final call and use `fn.apply(this, args)` to ensure the original function is executed with the correct context. If you build the curried function using arrow functions, `this` will be lexically bound from where the function was defined, which may not be what's intended.

- **Composition (`pipe`/`compose`)**: The `this` context is almost always lost. Each function in the pipeline is typically invoked as a standalone function, so its `this` will be `undefined` (in strict mode). If you need to preserve a specific context for a function in the chain, you must explicitly bind it beforehand: `pipe(myFunc.bind(myContext), anotherFunc)`.

---

#### 12\. How can error handling be integrated into composed functions?

A standard `pipe` or `compose` pipeline is "brittle"—it will throw an error and stop execution at the first function that fails.

To make a pipeline safer, you can't just use a `try...catch` inside the utility, as that would catch everything. A more functional approach involves each function in the pipeline returning a predictable "wrapper" object, similar to an `Either` monad.

```javascript
// A simple wrapper for success or failure
const tryCatch =
  (fn) =>
  (...args) => {
    try {
      return { ok: true, value: fn(...args) };
    } catch (error) {
      return { ok: false, error };
    }
  };

const safePipe =
  (...fns) =>
  (initialValue) =>
    fns.reduce(
      (result, fn) => {
        // If the previous step failed, just pass the error along.
        if (!result.ok) return result;

        // Otherwise, run the next function.
        return fn(result.value);
      },
      { ok: true, value: initialValue }
    );

// --- Usage ---
const parseJson = tryCatch(JSON.parse);
const getProp = (key) => tryCatch((obj) => obj[key]);

const getNestedProp = safePipe(parseJson, getProp("user"), getProp("address"));

const result = getNestedProp('{"user":{"address":"123 Main St"}}');
// result is { ok: true, value: "123 Main St" }

const failedResult = getNestedProp('{"user":{}}');
// failedResult is { ok: false, error: TypeError... }
```

---

#### 13\. Describe a real-world scenario where currying or composition can simplify code.

- **Currying**: A great scenario is in configuring generic utilities. Imagine you have a `fetch` wrapper. You can curry it to create pre-configured API endpoints.

  ```javascript
  const makeRequest = curry((baseUrl, method, endpoint) => {
    return fetch(`${baseUrl}${endpoint}`, { method });
  });

  const myApi = makeRequest("https://api.myservice.com");
  const getUser = myApi("GET", "/users/123"); // Easy to reuse
  const postUser = myApi("POST", "/users");
  ```

- **Composition**: A perfect use case is processing user input in a web application. Each step—trimming whitespace, validating, sanitizing, and saving—can be a small, pure function.

  ```javascript
  const processComment = pipe(
    trim,
    removeProfanity,
    capitalize,
    saveCommentToDb
  );

  processComment("  what a badword post!  ");
  ```

  This makes the logic clear, testable, and easy to modify.

---

#### 14\. How do you test curried and composed functions for correctness?

You should test them at different levels.

- **For Curried Functions**:

  1.  Test that calling it with all arguments at once works correctly.
  2.  Test that calling it with fewer arguments returns a function.
  3.  Test a partially applied function to ensure it works correctly when called with the remaining arguments.

- **For Composed Functions**:

  1.  **Unit Test** each small function that makes up the pipeline in isolation. This is the biggest advantage of this pattern.
  2.  **Integration Test** the final composed function. Give it a known input and assert that it produces the final expected output.

- **Edge Cases**: For utilities like `curry` or `pipe`, test what happens when you pass non-functions, no arguments, or functions that throw errors.

---

#### 15\. Explain how to combine async functions using composition or pipe.

A standard `pipe` or `compose` function built with `reduce` is synchronous and will not work correctly with `async` functions—it will just pass promises down the chain.

You need an **async-aware** version of the utility. It can be built using `reduce` as well, but the callback to `reduce` must be `async` and it must `await` the result of the previous step before calling the next function.

```javascript
const asyncPipe =
  (...fns) =>
  (initialValue) => {
    return fns.reduce(async (accPromise, fn) => {
      // Wait for the previous promise to resolve
      const value = await accPromise;
      // Then call the next function with the resolved value
      return fn(value);
    }, Promise.resolve(initialValue)); // Start the chain with a resolved promise
  };

// --- Usage ---
const fetchUser = async (id) => ({ id, name: `User ${id}` });
const getPostsForUser = async (user) => `Posts for ${user.name}`;

const getUserPosts = asyncPipe(fetchUser, getPostsForUser);

getUserPosts(1).then(console.log); // "Posts for User 1"
```

---

Alright, let's finish up with these last few.

---

### Low Importance

#### 16\. What are point-free style functions? How does currying enable point-free programming?

**Point-free style** is a way of writing code where the data being operated on—the "point"—is not explicitly named in the function's definition. You define a function simply by combining other functions.

**Currying is a key enabler** of this style. By creating specialized functions that are "waiting" for their data, you can pipe them together without needing to write wrapper functions that explicitly handle the data argument.

```javascript
const users = [
  { name: "Frodo", active: true },
  { name: "Sam", active: false },
];

// "Pointful" style - `users` is explicitly named and handled.
const getActiveUserNamesPointful = (users) => {
  return users.filter((user) => user.active).map((user) => user.name);
};

// "Point-free" style - `filter` and `map` are assumed to be curried.
// The `users` array is the implicit "point" that will be fed into the pipeline.
// const getActiveUserNamesPointFree = pipe(
//   filter(user => user.active),
//   map(user => user.name)
// );

// getActiveUserNamesPointFree(users);
```

This style can be very concise and declarative, but it can also be less readable for those unfamiliar with it.

---

#### 17\. How do you implement memoization for curried functions?

**Memoization** is a caching optimization where a function stores the results of expensive computations and returns the cached result for the same inputs on subsequent calls.

For curried functions, the most straightforward approach is to **memoize the original, uncurried function first**, and then curry the memoized version. This ensures the cache key is based on the complete set of final arguments.

```javascript
function memoize(fn) {
  const cache = {};
  return function (...args) {
    const key = JSON.stringify(args);
    if (cache[key]) {
      return cache[key];
    }
    const result = fn(...args);
    cache[key] = result;
    return result;
  };
}

// 1. Original expensive function
const slowAdd = (a, b) => {
  // Simulate expensive work
  return a + b;
};

// 2. Memoize the original function
const memoizedAdd = memoize(slowAdd);

// 3. Curry the memoized version
const curriedMemoizedAdd = curry(memoizedAdd);

const add5 = curriedMemoizedAdd(5);
add5(10); // Computes and caches the result for [5, 10]
add5(10); // Returns the cached result instantly
```

---

#### 18\. Explain how lazy evaluation can be achieved using composition.

Native function composition in JavaScript is "eager"—it computes the result immediately. To achieve **lazy evaluation**, the functions in the pipeline must operate on and return **iterators or generators** instead of fully computed values or arrays.

The composition builds a final generator that represents the entire sequence of operations. The computation is only performed when you actually try to consume the generator (e.g., with a `for...of` loop or by spreading it into an array). This avoids creating intermediate arrays and only does the minimum work required.

---

#### 19\. How can you invert the order of arguments in a curried function? Provide a utility to flip arguments.

You can create a `flip` utility that takes a function and returns a new function that accepts the same arguments but in reverse order.

```javascript
const flip =
  (fn) =>
  (...args) =>
    fn(...args.reverse());

// --- Usage ---
const subtract = (a, b) => a - b;
console.log(subtract(10, 5)); // 5

const flippedSubtract = flip(subtract);
console.log(flippedSubtract(10, 5)); // -5 (because it becomes 5 - 10)

// This can be useful in a pipeline with curried functions.
// const result = pipe(
//   flippedSubtract(10) // Creates a function equivalent to `x => 10 - x`
// )(5); // result is 5
```

---

#### 20\. Describe how composition relates to monads in other languages.

This is a deep functional programming topic. In simple terms:

- **Composition** is the act of chaining functions together (`f(g(x))`).
- A **Monad** is a design pattern—a type of "wrapper" or "container"—that provides a reliable structure for composition, especially when dealing with side effects, errors, or asynchronous operations.

Think of `pipe` as a simple assembly line. A **monad** is like an advanced assembly line with built-in error checking (`Either` monad) or rules for handling empty boxes (`Maybe` monad). It ensures that the composition doesn't break when it encounters these special cases.

---

#### 21\. What are transducers? How do they relate to the composition of `map`/`filter`/`reduce`?

A **transducer** is a high-performance pattern that takes composition a step further. It's a "composable algorithm" that is decoupled from the input source.

Essentially, you compose your `map` and `filter` logic together first to create a single, efficient **transducer function**. This transducer can then be used with `reduce` (or other processes) to transform a data source in a **single pass**, completely avoiding the creation of any intermediate arrays. They are a powerful but advanced optimization.

---

#### 22\. How can you implement a `validate` function in a pipeline that short-circuits on failure?

The validation function needs a way to stop the pipeline. The two common ways are:

1.  **Throw an Error**: This is the simplest approach. The `validate` function throws an error if the data is invalid, which will break the pipeline. The entire call must be wrapped in a `try...catch` block.

2.  **Return a Special Value**: In a "safe pipe" implementation (as discussed earlier), the validator would return a specific object (e.g., `{ ok: false, error: 'Invalid email' }`). The `pipe` utility itself would then be designed to check for this `ok: false` state and bypass all subsequent functions, immediately returning the error object.

---

#### 23\. Explain how you could use generator functions with composition for asynchronous control flow.

Before `async/await`, generators were a powerful way to manage complex async flows. You could create a pipeline of generator functions. Each generator would `yield` a promise.

A special **"runner" function** would then execute this pipeline. It would start the first generator, wait for the yielded promise to resolve, and then pass the resolved value to the next generator in the chain. This manually simulates the behavior of `async/await`, providing a way to write asynchronous code that looks synchronous. However, today, using an `asyncPipe` with `async/await` is a much cleaner and more standard approach.

---
