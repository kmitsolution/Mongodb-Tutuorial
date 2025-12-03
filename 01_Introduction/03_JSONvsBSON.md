✅ What is a **Document**
✅ What is **JSON**
✅ What is **BSON**
✅ What is the **_id field**
✅ MongoDB **data types**
✅ Comparison with **RDBMS (SQL databases)**

Everything is explained in a simple and example-based format.

---

# **📘 1.3 — Document, JSON, BSON, _id, Data Types & RDBMS Comparison**

---

# **1️⃣ What is a Document in MongoDB?**

A **document** is the basic unit of data in MongoDB.
It is similar to a **JSON object** and contains **key–value pairs**.

### ✔ Example of a Document:

```json
{
  "name": "John",
  "age": 30,
  "skills": ["MongoDB", "Node.js"],
  "address": {
    "city": "New York",
    "zip": "10001"
  }
}
```

Document = ✔ flexible, ✔ nested, ✔ schema-less.

* Values can be arrays
* Values can be nested objects
* Documents in the same collection can have different fields

---

# **2️⃣ What is JSON?**

**JSON** = JavaScript Object Notation

* Text-based format
* Human-readable
* Used for APIs, configs, and data sharing

### ✔ JSON Example:

```json
{ "name": "Laptop", "price": 899 }
```

JSON is great for readability but has limitations:
❌ No proper date data type
❌ No binary data
❌ Limited numeric precision

---

# **3️⃣ What is BSON? (Binary JSON)**

MongoDB stores data internally in **BSON**.

**BSON Advantages:**

* Binary format → faster
* Supports more data types than JSON
* Allows efficient indexing
* Optimized for MongoDB’s engine

### ✔ Extra data types supported by BSON:

* `Date`
* `ObjectId`
* `BinData`
* `Decimal128`
* `Int32`, `Int64`

### ✔ BSON Example (conceptual):

```json
{
  "name": "Laptop",
  "price": Decimal128("899.50"),
  "createdAt": ISODate("2025-01-01T10:00:00Z")
}
```

JSON cannot store `Date` or `Decimal128`, but BSON can.

---

# **4️⃣ The _id Field in MongoDB**

Every document automatically gets an **_id** field.

### ✔ Purpose of _id:

* Primary key of the document
* Must be **unique** inside a collection
* Automatically generated if not provided

### ✔ Default _id value:

MongoDB generates an **ObjectId**.

### ✔ Example ObjectId:

```
507f1f77bcf86cd799439011
```

### ObjectId contains:

* Timestamp
* Machine identifier
* Process ID
* Random counter

You can also manually set `_id`, like:

```json
{
  "_id": "user123",
  "name": "Alice"
}
```

---

# **5️⃣ MongoDB Data Types**

MongoDB supports many data types.
Below are the **most important** ones.

### ✔ Primitive Data Types

| Type         | Example               |
| ------------ | --------------------- |
| `String`     | `"Hello"`             |
| `Number`     | `42`                  |
| `Double`     | `42.6`                |
| `Decimal128` | `Decimal128("89.99")` |
| `Boolean`    | `true`                |
| `Null`       | `null`                |

### ✔ Object Types

| Type       | Example                 |
| ---------- | ----------------------- |
| `Object`   | `{ "city": "Delhi" }`   |
| `Array`    | `[1,2,3]`               |
| `ObjectId` | `ObjectId("507f1f...")` |
| `Date`     | `ISODate("2024-01-01")` |

### ✔ Binary / Special Types

| Type        | Use                      |
| ----------- | ------------------------ |
| `BinData`   | Files / encrypted data   |
| `Regex`     | Pattern matching         |
| `Timestamp` | Internal replication use |

---

# **6️⃣ Comparing MongoDB (NoSQL) vs RDBMS (SQL)**

Here is a clear comparison of how MongoDB differs from SQL databases like MySQL or PostgreSQL.

---

## 🔷 **A. Data Storage Model**

| RDBMS (SQL)                                   | MongoDB (NoSQL)                   |
| --------------------------------------------- | --------------------------------- |
| Tables with rows & columns                    | Collections with documents        |
| Fixed schema                                  | Schema-less                       |
| Normalized data (splits into multiple tables) | Embedded documents (denormalized) |

---

## 🔷 **B. Example: User with multiple addresses**

### **SQL Version (two tables)**

**Users Table**

```
id | name
1  | John
```

**Addresses Table**

```
id | user_id | city | zip
1  | 1       | NY   | 10001
2  | 1       | LA   | 90001
```

To get user + addresses → Requires **JOIN**.

---

### **MongoDB Version (one document)**

```json
{
  "_id": 1,
  "name": "John",
  "addresses": [
    { "city": "NY", "zip": "10001" },
    { "city": "LA", "zip": "90001" }
  ]
}
```

No JOIN needed because data is **embedded**.

---

## 🔷 **C. Flexibility**

| SQL                          | MongoDB                             |
| ---------------------------- | ----------------------------------- |
| Schema must be defined first | Documents can have different fields |
| Changing schema is costly    | Easy to add/remove fields anytime   |

---

## 🔷 **D. Transaction Model**

| SQL                       | MongoDB                                             |
| ------------------------- | --------------------------------------------------- |
| Strong ACID transactions  | Supports ACID, but usually used for flexible writes |
| Ideal for banking/finance | Ideal for flexible apps, big data                   |

---

## 🔷 **E. Scalability**

| SQL                              | MongoDB                                 |
| -------------------------------- | --------------------------------------- |
| Vertical scaling (bigger server) | Horizontal scaling (sharding)           |
| Hard to distribute               | Easy to distribute across many machines |

---

## 🔷 **F. Query Language**

| SQL      | MongoDB                |
| -------- | ---------------------- |
| Uses SQL | Uses JSON-like queries |
| JOINs    | Aggregation Pipeline   |

### MongoDB Query Example:

```js
db.users.find({ age: { $gt: 25 } })
```

---

# ✔ Quick Summary

| Concept            | Meaning                                                    |
| ------------------ | ---------------------------------------------------------- |
| **Document**       | JSON-like data stored in MongoDB                           |
| **JSON**           | Text-based, readable format                                |
| **BSON**           | Binary JSON with extra types & better performance          |
| **_id**            | Unique identifier for each document                        |
| **Data Types**     | String, Number, Date, Array, ObjectId, etc.                |
| **SQL vs MongoDB** | Tables vs Documents, JOINs vs Embedding, Fixed vs Flexible |

---


