---
marp: true
---

# Creating the API

## Working Directory

Create a working directory(folder) named `WorkshopExpenseAPI` or any name you prefer.

---

## Package.json

The `package.json` is the metadata that is used by nodejs runtime of how to run your app.

- scripts
- dependencies
- dev-dependencies

---

To create the `package.json` file, we need to execute this command in the CLI/terminal

```
npm init
```

follow the interactive prompt until the file is created. The output should be similar to this:

```json
{
  "name": "workshopexpenseapi1",
  "version": "1.0.0",
  "description": "",
  "main": "server.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "",
  "license": "ISC"
}
```

---

We also add `"type": "module"` so that we will be able to use ES module import style.

```diff
{
  "name": "workshopexpenseapi1",
+ "type": "module",
  "version": "1.0.0",
  "description": "",
  "main": "server.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "",
  "license": "ISC"
}
```

---

## Server.js

is the startup file to run our API and listen to http requests the we will support based on technical requirements.

In order for our app to accept http traffic, we will install the `express` package via npm by running the command:

```
npm install express
```

---

Then write the following code in the `server.js`

```javascript
import express from "express";

const PORT = process.env.PORT || 1234;

const app = express();

app.use(express.json());

app.listen(PORT, () => console.log(`Server is running on port: ${PORT}`));
```

To test if it is working, we can run `node server.js` in the CLI.

You should be seeing a log message `Server is running on port 1234`.

---

## Cross Origin Resource Sharing(CORS)


![CORS](./Assets/CORS.drawio.png)

---

`npm install cors`

Then, we update `server.js` to this

```diff + js
import express from "express";
+ import cors from "cors";

const PORT = process.env.PORT || 1234;

const app = express();

+ app.use(cors());

app.use(express.json());

app.listen(PORT, () => console.log(`Server is running on port: ${PORT}`));

```

---

## Defining API Endpoints

An "endpoint" is a specific URL (Uniform Resource Locator) or URI (Uniform Resource Identifier) that an API exposes to allow access to its functionality or data. It's like a designated entry point to interact with a particular feature or resource provided by the API.

---

**GET** mywebsite.com/**`api/expenses`** - returns a list of expenses

```json
[
  {
    "description": "Iced Coffee",
    "amount": "39",
    "date": "2023-11-05"
  },
  {
    "description": "Burger",
    "amount": "79",
    "date": "2023-11-05"
  }
]
```

---

**POST** mywebsite.com/**`api/expenses`** - receives input and saves to database with request body:

```json
{
  "description": "Iced Coffee",
  "amount": "39",
  "date": "2023-11-05"
}
```

---

# Routing

In the context of web development, routing refers to the process of defining a function that responds to a client request based on the requested URL and HTTP method.

To define our routes, create a file `routes/expense.js`.

---

Define a route to handle **`GET api/expenses`** request that returns hard-coded expense data.

```javascript
import express from "express";

const expenseRouter = express.Router();

expenseRouter.get("/", (request, response) => {
  const expenses = [
    {
      description: "Iced Coffee",
      amount: "39",
      date: "2023-11-05",
    },
    {
      description: "Burger",
      amount: "79",
      date: "2023-11-05",
    },
  ];

  return response.send(expenses);
});

export default expenseRouter;
```

---

and register the exported `expenseRouter` in `server.js`

```diff
import express from "express";
import cors from "cors";
+ import expenseRouter from "./routes/expense.js";

const PORT = process.env.PORT || 1234;

const app = express();

app.use(cors());

app.use(express.json());

+ app.use("/api/expenses", expenseRouter);

app.listen(PORT, () => console.log(`Server is running on port: ${PORT}`));

```

Now, run the server by `node server.js`

---

## Testing the endpoint with Thunder Client

Thunder Client is a lightweight Rest API Client Extension for Visual Studio Code to easily interact with our API endpoints.

To install, go to the `Extensions(CTRL + SHIFT + X)` tab in VSCode and search `Thunder Client`.

After installing, Thunder Client should appear in the toolbar (lightning logo).

---

![Thunder Client screenshot](https://raw.githubusercontent.com/rangav/thunder-client-support/master/images/thunder-client-v2.png)

---

## Hot Reload

`Run Server` -> `make changes` -> `changes applied`

Installing nodemon
`npm install --save-dev nodemon`

---

Modify `package.json`

```diff
{
  "name": "workshopexpenseapi1",
  "type": "module",
  "version": "1.0.0",
  "description": "",
  "main": "server.js",
  "scripts": {
+    "start": "nodemon server.js",
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "",
  "license": "ISC",
  "dependencies": {
    "cors": "^2.8.5",
    "express": "^4.18.2"
  },
+  "devDependencies": {
+    "nodemon": "^3.0.1"
+  }
}
```

new command **`npm start`**

---

# Retrieving expense data in MongoDb

## Dependencies

`mongodb` - The official MongoDB driver for Node.js.

`dotenv` - Dotenv is a zero-dependency module that loads environment variables from a `.env` file into `process.env`. Storing configuration in the environment separate from code

**`npm install mongodb dotenv`**

---

`package.json`

```diff
{
  "name": "workshopexpenseapi1",
  "type": "module",
  "version": "1.0.0",
  "description": "",
  "main": "server.js",
  "scripts": {
    "start": "nodemon server.js",
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "",
  "license": "ISC",
  "dependencies": {
    "cors": "^2.8.5",
+    "dotenv": "^16.3.1",
    "express": "^4.18.2",
+    "mongodb": "^6.3.0"
  },
  "devDependencies": {
    "nodemon": "^3.0.1"
  }
}

```

---

Create a file **`.env`**

```
DATABASE_URL=mongodb+srv://yourmongodb.com/?retryWrites=true&w=majority
```

**`DATABASE_URL`** is the environment variable

---

# connection.js

```js
import { MongoClient } from "mongodb";
import "dotenv/config";

async function getDbConnection() {
  const connectionString = process.env.DATABASE_URL || "";

  const client = new MongoClient(connectionString);

  var connection = await client.connect();

  return connection.db("ExpenseDatabase");
}

export default getDbConnection;
```
---

# expense.js

```diff
import express from "express";

const expenseRouter = express.Router();

expenseRouter.get("/", (request, response) => {
-  const expenses = [
-    {
-      description: "Iced Coffee",
-      amount: "39",
-      date: "2023-11-05",
-    },
-    {
-      description: "Burger",
-      amount: "79",
-      date: "2023-11-05",
-    },
-  ];

-  return response.send(expenses);
});

export default expenseRouter;
```

---

``` diff
import express from "express";
+import getDbConnection from "../connection.js";

const expenseRouter = express.Router();

+expenseRouter.get("/", async (request, response) => {
+  const connection = await getDbConnection();

+  const collection = connection.collection("expenses");

+  const results = await collection.find({}).toArray();

+  return response.send(results);
});

export default expenseRouter;

```
---

# Saving expense data to MongoDb

`expense.js`

``` diff
import express from "express";
import getDbConnection from "../connection.js";

const expenseRouter = express.Router();

expenseRouter.get("/", async (request, response) => {
  const connection = await getDbConnection();

  const collection = connection.collection("expenses");

  const results = await collection.find({}).toArray();

  return response.send(results);
});

+expenseRouter.post("/", async (request, response) => {
+  const body = request.body;

+  const newExpense = {
+    amount: body.amount,
+    description: body.description,
+    date: body.date,
+  };

+  const connection = await getDbConnection();

+  const collection = connection.collection('expenses');

+  await collection.insertOne(newExpense);

+  return response.status(201).send();
+});

export default expenseRouter;
```