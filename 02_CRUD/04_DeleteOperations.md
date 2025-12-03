Here is **Section 2.4 — Delete Operations**, including:

* `deleteOne()`
* `deleteMany()`
* How filters work
* Real examples
* Important notes and warnings

Everything is simple and well-explained.

---

# **📘 2.4 Delete Operations in MongoDB**

MongoDB provides two main delete methods:

1️⃣ **deleteOne()** → deletes the **first matching** document
2️⃣ **deleteMany()** → deletes **all matching** documents

Deletes are **permanent**, so use them carefully.

---

# **1️⃣ deleteOne()**

Deletes **one** document that matches the filter.

### ✔ Syntax:

```js
db.collection.deleteOne(filter)
```

### ✔ Example 1: Delete user named "Alice"

```js
db.users.deleteOne({ name: "Alice" })
```

If multiple documents match, **only the first** will be deleted.

### ✔ Example 2: Delete product with price = 200

```js
db.products.deleteOne({ price: 200 })
```

---

# **2️⃣ deleteMany()**

Deletes **all** documents that match the filter.

### ✔ Syntax:

```js
db.collection.deleteMany(filter)
```

### ✔ Example 1: Delete all users from Delhi

```js
db.users.deleteMany({ city: "Delhi" })
```

### ✔ Example 2: Delete all products with stock = 0

```js
db.products.deleteMany({ stock: 0 })
```

---

# **3️⃣ Delete All Documents in a Collection**

To remove everything (but keep the collection):

```js
db.collection.deleteMany({})
```

⚠️ **Use with caution** — this clears all documents.

---

# **4️⃣ Drop Entire Collection**

This removes the **collection itself**, not just the documents.

```js
db.collection.drop()
```

### Example:

```js
db.users.drop()
```

⚠️ After dropping → collection no longer exists.

---

# **5️⃣ Filtering in Delete Operations**

Delete supports all filter types:

### ✔ Comparison operators

* `$eq`, `$lt`, `$gt`, `$in`, `$ne`

### ✔ Logical operators

* `$and`, `$or`, `$nor`, `$not`

---

## **Example A: Delete users older than 40**

```js
db.users.deleteMany({ age: { $gt: 40 } })
```

---

## **Example B: Delete users from Mumbai OR Chennai**

```js
db.users.deleteMany({
  $or: [
    { city: "Mumbai" },
    { city: "Chennai" }
  ]
})
```

---

## **Example C: Delete users with age NOT equal to 25**

```js
db.users.deleteMany({ age: { $ne: 25 } })
```

---

# **6️⃣ Real-World Delete Examples**

---

## ✔ Delete a blog comment by ID

```js
db.comments.deleteOne({ _id: ObjectId("64d1223...") })
```

---

## ✔ Delete all inactive users

```js
db.users.deleteMany({ active: false })
```

---

## ✔ Remove all expired coupons

```js
db.coupons.deleteMany({
  expiryDate: { $lt: new Date() }
})
```

---

## ✔ Clear shopping cart of a particular user

```js
db.cart.deleteMany({ userId: "user101" })
```

---

## ✔ Delete all orders with status = "Cancelled"

```js
db.orders.deleteMany({ status: "Cancelled" })
```

---

# **7️⃣ Return Value of Delete Operations**

After deleting, MongoDB returns:

```json
{
  "acknowledged": true,
  "deletedCount": 3
}
```

Meaning:

* Operation successful
* 3 documents deleted

---

# **📌 Important Notes**

### ⚠️ 1. deleteOne() removes only FIRST matched document

Even if multiple documents match.

### ⚠️ 2. deleteMany() can delete entire collections if filter is empty

Be careful using:

```js
db.collection.deleteMany({})
```

### ⚠️ 3. deleteOne() with no filter is dangerous

Never write:

```js
db.users.deleteOne({})
```

This will delete the **first** document in the collection.

---

# **🎯 Summary**

| Operation            | Description                     |
| -------------------- | ------------------------------- |
| `deleteOne(filter)`  | Deletes first matching document |
| `deleteMany(filter)` | Deletes all matching documents  |
| `{}` empty filter    | Matches ALL documents           |
| `drop()`             | Deletes the entire collection   |

---


