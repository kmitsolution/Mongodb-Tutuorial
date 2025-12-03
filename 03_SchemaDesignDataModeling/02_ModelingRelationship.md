**Section 3.2 — Modeling Relationships**, covering:

* **1:1 (One-to-One)**
* **1:Many (One-to-Many)**
* **Many:Many**
* When to **Embed** and when to **Reference**
* Real-world examples for each
* Best practices

---

# **📘 3.2 Modeling Relationships in MongoDB**

MongoDB is a **document database**, so relationships are handled differently compared to SQL.
You can represent relationships using:

✔ **Embedding** (put data inside the document)
✔ **Referencing** (store `_id` of related document)

Selecting the right relationship model improves performance and scalability.

---

# **1️⃣ One-to-One (1:1 Relationship)**

One record relates to exactly one other record.

### ✔ Example: User ↔ Profile

One user has one profile.

---

## **Option A: Embedding (Recommended for small data)**

### Example:

```json
{
  "_id": 101,
  "name": "Alice",
  "profile": {
    "bio": "Developer",
    "website": "alice.dev"
  }
}
```

### ✔ When to embed:

* Data size small
* Always accessed together
* Rarely updated independently

---

## **Option B: Referencing (If data is large)**

### users collection:

```json
{
  "_id": 101,
  "name": "Alice",
  "profileId": 5001
}
```

### profiles collection:

```json
{
  "_id": 5001,
  "bio": "Developer",
  "website": "alice.dev"
}

```
```
db.users.aggregate([
  {
    $lookup: {
      from: "profiles",
      localField: "profileId",
      foreignField: "_id",
      as: "profile"
    }
  },
  { $unwind: "$profile" },
  {
    $project: {
      _id: 0,
      name: 1,
      bio: "$profile.bio"
    }
  }
])
```
### ✔ When to reference:

* Data is large
* Sensitive (stored separately)
* Accessed infrequently

---

# **2️⃣ One-to-Many (1:N Relationship)**

One record relates to **multiple** records.

### ✔ Example:

Customer → many orders
User → many posts
Product → many reviews

---

## **Option A: Embedding (for few items)**

### Example: Blog post with comments

```json
{
  "_id": 1,
  "title": "MongoDB Basics",
  "comments": [
    { "user": "John", "text": "Nice post", "likes": 5 },
    { "user": "Sara", "text": "Very useful", "likes": 3 }
  ]
}
```

### ✔ Good when:

* Number of child items is **small**
* Data is mostly read together
* Comments < 50
* Access pattern requires joining often

---

## **Option B: Referencing (for many items)**

### posts collection:

```json
{
  "_id": 1,
  "title": "MongoDB Basics"
}
```

### comments collection:

```json
{ "_id": 201, "postId": 1, "text": "Nice post" }
{ "_id": 202, "postId": 1, "text": "Helpful!" }
```

To retrieve:
Use `$lookup` (similar to JOIN)

```js
db.posts.aggregate([
  { $match: { _id: 1 } },
  { $lookup: {
      from: "comments",
      localField: "_id",
      foreignField: "postId",
      as: "comments"
  }}
])
```

### ✔ Use referencing when:

* Data grows large (e.g., millions of comments)
* Updates happen frequently
* You don't always need comments when fetching posts

---

# **3️⃣ Many-to-Many Relationship**

A record can relate to **many records**, and vice versa.

### ✔ Examples:

* Students ↔ Courses
* Users ↔ Roles
* Products ↔ Categories
* Authors ↔ Books

---

## **Option A: Referencing Both Sides (Common)**

### students collection:

```json
{ "_id": 1, "name": "Rahul", "courseIds": [101, 102] }
```

### courses collection:

```json
{ "_id": 101, "courseName": "Maths" }
{ "_id": 102, "courseName": "Science" }
```

Both collections have references.

---

## **Option B: Intermediate Linking Collection (Best for large data)**

### enrollments collection:

```json
{ "studentId": 1, "courseId": 101 }
{ "studentId": 1, "courseId": 102 }
{ "studentId": 2, "courseId": 101 }
```

This is similar to a **junction table** in SQL.

### ✔ Use this when:

* Very large relationships
* Query performance critical
* Many students enrolled in many courses

---

# **4️⃣ Embedding vs Referencing (Decision Table)**

| Situation                     | Recommended | Reason                     |
| ----------------------------- | ----------- | -------------------------- |
| Small related data            | Embed       | Fast retrieval, simple     |
| Large related data            | Reference   | Prevents huge documents    |
| Real-time read optimization   | Embed       | No join/lookups            |
| High-write frequency          | Reference   | Avoid rewriting entire doc |
| Many-to-Many                  | Reference   | Embedding impossible       |
| Data reused in many places    | Reference   | Avoid duplication          |
| Data always accessed together | Embed       | Efficient                  |

---

# **5️⃣ Real-World Examples (All Types)**

---

## ✔ Example 1: Customer ↔ Address (1:1 — embed)

```json
{
  "name": "John",
  "address": { "city": "Delhi", "pincode": 110001 }
}
```

---

## ✔ Example 2: User ↔ Orders (1:N — reference)

Users have many orders:

```json
// users
{ "_id": 1, "name": "John" }

// orders
{ "_id": 101, "userId": 1, "amount": 2000 }
{ "_id": 102, "userId": 1, "amount": 3500 }
```

---

## ✔ Example 3: Courses ↔ Students (M:N — reference both sides)

```json
// students
{ "_id": 1, "name": "Rahul", "courses": [101, 102] }

// courses
{ "_id": 101, "title": "Maths" }
{ "_id": 102, "title": "Science" }
```

---

## ✔ Example 4: Product ↔ Reviews (1:N — embed for few)

```json
{
  "product": "Laptop",
  "reviews": [
    { "user": "Amit", "rating": 5 },
    { "user": "Ritu", "rating": 4 }
  ]
}
```

---

# **6️⃣ Best Practices for Relationship Modeling**

✔ Embed **when data is small & access together**
✔ Reference **when child data grows large**
✔ Do not embed arrays with thousands of records
✔ Avoid deep nesting (more than 2–3 levels)
✔ Choose based on **query patterns**, not just structure
✔ Use `$lookup` carefully (it’s expensive)
✔ Avoid duplication of large fields
✔ Keep document size < **16 MB limit**

---

# **🎯 Summary**

| Relationship Type | Embedding          | Referencing                  |
| ----------------- | ------------------ | ---------------------------- |
| 1:1               | ✔ Best             | ✔ Only for large/secure data |
| 1:N               | ✔ Best for small N | ✔ Best for large N           |
| M:N               | ❌ Not possible     | ✔ Best choice                |

MongoDB gives flexibility, but choosing the right model improves performance.

---


