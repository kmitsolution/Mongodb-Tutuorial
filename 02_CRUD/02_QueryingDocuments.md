
# **📘 2.2 Querying Documents in MongoDB**

MongoDB uses **find()** and **findOne()** to retrieve documents.

Before we start, assume we have a **users** collection:

```js
db.users.insertMany([
  { name: "Alice", age: 25, city: "Delhi",  skills: ["Node.js", "MongoDB"] },
  { name: "Bob",   age: 30, city: "Mumbai", skills: ["Python", "SQL"] },
  { name: "Charlie", age: 35, city: "Delhi", skills: ["Java", "Spring"] },
  { name: "David", age: 20, city: "Chennai", skills: ["MongoDB", "Express"] }
])
```

---

# **1️⃣ find()**

Returns **all matching documents** (cursor).

### ✔ Syntax:

```js
db.collection.find(query, projection)
```

### Example: Find all users

```js
db.users.find()
```

---

# **2️⃣ findOne()**

Returns **first matching document**.

### Example:

```js
db.users.findOne({ city: "Delhi" })
```

---

# **3️⃣ Comparison Operators**

## ✔ `$eq` → equal to

```js
db.users.find({ age: { $eq: 25 } })
```

## ✔ `$lt` → less than

```js
db.users.find({ age: { $lt: 30 } })
```

## ✔ `$gt` → greater than

```js
db.users.find({ age: { $gt: 30 } })
```

## ✔ `$lte` → less than or equal

```js
db.users.find({ age: { $lte: 25 } })
```

## ✔ `$gte` → greater than or equal

```js
db.users.find({ age: { $gte: 30 } })
```

## ✔ `$ne` → not equal

```js
db.users.find({ city: { $ne: "Delhi" } })
```

## ✔ `$in` → match any value in an array

```js
db.users.find({ city: { $in: ["Delhi", "Mumbai"] } })
```

## ✔ `$nin` → not in

```js
db.users.find({ city: { $nin: ["Delhi", "Mumbai"] } })
```

---

# **4️⃣ Logical Operators**

---

## ✔ `$and` (both conditions true)

### Example: age > 20 AND city = Delhi

```js
db.users.find({
  $and: [
    { age: { $gt: 20 } },
    { city: "Delhi" }
  ]
})
```

**Shortcut (better):**

```js
db.users.find({ age: { $gt: 20 }, city: "Delhi" })
```

---

## ✔ `$or` (either condition true)

### Example: users in Delhi **OR** age < 25

```js
db.users.find({
  $or: [
    { city: "Delhi" },
    { age: { $lt: 25 } }
  ]
})
```

---

## ✔ `$not` (negates condition)

### Example: age NOT greater than 25

```js
db.users.find({ age: { $not: { $gt: 25 } } })
```

Means → age ≤ 25

---

## ✔ `$nor` (none of the conditions true)

### Example: neither city = Delhi nor age < 30

```js
db.users.find({
  $nor: [
    { city: "Delhi" },
    { age: { $lt: 30 } }
  ]
})
```

---

# **5️⃣ Projection (selecting specific fields)**

Projection decides which fields to **include** or **exclude**.

### ✔ Syntax:

```js
db.collection.find(query, { field: 1 or 0 })
```

---

## **A. Include fields**

(1 = include)

### Example: return only name and city

```js
db.users.find(
  { city: "Delhi" },
  { name: 1, city: 1 }
)
```

MongoDB always returns **_id** unless excluded.

### Exclude `_id`:

```js
db.users.find(
  { city: "Delhi" },
  { name: 1, city: 1, _id: 0 }
)
```

---

## **B. Exclude fields**

(0 = exclude)

### Example: exclude age

```js
db.users.find(
  { city: "Delhi" },
  { age: 0 }
)
```

❗ You cannot mix include and exclude fields
(except `_id`)

---

## **C. Projection with Array Fields**

### Example: show only the first skill

```js
db.users.find(
  {},
  { name: 1, skills: { $slice: 1 } }
)
```

---

# **6️⃣ Complete Real-World Examples**

---

## ✔ Example 1: Find all users older than 25

```js
db.users.find({ age: { $gt: 25 } })
```

---

## ✔ Example 2: Users from Delhi AND skilled in MongoDB

```js
db.users.find({
  city: "Delhi",
  skills: "MongoDB"
})
```

(MongoDB matches array values automatically.)

---

## ✔ Example 3: Users with age between 20 and 30

```js
db.users.find({
  age: { $gte: 20, $lte: 30 }
})
```

---

## ✔ Example 4: Users not living in Delhi

```js
db.users.find({
  city: { $ne: "Delhi" }
})
```

---

## ✔ Example 5: Users from Delhi or Mumbai, show only name + age

```js
db.users.find(
  { city: { $in: ["Delhi", "Mumbai"] } },
  { name: 1, age: 1, _id: 0 }
)
```

---

## ✔ Example 6: Find users with skill "MongoDB" and age < 30

```js
db.users.find({
  $and: [
    { skills: "MongoDB" },
    { age: { $lt: 30 } }
  ]
})
```

---

# **🧪 Quick Practice Tasks (Do These!)**

### 1️⃣ Find users with age >= 30

```js
db.users.find({ age: { $gte: 30 } })
```

### 2️⃣ Show only name and skills

```js
db.users.find({}, { name: 1, skills: 1, _id: 0 })
```

### 3️⃣ Find users living in Chennai OR age < 25

```js
db.users.find({
  $or: [
    { city: "Chennai" },
    { age: { $lt: 25 } }
  ]
})
```

### 4️⃣ Find users NOT from Mumbai

```js
db.users.find({ city: { $ne: "Mumbai" } })
```

---

# **🎯 Summary**

| Concept              | Description                               |
| -------------------- | ----------------------------------------- |
| `find()`             | Fetch multiple documents                  |
| `findOne()`          | Fetch first matching document             |
| Comparison operators | `$gt`, `$lt`, `$eq`, `$ne`, `$in`, `$nin` |
| Logical operators    | `$and`, `$or`, `$not`, `$nor`             |
| Projection           | Select specific fields to include/exclude |

---


