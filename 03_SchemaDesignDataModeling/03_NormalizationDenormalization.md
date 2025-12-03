 **Section 3.3 — Normalization vs Denormalization**, fully explained with examples, pros/cons, and when to use each in MongoDB.

---

# **📘 3.3 Normalization vs Denormalization (MongoDB Data Modeling)**

MongoDB is a **NoSQL document database**, so its schema design is more flexible than traditional SQL.

To design good MongoDB schemas, you must understand the two important approaches:

1️⃣ **Normalization**
2️⃣ **Denormalization**

Both approaches affect performance, storage, and ease of updates.

---

# **1️⃣ What is Normalization? (SQL Style)**

**Normalization = Splitting data into separate collections to avoid duplication.**

This is how relational databases (RDBMS) work.

### ✔ Characteristics:

* Data stored in multiple tables/collections
* Linked using foreign keys or references
* Reduces duplication
* Ensures consistency

---

## ✔ Example of Normalized Design (Referencing)

### **Users Collection**

```json
{
  "_id": 101,
  "name": "Alice",
  "email": "alice@example.com"
}
```

### **Orders Collection**

```json
{
  "_id": 501,
  "userId": 101,
  "total": 1500
}
```

Here **user data is stored only once**.
Orders refer to users using `userId`.

---

# **✔ Pros of Normalization**

| Advantage                            | Why?                                   |
| ------------------------------------ | -------------------------------------- |
| No data duplication                  | Each record stored once                |
| Consistent data                      | Update in one place updates everywhere |
| Smaller document size                | No repeated fields                     |
| Good for large/complex relationships | Especially many-to-many                |

---

# **❌ Cons of Normalization**

| Disadvantage                              | Why?                            |
| ----------------------------------------- | ------------------------------- |
| Requires JOIN-like operations (`$lookup`) | Slower queries                  |
| More round trips                          | Need multiple queries sometimes |
| Complex schema                            | Harder for beginners            |

---

# **2️⃣ What is Denormalization? (NoSQL Style)**

**Denormalization = Embedding related data inside a single document.**

This is common in MongoDB.

### ✔ Characteristics:

* Store related data together
* One document has everything needed
* Faster reads
* Some duplication allowed
* Larger document size

---

## ✔ Example of Denormalized Design (Embedding)

### **Order Document**

```json
{
  "_id": 501,
  "customer": {
    "name": "Alice",
    "email": "alice@example.com"
  },
  "items": [
    { "product": "Shirt", "qty": 2 },
    { "product": "Shoes", "qty": 1 }
  ],
  "total": 1500
}
```

Everything is embedded inside the order document.

No need for `$lookup`, no JOINs.

---

# **✔ Pros of Denormalization**

| Advantage                 | Why?                           |
| ------------------------- | ------------------------------ |
| Faster reads              | No JOIN or extra lookup        |
| Fewer queries             | Everything inside one document |
| Easy and simple structure | Natural JSON style             |
| Best for real-time apps   | Quick access                   |

---

# **❌ Cons of Denormalization**

| Disadvantage                               | Why?                             |
| ------------------------------------------ | -------------------------------- |
| Data duplication                           | Same data may appear many times  |
| Inconsistent updates possible              | Need to update all copies        |
| Bigger documents                           | Can approach 16 MB limit         |
| Poor choice for frequently changing fields | Harder to update embedded copies |

---

# **3️⃣ When to Use Normalization (Referencing)**

Use normalization when:

### ✔ 1. Data is huge

Example:

* Millions of comments
* Large logs
* Many relationships

### ✔ 2. Data updated frequently

You don’t want to update multiple embedded copies.

### ✔ 3. Many-to-many relationships

Example: students ↔ courses
Embedding is NOT practical.

### ✔ 4. Avoid document size growth

Large embedded arrays can hit the 16 MB limit.

---

# **4️⃣ When to Use Denormalization (Embedding)**

Use embedding when:

### ✔ 1. Data is small and fits together

Example: product → specs
Example: user → address

### ✔ 2. Read performance is critical

Apps that require **fast** and **simple** data retrieval.

### ✔ 3. Data is accessed together frequently

Example:
When showing an order, we always need items.

### ✔ 4. “1-to-few” relationship

Example: few comments, few tags, few addresses.

---

# **5️⃣ Practical Example (Normalized vs Denormalized)**

---

## **Scenario:** An e-commerce app

### **Normalized design:**

### products collection:

```json
{ "_id": 201, "name": "Laptop" }
```

### reviews collection:

```json
{ "_id": 401, "productId": 201, "rating": 5 }
```

#### ✔ Good when:

* Thousands of reviews
* Reviews updated independently

---

### **Denormalized design:**

### product document:

```json
{
  "_id": 201,
  "name": "Laptop",
  "reviews": [
    { "rating": 5 },
    { "rating": 4 }
  ]
}
```

#### ✔ Good when:

* Few reviews
* Read speed important
* Product + reviews accessed together

---

# **6️⃣ A Combined Approach (Hybrid)**

MongoDB often uses **both** methods together.

Example:

* Embed small and frequently accessed fields
* Reference large or rarely accessed fields

---

### ✔ Hybrid Example:

```json
{
  "_id": 1,
  "name": "John",
  "address": { "city": "Delhi", "zip": 110001 },
  "orders": [
    { "orderId": 101, "amount": 2000 }
  ],
  "orderHistoryRef": ["orderId_201", "orderId_202"]
}
```

Best of both worlds.

---

# **7️⃣ Summary Table**

| Feature           | Normalization      | Denormalization         |
| ----------------- | ------------------ | ----------------------- |
| Storage           | Small              | Larger                  |
| Read Performance  | Slower             | Faster                  |
| Write Performance | Faster             | Slower (if many copies) |
| Use When          | Large data, M:N    | 1-to-few, fast reads    |
| JOINs             | Required ($lookup) | Not needed              |
| Data Duplication  | Low                | High                    |

---

# **8️⃣ Rule of Thumb for MongoDB Schema**

✔ **Embed for one-to-few, read-heavy, small data**
✔ **Reference for large, growing, write-heavy data**
✔ **Normalize only where necessary**
✔ **Design based on query patterns**
✔ **Avoid deep nesting (maximum 2–3 levels)**
✔ **Document size < 16 MB**

---

