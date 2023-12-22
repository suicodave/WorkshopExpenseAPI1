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
---

# Commit the changes in Git 

---

# Saving an expense

## Step 1 - Preparing input variables

```diff
// Step 4

function App() {
   const [expenses, setExpenses] = useState([]);
+  const [description, setDescription] = useState("");
+  const [amount, setAmount] = useState(0);
+  const [date, setDate] = useState(
+    new Date(Date.now()).toLocaleDateString("en-CA")
+  );
+  const [onSuccessfulSave,setOnSuccessfulSave] = useState(false);

  ...
}

export default App;

```
---

## Step 2 - Define the saveExpense function

```diff
function App() {
  // Step 1
  ...  

+  const saveExpense = async (event) => {
+    event.preventDefault();

+    const apiUrl = "http://localhost:1234";

+    const endpoint = `${apiUrl}/api/expenses`;

+    const expense = {
+      description: description,
+      amount: amount,
+      date: date,
+    };

+    await fetch(endpoint, {
+      method: "POST",
+      headers: {
+        "Content-Type": "application/json",
+      },
+      body: JSON.stringify(expense),
+    });

+    setOnSuccessfulSave(true);
+  };

  return (
    ...
  );
}

export default App;

```

---

## Step 3 - Bind input variables to the HTML inputs

```diff
  return (
    <div>
+      <form onSubmit={saveExpense}>
        <textarea
          cols="30"
          rows="10"
+          value={description}
+          onChange={(event) => setDescription(event.target.value)}
        ></textarea>
        <input
          type="number"
+          value={amount}
+          onChange={(event) => setAmount(event.target.value)}
        />
        <input
          type="date"
+          value={date}
+          onChange={(event) => setDate(event.target.value)}
        />
        <button>Save</button>
      </form>

      <h2>My Expenses</h2>

      <table width="100%">
        ...
      </table>
    </div>
  );
```
---

## Step 4 - Reloading expense data after saving a new expense

```diff
function App() {
  ...

+  useEffect(()=>{
+    if(onSuccessfulSave){
+      fetchExpenses();
+    }
+  },[onSuccessfulSave])

  return (
    ...
  );
}
```

