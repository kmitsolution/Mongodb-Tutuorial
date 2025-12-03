**Section 5.2 — Advanced Aggregation**, covering:

* **$unwind** (flatten arrays)
* **$lookup** (join collections)
* **$facet** (multiple output pipelines at once)
* **Multi-stage pipelines** (complex reporting examples)

Everything is explained simply with diagrams & examples.

---

# **📘 5.2 Advanced Aggregation**

MongoDB’s aggregation becomes powerful when you start using:

* `$unwind`
* `$lookup`
* `$facet`
* Multi-stage pipelines

These allow complex analytics similar to SQL joins, array flattening, and multi-report outputs.

---

# **1️⃣ $unwind — Expand Array Elements**

`$unwind` **converts each element in an array into a separate document**.

### Example Document:

```json
{
  "name": "Alice",
  "skills": ["MongoDB", "Node.js", "React"]
}
```

---

## ✔ Use $unwind:

```js
{ $unwind: "$skills" }
```

### Output:

```json
{ "name": "Alice", "skills": "MongoDB" }
{ "name": "Alice", "skills": "Node.js" }
{ "name": "Alice", "skills": "React" }
```

---

## ✔ When to use $unwind:

* When you need to analyze individual elements in an array
* When grouping arrays
* When joining arrays with `$lookup`
* For counting array items

---

### Example: Count total skills used by all users

```js
db.users.aggregate([
  { $unwind: "$skills" },
  { $group: { _id: "$skills", count: { $sum: 1 } } }
])
```

---

# **2️⃣ $lookup — Join Two Collections**

`$lookup` is like **LEFT JOIN** in SQL.

### Syntax:

```js
{
  $lookup: {
    from: "otherCollection",
    localField: "fieldInThisCollection",
    foreignField: "fieldInOtherCollection",
    as: "resultArray"
  }
}
```

---

## ✔ Example: Join users with their orders

### users:

```json
{ "_id": 1, "name": "Alice" }
```

### orders:

```json
{ "_id": 101, "userId": 1, "amount": 3000 }
```

---

## Query:

```js
db.users.aggregate([
  {
    $lookup: {
      from: "orders",
      localField: "_id",
      foreignField: "userId",
      as: "userOrders"
    }
  }
])
```

---

### Output:

```json
{
  "_id": 1,
  "name": "Alice",
  "userOrders": [
    { "_id": 101, "userId": 1, "amount": 3000 }
  ]
}
```

---

# **Advanced Join with pipeline ($lookup + pipeline)**

```js
{
  $lookup: {
    from: "orders",
    let: { userId: "$_id" },
    pipeline: [
      { $match: { $expr: { $eq: ["$userId", "$$userId"] } } },
      { $sort: { amount: -1 } },
      { $limit: 1 }
    ],
    as: "topOrder"
  }
}
```

---

# **3️⃣ $facet — Multiple Pipelines in One Query**

`$facet` lets you run **multiple aggregations at once** and return results in a single document.

Useful for:

* Dashboards
* Analytics
* Multi-output queries

---

## ✔ Example:

Generate multiple reports:

* Count documents
* Group by city
* Average age

```js
db.users.aggregate([
  {
    $facet: {
      "totalUsers": [
        { $count: "count" }
      ],
      "usersByCity": [
        { $group: { _id: "$city", count: { $sum: 1 } } }
      ],
      "averageAge": [
        { $group: { _id: null, avgAge: { $avg: "$age" } } }
      ]
    }
  }
])
```

---

### Output:

```json
{
  "totalUsers": [{ "count": 100 }],
  "usersByCity": [
    { "_id": "Delhi", "count": 30 },
    { "_id": "Mumbai", "count": 40 }
  ],
  "averageAge": [{ "avgAge": 29.5 }]
}
```

---

# **4️⃣ Writing Multi-Stage Pipelines**

You can combine stages to produce complex analytics.

---

## ✔ Example 1: Top 3 customers with highest order value

```js
db.orders.aggregate([
  { $group: {
      _id: "$userId",
      totalAmount: { $sum: "$amount" }
  }},
  { $sort: { totalAmount: -1 } },
  { $limit: 3 }
])
```

---

## ✔ Example 2: For each user, list each item they bought

```js
db.orders.aggregate([
  { $unwind: "$items" },
  { $group: {
      _id: "$userId",
      items: { $addToSet: "$items.product" }
  }}
])
```

---

## ✔ Example 3: Join + Unwind + Group

Find top category purchased by each user:

```js
db.orders.aggregate([
  { $lookup: {
      from: "products",
      localField: "productId",
      foreignField: "_id",
      as: "productInfo"
  }},
  { $unwind: "$productInfo" },
  { $group: {
      _id: "$userId",
      topCategory: { $addToSet: "$productInfo.category" }
  }}
])
```

---

## ✔ Example 4: Full pipeline with all stages

Goal:
Get users from Delhi, join their orders, flatten, calculate totals, sort, and show top 5.

```js
db.users.aggregate([
  { $match: { city: "Delhi" } },

  { $lookup: {
      from: "orders",
      localField: "_id",
      foreignField: "userId",
      as: "orders"
  }},

  { $unwind: "$orders" },

  { $group: {
      _id: "$_id",
      name: { $first: "$name" },
      totalSpent: { $sum: "$orders.amount" }
  }},

  { $sort: { totalSpent: -1 } },

  { $limit: 5 }
])
```

---

# **🎯 Summary of Advanced Aggregation Operators**

| Operator              | Purpose                              |
| --------------------- | ------------------------------------ |
| `$unwind`             | Flatten array elements               |
| `$lookup`             | Join two collections                 |
| `$facet`              | Multiple pipelines in one query      |
| Multi-stage pipelines | Combine stages for complex analytics |

---


