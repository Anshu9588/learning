# Closure-Based Todo List (Console) & Pitfalls

---

## Code: `todoList.js`

```javascript
function createTodoList() {
  // Private state
  let todos = [];

  // Exposed API
  return {
    add(item) {
      if (typeof item !== "string" || !item.trim()) {
        console.error("Invalid todo item");
        return;
      }
      todos.push({ id: todos.length + 1, text: item.trim(), done: false });
      console.log(`Added: "${item.trim()}"`);
    },

    list() {
      console.log("To-Do List:");
      todos.forEach((todo) => {
        const status = todo.done ? "[x]" : "[ ]";
        console.log(`${status} (${todo.id}) ${todo.text}`);
      });
    },

    complete(id) {
      const todo = todos.find((t) => t.id === id);
      if (!todo) {
        console.error("Todo not found");
        return;
      }
      todo.done = true;
      console.log(`Completed: (${id}) ${todo.text}`);
    },

    remove(id) {
      todos = todos.filter((t) => t.id !== id);
      console.log(`Removed todo with id ${id}`);
    },
  };
}

// Usage example:
const myTodos = createTodoList();
myTodos.add("Buy milk");
myTodos.add("Write report");
myTodos.list();
// [ ] (1) Buy milk
// [ ] (2) Write report

myTodos.complete(1);
myTodos.list();
// [x] (1) Buy milk
// [ ] (2) Write report

myTodos.remove(2);
myTodos.list();
// [x] (1) Buy milk
```

---

## Notes: Pitfalls & Memory Usage

1. **Encapsulation via Closure**

   - The `todos` array is private inside `createTodoList()`. External code cannot access or modify it directly—only via the exposed methods.

2. **Memory Retention**

   - Because `todos` is captured by the returned methods’ closures, it remains in memory as long as any method reference exists.
   - To free memory, ensure you drop all references to `myTodos` when done (e.g., `myTodos = null`).

3. **Reassignment of Private State**

   - In `remove()`, we reassign `todos` to a new array. This works because `todos` is in the closure scope. Avoid direct mutation pitfalls (e.g., using `splice`) if you need immutability.

4. **Error Handling**

   - Methods check inputs (e.g., `typeof item !== 'string'`) to prevent invalid states.
   - Always safeguard closure variables against unexpected data types.

5. **Performance Considerations**

   - For large lists, methods like `find()` and `filter()` are O(n). If scaling up, consider data structures (e.g., a Map keyed by `id`) to improve lookup/removal speed.
   - Avoid creating too many closures in loops; here, only four closure functions exist regardless of list size.

6. **Method Binding**

   - The returned methods capture the lexical scope correctly. Avoid using methods as callbacks unbound, which could change `this`; here, we don’t rely on `this`, so it’s safe.

7. **Potential Memory Leaks**
   - If you attach DOM event listeners that reference `todos` methods, be sure to remove listeners when components unmount to allow garbage collection of the closure.

By understanding these pitfalls and memory aspects, you can confidently build robust closure-based modules in JavaScript.
