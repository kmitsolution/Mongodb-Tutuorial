**Section 5.3 — Common Aggregation Operators**, covering all core operators used inside `$group`, `$project`, and other aggregation stages:

* **$sum**
* **$avg**
* **$min**
* **$max**
* **$push**
* **$addToSet**
* **$first**
* **$last**
* (Plus small examples of combined usage)

Clear explanations with examples included.

---

# **📘 5.3 Common Aggregation Operators**

These operators are mainly used inside `$group`, `$project`, `$set`, `$bucket`, etc.

Let's explore each with examples.

---

# **1️⃣ $sum — Summation**

Used to calculate:

* Total amount
* Count (when adding 1)
* Quantity

### ✔ Example: Total sales by customer

```js
{
  $group: {
    _id: "$customerId",
    totalAmount: { $sum: "$amount" }
  }
}
```

---

### ✔ Counting documents using `$sum: 1`

```js
{
  $group: {
    _id: "$city",
    userCount: { $sum: 1 }
  }
}
```

---

# **2️⃣ $avg — Average**

Calculate average values.

### ✔ Example: Average rating of a product

```js
{
  $group: {
    _id: "$productId",
    avgRating: { $avg: "$rating" }
  }
}
```

---

# **3️⃣ $min — Minimum Value**

### ✔ Example: Lowest price of a product category

```js
{
  $group: {
    _id: "$category",
    minPrice: { $min: "$price" }
  }
}
```

---

# **4️⃣ $max — Maximum Value**

### ✔ Example: Highest order amount per customer

```js
{
  $group: {
    _id: "$customerId",
    largestOrder: { $max: "$amount" }
  }
}
```

---

# **5️⃣ $push — Create an Array (Allow Duplicates)**

Collect values into an array.

### ✔ Example:

Store ALL order IDs for each customer:

```js
{
  $group: {
    _id: "$customerId",
    orders: { $push: "$orderId" }
  }
}
```

### ✔ Allows duplicates:

If orderId repeats → it appears multiple times.

---

# **6️⃣ $addToSet — Create Unique Array (No Duplicates)**

Like `$push` but **removes duplicates**.

### ✔ Example:

Collect all unique categories:

```js
{
  $group: {
    _id: "$customerId",
    uniqueCategories: { $addToSet: "$category" }
  }
}
```

---

# **7️⃣ $first — First Document in Group**

Gets the first value **based on current pipeline order**.

### ✔ Example:

```js
{
  $group: {
    _id: "$customerId",
    firstOrder: { $first: "$orderDate" }
  }
}
```

**Important:** If you want earliest value, you must sort first:

```js
{ $sort: { orderDate: 1 } }
```

---

# **8️⃣ $last — Last Document in Group**

Gets last element after sorting.

### ✔ Example:

```js
{
  $group: {
    _id: "$customerId",
    lastOrder: { $last: "$orderDate" }
  }
}
```

Sort descending first for latest:

```js
{ $sort: { orderDate: -1 } }
```

---

# **9️⃣ Combining Operators — Complete Example**

### 🎯 Goal:

For each customer, show:

* Total orders
* Total amount
* Average order value
* Largest order
* Unique categories bought
* All order IDs

### Example Pipeline:

```js
db.orders.aggregate([
  {
    $group: {
      _id: "$customerId",
      totalOrders: { $sum: 1 },
      totalAmount: { $sum: "$amount" },
      avgAmount: { $avg: "$amount" },
      maxOrder: { $max: "$amount" },
      categories: { $addToSet: "$category" },
      orderIds: { $push: "$orderId" }
    }
  }
])
```

---

# **🔟 $project with Operators**

You can also use these operators in `$project`.

### ✔ Example: Convert price to annual revenue

```js
{
  $project: {
    product: 1,
    annualRevenue: { $multiply: ["$price", 12] }
  }
}
```

---

# **Examples of Combining $unwind + $group Operators**

If you have an array, use `$unwind` first.

### Example:

```json
{
  "name": "Alice",
  "scores": [10, 20, 30]
}
```

Pipeline:

```js
db.students.aggregate([
  { $unwind: "$scores" },
  {
    $group: {
      _id: "$name",
      total: { $sum: "$scores" },
      avgScore: { $avg: "$scores" },
      max: { $max: "$scores" }
    }
  }
])
```

---

# **🎯 Summary of Aggregation Operators**

| Operator      | Meaning                           | Use Case                      |
| ------------- | --------------------------------- | ----------------------------- |
| **$sum**      | Adds numbers                      | totals, counts                |
| **$avg**      | Average                           | ratings, performance          |
| **$min**      | Minimum                           | lowest price                  |
| **$max**      | Maximum                           | highest amount                |
| **$push**     | Add to array (duplicates allowed) | log lists                     |
| **$addToSet** | Unique array                      | categories, tags              |
| **$first**    | First value                       | earliest item (after sorting) |
| **$last**     | Last value                        | latest item (after sorting)   |

---

