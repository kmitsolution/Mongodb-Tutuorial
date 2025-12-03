**Section 5 — Aggregation Framework** with **5.1 Introduction to Pipelines**, covering:

* What aggregation pipelines are
* `$match`
* `$project`
* `$group`
* `$sort`
* `$limit`
* `$skip`

All with explanations + clear examples.

---

# **📘 5. Aggregation Framework**

Aggregation Framework in MongoDB is a powerful tool for:

✔ Data analysis
✔ Transformations
✔ Filtering
✔ Grouping
✔ Sorting
✔ Reporting (like SQL GROUP BY + functions)

It works like a **pipeline**, where data flows through multiple stages.

---

# **5.1 Introduction to Aggregation Pipelines**

An **aggregation pipeline** is a sequence of stages.
Each stage processes documents and passes results to the next.

### ✔ Basic Structure:

```js
db.collection.aggregate([
  { stage1 },
  { stage2 },
  { stage3 }
])
```

Example:

```js
db.orders.aggregate([
  { $match: { status: "Delivered" } },
  { $group: { _id: "$customerId", totalSpent: { $sum: "$amount" } } }
])
```

---

# **Aggregation Stages Covered**

### 1. `$match` → filter

### 2. `$project` → select fields / reshape documents

### 3. `$group` → group by fields

### 4. `$sort` → sort output

### 5. `$limit` → limit rows

### 6. `$skip` → skip rows (useful for pagination)

---

# **1️⃣ $match — Filtering Documents**

Works like `find()`, but **inside** an aggregation pipeline.

### ✔ Example: Find users from Delhi

```js
{ $match: { city: "Delhi" } }
```

### ✔ Example: Range filter

```js
{ $match: { age: { $gt: 25 } } }
```

### ⚡ Best Practice:

Place `$match` **first** for best performance.

---

# **2️⃣ $project — Reshape / Select Fields**

Used to show/hide fields or create computed fields.

### ✔ Basic example:

```js
{ $project: { name: 1, city: 1, _id: 0 } }
```

### ✔ Rename field or compute:

```js
{
  $project: {
    username: "$name",
    ageInMonths: { $multiply: ["$age", 12] }
  }
}
```

### ✔ Remove fields:

```js
{ $project: { password: 0 } }
```

---

# **3️⃣ $group — Group and Aggregate**

Equivalent of SQL `GROUP BY`.

You must include `_id` inside `$group`.

### ✔ Example: Group orders by customer and total amount spent

```js
{
  $group: {
    _id: "$customerId",
    totalAmount: { $sum: "$amount" }
  }
}
```

### ✔ Count documents:

```js
{
  $group: {
    _id: "$city",
    count: { $sum: 1 }
  }
}
```

### ✔ Average:

```js
{
  $group: {
    _id: "$category",
    avgPrice: { $avg: "$price" }
  }
}
```

---

# **4️⃣ $sort — Sorting Results**

### ✔ Example: Sort by age ascending

```js
{ $sort: { age: 1 } }
```

### ✔ Descending:

```js
{ $sort: { age: -1 } }
```

### ✔ Combine with group:

```js
{ $sort: { totalAmount: -1 } }
```

⚠ Sorting without indexes can be slow.

---

# **5️⃣ $limit — Limit Output**

Limits documents to N.

### Example:

```js
{ $limit: 5 }
```

Useful for:

* Pagination
* Top-N analysis
* Reports

---

# **6️⃣ $skip — Skip Documents**

Skips N documents.

### Example:

```js
{ $skip: 10 }
```

Used for pagination along with `$limit`.

---

# **7️⃣ Putting It All Together (Full Pipeline Example)**

### 🎯 Goal:

Top 3 customers who spent the most, only for delivered orders.

### Pipeline:

```js
db.orders.aggregate([
  { $match: { status: "Delivered" } },
  { $group: {
      _id: "$customerId",
      totalSpent: { $sum: "$amount" }
    }
  },
  { $sort: { totalSpent: -1 } },
  { $limit: 3 }
])
```

### Output:

```json
[
  { "_id": 101, "totalSpent": 5400 },
  { "_id": 205, "totalSpent": 4200 },
  { "_id": 107, "totalSpent": 3800 }
]
```

---

# **8️⃣ Example: Pagination Using $skip + $limit**

Page 2, page size = 10

```js
db.users.aggregate([
  { $sort: { age: 1 } },
  { $skip: 10 },
  { $limit: 10 }
])
```

---

# **9️⃣ Example: Complete Pipeline with All Operators**

### Goal:

Show name, total orders, average order amount for customers from Delhi.

```js
db.orders.aggregate([
  { $match: { city: "Delhi" } },

  { $group: {
      _id: "$customerId",
      totalOrders: { $sum: 1 },
      avgAmount: { $avg: "$amount" }
    }
  },

  { $project: {
      _id: 0,
      customerId: "$_id",
      totalOrders: 1,
      avgAmount: 1
    }
  },

  { $sort: { totalOrders: -1 } },

  { $limit: 5 }
])
```

---

# **🎯 Summary of Aggregation Operators Covered**

| Operator   | Purpose                     |
| ---------- | --------------------------- |
| `$match`   | Filter documents            |
| `$project` | Select fields, reshape data |
| `$group`   | Group by and aggregate      |
| `$sort`    | Sort documents              |
| `$limit`   | Limit number of docs        |
| `$skip`    | Skip documents (pagination) |

---


