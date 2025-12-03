Here is **Section 6.2 — Using MongoDB with Node.js**, covering:

* Connecting to MongoDB
* Performing CRUD operations
* Using the **async/await** driver API (modern best practice)

Everything is explained with clean, real-world code examples.

---

# **📘 6.2 Using MongoDB with Node.js**

MongoDB provides an official Node.js driver that supports **async/await**, making database work simple and readable.

---

# **1️⃣ Installing the MongoDB Node.js Driver**

Run:

```bash
npm install mongodb
```

Then import:

```js
const { MongoClient } = require("mongodb");
```

---

# **2️⃣ Connecting to a Database**

The recommended way is to create a **MongoClient** instance and connect using `async/await`.

### ✔ Basic Connection Example

```js
const { MongoClient } = require("mongodb");

const uri = "mongodb://localhost:27017"; // or your Atlas URL
const client = new MongoClient(uri);

async function connectDB() {
  try {
    await client.connect();
    console.log("Connected to MongoDB!");

    const db = client.db("shop");
    return db;

  } catch (err) {
    console.error(err);
  }
}

connectDB();
```

---

# **3️⃣ CRUD Operations Using Node.js (Async API)**

Let’s use a `users` collection.

```js
const db = client.db("shop");
const users = db.collection("users");
```

---

# **👉 CREATE (Insert)**

## ✔ Insert One

```js
await users.insertOne({
  name: "Alice",
  age: 25,
  city: "Delhi"
});
```

## ✔ Insert Many

```js
await users.insertMany([
  { name: "Bob", age: 30 },
  { name: "Charlie", age: 35 }
]);
```

---

# **👉 READ (Find)**

## ✔ Find One

```js
const user = await users.findOne({ name: "Alice" });
console.log(user);
```

## ✔ Find Many + Convert to Array

```js
const result = await users.find({ age: { $gt: 25 } }).toArray();
console.log(result);
```

---

# **👉 UPDATE**

## ✔ updateOne()

```js
await users.updateOne(
  { name: "Alice" },
  { $set: { age: 26 } }
);
```

## ✔ updateMany()

```js
await users.updateMany(
  { city: "Delhi" },
  { $inc: { age: 1 } }
);
```

---

# **👉 DELETE**

## ✔ deleteOne()

```js
await users.deleteOne({ name: "Bob" });
```

## ✔ deleteMany()

```js
await users.deleteMany({ age: { $gt: 40 } });
```

---

# **4️⃣ Full CRUD Example with Async/Await**

```js
const { MongoClient } = require("mongodb");

const uri = "mongodb://localhost:27017";
const client = new MongoClient(uri);

async function run() {
  try {
    await client.connect();
    const db = client.db("shop");
    const users = db.collection("users");

    // CREATE
    await users.insertOne({ name: "Alice", age: 25 });

    // READ
    const alice = await users.findOne({ name: "Alice" });
    console.log("Alice =", alice);

    // UPDATE
    await users.updateOne(
      { name: "Alice" },
      { $set: { age: 26 } }
    );

    // DELETE
    await users.deleteOne({ name: "Alice" });

  } catch (err) {
    console.error(err);
  } finally {
    await client.close();
  }
}

run();
```

---

# **5️⃣ Using Aggregation in Node.js**

Aggregation pipeline works with async too:

```js
const results = await users.aggregate([
  { $match: { age: { $gt: 25 } } },
  { $group: { _id: "$city", total: { $sum: 1 } } }
]).toArray();

console.log(results);
```

---

# **6️⃣ Handling Connection in Real Applications (Best Practice)**

For real apps (Express, Next.js), use:

* **Re-using a single MongoDB client**
* Avoid connecting repeatedly inside every request

### Example: Reusable DB Connection Helper

```js
let db;

async function getDB() {
  if (!db) {
    await client.connect();
    db = client.db("shop");
  }
  return db;
}

module.exports = getDB;
```

---

# **7️⃣ Using MongoDB Atlas (Cloud)**

Replace local URI with Atlas URI:

```js
const uri = "mongodb+srv://username:password@cluster0.mongodb.net/shop";
```

Everything else remains the same.

---

# **8️⃣ Advantages of Async Node.js Driver**

| Feature                 | Benefit                  |
| ----------------------- | ------------------------ |
| async/await support     | Clean code               |
| Promise-based           | No callbacks             |
| Connection pooling      | High performance         |
| Full CRUD               | Yes                      |
| Aggregation             | Yes                      |
| Indexing & transactions | Yes                      |
| Works with frameworks   | Express, NestJS, Next.js |

---

# **🎯 Summary**

You learned:

✔ How to install the Node.js driver
✔ How to connect to MongoDB
✔ How to perform CRUD
✔ How to use asynchronous (`async/await`) operations
✔ Aggregation in Node.js
✔ Best practices for real-world apps

---

