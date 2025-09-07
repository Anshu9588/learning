# Task Scheduler Library: Capstone Challenge

Below is a minimal, promise-based **Task Scheduler** library that lets you register asynchronous tasks (functions) and execute them sequentially using `async/await`. It supports a **fluent interface** (method chaining) for registering tasks and running them.

```javascript
// taskScheduler.js

class TaskScheduler {
  constructor() {
    this.tasks = [];
  }

  // Register a new task: fn can be sync or async
  add(fn) {
    if (typeof fn !== "function") {
      throw new TypeError("Task must be a function");
    }
    this.tasks.push(fn);
    return this; // Enable chaining
  }

  // Run all tasks sequentially with optional initial value
  async run(initialValue) {
    let result = initialValue;
    for (const task of this.tasks) {
      // Each task receives the previous result
      result = await task(result);
    }
    return result;
  }

  // Clear all registered tasks
  clear() {
    this.tasks = [];
    return this;
  }
}

// Usage Example
(async () => {
  const scheduler = new TaskScheduler();

  // Define sample tasks
  const task1 = async (val) => {
    console.log("Task 1 start, input:", val);
    await new Promise((r) => setTimeout(r, 500));
    const out = (val || 0) + 1;
    console.log("Task 1 end, output:", out);
    return out;
  };

  const task2 = (val) => {
    console.log("Task 2 start, input:", val);
    const out = val * 2;
    console.log("Task 2 end, output:", out);
    return out;
  };

  const task3 = async (val) => {
    console.log("Task 3 start, input:", val);
    await new Promise((r) => setTimeout(r, 300));
    const out = `Result: ${val}`;
    console.log("Task 3 end, output:", out);
    return out;
  };

  // Register and run tasks in sequence
  const finalResult = await scheduler.add(task1).add(task2).add(task3).run(5);

  console.log("Final Result:", finalResult);
  // Expected Console Output:
  // Task 1 start, input: 5
  // Task 1 end, output: 6
  // Task 2 start, input: 6
  // Task 2 end, output: 12
  // Task 3 start, input: 12
  // Task 3 end, output: Result: 12
  // Final Result: Result: 12
})();
```

---

## Key Features & Notes

1. **Fluent Interface**

   - `.add(fn)` returns the scheduler itself, allowing method chaining:
     ```javascript
     scheduler.add(task1).add(task2).run();
     ```

2. **Sequential Execution**

   - Tasks run one after another in the order they were added.
   - The result of each task is passed as the input to the next task.

3. **Support for Sync & Async Tasks**

   - Each registered function can return a value (synchronous) or a promise (asynchronous).
   - `await` ensures correct sequencing regardless of task type.

4. **Initial Value Passing**

   - `run(initialValue)` lets you provide a starting input to the first task.
   - If omitted, `initialValue` is `undefined`.

5. **Clearing Tasks**

   - `.clear()` resets the task list for reuse.

6. **Error Handling**

   - If any task throws or returns a rejected promise, `run()` will throw the error.
   - Wrap `run()` in `try/catch` when calling to handle failures gracefully.

7. **Extensibility**
   - You can extend `TaskScheduler` with methods like `.parallel()` to run tasks concurrently, or add timeout controls.

This small library demonstrates mastery of JavaScript classes, async/await control flow, and fluent API design—ideal for advanced interviews and portfolio projects.
