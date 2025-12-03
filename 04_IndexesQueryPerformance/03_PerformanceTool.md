 **Section 4.3 — Performance Tools**, covering:

* **explain() output (execution plans)**
* **Query Planner basics**
* **Covered indexes (Index-only queries)**

Everything is explained clearly with examples.

---

# **📘 4.3 Performance Tools**

MongoDB provides powerful tools to analyze **query performance**, detect **slow queries**, and improve indexing.

The most important tool is:

✔ **`explain()`**

It shows *how MongoDB executed your query*.

---

# **1️⃣ explain() Output**

You can append `.explain()` to any MongoDB query:

### ✔ Syntax

```js
db.collection.find({ field: value }).explain()
```

Or for more detail:

```js
db.collection.find({ field: value }).explain("executionStats")
```

---

# ✔ Modes of explain()

1. `"queryPlanner"` — shows which plan was selected
2. `"executionStats"` — shows execution time, number of documents scanned
3. `"allPlansExecution"` — shows stats of all possible plans

---

## **A. queryPlanner Output**

Example:

```js
db.users.find({ age: 25 }).explain("queryPlanner")
```

### Output (simplified):

```json
{
  "queryPlanner": {
    "plannerVersion": 1,
    "namespace": "test.users",
    "indexFilterSet": false,
    "winningPlan": {
      "stage": "IXSCAN",
      "keyPattern": { "age": 1 }
    },
    "rejectedPlans": []
  }
}
```

### Key Points:

* **winningPlan:** the plan MongoDB used
* **IXSCAN:** index scan (good, fast)
* **COLLSCAN:** collection scan (slow)
* **keyPattern:** the index used

---

## **B. executionStats Output**

Example:

```js
db.users.find({ age: 25 }).explain("executionStats")
```

### Output (simplified):

```json
{
  "executionStats": {
    "nReturned": 1,
    "totalKeysExamined": 1,
    "totalDocsExamined": 1
  }
}
```

---

# ✔ Key Metrics

| Field                   | Meaning                    |
| ----------------------- | -------------------------- |
| **nReturned**           | Number of documents output |
| **totalKeysExamined**   | Index entries scanned      |
| **totalDocsExamined**   | Documents scanned          |
| **executionTimeMillis** | Time taken                 |

---

# 🚀 Goal of Index Optimization

### Reduce:

* **totalDocsExamined** to as close to **0** as possible
* **totalKeysExamined** to minimal

If `totalDocsExamined` is huge → Index is NOT used → Slow query.

---

# ✔ Example: Slow Query (No Index)

```js
db.users.find({ age: 30 }).explain("executionStats")
```

Output:

```json
{
  "totalKeysExamined": 0,
  "totalDocsExamined": 100000
}
```

MongoDB scanned 100,000 documents → **bad performance**.

---

# ✔ Add Index

```js
db.users.createIndex({ age: 1 })
```

Run explain() again:

```json
{
  "totalKeysExamined": 1,
  "totalDocsExamined": 1
}
```

Huge improvement!

---

# **2️⃣ Query Planner Basics**

MongoDB’s **Query Planner** decides the best way to run a query.

It evaluates:

* Indexes available
* Query conditions
* Sort requirements
* Data distribution

Then chooses a **winning plan**.

---

## **A. Common Execution Stages**

### ✔ 1. COLLSCAN (Collection Scan)

* Scans entire collection
* Very slow
* Happens when **no index** is used

### ✔ 2. IXSCAN (Index Scan)

* Scans only the index
* Much faster
* Happens when query uses an index

### ✔ 3. FETCH

After index scan, MongoDB fetches real documents.

---

## **B. Example: Using Compound Index**

Index:

```js
db.users.createIndex({ city: 1, age: -1 })
```

Query:

```js
db.users.find({ city: "Delhi", age: { $gt: 25 } }).explain()
```

Winning Plan will show:

```json
"stage": "IXSCAN",
"keyPattern": { "city": 1, "age": -1 }
```

---

## **C. Query Planner Avoids Bad Plans (Rejected Plans)**

MongoDB may consider multiple plans but choose the fastest.

---

# **3️⃣ Covered Indexes (Index-only Queries)**

A **covered query** is one where:

* MongoDB reads ONLY from the index
* Does NOT need to fetch actual documents

### ✔ Covered queries are the fastest type of MongoDB queries

---

### ✔ Example Collection

```json
{ "name": "Alice", "city": "Delhi", "age": 25 }
```

### ✔ Create index on fields:

```js
db.users.createIndex({ name: 1, city: 1 })
```

---

# ✔ Covered Query Example

Query:

```js
db.users.find(
  { name: "Alice" },
  { name: 1, city: 1, _id: 0 }
)
```

### Why covered?

✔ Filter field in index (`name`)
✔ Projected fields also in index (`name`, `city`)
✔ `_id` excluded (not in index)

MongoDB will not read any documents — only index.

---

# 🔥 Benefits of Covered Queries

✔ Fastest possible query
✔ No document fetch
✔ Reduced I/O
✔ Lower CPU usage

---

# ❌ Not a Covered Query Example

```js
db.users.find(
  { name: "Alice" },
  { name: 1, city: 1, age: 1 }   // age not in index
)
```

`age` is not in index → MongoDB must fetch documents.

---

# **Requirements for a Covered Query**

✔ Index must include:

* Fields in **filter**
* Fields in **projection**

❌ No extra fields allowed
✔ `_id` must be excluded if not in index

---

# **4️⃣ Full Example: Covered Index + explain()**

### Step 1: Create Index

```js
db.products.createIndex({ category: 1, price: 1 })
```

### Step 2: Covered Query

```js
db.products.find(
  { category: "Mobile" },
  { category: 1, price: 1, _id: 0 }
).explain("executionStats")
```

### Output:

```json
{
  "totalKeysExamined": 50,
  "totalDocsExamined": 0
}
```

🔥 Perfect — no document scan.

---

# **🎯 Summary**

| Concept           | Meaning                                         |
| ----------------- | ----------------------------------------------- |
| **explain()**     | Shows how MongoDB executes a query              |
| **Query Planner** | Selects best plan using indexes                 |
| **IXSCAN**        | Fast index scan                                 |
| **COLLSCAN**      | Slow full collection scan                       |
| **Covered Query** | Uses only index, no document scan               |
| **Goal**          | Reduce totalDocsExamined & increase index usage |

---

