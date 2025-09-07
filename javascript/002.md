### High Importance

#### 1\. Define function scope and block scope in JavaScript. Explain how `var`, `let`, and `const` differ in scoping.

Certainly. **Scope** determines the accessibility of variables. JavaScript has two main types of scope:

- **Function Scope**: When a variable is declared with `var`, it is accessible anywhere within the **function** it was defined in, regardless of blocks like `if` statements or `for` loops.
- **Block Scope**: When a variable is declared with `let` or `const`, it is only accessible within the **block** (`{...}`) in which it was defined. A block can be a function, an `if` statement, a `for` loop, or any pair of curly braces.

Here’s how `var`, `let`, and `const` differ:

- **`var`** is **function-scoped**.
- **`let`** is **block-scoped**.
- **`const`** is also **block-scoped**, but with an added rule: its value cannot be reassigned after declaration.

This code illustrates the difference clearly:

```javascript
function scopeTest() {
  // var is function-scoped
  var movie = "Inception";

  if (true) {
    // let and const are block-scoped
    let director = "Nolan";
    const year = 2010;

    // movie is accessible here
    console.log(movie); // "Inception"
  }

  // director and year are NOT accessible here because they are block-scoped
  // console.log(director); // ReferenceError: director is not defined
  // console.log(year);     // ReferenceError: year is not defined
}
```

Because of its more predictable behavior, block scope with `let` and `const` is the standard in modern JavaScript.

---

#### 2\. What is lexical scope? How does JavaScript determine variable access during execution?

**Lexical scope**, also known as static scope, means that the scope of a variable is determined by its **position in the source code** at the time of writing, not by where the function is called.

In other words, an inner function has access to the variables and parameters of its outer (parent) functions simply because of where it was physically written. The JavaScript engine knows which variables a function can access by looking at the nesting of function blocks in the code.

During execution, when the engine needs to find a variable, it follows a simple rule:

1.  Look in the current function's scope.
2.  If not found, look in the scope of the outer function.
3.  Continue this process up the chain of parent scopes until the variable is found or the global scope is reached.

<!-- end list -->

```javascript
const globalVar = "I am global";

function outer() {
  const outerVar = "I am from outer";

  function inner() {
    // inner() can access outerVar and globalVar due to lexical scoping.
    console.log(outerVar);
    console.log(globalVar);
  }

  inner();
}

outer(); // Prints "I am from outer" and "I am global"
```

---

#### 3\. What are closures? Describe how they work and why they are useful.

A **closure** is a function combined with its surrounding state, or its **lexical environment**.

In simple terms, a closure gives an inner function the ability to **remember and access the variables of its outer function**, even after the outer function has finished executing and returned. The inner function essentially "closes over" the variables of its parent, carrying them around in a sort of "backpack."

**How they work:** When a function is created, it creates a closure. This closure saves references to the variables that were in scope at the time of its creation. When the function is later executed, it can still access these saved variables.

**Why they are useful:**

- **Data Encapsulation / Private State**: They allow us to create private variables that can only be accessed by a specific set of public functions.
- **Stateful Functions**: They enable functions to maintain state between calls, like in a counter or a generator.
- **Callbacks and Event Handlers**: They are essential for asynchronous operations, allowing a callback function to access variables from the context in which it was created.

---

#### 4\. Write a closure that implements a counter.

Absolutely. This is a classic example.

The outer function `makeCounter` runs once, creating the `count` variable. The inner function that it returns is a closure. Each time this inner function is called, it can access and modify the `count` variable from its parent's scope.

```javascript
function makeCounter() {
  // 1. `count` is a private variable, protected by the closure's scope.
  let count = 0;

  // 2. The returned function is a closure.
  //    It "remembers" the `count` variable.
  return function () {
    count++;
    return count;
  };
}

// 3. Create a counter instance.
const counter1 = makeCounter();

console.log(counter1()); // Output: 1
console.log(counter1()); // Output: 2

// 4. Each instance has its own separate, private state.
const counter2 = makeCounter();
console.log(counter2()); // Output: 1
```

---

#### 5\. How can closures be used to create private variables in JavaScript? Provide a code example.

Closures are the original way to achieve data privacy in JavaScript. You create an outer function that holds the "private" variables and return an object containing "public" methods that can access and manipulate those private variables.

The private data is inaccessible from the outside because it only exists within the scope of the outer function.

```javascript
function createBankAccount(initialBalance) {
  // This `balance` variable is private. It cannot be accessed directly.
  let balance = initialBalance;

  // Return an object with public "methods" that form a closure.
  return {
    deposit: function (amount) {
      if (amount > 0) {
        balance += amount;
      }
    },
    withdraw: function (amount) {
      if (amount > 0 && amount <= balance) {
        balance -= amount;
      }
    },
    getBalance: function () {
      return balance;
    },
  };
}

const myAccount = createBankAccount(100);

console.log(myAccount.getBalance()); // 100
myAccount.deposit(50);
myAccount.withdraw(20);
console.log(myAccount.getBalance()); // 130

// You cannot access the balance directly, it's truly private.
console.log(myAccount.balance); // undefined
```

---

#### 6\. Explain the scope chain. How does the engine resolve variable lookups in nested functions?

The **scope chain** is the "lookup ladder" that the JavaScript engine uses to find a variable. It's an ordered list of all the scopes that a function has access to.

When code inside a function references a variable, the engine performs the following lookup process:

1.  **Local Scope**: It first checks if the variable exists within the current function's scope.
2.  **Outer Scope(s)**: If not found, it moves up to the scope of the outer function (the function's lexical parent) and checks there.
3.  **Continue Upward**: It continues this process, moving up one level in the scope chain at a time.
4.  **Global Scope**: If the variable is still not found, it reaches the final link in the chain: the global scope.
5.  **Error**: If the variable is not found in the global scope, a `ReferenceError` is thrown.

This chain is established based on the lexical structure of the code—how the functions are nested within each other at the time of writing.

---

#### 7\. What is the temporal dead zone (TDZ) in ES6? How does it affect `let` and `const` declarations?

The **Temporal Dead Zone (TDZ)** is a behavior that occurs with variables declared using `let` and `const`.

Although `let` and `const` declarations are hoisted (their names are registered by the engine at the top of their scope), they are **not initialized** with a value like `var` is. The TDZ is the period from the start of the block until the line where the `let` or `const` variable is actually declared.

If you try to access the variable while it's in the TDZ, JavaScript will throw a `ReferenceError`.

```javascript
function checkTDZ() {
  // Start of the TDZ for `myVar`
  // console.log(myVar); // ReferenceError: Cannot access 'myVar' before initialization

  let myVar = "I am alive!"; // End of the TDZ for `myVar`

  console.log(myVar); // "I am alive!"
}
```

The TDZ enforces a stricter coding practice by preventing you from using a variable before it has been declared, which helps catch potential bugs.

---

### Medium Importance

#### 8\. What are immediately invoked function expressions (IIFE) and how do they relate to closures?

An **Immediately Invoked Function Expression (IIFE)** is a function that is defined and executed as soon as it is created. The syntax involves wrapping a function expression in parentheses and then calling it with another pair of parentheses.

```javascript
(function () {
  const message = "Hello from inside the IIFE!";
  console.log(message);
})(); // The final () invokes the function immediately.
```

**Relation to Closures and Scope**: Before `let` and `const` introduced block scope, IIFEs were the primary tool for creating a **private scope**. Any variables declared inside an IIFE were not added to the global scope, preventing "global namespace pollution." This is a direct application of function scope, and it creates a closure that encapsulates its internal state.

---

#### 9\. How does garbage collection interact with closures? Can closures lead to memory leaks?

Normally, when a function finishes executing, its local variables are marked for **garbage collection** (GC) because they are no longer needed.

However, with closures, the story is different. If an inner function is still alive (e.g., it was returned and is stored in a variable), it maintains a reference to its parent's scope. Because of this reference, the garbage collector **cannot** clean up the variables from the parent's scope. They must be kept in memory.

This is precisely what allows closures to work, but it can also lead to **memory leaks**. A memory leak occurs if a closure is unintentionally kept in memory long after it's needed. A common example is attaching a closure as an event listener to a DOM element. If the element is later removed from the DOM, but the event listener is not detached, the closure (and all the variables it closed over) will remain in memory forever.

**Prevention**: To prevent this, you must explicitly remove event listeners or nullify references to closures when they are no longer required, allowing the GC to do its job.

---

#### 10\. Demonstrate with code how a closure retains access to variables after the outer function has returned.

Of course. Here's a clear example:

```javascript
function createGreeter(greeting) {
  // 1. The outer function defines this `greeting` variable.
  const myGreeting = greeting;

  // 2. The outer function returns, and its scope is theoretically "gone".
  return function (name) {
    // 3. But this inner function, a closure, still remembers `myGreeting`.
    console.log(`${myGreeting}, ${name}!`);
  };
}

// 4. Call the outer function. It runs and returns the inner function.
const sayHello = createGreeter("Hello");
const sayHi = createGreeter("Hi");

// 5. Long after `createGreeter` has finished, we call the inner functions.
//    They still have access to the `myGreeting` from their respective creations.
sayHello("Alice"); // "Hello, Alice!"
sayHi("Bob"); // "Hi, Bob!"
```

---

#### 11\. Can closures be used with asynchronous code (e.g., in `setTimeout` callbacks)? Explain with an example.

Yes, absolutely. Closures are fundamental to making asynchronous code work correctly in JavaScript. The callback function passed to an async operation like `setTimeout` is a closure. It remembers the environment where it was created.

This allows the callback to access and use variables from that environment, even though the callback executes much later.

```javascript
function scheduleMessage(message, delay) {
  // The variables `message` and `delay` are part of this scope.

  setTimeout(function () {
    // This anonymous function is a closure.
    // It runs after `delay` milliseconds.
    // By the time it runs, `scheduleMessage` has already finished.
    // But it still remembers the `message` variable from its parent scope.
    console.log(`Your message: "${message}"`);
  }, delay);

  console.log("Message scheduled!");
}

scheduleMessage("Your meeting is in 5 minutes", 2000);
// Output:
// Message scheduled!
// (2 seconds later)
// Your message: "Your meeting is in 5 minutes"
```

Without closures, the callback would have no way to know what `message` it was supposed to log.

---

### Medium Importance (Continued)

#### 12\. Explain hoisting of function declarations vs. function expressions and how it interacts with scope.

This is a great question that ties hoisting and scope together.

- **Function Declarations (`function name() {}`)**: These are **fully hoisted**. The entire function, both its name and its body, is lifted to the top of its containing scope (be it function or global scope) during the creation phase. This means you can call a function declaration _before_ it appears in your code.

- **Function Expressions (`const name = function() {}`)**: With expressions, only the variable declaration (`var`, `let`, or `const`) is hoisted. The function body assigned to it is not.

  - If you use `var`, the variable is hoisted and initialized with `undefined`. Calling it before the assignment line results in a `TypeError`.
  - If you use `let` or `const`, the variable is hoisted but remains in the Temporal Dead Zone (TDZ). Calling it before the assignment line results in a `ReferenceError`.

**Interaction with Scope**: A function declaration is scoped to the nearest enclosing function or the global scope. A function expression is scoped to the variable it's assigned to, which follows standard block-scoping rules for `let` and `const`.

---

#### 13\. What is the difference between module scope (ES modules) and traditional function/block scope?

The key difference is that each ES module file has its own top-level **module scope**.

In a traditional script loaded via a `<script>` tag, a variable declared with `var` at the top level becomes a **global variable**, attached to the `window` object in browsers. This can easily lead to naming conflicts between different scripts.

In an ES module, variables declared at the top level are **scoped to that module only**. They are not global. This provides a clean separation and prevents accidental name collisions. To make something from a module accessible to the outside world, you must explicitly `export` it.

```javascript
// --- file: moduleA.js ---
// This `apiKey` is private to moduleA.js
const apiKey = "xyz123";

export function connect() {
  console.log("Connecting...");
}

// --- file: main.js ---
import { connect } from "./moduleA.js";

connect(); // Works because it was exported.
// console.log(apiKey); // ReferenceError: apiKey is not defined. It's private to moduleA.
```

---

### Low Importance

#### 14\. Why might relying too heavily on closures be problematic in large codebases?

While closures are powerful, overusing them can introduce a couple of challenges in large-scale applications:

1.  **Memory Management**: Because variables inside a closure are not garbage collected as long as the closure is in use, it can lead to higher memory consumption. If closures are created frequently and live for a long time, it can contribute to memory bloat or even leaks if not managed carefully.
2.  **Readability and Debugging**: Deeply nested closures create complex scope chains. This can make the code harder to reason about and debug. It might not be immediately obvious where a variable's state is coming from or being modified, requiring a developer to trace through multiple layers of scopes.

In modern JavaScript, features like classes with private fields (`#`) or ES modules often provide a more explicit and readable way to achieve encapsulation.

---

#### 15\. How do closures differ from objects with private properties (e.g., using `#` fields)?

Both are patterns for encapsulation, but they achieve it differently.

- **Closures** achieve privacy through **lexical scope**. The "private" data exists as a local variable in a parent function's scope. The "public" methods are inner functions that have access to this scope. It's a pattern based entirely on how functions and scopes work.
- **Objects with `#` private fields** achieve privacy through **dedicated language syntax**. The `#` prefix tells the JavaScript engine to enforce privacy, making the field inaccessible outside the class body. This is generally considered more explicit, more readable, and more intentional, especially for developers familiar with class-based programming.

Essentially, closures are a functional programming pattern for privacy, while private class fields are an object-oriented programming feature.

---

#### 16\. Explain currying and its relationship to closures.

**Currying** is a technique of transforming a function that takes multiple arguments into a sequence of nested functions, where each subsequent function takes only one argument.

**Closures are the mechanism that makes currying possible in JavaScript.**

Each function in the curried sequence is a closure. It "remembers" the argument that was passed to its parent function. When the final function in the chain is called, it has access to all the previously passed arguments through the nested scope chains of the closures.

```javascript
// A normal function
const add = (a, b) => a + b;

// A curried version of the same function
const curriedAdd = function (a) {
  // This inner function is a closure. It remembers `a`.
  return function (b) {
    return a + b;
  };
};

const addFive = curriedAdd(5); // Returns the inner function, with `a` "locked in" as 5.
console.log(addFive(3)); // 8
console.log(addFive(10)); // 15
```

---

#### 17\. How can you inspect the current scope chain in debugging tools?

You can easily inspect the scope chain using browser developer tools, like those in Chrome.

1.  Open the DevTools and go to the **Sources** tab.
2.  Find the JavaScript file you want to debug.
3.  Set a **breakpoint** by clicking on a line number inside the function (specifically, the closure) you want to inspect.
4.  Run your code. Execution will pause when it hits the breakpoint.
5.  On the right-hand panel, look for the **Scope** section. It will be expanded, showing you the entire scope chain, typically broken down into:
    - **Local**: Variables declared within the currently executing function.
    - **Closure**: Variables from any parent functions that the current function has closed over.
    - **Global**: The global scope (`window` in browsers).

This is an incredibly powerful tool for understanding how closures work and for debugging scope-related issues.

---

#### 18\. Describe a scenario where a closure can cause unexpected behavior and how to fix it.

The most classic example is the **loop variable trap**. This happens when you use `var` in a `for` loop to create closures, for instance, with `setTimeout`.

**The Problem**: Because `var` is function-scoped, all the `setTimeout` callbacks created in the loop close over the _exact same_ `i` variable. The loop finishes almost instantly, leaving `i` with its final value. When the timeouts eventually fire, they all see this final value.

```javascript
for (var i = 0; i < 3; i++) {
  setTimeout(function () {
    // All three callbacks reference the same `i`.
    // By the time they run, the loop is done and `i` is 3.
    console.log(i);
  }, 100);
}
// Expected: 0, 1, 2
// Actual Output: 3, 3, 3
```

**The Fix**: The modern solution is simple: use `let` instead of `var`. Because `let` is **block-scoped**, each iteration of the loop gets its _own, new_ `i` variable. Each callback closes over a different `i`, preserving the value from that specific iteration.

```javascript
for (let i = 0; i < 3; i++) {
  setTimeout(function () {
    // Each callback closes over a different `i` from each loop iteration.
    console.log(i);
  }, 100);
}
// Correct Output: 0, 1, 2
```

---

#### 19\. Write a function that generates unique IDs using closures to maintain an internal counter.

Certainly. This is a practical use case for a closure, very similar to the counter example. The closure maintains the state of the last ID generated, ensuring it's always unique.

```javascript
function createIdGenerator(prefix = "id") {
  // `lastId` is the private state, protected by the closure.
  let lastId = 0;

  // The returned function is the closure that maintains the state.
  return function () {
    lastId++;
    return `${prefix}_${lastId}`;
  };
}

// Create an instance of the generator.
const generateUserId = createIdGenerator("user");

console.log(generateUserId()); // "user_1"
console.log(generateUserId()); // "user_2"
console.log(generateUserId()); // "user_3"

// You can create another independent generator.
const generatePostId = createIdGenerator("post");
console.log(generatePostId()); // "post_1"
```

---

#### 20\. Explain the difference between closures in JavaScript and similar concepts in other languages (e.g., Python).

The core concept of a closure—a function remembering its lexical environment—is similar across many languages, but there are syntactical differences.

The main difference between JavaScript and Python is in **how they handle modifying closed-over variables**.

- **JavaScript**: A closure can read _and_ modify variables from its outer scope implicitly. There's no special syntax required.
  ```javascript
  function outer() {
    let count = 0;
    return () => {
      count++;
      console.log(count);
    }; // Implicitly modifies count
  }
  ```
- **Python**: A closure can read outer variables implicitly. However, to _modify_ an outer-scoped variable, you must explicitly declare it with the `nonlocal` keyword. This makes the intent to modify a variable from a parent scope more deliberate.
  ```python
  def outer():
      count = 0
      def inner():
          nonlocal count  # Must explicitly declare intent to modify
          count += 1
          print(count)
      return inner
  ```

So, while the principle is the same, Python is more explicit about mutation, which can sometimes prevent accidental side effects.

---
