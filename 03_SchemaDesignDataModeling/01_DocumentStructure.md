
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


