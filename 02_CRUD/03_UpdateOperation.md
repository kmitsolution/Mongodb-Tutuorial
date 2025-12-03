Below is **Section 2.3 — Update Operations** with complete explanations and examples for:

* `updateOne()`
* `updateMany()`
* Update operators: `$set`, `$inc`, `$push`, `$pull`
* Array update techniques
* Upserts (`upsert: true`)

Everything is beginner-friendly and practical.

---

# **📘 2.3 Update Operations in MongoDB**

MongoDB provides two major update methods:

1️⃣ `updateOne()` → updates **first matching document**
2️⃣ `updateMany()` → updates **all matching documents**

---

# **1️⃣ updateOne()**

### ✔ Syntax

```js
db.collection.updateOne(filter, update)
```

### ✔ Example

Update Alice’s age to 26:

```js
db.users.updateOne(
  { name: "Alice" },  
  { $set: { age: 26 } }
)
```

---

# **2️⃣ updateMany()**

### ✔ Syntax

```js
db.collection.updateMany(filter, update)
```

### ✔ Example

Increase the age of all users from Delhi by 1 year:

```js
db.users.updateMany(
  { city: "Delhi" },
  { $inc: { age: 1 } }
)
```

---

# **3️⃣ Update Operators**

MongoDB offers several powerful update operators:

## **A. `$set` – Set/Update a field**

Adds a new field or updates existing field.

### Example:

```js
db.users.updateOne(
  { name: "Bob" },
  { $set: { city: "Kolkata" } }
)
```

If `city` didn't exist → it will be created.

---

## **B. `$inc` – Increase/Decrease numeric value**

### Example: Increase salary by 5000:

```js
db.employees.updateOne(
  { name: "John" },
  { $inc: { salary: 5000 } }
)
```

### Example: Decrease stock by 1:

```js
db.products.updateOne(
  { name: "Laptop" },
  { $inc: { stock: -1 } }
)
```

---

## **C. `$push` – Add an element to an array**

### Example:

```js
db.users.updateOne(
  { name: "Alice" },
  { $push: { skills: "React" } }
)
```

---

## **D. `$push` with `$each` – Push multiple elements**

```js
db.users.updateOne(
  { name: "Alice" },
  { $push: { skills: { $each: ["HTML", "CSS"] } } }
)
```

---

## **E. `$pull` – Remove an element from an array**

Remove `"MongoDB"` from skills:

```js
db.users.updateOne(
  { name: "David" },
  { $pull: { skills: "MongoDB" } }
)
```

---

## **F. `$pull` with condition**

Remove all marks < 50:

```js
db.students.updateOne(
  { name: "Rahul" },
  { $pull: { marks: { $lt: 50 } } }
)
```

---

# **4️⃣ Array Updates**

## **A. Add element**

```js
$push
```

## **B. Remove element**

```js
$pull
```

## **C. Update specific array items using positional operator `$`**

### Example Dataset:

```js
{
  name: "Alice",
  scores: [10, 20, 30]
}
```

### Increase the first matching score (20 → 50):

```js
db.users.updateOne(
  { name: "Alice", scores: 20 },
  { $set: { "scores.$": 50 } }
)
```

---

## **D. Update multiple array elements using arrayFilters**

### Example:

Increase all scores less than 30 by 5:

```js
db.users.updateOne(
  { name: "Alice" },
  { $inc: { "scores.$[elem]": 5 } },
  { arrayFilters: [ { "elem": { $lt: 30 } } ] }
)
```

---

# **5️⃣ Upserts**

**Upsert = Update + Insert**
If a matching document exists → update it
If not → insert a new one

### ✔ Syntax

```js
db.collection.updateOne(filter, update, { upsert: true })
```

---

## ✔ Example 1: Upsert a user

If "Sam" exists → update city
If not → insert new document

```js
db.users.updateOne(
  { name: "Sam" },
  { $set: { city: "Bangalore" } },
  { upsert: true }
)
```

If Sam does not exist → MongoDB inserts:

```json
{
  "_id": ObjectId("..."),
  "name": "Sam",
  "city": "Bangalore"
}
```

---

## ✔ Example 2: Upsert with `$setOnInsert`

Set fields only when inserting, not when updating.

```js
db.users.updateOne(
  { email: "newuser@example.com" },
  {
    $set: { lastLogin: new Date() },
    $setOnInsert: { createdAt: new Date() }
  },
  { upsert: true }
)
```

---

# **💡 Real-World Update Examples**

## ✔ Update product price

```js
db.products.updateOne(
  { name: "iPhone" },
  { $set: { price: 79900 } }
)
```

## ✔ Increase view count by 1

```js
db.posts.updateOne(
  { postId: 101 },
  { $inc: { views: 1 } }
)
```

## ✔ Add a new comment to a blog post

```js
db.posts.updateOne(
  { postId: 101 },
  { $push: { comments: { user: "Amit", text: "Nice post!" } } }
)
```

## ✔ Remove a deleted tag

```js
db.posts.updateOne(
  { postId: 101 },
  { $pull: { tags: "deprecated" } }
)
```

## ✔ Upsert product stock

```js
db.stock.updateOne(
  { item: "Keyboard" },
  { $inc: { quantity: 10 } },
  { upsert: true }
)
```

---

# **🎯 Summary Table**

| Operator        | Purpose                       |
| --------------- | ----------------------------- |
| `$set`          | Update or add a field         |
| `$inc`          | Increase/decrease a number    |
| `$push`         | Add to array                  |
| `$push + $each` | Add multiple values           |
| `$pull`         | Remove from array             |
| `$setOnInsert`  | Only when inserting in upsert |
| Positional `$`  | Update matching array element |
| `arrayFilters`  | Update multiple array items   |

---


