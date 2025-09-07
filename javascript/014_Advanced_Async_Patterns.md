# - **Advanced Async Patterns**
  - `Promise.all`, `Promise.race`, throttling, debouncing
  - Build an image-preloader using `Promise.all`

---

### \#\# Part 1: High-Importance Questions 🌟

These are the questions you absolutely must nail. They test your ability to handle complex, real-world asynchronous scenarios.

---

#### **1. Explain the difference between `Promise.all`, `Promise.race`, `Promise.any`, and `Promise.allSettled`. When would you use each?**

**Interview Answer:**
"These are all static methods on the `Promise` object used to handle multiple promises concurrently, but they serve very different purposes based on the outcome you need.

- **`Promise.all`** is for when you need **all or nothing**. It takes an array of promises and returns a single promise. This new promise fulfills only when **all** of the input promises have fulfilled, and it gives you an array of their results. If **any** of the promises reject, `Promise.all` immediately rejects with the reason of the first one that failed.

  - **Use Case**: You'd use this when you need to make multiple independent API calls and wait for all of them to complete successfully before rendering a UI component. For example, fetching a user's profile, their posts, and their friends list simultaneously.

- **`Promise.allSettled`** is for when you need to know the outcome of **every single promise**, regardless of success or failure. It never rejects. It takes an array of promises and returns a single promise that fulfills with an array of status objects. Each object tells you if the corresponding promise was `'fulfilled'` (with a `value`) or `'rejected'` (with a `reason`).

  - **Use Case**: This is perfect for when you have multiple independent tasks, and a failure in one shouldn't stop the others. For example, you might be calling several third-party analytics services; even if one fails, you still want to see the results from the ones that succeeded.

- **`Promise.race`** is for when you only care about the **very first promise to settle** (either fulfill or reject). It's a race to the finish line, and it takes on the outcome of the winner.

  - **Use Case**: A common use case is implementing a timeout. You can race your main async operation against a `setTimeout` promise that rejects after a certain time. Whichever one finishes first determines the outcome.

- **`Promise.any`** is for when you just need **one successful result**. It takes an array of promises and returns a single promise that fulfills as soon as the **first** input promise fulfills. It only rejects if **all** of the input promises reject.

  - **Use Case**: This is useful for redundancy. For example, you might have multiple servers with the same data. You could request the data from all of them at once and use the response from whichever server replies first."

---

#### **2. Implement a custom `Promise.all` function from scratch. Handle both resolution and rejection cases.**

**Interview Answer:**
"Certainly. To implement a custom `Promise.all`, we need to create a function that takes an array of promises. This function will return a new promise that tracks the state of all the input promises.

The logic is as follows:

1.  Initialize an array to store the results in the correct order and a counter for resolved promises.
2.  If the input array is empty, resolve immediately with an empty array.
3.  Iterate through the input promises. For each promise:
    - Use `.then()` to handle its fulfillment. When it resolves, store its value in the results array at the correct index and increment the counter.
    - If the counter equals the total number of promises, it means they have all fulfilled, so we can resolve our main promise with the results array.
    - Crucially, if any promise rejects, our main promise must immediately reject with that same error."

**Code Implementation:**

```javascript
/**
 * A custom implementation of Promise.all.
 * @param {Array<Promise>} promises An array of promises.
 * @returns {Promise} A new promise that resolves with an array of results
 * or rejects with the reason of the first failed promise.
 */
function myPromiseAll(promises) {
  return new Promise((resolve, reject) => {
    // Handle empty input array
    if (!promises || promises.length === 0) {
      resolve([]);
      return;
    }

    const results = new Array(promises.length);
    let resolvedCount = 0;
    const totalPromises = promises.length;

    promises.forEach((promise, index) => {
      // Ensure we are working with a promise
      Promise.resolve(promise)
        .then((value) => {
          results[index] = value;
          resolvedCount++;

          // If all promises have resolved, resolve the main promise
          if (resolvedCount === totalPromises) {
            resolve(results);
          }
        })
        // If any promise rejects, immediately reject the main promise
        .catch((error) => {
          reject(error);
        });
    });
  });
}

// --- Example Usage ---
const p1 = Promise.resolve(1);
const p2 = new Promise((res) => setTimeout(() => res(2), 500));
const p3 = Promise.resolve(3);
const p4_fail = Promise.reject("Error!");

myPromiseAll([p1, p2, p3])
  .then((res) => console.log("Success:", res)) // Expected: Success: [1, 2, 3]
  .catch((err) => console.error("Failure:", err));

myPromiseAll([p1, p2, p4_fail])
  .then((res) => console.log("Success:", res))
  .catch((err) => console.error("Failure:", err)); // Expected: Failure: Error!
```

---

#### **3. What is debouncing? How do you implement a `debounce` function? Provide a real-world use case.**

**Interview Answer:**
"**Debouncing** is a rate-limiting technique that groups a rapid series of events into a single one. It ensures that a function is not called again until a certain amount of time has passed without it being called. Essentially, it waits for a pause in the events.

You implement it by using `setTimeout`. Each time the debounced function is called, you clear the previously set timer and start a new one. The actual function logic is only executed when the timer is allowed to complete without being cleared.

- **Real-world use case**: A perfect example is the search bar on an e-commerce site. You don't want to send an API request for every single keystroke as the user types. Instead, you debounce the input event. The API call is only made after the user has stopped typing for, say, 300 milliseconds."

**Code Implementation:**

```javascript
/**
 * A debounce function that delays invoking a function until after `wait`
 * milliseconds have elapsed since the last time the debounced function was invoked.
 * @param {Function} func The function to debounce.
 * @param {number} wait The number of milliseconds to delay.
 * @returns {Function} Returns the new debounced function.
 */
function debounce(func, wait) {
  let timeoutId;

  // The returned function is the one that will be called in place of the original.
  return function (...args) {
    // `this` context and arguments are preserved.
    const context = this;

    // Clear the previous timeout to reset the waiting period.
    clearTimeout(timeoutId);

    // Set a new timeout.
    timeoutId = setTimeout(() => {
      // When the timeout completes, call the original function.
      func.apply(context, args);
    }, wait);
  };
}

// --- Example Usage ---
// Imagine this function makes an expensive API call.
const processInput = (value) => {
  console.log(`Searching for: ${value}`);
};

const debouncedProcessInput = debounce(processInput, 500);

// Simulate rapid user typing
debouncedProcessInput("a");
debouncedProcessInput("ap");
debouncedProcessInput("app");
debouncedProcessInput("appl");
// Only the last call will trigger the console.log after 500ms of inactivity.
```

---

#### **4. What is throttling? How does it differ from debouncing? Implement a `throttle` function.**

**Interview Answer:**
"**Throttling** is another rate-limiting technique, but it ensures that a function is executed at most **once** per specified time period. Unlike debouncing, which waits for a pause, throttling guarantees a regular, controlled rate of execution.

The key difference is:

- **Debounce**: Calls the function _after_ a period of inactivity. Good for "final state" events like finishing typing.
- **Throttle**: Calls the function _at a regular interval_ during activity. Good for continuous events like scrolling or resizing a window.

You implement throttling by using a flag (e.g., `canRun`) and a `setTimeout`. When the throttled function is called, you check the flag. If you can run, you execute the function, set the flag to false, and use `setTimeout` to reset the flag back to true after your specified interval."

**Code Implementation:**

```javascript
/**
 * A throttle function that ensures a function is called at most once
 * every `limit` milliseconds.
 * @param {Function} func The function to throttle.
 * @param {number} limit The time limit in milliseconds.
 * @returns {Function} Returns the new throttled function.
 */
function throttle(func, limit) {
  let inThrottle = false;
  let lastArgs;
  let lastThis;

  return function (...args) {
    const context = this;
    if (!inThrottle) {
      inThrottle = true;
      func.apply(context, args);
      setTimeout(() => {
        inThrottle = false;
        // Optionally, call with the latest arguments after the cooldown
        // if it was called during the throttle period.
        if (lastArgs) {
          func.apply(lastThis, lastArgs);
          lastArgs = null;
          lastThis = null;
        }
      }, limit);
    } else {
      // Save the latest arguments to be used after the throttle period ends.
      lastArgs = args;
      lastThis = context;
    }
  };
}

// --- Example Usage ---
// Imagine this function tracks the window scroll position.
const onScroll = () => {
  console.log("Scroll event handled!");
};

const throttledOnScroll = throttle(onScroll, 1000);

// If you attach throttledOnScroll to a scroll event listener,
// it will only log 'Scroll event handled!' at most once per second,
// no matter how fast you scroll.
window.addEventListener("scroll", throttledOnScroll);
```

---

#### **5. Build an image preloader function using `Promise.all` that loads multiple images concurrently and resolves when all are loaded.**

**Interview Answer:**
"This is a fantastic use case for `Promise.all`. The goal is to ensure all necessary images are downloaded and ready before, for example, starting a game or showing a gallery.

The approach is to:

1.  Create a helper function, `loadImage`, that takes an image URL and returns a promise.
2.  Inside this function, create a new `Image` object.
3.  The promise will `resolve` in the image's `onload` handler and `reject` in its `onerror` handler.
4.  The main `preloadImages` function will then `map` over an array of image URLs, calling `loadImage` for each one to get an array of promises.
5.  Finally, it passes this array of promises to `Promise.all` and waits for it to resolve."

**Code Implementation:**

```javascript
/**
 * Preloads a set of images concurrently.
 * @param {string[]} imageUrls An array of image URLs to load.
 * @returns {Promise<HTMLImageElement[]>} A promise that resolves with an array of
 * the loaded image elements or rejects if any image fails.
 */
function preloadImages(imageUrls) {
  // Helper function to load a single image and return a promise.
  const loadImage = (url) => {
    return new Promise((resolve, reject) => {
      const img = new Image();
      img.onload = () => resolve(img);
      img.onerror = () => reject(new Error(`Failed to load image at: ${url}`));
      img.src = url;
    });
  };

  // Map each URL to a promise from the loadImage helper.
  const imagePromises = imageUrls.map(loadImage);

  // Wait for all the image loading promises to resolve.
  return Promise.all(imagePromises);
}

// --- Example Usage ---
const urlsToLoad = [
  "https://via.placeholder.com/150/1",
  "https://via.placeholder.com/150/2",
  "https://via.placeholder.com/150/3",
  "https://via.placeholder.com/150/nonexistent", // This one will fail
];

preloadImages(urlsToLoad)
  .then((loadedImages) => {
    console.log(`${loadedImages.length} images were loaded successfully!`);
    // Now you can safely append them to the DOM.
  })
  .catch((error) => {
    console.error(error.message);
  });
```

---

Of course. Let's dive into the next set of questions.

### \#\# Part 2: Medium-Importance Questions 🤔

These questions test your ability to build more complex, robust asynchronous utilities from scratch. Answering these well demonstrates a deep, practical command of promise-based patterns.

---

#### **6. How do you handle concurrency control with promises? Implement a function that limits the number of concurrent async operations.**

**Interview Answer:**
"Concurrency control is essential when you have a large number of tasks, like hundreds of API calls, and you don't want to overwhelm the network or the server. You can implement a function that processes an array of tasks (functions that return promises) but ensures no more than a specified number, say `N`, are running at any given time.

The strategy is to create a pool of workers.

1.  Initially, start `N` tasks from the list.
2.  When any of these tasks finishes, it should immediately pick up the next available task from the list and start it.
3.  This continues until all tasks have been processed. The main promise resolves when the very last task is complete."

**Code Implementation:**

```javascript
/**
 * Executes promise-returning functions in parallel with a limited concurrency.
 * @param {Array<Function>} tasks An array of functions that each return a promise.
 * @param {number} limit The maximum number of tasks to run at once.
 * @returns {Promise<Array>} A promise that resolves with the results of all tasks.
 */
function runWithConcurrencyLimit(tasks, limit) {
  return new Promise((resolve) => {
    const results = [];
    let running = 0;
    let taskIndex = 0;
    let completed = 0;

    function runNext() {
      // If all tasks are done, resolve.
      if (completed >= tasks.length) {
        resolve(results);
        return;
      }

      // While there are running workers and tasks to be run...
      while (running < limit && taskIndex < tasks.length) {
        const currentIndex = taskIndex;
        const task = tasks[taskIndex];

        running++;
        taskIndex++;

        task()
          .then((result) => {
            results[currentIndex] = result;
          })
          .catch((error) => {
            results[currentIndex] = error; // Or handle error as needed
          })
          .finally(() => {
            running--;
            completed++;
            runNext(); // A worker is free, start the next task.
          });
      }
    }

    runNext();
  });
}

// --- Example Usage ---
// Create 10 dummy tasks with varying delays
const dummyTasks = Array.from({ length: 10 }, (_, i) => {
  return () =>
    new Promise((res) => {
      const delay = Math.random() * 1000;
      console.log(`Task ${i} starting (will take ${delay.toFixed(0)}ms)`);
      setTimeout(() => {
        console.log(`Task ${i} finished.`);
        res(`Result ${i}`);
      }, delay);
    });
});

console.log("Starting tasks with a concurrency limit of 3...");
runWithConcurrencyLimit(dummyTasks, 3).then((allResults) => {
  console.log("All tasks completed!", allResults);
});
```

---

#### **7. Implement a custom `Promise.race` function.**

**Interview Answer:**
"A custom `Promise.race` is simpler to implement than `Promise.all` because we only care about the very first promise that settles, whether it fulfills or rejects.

The logic is:

1.  Return a new promise.
2.  Iterate through the input array of promises.
3.  For each promise, attach both a `.then()` and a `.catch()` handler.
4.  The first time _any_ of these handlers is called, it should immediately call the corresponding `resolve` or `reject` function of our main promise. We don't need to track any other promises after that point."

**Code Implementation:**

```javascript
/**
 * A custom implementation of Promise.race.
 * @param {Array<Promise>} promises An array of promises.
 * @returns {Promise} A promise that settles with the outcome of the first
 * promise in the iterable that settles.
 */
function myPromiseRace(promises) {
  return new Promise((resolve, reject) => {
    if (!promises || promises.length === 0) {
      // If the array is empty, the promise never settles, as per spec.
      return;
    }

    promises.forEach((promise) => {
      Promise.resolve(promise)
        .then((value) => {
          resolve(value); // Settle with the first fulfilled value
        })
        .catch((error) => {
          reject(error); // Settle with the first rejection reason
        });
    });
  });
}

// --- Example Usage ---
const p_slow = new Promise((res) => setTimeout(() => res("slow"), 1000));
const p_fast = new Promise((res) => setTimeout(() => res("fast"), 100));
const p_fail = new Promise((_, rej) => setTimeout(() => rej("fail"), 50));

myPromiseRace([p_slow, p_fast]).then((res) => console.log("Winner is:", res)); // Expected: Winner is: fast

myPromiseRace([p_slow, p_fail]).catch((err) =>
  console.error("Winner is:", err)
); // Expected: Winner is: fail
```

---

#### **9. How would you implement retry logic for failed promises with exponential backoff?**

**Interview Answer:**
"Implementing retry logic is a common requirement for making applications more resilient to transient network failures. **Exponential backoff** is a strategy where you increase the waiting time between retries exponentially.

The approach is to create a wrapper function that takes the async operation and the number of retry attempts.

1.  The wrapper will try to execute the operation in a `try/catch` block.
2.  If it succeeds, it returns the result.
3.  If it fails, it catches the error, checks if there are retries left, and if so, it waits for a calculated delay (`baseDelay * 2 ** attemptNumber`) before trying again.
4.  If all retries are exhausted, it throws the last error."

**Code Implementation:**

```javascript
/**
 * A utility to retry a promise-returning function with exponential backoff.
 * @param {Function} asyncFn The function to retry.
 * @param {number} retries The maximum number of retries.
 * @param {number} delay The initial delay in ms.
 * @returns {Function} A new function that wraps the original with retry logic.
 */
function retryWithBackoff(asyncFn, retries = 3, delay = 50) {
  return async function (...args) {
    for (let i = 0; i < retries; i++) {
      try {
        return await asyncFn(...args); // Attempt the operation
      } catch (error) {
        if (i === retries - 1) {
          console.error(`Final attempt failed. No more retries.`);
          throw error; // If it's the last attempt, re-throw the error
        }

        const backoffDelay = delay * 2 ** i;
        console.warn(
          `Attempt ${i + 1} failed. Retrying in ${backoffDelay}ms...`
        );
        await new Promise((res) => setTimeout(res, backoffDelay)); // Wait
      }
    }
  };
}

// --- Example Usage ---
let failCount = 0;
async function flakyAPICall() {
  failCount++;
  if (failCount < 3) {
    console.log(`API call #${failCount}: Failing...`);
    throw new Error("Network Error");
  }
  console.log(`API call #${failCount}: Succeeding!`);
  return "Success!";
}

const resilientCall = retryWithBackoff(flakyAPICall, 4, 100);
resilientCall()
  .then((result) => console.log("Result:", result))
  .catch((err) => console.error("Final Error:", err.message));
```

---

#### **10. Create a function that executes an array of async functions sequentially vs. in parallel.**

**Interview Answer:**
"This is a great question to demonstrate control over asynchronous flow.

- **Parallel execution** is the simpler case. You can use `Promise.all`. You just map the array of task functions to an array of called functions (which gives you promises) and pass that array to `Promise.all`.

- **Sequential execution** requires ensuring that one task finishes before the next one begins. A clean way to do this is with `Array.prototype.reduce`, where you chain each new promise onto the previous one. An even more readable modern approach is to use a `for...of` loop inside an `async` function."

**Code Implementation:**

```javascript
// A simple async task for demonstration
const asyncTask = (id, delay) => () => {
  return new Promise((res) =>
    setTimeout(() => {
      console.log(`Task ${id} completed.`);
      res(`Result ${id}`);
    }, delay)
  );
};

const tasks = [asyncTask(1, 300), asyncTask(2, 100), asyncTask(3, 200)];

// --- Parallel Execution ---
function executeInParallel(tasks) {
  const promises = tasks.map((task) => task());
  return Promise.all(promises);
}

// --- Sequential Execution (using async/await) ---
async function executeSequentially(tasks) {
  const results = [];
  for (const task of tasks) {
    const result = await task(); // await ensures we wait
    results.push(result);
  }
  return results;
}

console.log("--- Executing in Parallel ---");
executeInParallel([...tasks]).then((results) =>
  console.log("Parallel results:", results)
);
// Expected output: Task 2, then 3, then 1 complete. Total time ~300ms.

setTimeout(() => {
  console.log("\n--- Executing Sequentially ---");
  executeSequentially([...tasks]).then((results) =>
    console.log("Sequential results:", results)
  );
}, 1000);
// Expected output: Task 1, then 2, then 3 complete. Total time ~600ms.
```

---

#### **14. How do you implement timeout functionality for promises? Create a `promiseWithTimeout` utility.**

**Interview Answer:**
"Implementing a timeout is a classic use case for `Promise.race`. You create a race between your main promise and a 'timeout promise' that is set to reject after a specific duration. Whichever settles first—the main promise fulfilling or the timeout promise rejecting—will determine the outcome of the race."

**Code Implementation:**

```javascript
/**
 * Wraps a promise with a timeout.
 * @param {Promise} promise The promise to wrap.
 * @param {number} ms The timeout duration in milliseconds.
 * @returns {Promise} A new promise that either resolves with the original
 * promise's value or rejects with a timeout error.
 */
function promiseWithTimeout(promise, ms) {
  // Create a timeout promise that rejects after `ms` milliseconds
  const timeoutPromise = new Promise((_, reject) => {
    setTimeout(() => {
      reject(new Error(`Operation timed out after ${ms}ms`));
    }, ms);
  });

  // Race the input promise against the timeout promise
  return Promise.race([promise, timeoutPromise]);
}

// --- Example Usage ---
const longRunningTask = new Promise((res) =>
  setTimeout(() => res("Task Complete"), 2000)
);

// This will succeed because timeout (3000ms) > task duration (2000ms)
promiseWithTimeout(longRunningTask, 3000)
  .then((res) => console.log(res))
  .catch((err) => console.error(err.message));

// This will fail because timeout (1000ms) < task duration (2000ms)
promiseWithTimeout(longRunningTask, 1000)
  .then((res) => console.log(res))
  .catch((err) => console.error(err.message));
```

---

Good morning\! Let's finish up this last set of questions.

### \#\# Part 3: Low-Importance Questions 📚

These are deep-dive topics. You might not get asked these directly, but understanding them shows a sophisticated grasp of asynchronous architecture and optimization.

---

#### **15. What are the performance implications of using `Promise.all` vs. sequential promise execution?**

**Interview Answer:**
"The performance difference is significant and directly relates to parallel vs. sequential execution.

- **`Promise.all` (Parallel)**: When tasks are independent, `Promise.all` is much faster. It initiates all operations at roughly the same time. The total execution time is determined by the **longest-running single task**.

- **Sequential Execution**: When tasks are run one after another, the total execution time is the **sum of all individual task times**.

For example, if you have three independent tasks that take 100ms, 200ms, and 300ms, `Promise.all` would finish in about 300ms. A sequential execution would take 100 + 200 + 300 = 600ms. Therefore, for independent tasks, `Promise.all` is the clear performance winner."

---

#### **16. Implement a `Promise.any` polyfill that resolves with the first fulfilled promise.**

**Interview Answer:**
"Certainly. To implement `Promise.any`, the logic is the inverse of `Promise.all`. We want to ignore rejections until we run out of options.

1.  Return a new promise.
2.  Keep a counter for the number of rejections and an array to store the rejection reasons.
3.  Iterate through the input promises.
4.  The _first_ promise to **fulfill** should immediately cause our main promise to resolve with that value.
5.  If a promise **rejects**, we increment our rejection counter.
6.  If the rejection counter equals the total number of promises, it means they have all failed. At that point, we reject our main promise with an `AggregateError`, containing all the individual rejection reasons, as per the spec."

**Code Implementation:**

```javascript
function myPromiseAny(promises) {
  return new Promise((resolve, reject) => {
    if (!promises || promises.length === 0) {
      reject(new AggregateError([], "No promises were provided."));
      return;
    }

    let rejectedCount = 0;
    const errors = [];
    const totalPromises = promises.length;

    promises.forEach((promise, index) => {
      Promise.resolve(promise)
        .then((value) => {
          resolve(value); // Resolve with the very first success
        })
        .catch((error) => {
          errors[index] = error;
          rejectedCount++;

          // If all promises have rejected, reject the main promise
          if (rejectedCount === totalPromises) {
            reject(new AggregateError(errors, "All promises were rejected"));
          }
        });
    });
  });
}
```

---

#### **18. Create a batch processing function that processes items in chunks using promises.**

**Interview Answer:**
"Batch processing is useful for handling large datasets without making thousands of API calls at once. This function would take a large array of items and process them in smaller, sequential chunks.

The approach is to use a `for` loop that iterates over the array in steps of `chunkSize`. In each iteration, we slice a chunk of items, process that chunk in parallel using `Promise.all`, and `await` its completion before moving to the next chunk."

**Code Implementation:**

```javascript
/**
 * Processes an array of items in sequential chunks of a given size.
 * @param {Array<any>} items The array of items to process.
 * @param {number} chunkSize The size of each chunk.
 * @param {Function} processor A function that takes an item and returns a promise.
 */
async function processInBatches(items, chunkSize, processor) {
  let allResults = [];
  console.log(
    `Starting batch processing for ${items.length} items with chunk size ${chunkSize}.`
  );

  for (let i = 0; i < items.length; i += chunkSize) {
    const chunk = items.slice(i, i + chunkSize);
    console.log(`Processing chunk starting at index ${i}...`);

    // Process the current chunk in parallel
    const chunkPromises = chunk.map(processor);
    const chunkResults = await Promise.all(chunkPromises);

    allResults = allResults.concat(chunkResults);
    console.log(`...chunk finished.`);
  }

  console.log("Batch processing complete.");
  return allResults;
}

// --- Example Usage ---
const itemsToProcess = Array.from({ length: 10 }, (_, i) => i + 1);
const processItem = (item) =>
  new Promise((res) => {
    setTimeout(() => res(`Processed ${item}`), 100 + Math.random() * 100);
  });

processInBatches(itemsToProcess, 3, processItem).then((finalResults) =>
  console.log("Final results:", finalResults)
);
```

---

#### **21. Build a cache system using promises that avoids duplicate API calls for the same resource.**

**Interview Answer:**
"A promise-based cache is a great way to prevent the 'thundering herd' problem, where multiple parts of an application request the same data at the same time.

The key is to cache the **promise** itself, not just the result.

1.  When a request comes in for a resource, check a cache (like a `Map`).
2.  If a promise for that resource already exists in the cache, return that existing promise. All subsequent callers will now wait on the same single, in-flight request.
3.  If no promise exists, initiate the data-fetching operation, store the new pending promise in the cache, and return it.
4.  Once the promise resolves, you can either keep the promise in the cache or replace it with the final value."

**Code Implementation:**

```javascript
function createPromiseCache(apiFunction) {
  const cache = new Map();

  return function (...args) {
    const key = JSON.stringify(args); // Create a unique key from arguments

    if (cache.has(key)) {
      console.log(`Cache hit for key: ${key}`);
      return cache.get(key); // Return existing promise
    }

    console.log(`Cache miss for key: ${key}. Fetching...`);
    // Call the original API function
    const promise = apiFunction(...args).catch((error) => {
      // On failure, remove the promise from the cache to allow retries
      cache.delete(key);
      throw error; // Re-throw the error
    });

    cache.set(key, promise); // Store the new promise
    return promise;
  };
}

// --- Example Usage ---
const fetchUser = (userId) => {
  console.log("Making a real API call for user", userId);
  return new Promise((res) =>
    setTimeout(() => res({ id: userId, name: "Alice" }), 500)
  );
};

const cachedFetchUser = createPromiseCache(fetchUser);

// First call for user 123 (will trigger API call)
cachedFetchUser(123).then((user) => console.log(user));
// Second call for user 123 immediately after (will hit the cache)
cachedFetchUser(123).then((user) => console.log(user));
// Call for a different user (will trigger API call)
cachedFetchUser(456).then((user) => console.log(user));
```

---

#### **Brief Answers to Other Topics**

- **Promise Pool/Queue (Q19)**: This is another name for a concurrency-limiting function, like the one we built in the medium section. It's about managing a pool of "workers" to process a queue of tasks.
- **Circuit Breaker (Q22)**: This is a design pattern for fault tolerance. You wrap an async call in a state machine. If the call fails repeatedly, the circuit "opens," and subsequent calls fail immediately without even trying. After a cooldown period, it enters a "half-open" state to try one more call. If that succeeds, the circuit "closes" and resumes normal operation.
- **Async Validation (Q23)**: You can use `Promise.allSettled` to run an array of validation functions (that return promises). This ensures all validators run, and you can then filter the results to collect an array of all the reasons from the 'rejected' promises, giving you a complete list of validation errors.
- **Lazy Promises (Q20)**: Standard promises are "eager"—the code in the executor runs on creation. A "lazy" promise defers execution until it's needed, typically by wrapping the `new Promise` constructor inside a function that is only called later (e.g., when `.then()` is called for the first time).
- **`Promise.all` with different operations (Q25)**: `Promise.all` is agnostic to where the promises come from. It works perfectly fine with a mixed array of promises from `fetch` requests, database queries, file reads, or `setTimeout`, because it only cares about the promise interface (i.e., that the object has a `.then()` method).
