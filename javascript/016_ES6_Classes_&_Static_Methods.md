# - **ES6 Classes & Static Methods**

- Class syntax, inheritance, `super`, static members
- Convert prototype-based code to ES6 classes

---

### \#\# Part 1: High-Importance Questions 🌟

---

#### **1. What are ES6 classes? How are they different from constructor functions?**

ES6 classes are a modern syntax for creating objects and implementing inheritance in JavaScript. While they provide a cleaner, more familiar structure for developers coming from class-based languages like Java or Python, they are primarily **syntactic sugar** over the existing prototype-based inheritance system.

The main differences from traditional constructor functions are:

- **Syntax**: Class syntax is more concise and declarative. Methods are defined directly within the `class` block without needing to reference the `.prototype` property.
- **Hoisting**: Unlike function declarations, class declarations are **not hoisted**. You must declare a class before you can use it.
- **Strict Mode**: Code within a class body is always executed in strict mode, whether you declare `"use strict";` or not.
- **Constructor Calls**: A class constructor cannot be called without the `new` keyword, which prevents accidental function calls.

---

#### **2. Are ES6 classes "true classes" or syntactic sugar over prototypes? Explain the underlying mechanism.**

They are **syntactic sugar**. ES6 classes do not introduce a new object-oriented inheritance model to JavaScript. They provide a much cleaner and more convenient syntax for doing what we've always done with constructor functions and prototypes.

Under the hood, the JavaScript engine translates the class syntax into the traditional prototype-based model:

- The `class Animal {...}` declaration creates a constructor function named `Animal`.
- The `constructor(name) {...}` method becomes the body of that constructor function.
- All other methods defined in the class (like `speak()`) are automatically attached to the `Animal.prototype` object.
- The `extends` keyword handles the work of setting the prototype chain using `Object.create()`.

So, while the syntax is different, the underlying mechanism is still the prototype chain.

---

#### **3. How do you implement inheritance using ES6 classes? Explain the `extends` keyword.**

You implement inheritance using the **`extends`** keyword. This keyword creates a direct link between the child class and the parent class.

When you write `class Dog extends Animal`, you are telling JavaScript:

1.  The `Dog` class should inherit from the `Animal` class.
2.  Under the hood, this sets the prototype of `Dog` to `Animal` itself (for inheriting static members) and sets the prototype of `Dog.prototype` to `Animal.prototype` (for inheriting instance methods).

This single keyword replaces the manual work of `Dog.prototype = Object.create(Animal.prototype);` and `Dog.prototype.constructor = Dog;`.

**Code Example:**

```javascript
class Animal {
  constructor(name) {
    this.name = name;
  }
  speak() {
    console.log(`${this.name} makes a sound.`);
  }
}

// Dog inherits from Animal
class Dog extends Animal {
  constructor(name, breed) {
    super(name); // Must call the parent constructor
    this.breed = breed;
  }
}

const myDog = new Dog("Rex", "German Shepherd");
myDog.speak(); // "Rex makes a sound." (Inherited from Animal)
```

---

#### **4. What is the `super` keyword? How do you use it in constructors and methods?**

The **`super`** keyword is used in a child class to access and call functions on its parent class. It has two primary uses:

1.  **In the `constructor`**: You use `super(...)` to call the parent class's constructor. This is **mandatory** in a child class constructor before you can use the `this` keyword. It's how the parent class gets to initialize its part of the object.

2.  **In methods**: You use `super.methodName(...)` to call a method from the parent class. This is useful for method overriding, where you want to add new behavior to a method but also execute the parent's original logic.

**Code Example:**

```javascript
class Animal {
  constructor(name) {
    this.name = name;
  }
  speak() {
    return `${this.name} makes a sound.`;
  }
}

class Dog extends Animal {
  constructor(name, breed) {
    // 1. Use super() to call the Animal constructor
    super(name);
    this.breed = breed;
  }

  speak() {
    // 2. Use super.speak() to call the parent's method
    const parentMessage = super.speak();
    console.log(`${parentMessage} Specifically, it barks.`);
  }
}

const myDog = new Dog("Buddy", "Golden Retriever");
myDog.speak(); // "Buddy makes a sound. Specifically, it barks."
```

---

#### **5. Convert the following prototype-based inheritance to ES6 class syntax:**

```javascript
function Animal(name) {
  this.name = name;
}
Animal.prototype.speak = function () {
  console.log(`${this.name} speaks`);
};
function Dog(name, breed) {
  Animal.call(this, name);
  this.breed = breed;
}
Dog.prototype = Object.create(Animal.prototype);
```

Certainly. This conversion directly illustrates how classes simplify the old syntax.

**Converted ES6 Class Syntax:**

```javascript
class Animal {
  constructor(name) {
    this.name = name;
  }
  speak() {
    console.log(`${this.name} speaks`);
  }
}

class Dog extends Animal {
  constructor(name, breed) {
    super(name); // Replaces Animal.call(this, name)
    this.breed = breed;
  }
}

// Usage
const myDog = new Dog("Fido", "Poodle");
myDog.speak(); // "Fido speaks"
```

---

#### **6. What are static methods and properties in ES6 classes? How do you define and use them?**

**Static methods and properties** are members that belong to the **class itself**, not to any instance of the class. This means you call them directly on the class name, not on an object created from the class.

You define them using the **`static`** keyword.

They are useful for creating utility functions or defining constants that are related to the class but don't depend on the state of a specific instance.

**Code Example:**

```javascript
class Car {
  constructor(make) {
    this.make = make;
  }

  // This is an instance method
  getMake() {
    return this.make;
  }

  // This is a static property (a constant)
  static #numberOfWheels = 4;

  // This is a static method
  static compareMakes(carA, carB) {
    return carA.make === carB.make;
  }
}

const car1 = new Car("Honda");
const car2 = new Car("Toyota");
const car3 = new Car("Honda");

// You call static methods on the CLASS
console.log(Car.compareMakes(car1, car3)); // true

// You CANNOT call a static method on an instance
// car1.compareMakes(car2, car3); // TypeError: car1.compareMakes is not a function
```

---

#### **7. How does method overriding work in ES6 class inheritance? Provide an example.**

Method overriding works just like it does in prototype-based inheritance. When a child class defines a method with the same name as a method in its parent class, the child's version takes precedence.

When you call the method on an instance of the child class, JavaScript finds and executes the method on the child class first. If you need to access the parent's version from within the overridden method, you can use `super.methodName()`.

**Code Example:**

```javascript
class Shape {
  draw() {
    console.log("Drawing a generic shape.");
  }
}

class Circle extends Shape {
  // This method overrides the parent's draw() method
  draw() {
    console.log("Drawing a circle.");
  }
}

class Square extends Shape {
  draw() {
    // You can also call the parent method if needed
    super.draw();
    console.log("Specifically, a square.");
  }
}

const myCircle = new Circle();
const mySquare = new Square();

myCircle.draw(); // "Drawing a circle."
mySquare.draw(); // "Drawing a generic shape."
// "Specifically, a square."
```

---

### \#\# Part 2: Medium-Importance Questions 🤔

These questions test your understanding of the finer details and edge cases of working with ES6 classes.

---

#### **8. What happens if you don't call `super()` in a derived class constructor?**

If you have a constructor in a derived (child) class, you **must** call `super()` before you can access the `this` keyword. If you don't, JavaScript will throw a `ReferenceError`.

The reason for this is that in ES6 classes, the `this` context is not initialized until the parent constructor has been called. The `super()` call is what actually creates and binds `this`, allowing the child class to then add its own properties to it.

**Code Example:**

```javascript
class Animal {
  constructor() {}
}

class Dog extends Animal {
  constructor() {
    // Forgetting to call super() here
    this.breed = "Poodle"; // ReferenceError: Must call super constructor first
  }
}

// const myDog = new Dog(); // This line would throw an error
```

---

#### **9. Can you use arrow functions as class methods? What are the implications?**

Yes, you can, typically by defining them as class fields. The primary implication is how the `this` keyword is handled.

- **Regular Method**: A regular method's `this` context is determined by _how the method is called_. If it's called like `obj.myMethod()`, `this` is `obj`. If it's used as a callback, `this` can become `undefined` or the global object.
- **Arrow Function Method**: An arrow function **lexically binds `this`**. It captures the `this` value of the context where it was defined—which, in a class, is the instance itself.

This makes arrow function methods very useful for event handlers or callbacks, as you don't need to manually `.bind(this)` to ensure `this` points to the class instance.

**Code Example:**

```javascript
class ClickCounter {
  constructor() {
    this.count = 0;
  }

  // Regular method - 'this' can be lost
  incrementRegular() {
    this.count++;
    console.log(this.count);
  }

  // Arrow function method - 'this' is lexically bound
  incrementArrow = () => {
    this.count++;
    console.log(this.count);
  };
}

const counter = new ClickCounter();

// Simulating an event listener
const button = {
  // When this 'clicks', 'this' in incrementRegular will be 'button', not 'counter'
  // and will result in NaN.
  click: counter.incrementRegular,

  // When this 'clicks', 'this' in incrementArrow will correctly be 'counter'.
  clickArrow: counter.incrementArrow,
};

// button.click();      // Fails: outputs NaN because this.count is undefined on the 'button' object.
// button.clickArrow(); // Works: outputs 1
```

---

#### **10. How do private fields and methods work in ES6 classes? Explain the `#` syntax.**

Modern JavaScript classes support true private members using the hash `#` prefix. Any field or method prefixed with `#` is completely private to the class.

This means you cannot access it from outside the class, not even from a derived class. It provides true encapsulation, preventing accidental or intentional modification of a class's internal state.

**Code Example:**

```javascript
class BankAccount {
  // #balance is a private field
  #balance = 0;

  constructor(initialBalance) {
    this.#balance = initialBalance;
  }

  // #log is a private method
  #logTransaction(amount) {
    console.log(`Transaction logged for amount: ${amount}`);
  }

  deposit(amount) {
    if (amount > 0) {
      this.#balance += amount;
      this.#logTransaction(amount);
    }
  }

  getBalance() {
    return this.#balance;
  }
}

const account = new BankAccount(100);
account.deposit(50);

console.log(account.getBalance()); // 150

// Trying to access private members from outside will cause a SyntaxError.
// console.log(account.#balance);      // SyntaxError
// account.#logTransaction(10);      // SyntaxError
```

---

#### **11. What is the difference between static methods and instance methods? When would you use each?**

- **Instance Methods**: These are the most common type. They are defined on the class's prototype and operate on the **data of a specific instance** of the class. They need an instance to be created before they can be called, and they use the `this` keyword to access instance-specific properties.

  - **Use Case**: A `drive()` method on a `Car` class. It only makes sense to call `drive()` on a specific car object.

- **Static Methods**: These are defined directly on the **class itself**, not its prototype. They are not tied to any specific instance and therefore **do not have access to `this`**. They are often used as utility functions or factory methods that are related to the class but don't require an instance to work.

  - **Use Case**: A `Car.createSedan()` factory method that returns a new `Car` instance with pre-configured properties. You don't need an existing car to create a new one.

---

#### **13. What are getter and setter methods in ES6 classes? How do you implement them?**

Getters and setters allow you to define methods that look and act like properties. They provide a way to control access to an object's properties, allowing you to run code when a property is read (get) or written to (set).

You define them using the `get` and `set` keywords before a method name.

- **Getter (`get`)**: A getter is a function that is executed when you try to read a property's value. It should not take any parameters and should return a value.
- **Setter (`set`)**: A setter is a function that is executed when you try to assign a value to a property. It takes the new value as its only parameter.

**Code Example:**

```javascript
class User {
  constructor(firstName, lastName) {
    this.firstName = firstName;
    this.lastName = lastName;
  }

  // Getter for a computed property 'fullName'
  get fullName() {
    return `${this.firstName} ${this.lastName}`;
  }

  // Setter for 'fullName'
  set fullName(newName) {
    const [first, last] = newName.split(" ");
    this.firstName = first;
    this.lastName = last;
  }
}

const user = new User("John", "Doe");

// Use the getter like a property (no parentheses)
console.log(user.fullName); // "John Doe"

// Use the setter like a property
user.fullName = "Jane Smith";
console.log(user.firstName); // "Jane"
console.log(user.lastName); // "Smith"
```

---

#### **14. Can ES6 classes be hoisted? How do they behave differently from function declarations?**

No, ES6 classes are **not hoisted**.

- **Function Declarations**: These are hoisted to the top of their scope, meaning you can call a function before it is physically declared in the code.
- **Class Declarations**: Like `let` and `const`, classes are block-scoped and are not hoisted. You must declare the class before you can create an instance of it. Trying to access a class before its declaration will result in a `ReferenceError`. This behavior helps to enforce a more structured and predictable code flow.

---

### \#\# Part 3: Low-Importance Questions 📚

These questions cover advanced, specialized, or architectural topics. A strong answer here can really make you stand out.

---

#### **15. How do you handle multiple inheritance or mixins with ES6 classes?**

JavaScript classes only support **single inheritance** via the `extends` keyword. To achieve a similar result to multiple inheritance, the common pattern is to use **mixins**.

A mixin is typically a function that takes a base class as an argument and returns a _new class_ that extends that base class and adds new functionality. You can then compose these mixins together to build up a class with features from multiple sources.

**Code Example:**

```javascript
// A mixin that adds swimming capabilities
const Swimmable = (Base) =>
  class extends Base {
    swim() {
      console.log(`${this.name} is swimming.`);
    }
  };

// A mixin that adds flying capabilities
const Flyable = (Base) =>
  class extends Base {
    fly() {
      console.log(`${this.name} is flying.`);
    }
  };

class Bird {
  constructor(name) {
    this.name = name;
  }
}

// Create a Duck class by composing mixins
class Duck extends Swimmable(Flyable(Bird)) {}

const myDuck = new Duck("Donald");
myDuck.swim(); // "Donald is swimming."
myDuck.fly(); // "Donald is flying."
```

---

#### **16. What is the temporal dead zone in relation to ES6 classes?**

The **Temporal Dead Zone (TDZ)** is the period between entering a scope and the point where a `class`, `let`, or `const` is declared. If you try to access the class before its declaration line, you will get a `ReferenceError`.

This is the technical reason why classes are not hoisted. The JavaScript engine knows about the class when it enters the scope, but it keeps it in an uninitialized state until the line of declaration is reached.

---

#### **17. How do you extend built-in JavaScript objects (like `Array` or `Error`) using ES6 classes?**

You can extend built-in objects using the `extends` keyword just like any other class. This allows you to create custom, specialized versions of built-ins that inherit all their original functionality.

**Code Example (Custom Error):**

```javascript
// Create a custom error type for network issues
class NetworkError extends Error {
  constructor(message, status) {
    super(message); // Call the parent Error constructor
    this.name = "NetworkError";
    this.status = status;
  }
}

try {
  throw new NetworkError("Service Unavailable", 503);
} catch (error) {
  if (error instanceof NetworkError) {
    console.error(`${error.name} (${error.status}): ${error.message}`);
  }
}
```

---

#### **19. How do you implement method chaining in ES6 classes?**

You implement method chaining by having each method in the chain **`return this`**. Returning `this` passes the instance of the object back, allowing the next method call to be made on that same instance. This creates a fluent, expressive API.

**Code Example:**

```javascript
class Calculator {
  constructor(value = 0) {
    this.value = value;
  }

  add(num) {
    this.value += num;
    return this; // Return the instance for chaining
  }

  subtract(num) {
    this.value -= num;
    return this; // Return the instance for chaining
  }
}

const calc = new Calculator();
const result = calc.add(10).subtract(3).add(5);

console.log(result.value); // 12
```

---

#### **20. Can you override static methods in derived classes? How does static inheritance work?**

Yes, static methods can be overridden, and they are inherited. The `extends` keyword creates a prototype chain not only for the instance methods (via the `prototype` property) but also for the **constructors themselves**. This means a child class's constructor will have the parent class's constructor as its prototype, which is how static members are inherited.

**Code Example:**

```javascript
class Animal {
  static getKingdom() {
    return "Animalia";
  }
}

class Mammal extends Animal {
  // Override the parent's static method
  static getKingdom() {
    return "Mammalia (from Animalia)";
  }
}

console.log(Animal.getKingdom()); // "Animalia"
console.log(Mammal.getKingdom()); // "Mammalia (from Animalia)"
```

---

#### **22. How do you implement the singleton pattern using ES6 classes?**

The singleton pattern ensures that a class can only ever have **one instance**. You can implement this by:

1.  Keeping a private static reference to the single instance.
2.  Creating a public static method (e.g., `getInstance()`) that handles creating and returning the instance.
3.  The `getInstance()` method creates the instance on its first call and simply returns that same instance on all subsequent calls.

**Code Example:**

```javascript
class DatabaseConnection {
  static #instance;

  constructor() {
    // This is just to demonstrate that the constructor is only called once.
    console.log("Initializing database connection...");
  }

  static getInstance() {
    if (!DatabaseConnection.#instance) {
      DatabaseConnection.#instance = new DatabaseConnection();
    }
    return DatabaseConnection.#instance;
  }
}

const db1 = DatabaseConnection.getInstance();
const db2 = DatabaseConnection.getInstance();

console.log(db1 === db2); // true (they are the same instance)
```
