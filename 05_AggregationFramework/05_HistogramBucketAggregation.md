**Section 5.5 — $bucket and $bucketAuto**, which are powerful aggregation operators used for **Histogram creation**, **Data bucketing**, **Range grouping**, and **Analytics summarization**.

---

# **📘 5.5 — $bucket / $bucketAuto (Histogram & Bucket Aggregation)**

MongoDB provides two operators to create **histograms** and **range-based summaries**:

* **$bucket** → manual bucket boundaries
* **$bucketAuto** → automatic bucket boundaries

These are useful for:
✔ Analytics dashboards
✔ Data science
✔ Range grouping
✔ Creating intervals (age groups, salary slabs, price ranges)

---

# **1️⃣ $bucket — Manual Bucketing**

You define custom bucket boundaries.

---

## ✔ Syntax:

```js
{
  $bucket: {
    groupBy: <expression>,
    boundaries: [min, ..., max],
    default: "Other",    // optional
    output: { }          // aggregated fields
  }
}
```

---

## 📌 Example: Categorize users based on age

### Sample ages: 15, 22, 28, 35, 42, 55, etc.

### Pipeline:

```js
db.users.aggregate([
  {
    $bucket: {
      groupBy: "$age",
      boundaries: [0, 18, 30, 50, 100],
      default: "Unknown",
      output: {
        count: { $sum: 1 },
        averageAge: { $avg: "$age" }
      }
    }
  }
])
```

---

## ✔ Output:

```json
[
  { "_id": 0, "count": 1, "averageAge": 15 },
  { "_id": 18, "count": 2, "averageAge": 25 },
  { "_id": 30, "count": 2, "averageAge": 38.5 },
  { "_id": 50, "count": 1, "averageAge": 55 }
]
```

This creates age groups:

* 0–18
* 18–30
* 30–50
* 50–100

---

# **2️⃣ $bucketAuto — Automatic Bucketing**

MongoDB automatically chooses bucket boundaries based on data distribution.

### ✔ You only specify **number of buckets**.

---

## ✔ Syntax:

```js
{
  $bucketAuto: {
    groupBy: <expression>,
    buckets: <number>,
    output: { }     // optional
  }
}
```

---

## 📌 Example: Automatically divide product prices into 4 buckets

```js
db.products.aggregate([
  {
    $bucketAuto: {
      groupBy: "$price",
      buckets: 4,
      output: {
        count: { $sum: 1 },
        averagePrice: { $avg: "$price" }
      }
    }
  }
])
```

---

## ✔ Output Example:

```json
[
  { "min": 100, "max": 500, "count": 50, "averagePrice": 350 },
  { "min": 501, "max": 1500, "count": 40, "averagePrice": 960 },
  { "min": 1501, "max": 4000, "count": 25, "averagePrice": 2700 },
  { "min": 4001, "max": 9000, "count": 15, "averagePrice": 6000 }
]
```

MongoDB automatically splits into 4 ranges based on data distribution.

---

# **3️⃣ Difference Between $bucket and $bucketAuto**

| Feature     | $bucket                                  | $bucketAuto           |
| ----------- | ---------------------------------------- | --------------------- |
| Boundaries  | Manual                                   | Automatic             |
| When to use | Known ranges (age groups, grades, slabs) | Unknown ranges        |
| Control     | Total control                            | Limited control       |
| Output      | Customizable                             | Customizable          |
| Ease        | Harder                                   | Easier                |
| Best for    | Reporting dashboards                     | Exploratory analytics |

---

# **4️⃣ Real-World Examples**

---

## ✔ Example 1: Income distribution (manual buckets)

```js
db.employees.aggregate([
  {
    $bucket: {
      groupBy: "$salary",
      boundaries: [0, 20000, 40000, 60000, 100000],
      default: "Others",
      output: {
        count: { $sum: 1 }
      }
    }
  }
])
```

---

## ✔ Example 2: Auto-generate 5 age groups

```js
db.users.aggregate([
  {
    $bucketAuto: {
      groupBy: "$age",
      buckets: 5
    }
  }
])
```

---

## ✔ Example 3: Product price histogram with count + avg rating

```js
db.products.aggregate([
  {
    $bucketAuto: {
      groupBy: "$price",
      buckets: 3,
      output: {
        productCount: { $sum: 1 },
        avgRating: { $avg: "$rating" }
      }
    }
  }
])
```

---

## ✔ Example 4: Customer spending categories

```js
db.orders.aggregate([
  {
    $bucket: {
      groupBy: "$amount",
      boundaries: [0, 500, 2000, 5000, 10000],
      output: {
        count: { $sum: 1 },
        totalAmount: { $sum: "$amount" }
      }
    }
  }
])
```

---

# **5️⃣ Combined Pipeline Example (Real Dashboard)**

### 🎯 Goal:

Group product prices into 3 auto buckets
AND also show category-wise count
AND month-wise summary (all in one query)

Using `$facet` + `$bucketAuto` + `$bucket`:

```js
db.products.aggregate([
  {
    $facet: {
      priceHistogram: [
        { 
          $bucketAuto: {
            groupBy: "$price",
            buckets: 3,
            output: {
              count: { $sum: 1 },
              avgPrice: { $avg: "$price" }
            }
          }
        }
      ],
      categoryCounts: [
        { $group: { _id: "$category", count: { $sum: 1 } } }
      ],
      monthlySales: [
        { 
          $bucket: {
            groupBy: "$createdAt",
            boundaries: [
              ISODate("2024-01-01"),
              ISODate("2024-05-01"),
              ISODate("2024-09-01"),
              ISODate("2025-01-01")
            ],
            output: { total: { $sum: "$price" } }
          }
        }
      ]
    }
  }
])
```

---

# **6️⃣ Summary**

| Operator        | Purpose                         | Best Use Case                                |
| --------------- | ------------------------------- | -------------------------------------------- |
| **$bucket**     | Manual bucket ranges            | Age groups, salary slabs, grade distribution |
| **$bucketAuto** | Automatic buckets based on data | Exploratory analytics, histograms            |
| Boundaries      | Manual                          | Automatic                                    |
| Output fields   | Customizable                    | Customizable                                 |
| Best for        | Dashboards                      | Statistics                                   |

---

