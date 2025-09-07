# JavaScript Syntax & Data Types: Learning Material and Interview Questions

**Scope:** Review JavaScript syntax and core data types (primitive vs. reference) and practice `typeof`, `null`, `undefined`, arrays, and objects.

---

## 1. Learning Material

### 1.1 JavaScript Syntax Overview

- **Statements & Expressions:**
  - Statements end with semicolons (optional but recommended).
  - Expressions produce values—e.g., arithmetic, function calls.
- **Variables:**
  - `let` and `const` for block scope; `const` for immutable bindings.
  - Avoid `var` due to hoisting quirks.
- **Comments:**
  - Single-line: `// comment`
  - Multi-line: `/* comment */`
- **Operators:**
  - Arithmetic (`+ – * / %`), assignment (`= += –=`), comparison (`== === != !==`), logical (`&& || !`).

---

### 1.2 Data Types

| Category           | Types                                                                  | Description                  |
| ------------------ | ---------------------------------------------------------------------- | ---------------------------- |
| Primitive          | `Number`, `String`, `Boolean`, `Undefined`, `Null`, `Symbol`, `BigInt` | Immutable, passed by value   |
| Reference (Object) | `Object`, `Array`, `Function`, `Date`, `RegExp`, etc.                  | Mutable, passed by reference |

---

### 1.3 Key Concepts & Snippets

1. **`typeof` Operator**
   - Returns a string indicating the operand’s type.
     ```javascript
     console.log(typeof 42); // "number"
     console.log(typeof "hello"); // "string"
     console.log(typeof true); // "boolean"
     console.log(typeof {}); // "object"
     console.log(typeof []); // "object"
     console.log(typeof undefined); // "undefined"
     console.log(typeof null); // "object"  // historical quirk
     ```
2. **`null` vs. `undefined`**
   - `undefined`: variable declared but no value.
   - `null`: explicitly assigned “no value.”
     ```javascript
     let a;
     console.log(a); // undefined
     a = null;
     console.log(a); // null
     ```
3. **Arrays**
   - Ordered, zero-indexed collections.
     ```javascript
     const arr = [1, "two", true];
     console.log(arr.length); // 3
     console.log(arr[1]); // "two"
     arr.push(4);
     console.log(arr); // [1,"two",true,4]
     ```
4. **Objects**
   - Key–value pairs.
     ```javascript
     const obj = { name: "Alice", age: 25 };
     console.log(obj.name); // "Alice"
     obj.city = "Delhi";
     console.log(obj); // {name:"Alice",age:25,city:"Delhi"}
     ```
5. **Primitive vs. Reference Behavior**

   ```javascript
   let x = 10;
   let y = x;
   y = 20;
   console.log(x, y); // 10 20  (primitives copied by value)

   const arr1 = [1, 2, 3];
   const arr2 = arr1;
   arr2.push(4);
   console.log(arr1, arr2); // [1,2,3,4] [1,2,3,4]  (arrays shared by reference)
   ```

---

## 2. Common Interview Questions

---

### High Importance

#### 1\. What are JavaScript’s primitive data types? List and describe each.

Sure. JavaScript has seven primitive data types. A primitive is a value that is not an object and has no methods of its own. They are immutable, meaning their value cannot be changed once created.

The seven primitives are:

- **String**: Represents textual data. For example, `"hello world"`.
- **Number**: Represents both integer and floating-point numbers, like `42` or `3.14`.
- **Boolean**: Represents a logical entity and can have two values: `true` or `false`.
- **Undefined**: Represents a variable that has been declared but has not yet been assigned a value.
- **Null**: Represents the intentional absence of any object value. It's a deliberate assignment.
- **Symbol**: A unique and immutable value that's often used as a key for an object's properties to avoid naming conflicts.
- **BigInt**: Represents whole numbers larger than the maximum safe integer that the `Number` type can represent.

---

#### 2\. What are non-primitive (reference) data types in JavaScript? Give examples.

Non-primitive types, or reference types, are primarily **Objects**. Unlike primitives, they are mutable, and their values are stored as a reference in memory—think of it as an address pointing to where the data lives.

The main examples are:

- **Object**: The most fundamental reference type. It's a collection of key-value pairs, like `{ name: 'Alex', age: 30 }`.
- **Array**: A special kind of object, used for ordered collections of values, like `[1, 'apple', true]`.
- **Function**: Also a special type of object that can be called to execute code.

---

#### 3\. Explain the difference between primitive and reference types in terms of memory and assignment.

This is a key concept. The main difference lies in how they are stored and copied.

- **Primitives are copied by value**. When you assign a primitive variable to another, you are copying the actual value. They are stored directly in the location the variable accesses on the stack.

  ```javascript
  let a = 10;
  let b = a; // The value 10 is copied to b.
  b = 20; // Changing b does not affect a.

  console.log(a); // Output: 10
  console.log(b); // Output: 20
  ```

- **Reference types are copied by reference**. When you assign an object or array to another variable, you are copying the _memory address_ (the reference), not the actual object. Both variables point to the same object in memory (on the heap).

  ```javascript
  let user1 = { name: "Alice" };
  let user2 = user1; // user2 now points to the same object as user1.

  user2.name = "Bob"; // We are modifying the *one* object that both variables point to.

  console.log(user1.name); // Output: "Bob"
  console.log(user2.name); // Output: "Bob"
  ```

So, with primitives, you get two independent copies. With reference types, you get two pointers to the same underlying data.

---

#### 4\. What does `typeof` return for each of the following?

- `typeof 42` -\> `"number"`
- `typeof "hello"` -\> `"string"`
- `typeof true` -\> `"boolean"`
- `typeof undefined` -\> `"undefined"`
- `typeof Symbol()` -\> `"symbol"`
- `typeof [1,2,3]` -\> `"object"` (Arrays are objects)
- `typeof {}` -\> `"object"`
- `typeof null` -\> `"object"` (This is a well-known historical quirk)

---

#### 5\. Why does `typeof null` return `"object"`?

That's a great question that points to a quirk from the very first version of JavaScript. In the original implementation, values were represented with a type tag and the value itself. The type tag for objects was `0`. `null` was represented as the NULL pointer (`0x00` in most platforms). As a result, the logic for `typeof` checks for the `0` type tag and incorrectly returns `"object"` for `null`.

It was identified as a bug, but fixing it would break a lot of existing code on the web, so it has been left as is for legacy reasons.

---

#### 6\. How do you check if a variable is an array?

The best and most reliable way is to use the `Array.isArray()` method. It returns `true` if the value is an array and `false` otherwise.

```javascript
const fruits = ["apple", "banana"];
const person = { name: "John" };

console.log(Array.isArray(fruits)); // Output: true
console.log(Array.isArray(person)); // Output: false
```

Using `typeof` doesn't work because it returns `"object"` for arrays. Other older methods, like checking the constructor, can fail across different frames or realms in a browser, so `Array.isArray()` is the standard, safest approach.

---

#### 7\. What is the difference between `==` and `===`? When would you use each?

The difference is that `===` is the **strict equality** operator, while `==` is the **loose equality** operator.

- `===` (Strict Equality): Compares both the **value** and the **type** of the two operands. It will only return `true` if both are the same.
- `==` (Loose Equality): Compares only the **value** after performing **type coercion** if the types are different. This can lead to unexpected results.

Here's an example:

```javascript
console.log(5 === "5"); // false (number vs. string)
console.log(5 == "5"); // true (string "5" is coerced to number 5)

console.log(0 == false); // true (false is coerced to 0)
console.log(0 === false); // false (number vs. boolean)
```

**When to use them?** The best practice is to **always use `===`** to avoid bugs from unexpected type coercion. You get more predictable and safer code. I would only use `==` if I had a very specific reason to allow type coercion, which is extremely rare. A common example is `value == null`, which conveniently checks for both `null` and `undefined`, but even that can often be replaced with clearer code.

---

#### 8\. Show with code how reassignment behaves for primitives vs. objects/arrays.

Certainly. This goes back to the "copy by value" vs. "copy by reference" concept.

**For Primitives (Copy by Value):**
When you reassign a primitive, you are creating a completely new, independent value.

```javascript
// 1. Declare and initialize a primitive variable
let color1 = "blue";

// 2. Assign it to another variable. The value 'blue' is copied.
let color2 = color1;

// 3. Reassign color2. This has no effect on color1.
color2 = "red";

console.log(color1); // Output: "blue" (unchanged)
console.log(color2); // Output: "red"
```

**For Objects/Arrays (Copy by Reference):**
When you "reassign" by modifying a property, both variables see the change because they point to the same object.

```javascript
// 1. Declare and initialize an object
let car1 = { brand: "Toyota", model: "Camry" };

// 2. Assign it to another variable. The reference (memory address) is copied.
let car2 = car1;

// 3. Modify a property of the object through car2
car2.model = "Corolla";

// The change is visible through car1 as well, because it's the same object.
console.log(car1.model); // Output: "Corolla"
console.log(car2.model); // Output: "Corolla"
```

It's important to note that if you reassign the entire variable `car2` to a brand new object, then you break the link: `car2 = { brand: 'Honda' };`. After that, `car1` and `car2` would point to different objects.

---

#### 9\. How does passing primitives vs. objects to functions differ?

It's the same principle as assignment: primitives are passed **by value**, and objects are passed **by reference** (or more accurately, the reference is passed by value).

**Passing a Primitive:**
The function receives a _copy_ of the primitive's value. Modifying it inside the function does not affect the original variable outside.

```javascript
function updateScore(score) {
  // score is a copy of myScore's value (100)
  score = score + 10;
  console.log("Inside function:", score); // Output: 110
}

let myScore = 100;
updateScore(myScore);
console.log("Outside function:", myScore); // Output: 100 (original is unchanged)
```

**Passing an Object:**
The function receives a _copy of the reference_ to the object. This means it's pointing to the exact same object in memory. Modifying the object's properties inside the function will affect the original object.

```javascript
function updatePlayer(player) {
  // player points to the same object as myPlayer
  player.level = player.level + 1;
  console.log("Inside function:", player.level); // Output: 6
}

let myPlayer = { name: "Zelda", level: 5 };
updatePlayer(myPlayer);
console.log("Outside function:", myPlayer.level); // Output: 6 (original is changed)
```

### Medium Importance

#### 10\. What are the falsy values in JavaScript?

There are exactly seven falsy values in JavaScript. These are values that coerce to `false` in a boolean context, like in an `if` statement.

They are:

- `false`
- `0` (the number zero)
- `-0` (negative zero)
- `0n` (BigInt zero)
- `""` (an empty string)
- `null`
- `undefined`
- `NaN` (Not a Number)

Everything else is "truthy," including empty arrays `[]` and empty objects `{}`.

---

#### 11\. How can you create an immutable object?

You can create a shallowly immutable object using `Object.freeze()`. This method prevents new properties from being added, existing properties from being removed, and prevents the values of existing properties from being changed.

```javascript
const config = {
  host: "localhost",
  port: 8080,
};

Object.freeze(config);

// Try to modify the object
config.port = 9000; // This will fail silently in non-strict mode.
// It throws a TypeError in strict mode.

console.log(config.port); // Output: 8080
```

It's important to remember that `Object.freeze()` is **shallow**. If the object contains other objects (like a nested object or an array), those nested objects can still be modified. For deep immutability, you'd need to write a recursive function or use a library like Immer.

---

#### 12\. Explain hoisting. How are variables declared with `var`, `let`, and `const` hoisted differently?

Hoisting is JavaScript's behavior of moving declarations to the top of their scope _before_ code execution. However, how they are initialized differs significantly.

- `var`: Both the declaration and the initialization (`= undefined`) are hoisted. This means you can access a `var` variable before it's declared in the code, and its value will be `undefined`.

  ```javascript
  console.log(myVar); // Output: undefined
  var myVar = 10;
  console.log(myVar); // Output: 10
  ```

- `let` and `const`: The declarations are hoisted, but they are **not initialized**. They exist in a "Temporal Dead Zone" (TDZ) from the start of the block until the declaration is encountered. Accessing them in the TDZ results in a `ReferenceError`. This makes the code more predictable and helps catch errors.

  ```javascript
  // console.log(myLet); // Throws ReferenceError: Cannot access 'myLet' before initialization
  let myLet = 20;
  console.log(myLet); // Output: 20
  ```

So, in modern JavaScript, it's best to use `let` and `const` to avoid the potentially confusing behavior of `var` hoisting.

---

### Medium Importance (Continued)

#### 13\. What are JavaScript modules? Compare CommonJS (require) vs. ES modules (import/export).

Modules allow us to split our code into separate, reusable files. This keeps our codebase organized, prevents naming conflicts with global variables, and helps manage dependencies. There are two major module systems in the JavaScript world.

- **CommonJS (CJS)**: This was the original standard for Node.js.

  - **Loading**: It loads modules **synchronously**. When you `require` a module, the execution of the current file pauses until that module is loaded and processed.
  - **Syntax**: It uses `require()` to import and `module.exports` or `exports` to export.
  - **Environment**: Primarily used in server-side JavaScript with Node.js.

  <!-- end list -->

  ```javascript
  // math.js
  const add = (a, b) => a + b;
  module.exports = { add };

  // main.js
  const { add } = require("./math.js");
  console.log(add(2, 3)); // 5
  ```

- **ES Modules (ESM)**: This is the official standard introduced in ES6 (ECMAScript 2015).

  - **Loading**: It loads modules **asynchronously**, which is better for browser performance as it doesn't block rendering.
  - **Syntax**: It uses the `import` and `export` keywords. It also supports static analysis, which allows bundlers to perform optimizations like tree-shaking (removing unused code).
  - **Environment**: It's the standard for modern browsers and is now the default in newer versions of Node.js.

  <!-- end list -->

  ```javascript
  // math.js
  export const add = (a, b) => a + b;

  // main.js
  import { add } from "./math.js";
  console.log(add(2, 3)); // 5
  ```

In short, **ESM is the modern standard** for both browsers and servers, while **CommonJS is the legacy system** for Node.js.

---

#### 14\. How do you clone an array and how do you deep-clone an object?

**Cloning an Array (Shallow Clone):**
The easiest way to create a shallow clone of an array is using the **spread syntax (`...`)** or the `.slice()` method. Both create a new array containing the same elements as the original.

```javascript
const originalArray = [1, 2, { a: 3 }];
// Using spread syntax
const clonedArray1 = [...originalArray];
// Using .slice()
const clonedArray2 = originalArray.slice();

clonedArray1.push(4);
console.log(originalArray); // [1, 2, { a: 3 }] (unchanged)
console.log(clonedArray1); // [1, 2, { a: 3 }, 4]
```

It's important to remember this is a **shallow** clone. If the array contains objects, the objects themselves are not cloned, only the references to them are. Modifying a nested object in the clone will affect the original.

**Deep-Cloning an Object:**
A deep clone creates a copy of the object and recursively copies all the objects nested within it.

- **The Quick & Dirty Way (`JSON`)**: The simplest method is `JSON.parse(JSON.stringify(object))`. It's easy but has limitations: it loses `undefined`, functions, and Symbols, and it can't handle circular references.

  ```javascript
  const originalObject = { name: "Admin", details: { loggedIn: true } };
  const deepClonedObject = JSON.parse(JSON.stringify(originalObject));

  deepClonedObject.details.loggedIn = false;
  console.log(originalObject.details.loggedIn); // true (unchanged)
  ```

- **The Robust Way (`structuredClone`)**: The modern, built-in way to perform a deep clone is the `structuredClone()` global function. It's more robust than the JSON method and can handle complex types like Dates, RegExps, and circular references.

  ```javascript
  const original = { name: "Admin", details: { loggedIn: true } };
  const deepClone = structuredClone(original);

  deepClone.details.loggedIn = false;
  console.log(original.details.loggedIn); // true (unchanged)
  ```

For complex scenarios, a library like Lodash (`_.cloneDeep()`) is also a reliable option.

---

#### 15\. What is a `Symbol`? How can it be used as a unique object key?

A `Symbol` is one of the primitive data types. Its main purpose is to create a **completely unique value**. Every time you call `Symbol()`, you get a new, unique symbol that is guaranteed not to conflict with any other symbol or string key.

You can use a Symbol as an object key to create "hidden" or special properties that won't accidentally collide with other property names. This is useful for adding metadata to an object without risking a name clash with keys added by other code.

```javascript
// Create a unique symbol to use as a key
const idSymbol = Symbol("id");

const user = {
  name: "John Doe",
  email: "john@example.com",
};

// Use the symbol to add a unique property
user[idSymbol] = "xyz-123-abc";

// The symbol key is not included in typical object iterations
console.log(user.name); // "John Doe"
console.log(user[idSymbol]); // "xyz-123-abc"
console.log(Object.keys(user)); // ["name", "email"] - idSymbol is not here
```

This prevents the `id` from showing up in a `for...in` loop or in `JSON.stringify()`, making it a good way to attach internal metadata to an object.

---

#### 16\. Explain the difference between `null` and `undefined`.

Both `null` and `undefined` represent the absence of a value, but they have different semantic meanings.

- **`undefined`** typically means a variable has been **declared, but not yet assigned a value**. It's the default value JavaScript gives to things that have no value. For example, a function that doesn't return anything will implicitly return `undefined`.

- **`null`** is an **explicitly assigned value**. It's used to intentionally signify "no value" or that an object is deliberately empty. A developer sets a variable to `null` to indicate it is empty.

Here's a simple analogy: `undefined` is a box that you haven't put anything in yet. `null` is a box you've intentionally put a note inside that says "this box is empty."

```javascript
let notInitialized; // It's undefined by default
let emptyObject = null; // We explicitly set it to null

console.log(notInitialized); // undefined
console.log(emptyObject); // null
```

---

#### 17\. How do you loop over object properties? List and compare `for…in`, `Object.keys()`, `Object.entries()`.

There are several ways to iterate over object properties, and the best choice depends on what you need.

- **`for...in` loop**: This loop iterates over all **enumerable properties** of an object, including properties inherited from its **prototype chain**. This can sometimes lead to unexpected results if you only want the object's own properties.

  ```javascript
  for (const key in user) {
    // It's good practice to add an own property check
    if (Object.hasOwn(user, key)) {
      console.log(`${key}: ${user[key]}`);
    }
  }
  ```

- **`Object.keys(obj)`**: This method returns an **array of a given object's own enumerable property names (keys)**. It doesn't include properties from the prototype chain. You can then loop over this array using methods like `forEach`. This is generally safer and more predictable than `for...in`.

  ```javascript
  Object.keys(user).forEach((key) => {
    console.log(`${key}: ${user[key]}`);
  });
  ```

- **`Object.entries(obj)`**: This is often the most useful. It returns an **array of a given object's own enumerable string-keyed property `[key, value]` pairs**. This gives you both the key and the value directly, which is very convenient.

  ```javascript
  Object.entries(user).forEach(([key, value]) => {
    console.log(`${key}: ${value}`);
  });
  // Or with a for...of loop
  for (const [key, value] of Object.entries(user)) {
    console.log(`${key}: ${value}`);
  }
  ```

**Recommendation:** For most use cases, `Object.keys()` or `Object.entries()` combined with a modern array method like `forEach` or a `for...of` loop is the preferred approach. They are more explicit and avoid issues with the prototype chain.

---

### Low Importance

#### 18\. How does JavaScript handle numeric precision for very large integers? What is `BigInt`?

Standard JavaScript `Number` types are 64-bit floating-point numbers. This means they can only safely represent integers up to `Number.MAX_SAFE_INTEGER`, which is about 9 quadrillion. Beyond that point, you can run into precision errors.

```javascript
const maxSafeInt = Number.MAX_SAFE_INTEGER;
console.log(maxSafeInt + 1); // 9007199254740992
console.log(maxSafeInt + 2); // 9007199254740992  <-- Oops, same result!
```

**`BigInt`** was introduced to solve this. It's a separate primitive type that can represent integers of arbitrary precision. You create a `BigInt` by appending `n` to the end of an integer literal or by calling the `BigInt()` function.

```javascript
const largeNumber = 9007199254740992n;
console.log(largeNumber + 1n); // 9007199254740993n
console.log(largeNumber + 2n); // 9007199254740994n <-- Correct!
```

You cannot mix `BigInt` and `Number` types in standard arithmetic operations; you must explicitly convert one to the other.

---

#### 19\. What is the purpose of template literals? How do they differ from string concatenation?

**Template literals**, or template strings, are a modern way to create strings in JavaScript, denoted by backticks (`` ` ``). Their main purposes are to simplify string interpolation and creating multi-line strings.

They differ from traditional string concatenation (`+`) in two key ways:

1.  **Easy Variable Interpolation**: You can embed expressions directly inside the string using `${expression}` syntax. This is much cleaner and more readable than breaking the string to add variables with `+`.

    ```javascript
    const name = "Alice";
    const score = 98;

    // Traditional Concatenation
    const message1 = "Hello, " + name + "! Your score is " + score + ".";

    // Template Literal
    const message2 = `Hello, ${name}! Your score is ${score}.`;
    ```

2.  **Multi-line Strings**: Template literals respect newlines, allowing you to create multi-line strings without needing newline characters like `\n`.

    ```javascript
    // Traditional
    const html1 = "<div>\n" + "  <p>Hello World</p>\n" + "</div>";

    // Template Literal
    const html2 = `<div>
      <p>Hello World</p>
    </div>`;
    ```

They make working with dynamic strings much more intuitive and less error-prone.

---

#### 20\. Explain optional chaining (`?.`) and nullish coalescing (`??`).

These are two modern operators that help write safer and more concise code when dealing with potentially `null` or `undefined` values.

- **Optional Chaining (`?.`)**: This operator lets you safely access nested properties of an object without having to write long chains of `if` checks. If any link in the chain is `null` or `undefined`, the expression short-circuits and returns `undefined` instead of throwing a `TypeError`.

  ```javascript
  const user = {
    name: "Bob",
    // address is missing
  };

  // Without optional chaining (throws an error)
  // const street = user.address.street; // TypeError: Cannot read properties of undefined

  // With optional chaining (safe)
  const street = user.address?.street;
  console.log(street); // undefined
  ```

- **Nullish Coalescing (`??`)**: This logical operator provides a default value for a variable only when it is `null` or `undefined`. It's a stricter version of the logical OR (`||`) operator, which provides a default for _any falsy value_ (like `0`, `""`, or `false`).

  ```javascript
  let userSettings = {
    volume: 0,
    theme: null,
  };

  // Using || (incorrectly defaults volume)
  const volume1 = userSettings.volume || 10; // volume1 is 10, because 0 is falsy.

  // Using ?? (correctly keeps 0)
  const volume2 = userSettings.volume ?? 10; // volume2 is 0.

  // Using ?? to provide a default theme
  const theme = userSettings.theme ?? "dark"; // theme is 'dark'.
  ```

Together, they are powerful tools for handling missing data gracefully.

Excellent. Let's wrap up with the final set of questions.

---

### Low Importance (Continued)

#### 21\. How do you use destructuring assignment with arrays and objects?

Destructuring is a convenient syntax that allows you to "unpack" values from arrays or properties from objects into distinct variables. It makes the code cleaner and more readable.

**Object Destructuring**: You use curly braces `{}` to pull out properties by their key names.

```javascript
const person = {
  firstName: "Jane",
  lastName: "Doe",
  age: 28,
};

// Extracting properties into variables with the same name
const { firstName, age } = person;

console.log(firstName); // "Jane"
console.log(age); // 28

// You can also rename variables
const { lastName: surname } = person;
console.log(surname); // "Doe"
```

**Array Destructuring**: You use square brackets `[]` to pull out values by their position in the array.

```javascript
const coordinates = [100, 200, 300];

// Extracting values by their position
const [x, y] = coordinates;

console.log(x); // 100
console.log(y); // 200

// You can also skip elements with a comma
const [val1, , val3] = coordinates;
console.log(val3); // 300
```

---

#### 22\. What are rest (`…args`) and spread (`…`) operators? Provide examples.

They both use the same syntax (`...`), but they do opposite things depending on the context.

**Spread Operator (`...`)**: This operator **expands** an iterable (like an array or string) or an object into its individual elements. You use it where multiple elements are expected, like in an array literal, a function call, or an object literal.

```javascript
// Spread with Arrays (to combine or clone)
const fruits = ["apple", "banana"];
const moreFruits = ["orange", ...fruits]; // ['orange', 'apple', 'banana']

// Spread with Function Calls
const numbers = [1, 5, 3];
console.log(Math.max(...numbers)); // Same as Math.max(1, 5, 3)

// Spread with Objects (to merge or clone)
const user = { name: "Alex" };
const userWithRole = { ...user, role: "admin" }; // { name: 'Alex', role: 'admin' }
```

**Rest Parameter (`...`)**: This syntax **collects** multiple elements into a single array. It's used in a function's parameter list to gather all remaining arguments into an array. It must be the last parameter.

```javascript
// Using rest to capture multiple arguments into an array
function sum(...numbers) {
  // 'numbers' is a real array: [2, 3, 5, 10]
  return numbers.reduce((total, num) => total + num, 0);
}

console.log(sum(2, 3, 5, 10)); // 20
```

So, **spread** _unpacks_ elements, while **rest** _packs_ them up.

---

#### 23\. How do you convert an array-like object (e.g., `arguments`) into a true array?

An array-like object has a `length` property and indexed elements, but it doesn't have the built-in array methods like `map`, `filter`, or `forEach`. The `arguments` object in non-arrow functions is a classic example.

There are two excellent, modern ways to convert it into a true array:

1.  **`Array.from()`**: This is the most explicit and readable method. It's designed specifically for this purpose.

    ```javascript
    function myFunction() {
      const argsArray = Array.from(arguments);
      argsArray.forEach((arg) => console.log(arg));
    }
    ```

2.  **Spread Syntax (`...`)**: This is often more concise and is also very popular.

    ```javascript
    function myFunction() {
      const argsArray = [...arguments];
      argsArray.forEach((arg) => console.log(arg));
    }
    ```

Both achieve the same result, so the choice often comes down to code style.

---

#### 24\. What built-in methods can you use to merge objects or arrays immutably?

Immutably merging means creating a _new_ array or object with the combined contents, leaving the original sources unchanged.

**For Arrays**:
The **spread syntax (`...`)** is the most common and readable way. The `.concat()` method also works.

```javascript
const arr1 = [1, 2];
const arr2 = [3, 4];

// Using spread
const mergedArray1 = [...arr1, ...arr2, 5]; // [1, 2, 3, 4, 5]

// Using concat
const mergedArray2 = arr1.concat(arr2); // [1, 2, 3, 4]
```

**For Objects**:
Again, the **spread syntax (`...`)** is the preferred modern approach. `Object.assign()` can also be used.

```javascript
const obj1 = { a: 1, b: 2 };
const obj2 = { b: 3, c: 4 }; // Note the overlapping key 'b'

// Using spread. Properties from later objects overwrite earlier ones.
const mergedObject1 = { ...obj1, ...obj2 }; // { a: 1, b: 3, c: 4 }

// Using Object.assign(). The first argument is the target object.
// To merge immutably, we use an empty object {} as the target.
const mergedObject2 = Object.assign({}, obj1, obj2); // { a: 1, b: 3, c: 4 }
```

---

#### 25\. Explain prototypes and the prototype chain. Why is prototypal inheritance important?

In JavaScript, objects can have a "prototype" object, which acts as a template from which they can inherit properties and methods.

The **prototype chain** is the mechanism for this inheritance. When you try to access a property on an object, the JavaScript engine first checks the object itself. If it doesn't find the property there, it looks at the object's prototype. If it's not on the prototype, it looks at the prototype's prototype, and so on, until it either finds the property or reaches the end of the chain (which is usually `Object.prototype`, which has a `null` prototype).

**Why it's important**: Prototypal inheritance is the core way JavaScript handles code reuse and inheritance. For example, every array you create doesn't have its own copy of `.map()` and `.filter()`. Instead, they all share a single prototype, `Array.prototype`, which holds these methods. This is incredibly memory-efficient. Understanding prototypes is key to understanding how classes, `this`, and inheritance work under the hood in JavaScript.

---

#### 26\. How would you detect and prevent memory leaks?

A memory leak occurs when memory is allocated but not returned to the operating system after it's no longer needed. Over time, this can degrade or crash the application.

**Common Causes**:

1.  **Accidental Global Variables**: If you forget `let` or `const`, a variable can be created on the global object and never garbage collected.
2.  **Detached DOM Elements**: If you remove a DOM element but still have a reference to it in your JavaScript (e.g., in a closure), it can't be cleaned up.
3.  **Uncleared Timers/Intervals**: `setInterval` will run forever if not explicitly cleared with `clearInterval`, holding onto any variables it references.

**Detection & Prevention**:

- **Detection**: Browser developer tools, particularly the **Memory** and **Performance** tabs in Chrome DevTools, are essential. You can take heap snapshots, compare them over time, and identify objects that are growing in number unexpectedly.
- **Prevention**:
  - Always use `'use strict';` at the top of your files to prevent accidental globals.
  - Always declare variables with `let` or `const`.
  - When you're done with a timer or an event listener, clean it up (`clearInterval`, `removeEventListener`).
  - Avoid long-term references to DOM elements in your code, especially if those elements can be removed from the page.

---

#### 27\. What is the difference between function declarations and function expressions?

The main differences are syntax and hoisting behavior.

**Function Declaration**: It's declared with the `function` keyword, followed by the function name.

```javascript
// Function Declaration
function greet() {
  return "Hello!";
}
```

- **Hoisting**: Function declarations are **fully hoisted**. This means the entire function, including its body, is moved to the top of its scope. You can call the function _before_ it appears in the code.

**Function Expression**: It involves creating a function and assigning it to a variable. The function can be named or anonymous.

```javascript
// Function Expression
const greet = function () {
  return "Hello!";
};
// Arrow functions are also expressions
const greetArrow = () => "Hello!";
```

- **Hoisting**: With function expressions, the variable declaration (`const greet`) is hoisted, but the assignment of the function to it is not. If you try to call it before the assignment, you'll get a `TypeError` or `ReferenceError`, because the variable exists but doesn't hold a function yet.

Generally, function expressions (especially arrow functions) are more common in modern JavaScript as they offer more flexibility and avoid the sometimes confusing behavior of hoisting.

---

#### 28\. How can you create private variables in JavaScript?

The goal of private variables is to create state that can only be accessed or modified from within the object or class itself, not from the outside.

1.  **Modern Approach: Private Class Fields (`#`)**: The standard, modern way is to use a hash (`#`) prefix for class fields. JavaScript enforces that these fields are only accessible from within the class.

    ```javascript
    class Wallet {
      #balance = 0; // This is a private field

      deposit(amount) {
        this.#balance += amount;
      }

      getBalance() {
        return this.#balance;
      }
    }

    const myWallet = new Wallet();
    myWallet.deposit(100);
    console.log(myWallet.getBalance()); // 100
    // console.log(myWallet.#balance); // SyntaxError: Private field must be declared in an enclosing class
    ```

2.  **Older Approach: Closures**: Before classes, the common pattern was to use closures. A function would create variables and then return an object with methods that could access those variables. The variables themselves remained "closed over" and inaccessible from the outside.

---

#### 29\. What is the difference between synchronous and asynchronous code?

This distinction is fundamental to how JavaScript works, especially in browsers and Node.js.

- **Synchronous (Sync)**: Code executes in a **blocking** manner, one line at a time. Nothing else can happen until the current operation finishes. If you have a long-running task (like a complex calculation), the entire program or UI will freeze until it's done.

  ```javascript
  console.log("First");
  // Imagine a long task here that takes 5 seconds
  console.log("Second"); // This will only run after the 5-second task is complete.
  ```

- **Asynchronous (Async)**: Code executes in a **non-blocking** manner. You can start a long-running task (like fetching data from a server or setting a timer) and the rest of your code will continue to run immediately. When the async task finishes, it provides its result, usually via a callback, a Promise, or `async/await`.

  ```javascript
  console.log("First");

  setTimeout(() => {
    console.log("Second (from the future)");
  }, 2000); // Wait 2 seconds

  console.log("Third"); // This runs immediately, without waiting for the timeout.
  // Output: First, Third, Second...
  ```

Asynchronous code is essential for performing tasks like network requests, file operations, or user interactions without freezing the user interface.

---

#### 30\. Describe how the JavaScript engine parses and executes code.

At a high level, the JavaScript engine processes code in two main phases for any given scope (global, or inside a function).

1.  **Creation Phase (or Parsing Phase)**: The engine first reads through the code _without executing it_. In this phase, it:

    - Determines the scope of all variables and functions.
    - Allocates memory for variables and functions. This is where **hoisting** happens. Variable declarations (`var`, `let`, `const`) are noted, and `var` variables are initialized to `undefined`. Function declarations are stored in memory completely.
    - Sets up the scope chain and determines the value of `this`.

2.  **Execution Phase**: After the creation phase is complete, the engine goes through the code again, this time executing it line by line. In this phase, it:

    - Assigns the values to the variables it allocated memory for earlier.
    - Executes function calls. When a function is called, a new execution context is created, and it goes through its own creation and execution phases.
    - Runs the code's logic.

This two-phase process explains why we can call a declared function before it's defined in the code (hoisting) and why `var` variables are `undefined` before their assignment line.

---
