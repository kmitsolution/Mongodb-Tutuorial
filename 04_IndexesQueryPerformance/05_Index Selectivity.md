 **Section 4.5 — Index Selectivity**, one of the **most important concepts** in MongoDB indexing.
It helps you decide **which fields to index**, and **why some indexes improve performance while others don’t**.

---

# **📘 4.5 Index Selectivity**

**Selectivity** refers to **how many unique values** a field has relative to the total number of documents.

Selectivity directly affects whether an index will:

✔ Speed up queries
❌ OR become useless

High-selectivity fields → **Good indexes**
Low-selectivity fields → **Bad & wasteful indexes**

---

# **1️⃣ What is Selectivity?**

Selectivity =
**(# of unique values in a field) / (total number of documents)**

➡️ More unique values → **higher selectivity**
➡️ Fewer unique values → **lower selectivity**

---

# **2️⃣ Why Selectivity Matters?**

MongoDB uses indexes to reduce the number of scanned documents.

✔ High-selectivity index = fewer documents match → FAST
❌ Low-selectivity index = too many documents match → SLOW

---

# **3️⃣ High vs Low Selectivity Examples**

---

## **A. High Selectivity Fields (Good for Indexing)**

Examples:

* `email` (unique per user)
* `username`
* `_id`
* `phoneNumber`
* `productId`
* `orderId`
* `studentRollNo`
* `invoiceNumber`

These fields uniquely identify a document.

### ✔ Best choice for indexes:

```js
db.users.createIndex({ email: 1 })
```

---

## **B. Medium Selectivity Fields (Conditional)**

Examples:

* `age` (20–80 range)
* `price`
* `date`
* `rating`

May or may not be useful depending on data distribution.

---

## **C. Low Selectivity Fields (Bad Indexes)**

Examples:

* `gender` → only M/F
* `status` → active/inactive
* `country` → only 5–10 options
* `category` → few values
* `isVerified` → true/false

If many documents share the same value, index is useless.

### ❌ Example of poor index:

```js
db.users.createIndex({ gender: 1 })
```

### Why bad?

Most documents will match `gender: "M"` → index gives no advantage.

---

# **4️⃣ Index Selectivity in Numbers (Example)**

Assume collection has **100,000 documents**.

---

## ✔ High-selectivity field:

email → unique
100,000 unique values

Selectivity = 100,000 / 100,000 = 1 (perfect)

MongoDB uses the index efficiently.

---

## ✔ Low-selectivity field:

gender → only 2 possible values (M/F)

Selectivity = 2 / 100,000 = **0.00002** (very low)

MongoDB will ignore this index in many queries because:

* Too many rows match
* Using index is slower than full scan

---

# **5️⃣ How MongoDB Determines if Index Should Be Used**

MongoDB checks:

* Selectivity
* Query shape
* Index order
* Query filters

If index will NOT reduce the number of documents significantly → MongoDB **skips it**.

---

# **6️⃣ Impact on Compound Indexes**

### Example index:

```js
{ city: 1, age: 1 }
```

If `city` has low selectivity (like `"Delhi"` appears in 80% of documents) →
MongoDB may skip this index unless query also includes **age** (higher selectivity).

---

# **7️⃣ How to Evaluate Selectivity Using explain()**

You can see if MongoDB used an index by checking:

### Key metrics:

* `totalDocsExamined`
* `totalKeysExamined`
* `stage: "IXSCAN"` vs `"COLLSCAN"`

Example:

```js
db.users.find({ city: "Delhi" }).explain("executionStats")
```

If output shows:

```
totalDocsExamined: 50000
```

→ Index was not selective enough.

---

# **8️⃣ Best Practices for Selectivity**

### ✔ 1. Always index high-selectivity fields

(Unique identifiers, emails, ids)

### ✔ 2. Avoid indexing low-selectivity fields

(true/false, yes/no, categories)

### ✔ 3. For compound indexes:

Place **high-selectivity fields first**.

Example:

```js
{ email: 1, status: 1 }
```

NOT:

```js
{ status: 1, email: 1 }
```

### ✔ 4. Use explain() to verify index usage

### ✔ 5. Monitor index selectivity using Compass or Atlas Performance Advisor

---

# **9️⃣ Real-World Examples**

---

## ✔ Example 1: E-commerce

### Indexing `category` (bad):

```js
db.products.createIndex({ category: 1 })
```

If 60% products are "Electronics", index is useless.

### Better index:

```js
db.products.createIndex({ category: 1, price: 1 })
```

Price has high selectivity → solves problem.

---

## ✔ Example 2: User search

Field: `city`
If 40% users are from "Mumbai", this index may not help.

Better:

```js
db.users.createIndex({ city: 1, age: 1 })
```

`age` increases selectivity.

---

## ✔ Example 3: Logs

Field: `status: "success" / "fail"`
Too low selectivity → no index.

Better:

```js
db.logs.createIndex({ timestamp: 1 })
```

Time fields have high selectivity → good index.

---

# **🔟 Summary Table**

| Index Type        | Selectivity | Good/Bad    |
| ----------------- | ----------- | ----------- |
| email, id, phone  | High        | ✔ Good      |
| category, country | Low-Medium  | ✔ Sometimes |
| gender, status    | Very Low    | ❌ Bad       |
| boolean fields    | Very Low    | ❌ Bad       |
| time/date fields  | High        | ✔ Good      |

---

# **🎯 Final Summary**

* Selectivity = uniqueness of values
* High selectivity → more efficient index
* Low selectivity → index useless
* Compound index leftmost field should be selective
* Use `explain()` to evaluate selectivity

---
