
* **Embedding vs Referencing**
* **Nested Documents**
* **Arrays and Subdocuments**

All explained clearly with real-world examples.

---

# **📘 3.1 Document Structure (MongoDB)**

MongoDB stores data as **documents** (JSON-like objects).
Documents can contain:

* Primitive fields (name, age, city)
* Nested documents (address, profile, specs)
* Arrays (skills, tags, items)
* Arrays of documents (orders, comments, reviews)

This flexibility is what makes MongoDB powerful.

---

# **1️⃣ Embedding vs Referencing**

MongoDB supports two ways to store related data:

---

# **A. Embedding (Denormalization)**

You store related data **inside the same document**.

### ✔ Example: User with embedded address

```json
{
  "name": "Alice",
  "age": 25,
  "address": {
    "city": "Delhi",
    "zip": 110001
  }
}
```

### ✔ Example: Blog post with embedded comments

```json
{
  "title": "MongoDB Basics",
  "comments": [
    { "user": "John", "text": "Nice post!", "likes": 5 },
    { "user": "Sara", "text": "Very helpful!", "likes": 3 }
  ]
}
```

### ✔ When to Embed:

* Data is **accessed together**
* Relationship is **1-to-few**
* No need for huge list of related data
* Simpler, faster reads

### 📌 Example scenarios:

* User → address
* Blog post → comments
* Product → reviews
* Order → items

---
### Example
Nice — let’s walk through this step-by-step using the document you gave and a few more sample documents. I’ll show:

1. **Create database & collection**
2. **Insert sample documents (single + many)**
3. **Find / query (basic, projection, filters, sorting)**
4. **Update (updateOne, updateMany, array updates, upsert)**
5. **Delete (deleteOne, deleteMany, drop)**
6. **A few extra sample documents** you can use for testing

I'll give **mongosh** (MongoDB shell) commands first (quick & direct), then a short **Node.js (async/await)** snippet you can copy into a JS project.

---

# 0. Example base document (you provided)

```json
{
  "name": "Alice",
  "age": 25,
  "address": {
    "city": "Delhi",
    "zip": 110001
  }
}
```

---

# 1. Create DB & Collection (mongosh)

Open `mongosh` and run:

```js
// switch/create DB
use demoDB

// create collection explicitly (optional)
db.createCollection("users")

// confirm
show collections
```

> If you skip `createCollection`, MongoDB will create the collection automatically on first insert.

---

# 2. Insert sample documents

## A. Insert one document

```js
db.users.insertOne({
  name: "Alice",
  age: 25,
  address: { city: "Delhi", zip: 110001 }
})
```

## B. Insert many sample documents

```js
db.users.insertMany([
  {
    name: "Bob",
    age: 30,
    address: { city: "Mumbai", zip: 400001 },
    skills: ["Python", "SQL"]
  },
  {
    name: "Charlie",
    age: 35,
    address: { city: "Delhi", zip: 110002 },
    skills: ["Java", "Spring"],
    active: true
  },
  {
    name: "David",
    age: 22,
    address: { city: "Chennai", zip: 600001 },
    skills: ["MongoDB", "Express", "Node.js"]
  },
  {
    name: "Eve",
    age: 28,
    address: { city: "Bengaluru", zip: 560001 },
    skills: [],
    tags: ["new", "trial"]
  }
])
```

---

# 3. Find / Query examples

## A. Find all documents

```js
db.users.find().pretty()
```

## B. Find one

```js
db.users.findOne({ name: "Alice" })
```

## C. Filter with comparison operators

```js
// age greater than 25
db.users.find({ age: { $gt: 25 } }).pretty()

// age between 23 and 35
db.users.find({ age: { $gte: 23, $lte: 35 } })
```

## D. Logical operators

```js
// city Delhi OR Mumbai
db.users.find({ $or: [{ "address.city": "Delhi" }, { "address.city": "Mumbai" }] })

// age > 25 AND city = Delhi
db.users.find({ age: { $gt: 25 }, "address.city": "Delhi" })
```

## E. Projection (return only certain fields)

```js
// include name and city, exclude _id
db.users.find({ "address.city": "Delhi" }, { name: 1, "address.city": 1, _id: 0 })
```

## F. Sort and limit

```js
// sort by age descending, limit 3
db.users.find().sort({ age: -1 }).limit(3)
```

---

# 4. Update operations

## A. updateOne + $set

```js
// set Alice's age to 26
db.users.updateOne(
  { name: "Alice" },
  { $set: { age: 26 } }
)
```

## B. updateMany + $inc

```js
// add 1 year to everyone in Delhi
db.users.updateMany(
  { "address.city": "Delhi" },
  { $inc: { age: 1 } }
)
```

## C. Add element to an array ($push, $each)

```js
// push a new skill to David
db.users.updateOne(
  { name: "David" },
  { $push: { skills: "React" } }
)

// push multiple skills
db.users.updateOne(
  { name: "Eve" },
  { $push: { skills: { $each: ["HTML", "CSS"] } } }
)
```

## D. Remove element from array ($pull)

```js
// remove "SQL" from Bob's skills
db.users.updateOne(
  { name: "Bob" },
  { $pull: { skills: "SQL" } }
)
```

## E. Update specific array element (positional $)

Suppose a document has an `orders` array; here is an example pattern:

```js
// sample structure: { name: "X", orders: [{ id:1, qty:2 }, { id:2, qty:1 }] }
// increase qty of order id:2 for user X
db.users.updateOne(
  { name: "X", "orders.id": 2 },
  { $inc: { "orders.$.qty": 1 } }
)
```

## F. Upsert (update or insert)

```js
// if user Frank doesn't exist, insert him; otherwise update city
db.users.updateOne(
  { name: "Frank" },
  { $set: { "address.city": "Hyderabad", age: 29 } },
  { upsert: true }
)
```

---

# 5. Delete operations

## A. deleteOne

```js
// delete Bob
db.users.deleteOne({ name: "Bob" })
```

## B. deleteMany

```js
// delete all users from Chennai
db.users.deleteMany({ "address.city": "Chennai" })
```

## C. Drop collection

```js
db.users.drop()
```

---

# 6. Some additional useful queries

## A. Count

```js
db.users.countDocuments({ "address.city": "Delhi" })
```

## B. Distinct

```js
db.users.distinct("address.city")
```

## C. Aggregation (group by city, avg age)

```js
db.users.aggregate([
  { $group: { _id: "$address.city", avgAge: { $avg: "$age" }, count: { $sum: 1 } } },
  { $sort: { count: -1 } }
])
```

---

# 7. Extra sample documents you can insert for richer testing

```js
[
  {
    name: "Farah",
    age: 32,
    address: { city: "Delhi", zip: 110003 },
    skills: ["Go", "Docker"],
    active: true,
    joinedAt: ISODate("2023-07-15T10:00:00Z")
  },
  {
    name: "George",
    age: 40,
    address: { city: "Mumbai", zip: 400002 },
    skills: ["C#", "Azure"],
    tags: ["vip"],
    purchases: [{ id: 1, amount: 250 }, { id: 2, amount: 800 }]
  },
  {
    name: "Hina",
    age: 21,
    address: { city: "Pune", zip: 411001 },
    skills: ["Design"],
    notes: null
  }
]
```

Insert all at once:

```js
db.users.insertMany([
  {
    name: "Farah",
    age: 32,
    address: { city: "Delhi", zip: 110003 },
    skills: ["Go", "Docker"],
    active: true,
    joinedAt: ISODate("2023-07-15T10:00:00Z")
  },
  {
    name: "George",
    age: 40,
    address: { city: "Mumbai", zip: 400002 },
    skills: ["C#", "Azure"],
    tags: ["vip"],
    purchases: [{ id: 1, amount: 250 }, { id: 2, amount: 800 }]
  },
  {
    name: "Hina",
    age: 21,
    address: { city: "Pune", zip: 411001 },
    skills: ["Design"],
    notes: null
  }
])
```

---

# **B. Referencing (Normalization)**

Store related data in **separate collections**, link using an id.

### ✔ Example: Referencing user in an order

**Orders Collection**

```json
{
  "_id": 501,
  "userId": "u101",
  "products": ["Shirt", "Shoes"],
  "total": 1500
}
```

**Users Collection**

```json
{
  "_id": "u101",
  "name": "Alice",
  "city": "Delhi"
}
```

To get full order info → you perform `$lookup` (like JOIN).

### ✔ When to Reference:

* Data is **large**
* Relationship is **1-to-many or many-to-many**
* Data updated frequently
* Avoid large embedded arrays

### 📌 Example scenarios:

* Customer ↔ Orders
* Student ↔ Courses
* Users ↔ Roles
* Authors ↔ Books

---
### Example
Here is a **complete, step-by-step MongoDB example** using your **Users** and **Orders** collections — with:

✔ Creating database & collections
✔ Inserting multiple documents
✔ Querying (find, filters, projections)
✔ Updating orders & users
✔ Deleting data
✔ Aggregation examples (JOIN using `$lookup`)

**⚠️ No Node.js code included — only mongosh/MongoDB shell commands as you requested.**

---

# **📌 DATABASE SETUP**

### **1. Switch/Create Database**

```js
use shopDB
```

---

# **📌 2. Create Collections (optional)**

```js
db.createCollection("users")
db.createCollection("orders")
```

---

# **📌 3. Insert Users Collection Data**

### Insert Single

```js
db.users.insertOne({
  _id: "u101",
  name: "Alice",
  city: "Delhi"
})
```

### Insert Many Extra Users

```js
db.users.insertMany([
  { _id: "u102", name: "Bob", city: "Mumbai" },
  { _id: "u103", name: "Charlie", city: "Delhi" },
  { _id: "u104", name: "David", city: "Chennai" }
])
```

---

# **📌 4. Insert Orders Collection Data**

### Insert One

```js
db.orders.insertOne({
  _id: 501,
  userId: "u101",
  products: ["Shirt", "Shoes"],
  total: 1500
})
```

### Insert Many

```js
db.orders.insertMany([
  {
    _id: 502,
    userId: "u102",
    products: ["Laptop Bag"],
    total: 800
  },
  {
    _id: 503,
    userId: "u101",
    products: ["Mobile Cover", "Charger"],
    total: 600
  },
  {
    _id: 504,
    userId: "u103",
    products: ["Watch"],
    total: 1200
  }
])
```

---

# **📌 5. FIND (Querying Documents)**

---

## **A. Find all users**

```js
db.users.find().pretty()
```

## **B. Find orders by a specific user**

```js
db.orders.find({ userId: "u101" })
```

## **C. Find orders above total > 1000**

```js
db.orders.find({ total: { $gt: 1000 } })
```

## **D. Show only orderId and total (projection)**

```js
db.orders.find({}, { _id: 1, total: 1 })
```

---

# **📌 6. UPDATE Operations**

---

## **A. Update the city of a user**

```js
db.users.updateOne(
  { _id: "u101" },
  { $set: { city: "Noida" } }
)
```

---

## **B. Add a new product to an order**

```js
db.orders.updateOne(
  { _id: 501 },
  { $push: { products: "Socks" } }
)
```

---

## **C. Increase the total amount**

```js
db.orders.updateOne(
  { _id: 503 },
  { $inc: { total: 100 } }
)
```

---

## **D. Update multiple users (Delhi → NCR)**

```js
db.users.updateMany(
  { city: "Delhi" },
  { $set: { city: "NCR" } }
)
```

---

# **📌 7. DELETE Operations**

---

## **A. Delete one order**

```js
db.orders.deleteOne({ _id: 504 })
```

---

## **B. Delete all orders of a user**

```js
db.orders.deleteMany({ userId: "u103" })
```

---

## **C. Drop entire collection**

```js
db.orders.drop()
```

---

# **📌 8. Aggregation Examples (JOIN: Users + Orders)**

Since MongoDB is NoSQL, join is done using **$lookup**.

---

## **A. Join orders with users (like SQL inner join)**

```js
db.orders.aggregate([
  {
    $lookup: {
      from: "users",
      localField: "userId",
      foreignField: "_id",
      as: "userInfo"
    }
  }
])
```

---

## **B. Show each order with user's name and city**

```js
db.orders.aggregate([
  {
    $lookup: {
      from: "users",
      localField: "userId",
      foreignField: "_id",
      as: "user"
    }
  },
  { $unwind: "$user" },
  {
    $project: {
      _id: 1,
      userId: 1,
      products: 1,
      total: 1,
      customerName: "$user.name",
      customerCity: "$user.city"
    }
  }
])
```

---

## **C. Total spending by each user**

```js
db.orders.aggregate([
  { $group: { _id: "$userId", totalSpent: { $sum: "$total" } } }
])
```

---

## **D. Count orders by city (Join + Group)**

```js
db.orders.aggregate([
  {
    $lookup: {
      from: "users",
      localField: "userId",
      foreignField: "_id",
      as: "user"
    }
  },
  { $unwind: "$user" },
  {
    $group: {
      _id: "$user.city",
      orderCount: { $sum: 1 }
    }
  }
])
```

---

# **9. Additional Sample Documents (Optional)**

You can insert these for more realistic testing:

```js
db.orders.insertMany([
  {
    _id: 505,
    userId: "u104",
    products: ["Keyboard", "Mouse"],
    total: 2000
  },
  {
    _id: 506,
    userId: "u102",
    products: ["Pen Drive"],
    total: 500
  }
])
```

---




# **Embedding vs Referencing (Quick Comparison)**

| Feature           | Embedding | Referencing              |
| ----------------- | --------- | ------------------------ |
| Read Performance  | Fast      | Slower                   |
| Write Performance | Simple    | More complex             |
| Relational Size   | Small     | Large or many            |
| Duplication       | Possible  | Minimal                  |
| Recommended       | 1-to-few  | 1-to-many / many-to-many |

---

# **2️⃣ Nested Documents**

A document can contain another **document** as its value.

### ✔ Example of nested document:

```json
{
  "product": "Laptop",
  "brand": {
    "name": "Dell",
    "yearFounded": 1984
  },
  "specs": {
    "cpu": "i7",
    "ram": "16GB"
  }
}
```

This structure represents real-world objects very naturally.

---

# **Nested Document Queries**

### Find product where brand name = "Dell"

```js
db.products.find({ "brand.name": "Dell" })
```

### Find product with cpu = "i7"

```js
db.products.find({ "specs.cpu": "i7" })
```

Dot notation (`brand.name`) is used for nested fields.

---

# **3️⃣ Arrays and Subdocuments**

Arrays can contain:

* Primitive values
* Documents (subdocuments)
* Mixed types (not recommended)

---

# **A. Arrays of simple values**

### Example:

```json
{
  "name": "Alice",
  "skills": ["MongoDB", "Node.js", "React"]
}
```

### Query: find users with skill MongoDB

```js
db.users.find({ skills: "MongoDB" })
```

---

# **B. Arrays of subdocuments**

This is very common in MongoDB.

### Example: Order with items array

```json
{
  "orderId": 2001,
  "items": [
    { "product": "Shirt", "qty": 2, "price": 500 },
    { "product": "Shoes", "qty": 1, "price": 1200 }
  ],
  "totalAmount": 2200
}
```

---

# **Queries on Subdocuments**

### 1. Match any item with qty = 2

```js
db.orders.find({ "items.qty": 2 })
```

### 2. Match item exactly

```js
db.orders.find({
  items: { product: "Shirt", qty: 2, price: 500 }
})
```

---

# **C. Adding items using `$push`**

### Example: Add new skill:

```js
db.users.updateOne(
  { name: "Alice" },
  { $push: { skills: "Express" } }
)
```

---

# **D. Removing items using `$pull`**

### Example: Remove a skill

```js
db.users.updateOne(
  { name: "Alice" },
  { $pull: { skills: "React" } }
)
```

---

# **E. Updating specific array element using `$` (positional operator)**

### Example:

Update qty of product "Shirt" to 3:

```js
db.orders.updateOne(
  { "items.product": "Shirt" },
  { $set: { "items.$.qty": 3 } }
)
```

---

# **F. Updating multiple matching elements using arrayFilters**

Increase qty of all items less than 2:

```js
db.orders.updateOne(
  { orderId: 2001 },
  { $inc: { "items.$[elem].qty": 1 } },
  { arrayFilters: [ { "elem.qty": { $lt: 2 } } ] }
)
```

---

# **💡 Real-World Example: Embedded Address + Orders**

### Users collection:

```json
{
  "name": "John",
  "address": { "city": "Chennai", "pincode": 600001 },
  "orders": [
    { "orderId": 1, "amount": 2000 },
    { "orderId": 2, "amount": 3500 }
  ]
}
```

This shows nested document + array + subdocuments.

---

# **🎯 Summary Table**

| Concept          | Meaning                                |
| ---------------- | -------------------------------------- |
| Embedding        | Store related data inside the document |
| Referencing      | Link documents via _id                 |
| Nested Documents | Document inside a document             |
| Arrays           | List of values                         |
| Subdocuments     | Documents inside an array              |
| Dot Notation     | Access nested fields (`address.city`)  |
| Positional `$`   | Update first matched array item        |

---


