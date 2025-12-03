**Section 6.3 — Environment Configuration**, covering:

* Using environment variables
* Securing MongoDB connection strings
* Connection pooling (important for performance)

Clear explanations + examples for Node.js (but concepts apply to all languages).

---

# **📘 6.3 Environment Configuration**

When building real applications, managing the database configuration safely and efficiently is critical.

This section covers three important concepts:

1️⃣ Environment variables
2️⃣ Securing MongoDB connection strings
3️⃣ Connection pooling

---

# **1️⃣ Using Environment Variables**

### ❗ NEVER hardcode your MongoDB connection string inside your JavaScript code.

Instead, use a `.env` file or environment variables.

---

# ✔ Step 1: Install dotenv

```bash
npm install dotenv
```

---

# ✔ Step 2: Create a `.env` file

```env
MONGODB_URI=mongodb://localhost:27017/shop
PORT=3000
```

For Atlas:

```env
MONGODB_URI=mongodb+srv://user:password@cluster.mongodb.net/mydb
```

---

# ✔ Step 3: Load .env in your Node.js app

```js
require("dotenv").config();
const { MongoClient } = require("mongodb");

const uri = process.env.MONGODB_URI;
const client = new MongoClient(uri);
```

Now your code is clean and secure.

---

# **2️⃣ Secure Connection Strings**

Connection strings often contain:

* Database username
* Database password
* Cluster details

### Example:

```text
mongodb+srv://username:password@cluster.mongodb.net/shop
```

This must **NOT** be pushed to GitHub or shared publicly.

---

## ✔ How to secure connection strings

### **A. Use .env (most common)**

Never commit `.env` to Git:

```
.gitignore:
  .env
```

---

### **B. Use environment variables in server environments**

Examples:

#### Linux:

```bash
export MONGODB_URI="mongodb+srv://...."
```

#### Windows (PowerShell):

```powershell
setx MONGODB_URI "mongodb+srv://..."
```

---

### **C. Use secrets managers (for production)**

* AWS Secrets Manager
* Azure Key Vault
* Google Secret Manager
* Docker Secrets
* Kubernetes Secrets

These store connection strings safely.

---

### **D. Use SRV format for secure connections**

Always prefer using SRV (recommended):

```
mongodb+srv://username:password@cluster.mongodb.net/dbname
```

MongoDB automatically uses:

✔ TLS/SSL
✔ Driver auto-discovery
✔ Secure connection defaults

---

# **3️⃣ Connection Pooling**

**Connection Pooling** allows MongoDB to reuse open connections instead of opening a new one for every request.

This improves:

✔ Performance
✔ Scalability
✔ Memory usage
✔ Speed of API calls

---

# **Why Connection Pooling?**

Without pooling:

* Each request → new DB connection
* Slow
* High server load
* Errors under heavy traffic

With pooling:

* A pool of ready connections
* Requests reuse them
* Much faster

---

# **Enabling Connection Pooling (Node.js)**

### Modern MongoDB driver enables pooling automatically.

You can configure it with:

```js
const client = new MongoClient(uri, {
  maxPoolSize: 20,      // default = 100
  minPoolSize: 5,
  serverSelectionTimeoutMS: 5000
});
```

---

# **Recommended Settings**

| Setting                      | Meaning                     | Usage                      |
| ---------------------------- | --------------------------- | -------------------------- |
| **maxPoolSize**              | Maximum connections in pool | For heavy traffic apps     |
| **minPoolSize**              | Minimum idle connections    | For consistent performance |
| **serverSelectionTimeoutMS** | Timeout for finding servers | Avoid long waits           |

---

# **Example: Reusable Connection with Pooling**

```js
require("dotenv").config();
const { MongoClient } = require("mongodb");

let db;
const client = new MongoClient(process.env.MONGODB_URI, {
  maxPoolSize: 20
});

async function connectDB() {
  if (!db) {
    await client.connect();
    db = client.db("shop");
    console.log("DB Connected");
  }
  return db;
}

module.exports = connectDB;
```

Now your Express/Node app can use:

```js
const getDB = require("./db");

app.get("/users", async (req, res) => {
  const db = await getDB();
  const users = await db.collection("users").find().toArray();
  res.json(users);
});
```

✔ No reconnection
✔ Efficient pooling
✔ High performance

---

# **Connection Pooling Best Practices**

### ✔ DO

* Use **a single MongoClient instance** per application
* Configure pool size based on server traffic
* Always use async/await
* Use SRV URIs for secure defaults

### ❌ DON'T

* Create a new DB connection inside each route
* Hardcode Mongo URI in your code
* Use very high pool sizes (causes resource exhaustion)

---

# **🎯 Summary**

| Concept                       | Purpose                           |
| ----------------------------- | --------------------------------- |
| **Environment variables**     | Hide sensitive data               |
| **Secure connection strings** | Prevent credential leaks          |
| **Connection pooling**        | Improve performance & scalability |

You now know the best practices for environment configuration & secure MongoDB usage in applications.


