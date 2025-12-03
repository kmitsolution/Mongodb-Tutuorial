## **2.1 Insert Operations**

In MongoDB, documents are inserted into **collections** using three main commands:

1. **insertOne()**
2. **insertMany()**
3. **insert()** (older, not recommended)

The commands are executed inside the Mongo Shell (`mongosh`) or Compass.

---

# ✅ **1. insertOne()**

This command inserts **one document** into a collection.

### ✔ Syntax:

```js
db.collectionName.insertOne(document)
```

### ✔ Example:

Insert a user into the **users** collection:

```js
db.users.insertOne({
  name: "Alice",
  age: 25,
  email: "alice@example.com",
  skills: ["MongoDB", "Node.js"]
})
```

### ✔ Output:

```json
{
  "acknowledged": true,
  "insertedId": ObjectId("64d1234567890abcd1234567")
}
```

MongoDB automatically generates the **_id** if you do not provide one.

---

# 🧩 **Inserting a Document with Custom _id**

You can manually set the `_id`:

```js
db.users.insertOne({
  _id: "user101",
  name: "John",
  age: 30
})
```

If a document with the same `_id` exists → ❌ **Error: duplicate key**.

---

# 🟦 Practice Example

Insert one product:

```js
db.products.insertOne({
  name: "Laptop",
  brand: "Dell",
  price: 55000,
  inStock: true
})
```

---

# ✅ **2. insertMany()**

Used to insert **multiple documents at once**.

### ✔ Syntax:

```js
db.collectionName.insertMany([ document1, document2, ... ])
```

---

## ✔ Example:

Insert multiple students:

```js
db.students.insertMany([
  { name: "Rahul", age: 20, marks: 85 },
  { name: "Anita", age: 22, marks: 92 },
  { name: "Sara", age: 21, marks: 78 }
])
```

### ✔ Output:

```json
{
  "acknowledged": true,
  "insertedIds": {
    "0": ObjectId("64d..."),
    "1": ObjectId("64d..."),
    "2": ObjectId("64d...")
  }
}
```

MongoDB creates `_id` for each document.

---

# 🧩 **Ordered Inserts vs Unordered Inserts**

### **Ordered (default):**

If one document fails, MongoDB **stops** inserting the rest.

```js
db.items.insertMany(
  [
    { _id: 1, item: "Pen" },
    { _id: 1, item: "Pencil" } // duplicate error
  ]
)
```

Result: Only the first document inserted. Second fails → operation stops.

---

### **Unordered Insert**

Use `{ ordered: false }` to continue even if errors occur:

```js
db.items.insertMany(
  [
    { _id: 1, item: "Pen" },
    { _id: 1, item: "Pencil" }  // error, but ignored
  ],
  { ordered: false }
)
```

Now, MongoDB inserts all valid documents and reports errors separately.

---

# ✔ **3. insert()** (Deprecated)

Older command:

```js
db.users.insert({ name: "Old Method" })
```

This is still supported but **not recommended**.
Prefer **insertOne()** and **insertMany()**.

---

# 🔥 **Important Notes about Insert Operations**

### ✔ A. MongoDB stores documents dynamically

No need to define columns before inserting.

### ✔ B. Different documents can have different fields

Example:

```js
db.users.insertMany([
  { name: "Amit", age: 25 },
  { name: "Sonia", city: "Delhi" }  // no age field
])
```

This is allowed — MongoDB is schema-less.

---

# 📌 Real-World Example

Storing order data:

```js
db.orders.insertOne({
  orderId: 501,
  customer: "Ravi",
  items: [
    { product: "Shirt", quantity: 2 },
    { product: "Shoes", quantity: 1 }
  ],
  totalAmount: 4500,
  orderDate: new Date()
})
```

MongoDB automatically stores `Date()` as a BSON date type.

---

# 🧪 Quick Practice Session (Do These!)

### 1. Insert a book

```js
db.books.insertOne({
  title: "MongoDB Basics",
  author: "Max Doe",
  pages: 240
})
```

### 2. Insert multiple employees

```js
db.employees.insertMany([
  { name: "John", dept: "IT", salary: 35000 },
  { name: "Priya", dept: "HR", salary: 25000 },
  { name: "Kumar", dept: "Finance", salary: 30000 }
])
```

### 3. Insert with nested objects

```js
db.students.insertOne({
  name: "Meera",
  subjects: ["Math", "Science"],
  address: { city: "Mumbai", pincode: 400001 }
})
```

---

# 🎯 Summary of Insert Operations

| Operation          | Use                                    |
| ------------------ | -------------------------------------- |
| **insertOne()**    | Insert 1 document                      |
| **insertMany()**   | Insert multiple documents              |
| **ordered: false** | Continue insert even if some docs fail |
| **_id field**      | Unique ID assigned automatically       |

---

