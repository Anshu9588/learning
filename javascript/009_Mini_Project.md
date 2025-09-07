
# - **Mini Project**
  - Build a data-transformation utility using map/filter, arrow functions, and destructuring


## Data-Transformation Utility: User Data Processor

### Task
Given a list of users with information like name, age, email, and roles, create a utility to:
- Filter users above a certain age
- Extract and format key details (name and email)
- Count users by role
- Return a summary object

***

### Sample Data
```javascript
const users = [
  { id: 1, name: "Alice", age: 28, email: "alice@example.com", roles: ["admin", "editor"] },
  { id: 2, name: "Bob", age: 22, email: "bob@example.com", roles: ["user"] },
  { id: 3, name: "Charlie", age: 35, email: "charlie@example.com", roles: ["editor"] },
  { id: 4, name: "Dave", age: 20, email: "dave@example.com", roles: ["user", "subscriber"] },
];
```

***

### Code: Data Transformation Utility

```javascript
const transformUserData = (userList, minAge) => {
  // Filter users older than minAge
  const filteredUsers = userList.filter(({ age }) => age >= minAge);

  // Extract name and email using destructuring inside map + arrow function
  const userContacts = filteredUsers.map(({ name, email }) => ({ name, email }));

  // Count users by role using reduce
  const roleCounts = filteredUsers.reduce((acc, { roles }) => {
    roles.forEach(role => {
      acc[role] = (acc[role] || 0) + 1;
    });
    return acc;
  }, {});

  // Return summary object
  return {
    totalUsers: filteredUsers.length,
    userContacts,   // Array of { name, email }
    roleCounts      // Object with role counts
  };
};

// Usage
const summary = transformUserData(users, 25);
console.log(summary);
```

***

### Expected Output
```json
{
  "totalUsers": 2,
  "userContacts": [
    { "name": "Alice", "email": "alice@example.com" },
    { "name": "Charlie", "email": "charlie@example.com" }
  ],
  "roleCounts": {
    "admin": 1,
    "editor": 2
  }
}
```

***

### Explanation
- **Filter:** Uses arrow function with destructured `age` to select users age 25 and above.
- **Map:** Extracts only `name` and `email` fields using destructuring.
- **Reduce:** Counts the occurrence of every role across filtered users.
- The entire processing pipeline is clean, concise, and efficient.

***

This project demonstrates practical mastery of ES6+ features and functional programming patterns, a great portfolio piece and strong interview talking point.