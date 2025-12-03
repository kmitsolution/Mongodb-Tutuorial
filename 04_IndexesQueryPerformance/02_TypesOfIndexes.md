**Section 4.2 — Types of Indexes in MongoDB**, covering:

* **Single-field indexes**
* **Compound indexes**
* **Multikey indexes (for arrays)**
* **Text indexes**
* **TTL indexes (Time-To-Live)**
* **Geospatial indexes**

All explained clearly with examples and when to use each.

---

# **📘 4.2 Types of Indexes in MongoDB**

Indexes make queries faster. MongoDB supports several index types to optimize different query patterns.

---

# **1️⃣ Single-Field Indexes**

An index on **one field**.

### ✔ Syntax:

```js
db.collection.createIndex({ fieldName: 1 })
```

(1 = ascending, -1 = descending)

---

### ✔ Example:

```js
db.users.createIndex({ age: 1 })
```

### ✔ Use When:

* You frequently query one field
* Fields like `email`, `username`, `age`, `price`

---

### ✔ Query Example:

```js
db.users.find({ age: 25 })
```

This query becomes very fast with an index on `age`.

---

# **2️⃣ Compound Indexes (Multi-field Indexes)**

Index on **multiple fields together**.

### ✔ Syntax:

```js
db.collection.createIndex({ field1: 1, field2: -1 })
```

---

### ✔ Example:

Create index on `city` and `age`:

```js
db.users.createIndex({ city: 1, age: -1 })
```

---

### ✔ Use When:

* Query uses **multiple conditions**
* Example:

```js
db.users.find({ city: "Delhi", age: { $gt: 25 } })
```

* Common in sorting and range queries

---

### ⚠ Index Order Matters

Index `{ city: 1, age: -1 }` works for:

✔ Queries using `city`
✔ Queries using `city` + `age`
❌ But **NOT** queries using only `age`

---

# **3️⃣ Multikey Indexes (Array Fields)**

Used when the indexed field contains an **array**.

### When you index an array field → MongoDB creates a **multikey index**.

---

### ✔ Example:

```json
{
  "name": "Alice",
  "skills": ["MongoDB", "Node.js", "React"]
}
```

Index `skills`:

```js
db.users.createIndex({ skills: 1 })
```

Now queries become faster:

```js
db.users.find({ skills: "React" })
```

---

### ✔ Use When:

* Field is an array (skills, tags, items)
* You query individual array elements

---

### ⚠ Limitations:

❌ Cannot combine **two multikey fields** in one compound index
❌ Very large arrays may slow updates

---

# **4️⃣ Text Indexes**

Used for **text search** within string fields.

MongoDB provides **full-text search** using text index.

---

### ✔ Syntax:

```js
db.articles.createIndex({ content: "text" })
```

---

### Query text:

```js
db.articles.find({ $text: { $search: "mongodb indexing" } })
```

---

### ✔ Features:

* Search multiple words
* Supports stemming (e.g., "run" matches "running")
* Supports scores using `$meta`

---

### ✔ Multi-field text index:

```js
db.blogs.createIndex({ title: "text", body: "text" })
```

---

### ⚠ Limitations:

❌ Only **one** text index per collection
❌ Cannot combine text index with other index types in compound index (except special rules)

---

# **5️⃣ TTL Indexes (Time-To-Live)**

TTL indexes automatically **delete documents** after a specific time.

Used for:
✔ Logs
✔ Session tokens
✔ Cache
✔ Temporary data

---

### ✔ Syntax:

```js
db.sessions.createIndex(
    { createdAt: 1 },
    { expireAfterSeconds: 3600 }
)
```

This deletes documents **1 hour** after `createdAt`.

---

### ✔ Example Document:

```json
{
  "sessionId": "abc123",
  "createdAt": new Date()
}
```

After 1 hour → deleted automatically.

---

### ⚠ Limitations:

* Field must be a **Date** type
* TTL deletion may take up to ~60 seconds delay

---

# **6️⃣ Geospatial Indexes**

Used for **location-based** queries.

Types:

* 2D Index (older)
* 2dsphere Index (modern and preferred)

---

## **A. 2dsphere Index**

Supports real-world Earth geometry.
Used for:
✔ Finding nearest locations
✔ Tracking users
✔ Delivery apps
✔ Map services

---

### ✔ Syntax:

```js
db.places.createIndex({ location: "2dsphere" })
```

### Example document:

```json
{
  "name": "Coffee Shop",
  "location": { 
    "type": "Point",
    "coordinates": [77.5946, 12.9716]   // [longitude, latitude]
  }
}
```

---

### Query: Find places near a point

```js
db.places.find({
  location: {
    $near: {
      $geometry: {
        type: "Point",
        coordinates: [77.6, 12.97]
      },
      $maxDistance: 5000   // 5 km
    }
  }
})
```

---

## **B. 2D Legacy Index**

Used for flat 2D coordinate systems (older system).

---

# **Summary Table: Types of Indexes**

| Index Type   | When to Use           | Example                    |
| ------------ | --------------------- | -------------------------- |
| Single-field | Simple queries        | `{ age: 1 }`               |
| Compound     | Multi-field queries   | `{ city: 1, age: -1 }`     |
| Multikey     | Array field           | `{ skills: 1 }`            |
| Text         | Search inside text    | `{ content: "text" }`      |
| TTL          | Auto-delete documents | `expireAfterSeconds: 3600` |
| Geospatial   | Location queries      | `"2dsphere"`               |

---

# **✔ Quick Real-World Examples**

### 🌍 Geo apps:

* Swiggy, Zomato use **2dsphere indexes**
* Uber finds nearest driver using geospatial index

### 🔎 Search apps:

* Blog websites use **text indexes**

### 🚀 E-commerce:

* Products sorted by price using **single/compound indexes**

### 💬 Chat apps:

* Session tokens auto-delete using **TTL indexes**

---


