---
marp: true
---

# Creating the UI

---

## Run the commands in PowerShell or CMD

`npx create-react-app expense-ui`

![Create react project](./Assets/ui/npmx%20create%20react.png)

## Navigate to the created project

`cd expense-ui`

## Open in VS Code

`npm start`

---

## App.js

```js
import logo from "./logo.svg";
import "./App.css";

function App() {
  return (
    <div className="App">
      <header className="App-header">
        <img src={logo} className="App-logo" alt="logo" />
        <p>
          Edit <code>src/App.js</code> and save to reload.
        </p>
        <a
          className="App-link"
          href="https://reactjs.org"
          target="_blank"
          rel="noopener noreferrer"
        >
          Learn React
        </a>
      </header>
    </div>
  );
}

export default App;
```

---

# Remove the default code

```diff
- import logo from "./logo.svg";
- import "./App.css";

function App() {
  return (
-    <div className="App">
-      <header className="App-header">
-        <img src={logo} className="App-logo" alt="logo" />
-        <p>
-          Edit <code>src/App.js</code> and save to reload.
-        </p>
-        <a
-          className="App-link"
-          href="https://reactjs.org"
-          target="_blank"
-          rel="noopener noreferrer"
-        >
-          Learn React
-        </a>
-      </header>
-    </div>
  );
}

export default App;
```

---

# Create the initial structure with forms

```js
function App() {
  return (
    <div>
      <form>
        <textarea cols="30" rows="10"></textarea>
        <input type="number" />
        <input type="date" />
        <button>Save</button>
      </form>
    </div>
  );
}

export default App;
```

---

# Create the table next to the forms

```diff
function App() {
  return (
    <div>
      <form>
        <textarea cols="30" rows="10"></textarea>
        <input type="number" />
        <input type="date" />
        <button>Save</button>
      </form>

+      <h2>My Expenses</h2>

+      <table width="100%">
+        <thead>
+          <tr>
+            <th>Id</th>
+            <th>Description</th>
+            <th>Amount</th>
+            <th>Date</th>
+          </tr>
+        </thead>

+        <tbody>
+        </tbody>
+      </table>
    </div>
  );
}

export default App;
```

---

```js
function App() {
  return (
    <div>
      <form>
        <textarea cols="30" rows="10"></textarea>
        <input type="number" />
        <input type="date" />
        <button>Save</button>
      </form>

      <h2>My Expenses</h2>

      <table width="100%">
        <thead>
          <tr>
            <th>Id</th>
            <th>Description</th>
            <th>Amount</th>
            <th>Date</th>
          </tr>
        </thead>

        <tbody>
        </tbody>
      </table>
    </div>
  );
}

export default App;
```

---

# Commit the changes in Git

---

# Fetching the expense data from the API

```diff
// Step 4
+ import { useEffect, useState } from "react";

function App() {
  // Step 1
+  const [expenses, setExpenses] = useState([]);

  // Step 2
+  const fetchExpenses = async () => {
+    const apiUrl = "http://localhost:1234";

+    const endpoint = `${apiUrl}/api/expenses`;

+    const response = await fetch(endpoint);

+    const expenseData = await response.json();

+    setExpenses(expenseData);
+  };

  // Step 3
+  useEffect(() => {
+    fetchExpenses();
+  }, []);

  return (
    ...
  );
}

export default App;
```

---

# Display the expense data

```diff
import { useEffect, useState } from "react";

+ function renderExpenses(expenses) {
+   const rows = expenses.map((expense) => {
+     return (
+       <tr key={expense._id}>
+         <td>{expense._id}</td>
+         <td>{expense.description}</td>
+         <td>{expense.amount}</td>
+        <td>{new Date(expense.date).toDateString()}</td>
+      </tr>
+     );
+  });

+  return rows;
+ }

function App() {
    ...

    <table width="100%">
    <thead>
        <tr>
        <th>Id</th>
        <th>Description</th>
        <th>Amount</th>
        <th>Date</th>
        </tr>
    </thead>

    <tbody>
+        {renderExpenses(expenses)}
    </tbody>
    </table>  
}  
```