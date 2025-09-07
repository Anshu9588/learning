# Topic:- Hoisting & Execution Context**
  - Understand creation vs. execution phases
  - Experiment with function vs. variable hoisting
---

### High Importance

#### 1\. What are the two phases of JavaScript execution context and what happens in each (creation vs. execution)?

Every time JavaScript runs code, it does so inside an **execution context**. This context is created in two phases:

1.  **Creation Phase**: Before any code is executed, the JavaScript engine scans the code and sets up the environment. It does three main things:

    - **Memory Allocation (Hoisting)**: It finds all variable (`var`, `let`, `const`) and function declarations and allocates memory for them. `var` variables are initialized to `undefined`, while functions are stored in their entirety.
    - **Scope Chain Creation**: It establishes the scope chain, which determines how variables will be looked up (i.e., it links to its parent's scope).
    - **`this` Binding**: It determines the value of the `this` keyword for that context.

2.  **Execution Phase**: After the setup is complete, the engine reads the code line by line and executes it. In this phase, it:

    - Assigns the actual values to the variables it allocated memory for earlier.
    - Executes the code's logic and calls functions.

Think of it like a chef preparing to cook (**Creation Phase**: getting out all the ingredients and tools) and then actually cooking the meal (**Execution Phase**: following the recipe step-by-step).

---

#### 2\. Explain hoisting. How are variable declarations (`var`, `let`, `const`) and function declarations hoisted differently?

**Hoisting** is JavaScript's behavior of moving all declarations to the top of their scope during the creation phase. However, the _initialization_ of these declarations varies significantly.

- **`var`**: The declaration is hoisted and the variable is automatically **initialized with `undefined`**. This means you can access a `var` variable before its line in the code without an error, and you'll get `undefined`.

- **`let` and `const`**: Their declarations are also hoisted, but they are **not initialized**. They are placed in a "Temporal Dead Zone" (TDZ). Trying to access them before their declaration line will result in a `ReferenceError`.

- **Function Declarations**: These are **fully hoisted**—both the function's name and its entire body are moved to the top. This allows you to call a function before it's physically written in the code.

<!-- end list -->

```javascript
// Function declarations are fully hoisted
sayHello(); // Works fine, logs "Hello!"

// `var` is hoisted and initialized as undefined
console.log(myVar); // undefined

// `let` is hoisted but not initialized (in TDZ)
// console.log(myLet); // ReferenceError

function sayHello() {
  console.log("Hello!");
}

var myVar = "I am a var";
let myLet = "I am a let";
```

---

#### 3\. What is the temporal dead zone (TDZ)? How does it affect `let` and `const` variables before they are initialized?

The **Temporal Dead Zone (TDZ)** is the term for the state where `let` and `const` variables exist from the start of their enclosing scope until the line where they are declared and initialized.

While their declarations are hoisted, they are not accessible. The TDZ is not a place; it's a time—the time between the scope's start and the variable's declaration.

**Effect**: If you attempt to read or write to a `let` or `const` variable inside its TDZ, JavaScript will throw a `ReferenceError`. This is a protective measure that prevents bugs that could arise from using a variable before it's been properly initialized.

```javascript
{
  // Start of a new block scope
  // TDZ for `bestMovie` starts here.
  // console.log(bestMovie); // ReferenceError: Cannot access 'bestMovie' before initialization

  let bestMovie = "The Matrix"; // TDZ for `bestMovie` ends here.
  console.log(bestMovie); // "The Matrix"
}
```

---

#### 4\. How does JavaScript handle function declarations vs. function expressions during hoisting? Provide code examples.

This is a critical distinction that often comes up in interviews.

- **Function Declarations**: As mentioned, they are fully hoisted. The engine knows about the entire function before the execution phase begins.

- **Function Expressions**: They are not hoisted in the same way. What gets hoisted is the **variable declaration** (`var`, `let`, or `const`) that the function is assigned to. The function body itself is an assignment, which only happens during the execution phase.

Here's a direct comparison:

```javascript
// Example 1: Function Declaration
// This works because the entire function is hoisted.
hoistedDeclaration(); // "I am a declaration!"

function hoistedDeclaration() {
  console.log("I am a declaration!");
}

// Example 2: Function Expression
// This fails because only `hoistedExpression` is hoisted (as `undefined`), not the function itself.
// hoistedExpression(); // TypeError: hoistedExpression is not a function

var hoistedExpression = function () {
  console.log("I am an expression!");
};

// It works correctly after the assignment.
hoistedExpression(); // "I am an expression!"
```

---

#### 5\. Describe the global execution context vs. function execution contexts. How is the call stack used to manage them?

- **Global Execution Context (GEC)**: This is the default, base-level context. It's created when your script first starts to run. It creates the global object (e.g., `window` in a browser) and sets the `this` keyword to point to it. There is only one GEC.

- **Function Execution Context (FEC)**: A new FEC is created **every time a function is called**. Each function call gets its own context with its own set of local variables, a scope chain, and its own `this` value. When the function finishes, its FEC is destroyed.

The **Call Stack** is the mechanism the engine uses to manage all these contexts. It's a LIFO (Last-In, First-Out) structure.

1.  When the script starts, the **GEC** is pushed onto the bottom of the stack.
2.  When a function `foo()` is called, its **FEC** is pushed on top of the GEC.
3.  If `foo()` calls another function `bar()`, `bar()`'s **FEC** is pushed on top of `foo()`'s.
4.  When `bar()` returns, its FEC is **popped off** the stack.
5.  When `foo()` returns, its FEC is **popped off**.
6.  The script ends when the GEC is popped off.

---

#### 6\. What happens when you reference a variable declared with `var`, `let`, or `const` before its declaration? Show code examples.

This goes back to hoisting and the TDZ.

- **`var`**: You get `undefined`. The variable is known to the engine and has been given a default value.

  ```javascript
  console.log(myVar); // Output: undefined
  var myVar = 10;
  ```

- **`let`**: You get a `ReferenceError`. The variable is in the Temporal Dead Zone and cannot be accessed.

  ```javascript
  // console.log(myLet); // ReferenceError: Cannot access 'myLet' before initialization
  let myLet = 20;
  ```

- **`const`**: You get a `ReferenceError`, for the same reason as `let`. It's also in the TDZ.

  ```javascript
  // console.log(myConst); // ReferenceError: Cannot access 'myConst' before initialization
  const myConst = 30;
  ```

---

### Medium Importance

#### 7\. Can you explain how arrow functions differ in terms of `this` binding and hoisting compared to regular functions?

Yes, they differ in two very important ways.

1.  **`this` Binding**:

    - **Regular Functions**: Get their own `this` value, which is determined **dynamically** based on how the function is called (e.g., as a method, as a standalone function, with `new`).
    - **Arrow Functions**: Do **not** have their own `this`. They **lexically inherit** `this` from their parent scope. The value of `this` inside an arrow function is the same as the value of `this` outside of it. This makes them predictable and great for callbacks.

2.  **Hoisting**:

    - Arrow functions are always **function expressions**. They are not hoisted in the same way function declarations are. They follow the hoisting rules of the variable they are assigned to (`var`, `let`, or `const`), meaning you cannot call them before their assignment.

---

#### 8\. What is the role of the scope chain in execution context?

The scope chain is a critical component that is created during the **creation phase** of an execution context. Its role is to provide a mechanism for **variable lookup**.

Each execution context has a reference to its outer (parent) lexical environment. This creates a "chain" of scopes. When you reference a variable, the engine:

1.  Looks for the variable in the current execution context's scope.
2.  If it doesn't find it, it follows the reference to the parent's scope and looks there.
3.  It continues up this chain until it finds the variable or reaches the global scope.

So, the scope chain is essentially the "lookup path" that connects nested contexts and allows closures to work.

---

#### 9\. How does hoisting affect class declarations in ES6? What happens if you try to use a class before its declaration?

This is a great "gotcha" question. ES6 `class` declarations are hoisted, but they behave like `let` and `const`, not like `function` declarations.

They are hoisted to the top of their scope, but they are **not initialized**. They live in the **Temporal Dead Zone (TDZ)**.

Therefore, if you try to instantiate a class or even reference it before its declaration line, you will get a **`ReferenceError`**. You must always declare a class before you can use it.

```javascript
// const myCar = new Car(); // ReferenceError: Cannot access 'Car' before initialization

class Car {
  constructor(make) {
    this.make = make;
  }
}

const myCar = new Car("Toyota"); // This works perfectly.
```

---

Absolutely, let's get through the rest of this topic.

---

### Medium Importance (Continued)

#### 10\. Explain the difference between declaration and initialization phases for variables and functions.

This is a great question that gets to the heart of how the engine works.

- **Declaration** is the act of telling the engine that a variable or function exists. This happens during the **creation phase** (hoisting), where the engine allocates memory and registers the identifier (the name) in the current scope.

- **Initialization** is the act of assigning a value to a declared variable. This happens during the **execution phase**, when the engine reaches the line of code with the assignment.

Here's how it breaks down for different keywords:

- **`var`**: Declaration and initialization (to `undefined`) happen together during the creation phase. The actual value assignment happens later, during execution.
- **`let` and `const`**: Only the declaration happens during the creation phase. Initialization doesn't happen until the execution phase reaches their declaration line. The time between is the TDZ.
- **Function Declaration**: Both declaration and initialization (with the function body) happen fully during the creation phase.

---

#### 11\. What are the `arguments` object and the Activation Object in a function execution context?

This touches on some of the internal mechanics of the JavaScript engine.

- **Activation Object** (or Variable Object): This is a **conceptual object** that you can't access directly in your code. It's created during the creation phase of a function execution context. It holds all the local variables, parameters (arguments), and inner function declarations for that function. It's the "storage container" for everything in that scope.

- **`arguments` Object**: This is a **real, array-like object** that is a property of the Activation Object. It's available inside every _non-arrow_ function and contains a list of all the arguments that were passed to the function when it was called. While it's not a true array, you can access arguments by index (e.g., `arguments[0]`) and check its length.

---

#### 12\. How do IIFEs (Immediately Invoked Function Expressions) leverage execution context for variable privacy?

An **IIFE** works by creating a new **function execution context** the moment it's defined.

Here's the key:

1.  When the IIFE is encountered, a new FEC is created.
2.  During the creation phase of this context, all variables declared with `var` inside the IIFE are scoped _only_ to this new context.
3.  The function executes immediately.
4.  After it finishes, its execution context is popped off the call stack and destroyed (unless a closure is returned).

This process effectively creates a temporary, private "bubble" of scope. The variables inside are completely isolated and don't leak into the surrounding global or parent scope, which was the primary way to achieve encapsulation before ES6 modules and block scope.

---

#### 13\. Describe how the `this` keyword is resolved in different execution contexts.

The value of `this` is determined by how a function is called, which creates its execution context. There are four main rules:

1.  **Global Context**: Outside of any function, `this` refers to the global object (`window` in browsers). In strict mode, it's `undefined`.

2.  **Method Call**: When a function is called as a method of an object (e.g., `myObj.myMethod()`), `this` is set to the object the method was called on (`myObj`).

3.  **Constructor Call**: When a function is called with the `new` keyword, `this` is set to the brand-new object instance that is being created.

4.  **Arrow Function**: Arrow functions are special. They don't have their own `this` binding. They **lexically inherit** `this` from their parent's execution context.

---

#### 14\. What are block-level execution contexts for `let`/`const`? How do they differ from function-level contexts?

This is a subtle but important point. Technically, JavaScript only has global and function-level execution contexts. However, `let` and `const` introduced the concept of **block-level scope**, which is handled _within_ an execution context.

Think of it this way:

- A **function-level context** is created when a function is called. It manages the scope for the entire function.
- A **block-level context** (more accurately, a "lexical environment") is a smaller, nested scope created for a specific `{...}` block.
- Crucially, in a `for` loop, a **new block-level environment is created for each iteration**. This is why `let` works as expected in loops with closures—each iteration captures a variable from its own unique block-level scope. This is very different from `var`, which has only one function-level scope for the entire loop.

---

### Low Importance

#### 15\. How can you simulate block scoping for `var` (prior to ES6)?

Before `let` and `const` existed, the only way to create a new scope was with a function. You would simulate block scope by wrapping your code in an **IIFE (Immediately Invoked Function Expression)**.

```javascript
// Global scope
var x = 1;

if (true) {
  // We want a private scope here
  (function () {
    var x = 2; // This `x` is private to the IIFE
    console.log(x); // 2
  })();
}

console.log(x); // 1 (The global `x` is unaffected)
```

This pattern created a function execution context that acted like a block scope, preventing the inner `var x` from polluting the outer scope.

---

#### 16\. Explain how exception handling (`try/catch/finally`) influences the execution context.

The `try` and `finally` blocks don't create a new scope. However, the **`catch (e)` block does create its own special block scope**.

The error object that you declare in the `catch` clause (e.g., `e`) is a variable that is **scoped only to that `catch` block**. You cannot access it from outside the block. This is useful because it encapsulates the error information without leaking it into the surrounding scope.

```javascript
try {
  // ... some code that might throw an error
} catch (error) {
  // `error` only exists inside this block.
  console.log(error.message);
}

// console.log(error); // ReferenceError: error is not defined
```

---

#### 17\. Describe how tail-call optimization affects execution context and the call stack.

**Tail-Call Optimization (TCO)** is a feature where if the very last action of a function is to call another function, the engine can optimize the operation.

Normally, a new function call creates a new execution context and adds a new frame to the call stack. With TCO, the engine can **reuse the current stack frame** for the new function call instead of creating a new one on top.

The primary benefit is for deep recursive functions. Without TCO, each recursive call adds to the stack, eventually causing a "Maximum call stack size exceeded" error. With TCO, the recursion can run indefinitely without growing the stack.

It's important to note that while TCO is part of the ECMAScript specification, it has **very limited support** across major JavaScript engines today, so it's mostly a theoretical concept in interviews.

---

#### 18\. How does the `new` keyword create a new execution context when invoking constructor functions?

To be precise, the `new` keyword doesn't create a _different type_ of execution context. Calling a constructor is still a function call, so a standard **function execution context** is created.

However, the `new` keyword modifies **how that context is set up**, specifically regarding the `this` binding. Here’s what it does:

1.  Creates a new, empty plain JavaScript object.
2.  Links this new object to the constructor function's `prototype`.
3.  **Sets the `this` binding for the execution context to this new object.**
4.  Calls the constructor function.
5.  If the constructor doesn't explicitly return another object, it implicitly returns the new object (`this`).

---

#### 19\. What is the module execution context in ES modules, and how does it differ from a script execution context?

They are two different top-level contexts.

- **Script Execution Context**: This is the traditional context for a standard `<script>` file.

  - `this` at the top level refers to the **global object** (`window`).
  - Top-level `var`, `let`, and `const` declarations can become global variables (especially `var`).

- **Module Execution Context**: This is the context for an ES module (`<script type="module">` or `.js` files in Node.js).

  - `this` at the top level is **`undefined`**.
  - Each module has its **own lexical scope**. Top-level declarations are local to the module and are **not** added to the global object.
  - It's implicitly in **strict mode**.

The module context provides much better encapsulation and prevents the global namespace pollution that was common with script contexts.

---

#### 20\. How do Web Workers create separate execution contexts, and what are the implications for hoisting and scope?

A Web Worker runs in a completely **separate and parallel thread** from the main UI thread.

When you create a new Worker, the browser spins up a new OS-level thread and creates a brand-new JavaScript environment for it. This means the worker gets its own:

- **Global Execution Context**: It has its own global object, which is `self` (not `window`).
- **Call Stack**: It manages its own call stack independently.
- **Memory Heap**: It has its own memory space.

**Implications**: Because it's a totally separate environment, there is **no shared scope or memory** with the main thread. Hoisting, scope chains, and execution contexts all work exactly as they normally would, but they are completely isolated within the worker's world. You can't access the DOM, and communication with the main thread can only happen by passing messages with `postMessage()`. This isolation is what allows workers to perform heavy tasks without freezing the user interface.

---
