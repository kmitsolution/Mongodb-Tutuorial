# **📘 Working with MongoDB Compass (GUI Tool)**

MongoDB Compass is the **official graphical interface** for MongoDB.
It allows you to work visually instead of writing shell commands.

---

# **1️⃣ Installing MongoDB Compass**

### Steps:

1. Visit the official download page:
   [https://www.mongodb.com/try/download/compass](https://www.mongodb.com/try/download/compass)
2. Select your OS:
   ✔ Windows
   ✔ macOS
   ✔ Linux
3. Download and install the application like a normal program.

After installation → open **MongoDB Compass**.

---

# **2️⃣ Connecting to MongoDB Using Compass**

Compass requires a **connection string**.

## **A. Connect to Local MongoDB**

If MongoDB is running on your system:

```
mongodb://localhost:27017
```

Paste this URL into Compass → Click **Connect**.

---

## **B. Connect to MongoDB Atlas (Cloud)**

Atlas provides a connection string like:

```
mongodb+srv://username:password@cluster0.xxxxx.mongodb.net/
```

Steps:

1. Copy connection string from Atlas
2. Paste into Compass
3. Replace `username` and `password`
4. Click **Connect**

Now Compass shows your **cloud cluster** visually.

---

# **3️⃣ Working with Databases and Collections**

After connecting, Compass shows:

* Databases
* Collections inside each database
* Documents inside each collection

---

## **A. Create a New Database**

1. Click **Create Database**
2. Enter:

   * Database Name (e.g., `school`)
   * Collection Name (e.g., `students`)
3. Click **Create**

---

## **B. Create a New Collection**

Inside a database:

1. Click **Add Collection**
2. Enter collection name
3. Click **Create**

---

# **4️⃣ Inserting Documents (Graphically)**

To insert a document:

1. Open the collection
2. Click **Insert Document**
3. A JSON editor opens
4. Add document like:

```json
{
  "name": "Alice",
  "age": 22,
  "city": "Delhi"
}
```

5. Click **Insert**

Document is saved in the database.

---

# **5️⃣ Querying Documents in Compass**

Compass has a **Query Bar** at the top.

### Example: Show users whose age > 25

```
{ age: { $gt: 25 } }
```

Click **Find**.

---

## **Projections**

To show only selected fields:

### Example: Show only name and city

```
Filter:     { city: "Delhi" }
Project:    { name: 1, city: 1, _id: 0 }
```

---

## **Sorting**

Use Sort box:

### Example: Sort by age descending:

```
{ age: -1 }
```

---

# **6️⃣ Updating Documents in Compass**

To update:

1. Click on a document
2. Click **Edit Document**
3. Change values
4. Click **Update**

---

## Using Update Query (Advanced)

Compass also supports update queries.

### Example: Update age to 30

```
Filter:  { name: "Bob" }
Update:  { $set: { age: 30 } }
```

Then click **Update** or **Update Many**.

---

# **7️⃣ Deleting Documents in Compass**

You can delete:

### ✔ Delete a single document

* Click the document
* Click **Delete**
* Confirm

### ✔ Delete multiple documents using filter

```
Filter: { city: "Delhi" }
```

Click **Delete Many**.

---

# **8️⃣ Visualizing Data**

Compass can display:

* Document count
* Schema visualization
* Indexes
* Data distribution
* Performance metrics

---

## **A. Schema Tab**

Shows:

* Fields
* Data types
* Ranges
* Array structures

Example:
You can see that `age` is a Number and its range is 18–40.

---

## **B. Indexes Tab**

Shows:

* Existing indexes
* Create Index button

### Example: Create index on age:

1. Click **Create Index**
2. Index Field: `age`
3. Type: Ascending
4. Click **Create**

---

# **9️⃣ Working With Aggregation in Compass**

Compass provides a **pipeline builder**.

### Example Pipeline:

1. Stage 1:

   ```json
   { $match: { city: "Delhi" } }
   ```
2. Stage 2:

   ```json
   { $group: { _id: "$city", avgAge: { $avg: "$age" } } }
   ```

Click **Run** to get aggregated results.

---

# **🔟 Compass Advantages (Why Use GUI?)**

✔ No shell commands required
✔ Visual query builder
✔ Easy schema exploration
✔ Create/modify indexes visually
✔ Great for beginners
✔ Perfect for learning MongoDB

---

# **🎯 Summary of Key Actions in Compass**

| Task              | How to do it                  |
| ----------------- | ----------------------------- |
| Connect           | Enter connection string       |
| Create DB         | Create Database → Name        |
| Create Collection | Add Collection                |
| Insert            | Insert Document               |
| Query             | Use Filter box                |
| Projection        | Use Project field             |
| Sort              | Use Sort box                  |
| Update            | Edit Document or Update Query |
| Delete            | Delete button or Delete Many  |
| Aggregation       | Aggregation Builder           |
| Indexes           | Indexes tab                   |

---

