# **📘 3.4 Schema Design Best Practices (MongoDB)**

MongoDB schema design is very different from SQL.
In SQL: you normalize.
In MongoDB: you model based on **how the application uses the data**.

MongoDB schema design focuses on:

✔ Read/Write patterns
✔ Data size
✔ Relationship types
✔ Performance
✔ Scalability (sharding)

Below are the **top best practices** every developer should follow.

---

# **1️⃣ Design Based on Query Patterns (Very Important)**

MongoDB schemas should be created based on:

* What data is read together?
* What fields are frequently updated?
* What queries will be used?

### ✔ Example:

If the app frequently loads:

* user info
* latest orders

Then embed recent orders in the user document:

```json
{
  "name": "John",
  "recentOrders": [
    { "orderId": 101, "amount": 2000 },
    { "orderId": 102, "amount": 1500 }
  ]
}
```

MongoDB is optimized for **read-mostly applications**.

---

# **2️⃣ Prefer Embedding for One-to-Few Relationships**

Use embedding when:

✔ Data is small
✔ Accessed together
✔ Rarely updated independently

### Example:

Embed address in user:

```json
{
  "name": "Alice",
  "address": {
    "city": "Delhi",
    "pin": 110001
  }
}
```

---

# **3️⃣ Use Referencing for Large / Growing Relationships**

If related data grows a lot → **reference** instead of embedding.

### Example:

User → Orders (may reach thousands)

```json
{
  "_id": 1,
  "name": "Alice"
}
```

```json
{
  "orderId": 501,
  "userId": 1,
  "amount": 3500
}
```

Using `$lookup` during aggregation is fine for large data.

---

# **4️⃣ Avoid Large Documents (16 MB Limit)**

MongoDB documents have a maximum size of **16 MB**.

➡️ Don't embed huge arrays
➡️ Don’t store images/videos directly inside documents
➡️ Use GridFS for large files

### ❌ Bad design:

```json
{
  "_id": 1,
  "logs": [ ... millions of items ... ]
}
```

### ✔ Good design:

Store logs in separate collection and reference:

```json
{ "userId": 1, "log": "..." }
```

---

# **5️⃣ Avoid Deep Nesting (Max 2–3 Levels)**

Deep nesting slows down queries.

### ❌ Too deep:

```json
{
  "a": {
    "b": {
      "c": {
        "d": {
          "e": "value"
        }
      }
    }
  }
}
```

### ✔ Recommended:

Flatten structures when possible.

```json
{
  "level1": "value",
  "metadata": { "key": "value" }
}
```

---

# **6️⃣ Use Meaningful Field Names (But Not Too Long)**

Avoid long names because they increase document size.

### ❌ Bad:

```json
{
  "user_address_information_city_name": "Mumbai"
}
```

### ✔ Good:

```json
{
  "address": { "city": "Mumbai" }
}
```

---

# **7️⃣ Index Fields That You Query Frequently**

Indexes improve lookup performance.

### Example:

Querying users by email?

```js
db.users.createIndex({ email: 1 })
```

---

# **8️⃣ Avoid Unbounded Arrays**

Arrays that grow without limits cause:

* Large document size
* High update cost
* Risk of hitting 16 MB document limit

### ❌ Bad:

```json
{
  "_id": 1,
  "messages": [ ... unlimited messages ... ]
}
```

### ✔ Good:

Store messages in separate collection:

```json
{ "userId": 1, "message": "Hello!" }
```

---

# **9️⃣ Use Schema Validation (Optional)**

To enforce rules (similar to SQL constraints):

```js
db.createCollection("users", {
  validator: {
    $jsonSchema: {
      bsonType: "object",
      required: ["name", "email"],
      properties: {
        email: { bsonType: "string" },
        age: { bsonType: "int" }
      }
    }
  }
})
```

---

# **🔟 Use the Right Data Types**

Always store fields using correct types:

* Date → `ISODate()`
* Price → `Decimal128`
* ID → `ObjectId`
* Flags → Boolean
* Arrays for lists

### ❌ Bad:

```json
{ "price": "2500" }
```

### ✔ Good:

```json
{ "price": Decimal128("2500") }
```

---

# **1️⃣1️⃣ Use Bucketing for Time-Series Data**

If storing logs, events, analytics → use **bucketing**.

### Example:

Daily bucket:

```json
{
  "date": "2024-02-01",
  "logs": [
    { "time": "10:00", "event": "login" },
    { "time": "10:05", "event": "view" }
  ]
}
```

Better than storing each log as separate large document.

---

# **1️⃣2️⃣ Separate Hot and Cold Data**

Hot data = frequently accessed
Cold data = rarely accessed

### Example:

```json
{
  "userId": 1,
  "name": "Tom",
  "recentActivities": [...],
  "archivedActivitiesRef": ["arch101", "arch102"]
}
```

---

# **1️⃣3️⃣ Use Appropriate Relationship Type**

✔ 1:1 → Embed
✔ 1:Many → Embed or Reference based on size
✔ Many:Many → Always reference

---

# **1️⃣4️⃣ Monitor Schema Using Compass / Schema Analyzer**

Use **MongoDB Compass** to analyze:

* Field types
* Data distribution
* Array sizes
* Common queries

This helps redesign or optimize schema later.

---

# **1️⃣5️⃣ Avoid Storing Redundant Data Unless Needed for Read Speed**

Denormalization is useful BUT…

Only duplicate fields when absolutely necessary.

Example:
Store customer name inside order for fast reporting:

```json
{
  "orderId": 101,
  "customerName": "Alice"
}
```

✔ Faster reads
❌ Need to update many documents if customer changes name

---

# **🎯 Summary of Schema Best Practices**

| Best Practice                     | Benefit                   |
| --------------------------------- | ------------------------- |
| Model based on queries            | High performance          |
| Embed for small related data      | Faster reads              |
| Reference for large relationships | Smaller docs              |
| Avoid deep nesting                | Easier and faster queries |
| Limit array sizes                 | Avoid 16MB limit          |
| Use indexes                       | Fast search               |
| Choose correct data types         | Efficient storage         |
| Avoid redundancy                  | Easier updates            |
| Use schema validation             | Data consistency          |
| Use hybrid approach               | Balanced design           |

---

