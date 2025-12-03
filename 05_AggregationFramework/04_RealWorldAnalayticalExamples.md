 **Section 5.4 — Real-World Aggregation Examples**, with practical use cases for analytics, reporting, dashboards, and e-commerce scenarios.

These examples combine all major stages:

* `$match`
* `$group`
* `$project`
* `$sort`
* `$limit`
* `$lookup`
* `$unwind`
* `$facet`

---

# **📘 5.4 Real-World Aggregation Examples**

These examples simulate real business problems and show how MongoDB’s aggregation pipeline can solve them.

---

# **1️⃣ E-Commerce Analytics**

## **Example 1: Total sales by category**

```js
db.orders.aggregate([
  {
    $group: {
      _id: "$category",
      totalSales: { $sum: "$amount" }
    }
  },
  { $sort: { totalSales: -1 } }
])
```

---

## **Example 2: Top 5 most sold products**

```js
db.orders.aggregate([
  { $group: {
      _id: "$productId",
      totalSold: { $sum: "$quantity" }
    }
  },
  { $sort: { totalSold: -1 } },
  { $limit: 5 }
])
```

---

## **Example 3: Daily revenue report**

```js
db.orders.aggregate([
  {
    $group: {
      _id: { $dateToString: { format: "%Y-%m-%d", date: "$orderDate" } },
      dailyRevenue: { $sum: "$amount" }
    }
  },
  { $sort: { "_id": 1 } }
])
```

---

# **2️⃣ User & Customer Analytics**

## **Example 4: Count users by city**

```js
db.users.aggregate([
  {
    $group: {
      _id: "$city",
      count: { $sum: 1 }
    }
  }
])
```

---

## **Example 5: Average age of users by city**

```js
db.users.aggregate([
  { $group: {
      _id: "$city",
      avgAge: { $avg: "$age" }
    }
  },
  { $sort: { avgAge: 1 } }
])
```

---

## **Example 6: Most active users (max orders)**

```js
db.orders.aggregate([
  { $group: {
      _id: "$userId",
      totalOrders: { $sum: 1 }
    }
  },
  { $sort: { totalOrders: -1 } },
  { $limit: 10 }
])
```

---

# **3️⃣ Inventory & Product Reporting**

## **Example 7: Low stock products**

```js
db.products.aggregate([
  { $match: { stock: { $lt: 10 } } },
  { $sort: { stock: 1 } }
])
```

---

## **Example 8: Average product rating**

```js
db.reviews.aggregate([
  {
    $group: {
      _id: "$productId",
      avgRating: { $avg: "$rating" }
    }
  },
  { $sort: { avgRating: -1 } }
])
```

---

# **4️⃣ Using $lookup — Joining Collections**

## **Example 9: List users with their orders**

```js
db.users.aggregate([
  {
    $lookup: {
      from: "orders",
      localField: "_id",
      foreignField: "userId",
      as: "orders"
    }
  }
])
```

---

## **Example 10: Show each order with product details**

```js
db.orders.aggregate([
  {
    $lookup: {
      from: "products",
      localField: "productId",
      foreignField: "_id",
      as: "productDetails"
    }
  }
])
```

---

# **5️⃣ Using $unwind — Expand arrays**

## **Example 11: Count how many times each tag appears**

```js
db.posts.aggregate([
  { $unwind: "$tags" },
  { $group: {
      _id: "$tags",
      count: { $sum: 1 }
    }
  },
  { $sort: { count: -1 } }
])
```

---

## **Example 12: Total quantity sold for each item in order items array**

```js
db.orders.aggregate([
  { $unwind: "$items" },
  { $group: {
      _id: "$items.productId",
      totalQty: { $sum: "$items.quantity" }
    }
  }
])
```

---

# **6️⃣ Using $facet — Multi-Report Dashboard**

## **Example 13: Build dashboard with multiple outputs**

```js
db.orders.aggregate([
  {
    $facet: {
      topCustomers: [
        { $group: { _id: "$userId", totalSpent: { $sum: "$amount" } } },
        { $sort: { totalSpent: -1 } },
        { $limit: 5 }
      ],
      salesByCategory: [
        { $group: { _id: "$category", total: { $sum: "$amount" } } }
      ],
      dailySales: [
        {
          $group: {
            _id: { $dateToString: { format: "%Y-%m-%d", date: "$orderDate" } },
            total: { $sum: "$amount" }
          }
        },
        { $sort: { "_id": 1 } }
      ]
    }
  }
])
```

✔ Returns **3 reports at the same time**, ideal for dashboards.

---

# **7️⃣ Complex Pipeline Example (Full Business Use-Case)**

### 🎯 Goal:

For each user, find:

* Total orders
* Total amount spent
* Latest order date
* Unique categories purchased
* Top 3 most expensive orders

---

### ✔ Pipeline:

```js
db.orders.aggregate([
  // Group by user
  {
    $group: {
      _id: "$userId",
      totalOrders: { $sum: 1 },
      totalSpent: { $sum: "$amount" },
      categories: { $addToSet: "$category" },
      maxAmounts: { $push: "$amount" },
      lastOrder: { $max: "$orderDate" }
    }
  },

  // Sort orders (take top 3)
  {
    $project: {
      totalOrders: 1,
      totalSpent: 1,
      categories: 1,
      lastOrder: 1,
      top3Orders: { $slice: [ { $sortArray: "$maxAmounts" }, -3 ] }
    }
  }
])
```

---

# **8️⃣ Social Media Analytics Example**

## **Example 14: Most liked posts**

```js
db.posts.aggregate([
  { $sort: { likes: -1 } },
  { $limit: 10 }
])
```

---

## **Example 15: Count comments per user**

```js
db.comments.aggregate([
  { $group: {
      _id: "$userId",
      totalComments: { $sum: 1 }
    }
  },
  { $sort: { totalComments: -1 } }
])
```

---

# **🎯 Summary of What You Learned**

Real-world examples using:

* `$match` (filter)
* `$group` (totals, averages, counts)
* `$project` (reshape output)
* `$sort` + `$limit` (ranking reports)
* `$lookup` (joins)
* `$unwind` (flatten arrays)
* `$facet` (multi-output dashboards)

You now know how to use aggregation to build dashboards, analytics, and business intelligence queries.

---

Just tell me!
