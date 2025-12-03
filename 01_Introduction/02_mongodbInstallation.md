# **📘 1.2 Installing MongoDB**

MongoDB can be installed in **two main ways**:

1️⃣ **Locally on your computer**
2️⃣ **On the cloud using MongoDB Atlas**

Both options are easy — Atlas requires no installation and is recommended for beginners.

---

# **1️⃣ Install MongoDB Locally (Windows / macOS / Linux)**

You install the **MongoDB Community Server** which contains:

* `mongod` → MongoDB server
* `mongosh` → MongoDB shell (command-line client)

---

## **🔷 A. Installing on Windows**

### **Steps**

1. Go to:
   **[https://www.mongodb.com/try/download/community](https://www.mongodb.com/try/download/community)**
2. Choose:

   * Platform: Windows
   * Package: MSI Installer
3. Run the installer
4. Select **Complete** installation
5. Make sure to check **Install MongoDB as a Service** (recommended)
6. Finish installation

MongoDB will now run on:

```
mongodb://localhost:27017
```

### **Check installation**

Open **Command Prompt**:

```
mongosh
```

If MongoDB is running, you'll enter the mongo shell.

---

## **🔷 B. Installing on macOS**

### **Method 1: Using Homebrew (recommended)**

Run these commands:

```
brew tap mongodb/brew
brew install mongodb-community
```

Start MongoDB:

```
brew services start mongodb-community
```

Connect:

```
mongosh
```

### **Method 2: Using .tgz installer**

Only for advanced users.

---

## **🔷 C. Installing on Linux**

For Ubuntu:

```
sudo apt install mongodb-org
```

Start service:

```
sudo systemctl start mongod
```

Check:

```
mongosh
```

---

# **2️⃣ Install MongoDB Compass (GUI)**

MongoDB Compass is the **official GUI tool** to:

* View collections
* Insert documents
* Run queries
* Visualize data

### **Download**

[https://www.mongodb.com/try/download/compass](https://www.mongodb.com/try/download/compass)

### **Connect locally using Compass**

Use this connection string:

```
mongodb://localhost:27017
```

Click **Connect** → you can now see databases visually.

---

# **3️⃣ Using MongoDB Shell (mongosh)**

`mongosh` is the command-line tool to interact with the database.

Examples:

```
show dbs
use test
db.users.insertOne({ name: "Alice" })
db.users.find()
```

---

# **4️⃣ Install MongoDB on Cloud: MongoDB Atlas (Recommended)**

Atlas is **free**, secure, and very easy for beginners.

---

## **🔷 Steps to Set Up MongoDB Atlas (Cloud)**

### **Step 1: Create an Account**

Go to:
[https://www.mongodb.com/atlas](https://www.mongodb.com/atlas)

Sign up (Google/Github available).

---

### **Step 2: Create a Free Cluster**

Choose:

* Free tier
* Region: Select the closest region

Click **Create Cluster**

---

### **Step 3: Create a Database User**

You need to create a username/password for your cluster.

Example:

* Username: `admin`
* Password: `YourPassword123`

---

### **Step 4: Add IP Address**

Choose:

```
Allow Access From Anywhere (0.0.0.0/0)
```

(For learning only; not for production.)

---

### **Step 5: Connect to Cluster**

Click → **Connect**

Choose:

* **MongoDB Compass**
* or **Mongo Shell**
* or **Your Application**

Example connection string:

```
mongodb+srv://admin:YourPassword123@cluster0.mongodb.net/test
```

Paste this connection string into:

* Compass OR
* Your Node.js/Python app OR
* mongosh

---

# **5️⃣ Local vs Cloud: Which Should You Use?**

| Feature                   | Local MongoDB      | Atlas Cloud                      |
| ------------------------- | ------------------ | -------------------------------- |
| Installation              | Required           | None                             |
| Beginner-friendly         | Medium             | Very easy                        |
| Requires system resources | Yes                | No                               |
| Internet required         | No                 | Yes                              |
| Scalable                  | Limited            | Highly scalable                  |
| Best for                  | Practice on laptop | Real projects, learning, sharing |

**Recommendation:**
➡️ Beginners should start with **MongoDB Atlas** (no installation, less setup).
➡️ Install locally if you want full developer control or offline usage.

---

# **Summary of What You Learned**

✔ Local installation steps (Windows/macOS/Linux)
✔ Using MongoDB Shell (`mongosh`)
✔ Using MongoDB Compass GUI
✔ Setting up MongoDB Atlas cloud cluster
✔ How to connect to local or cloud MongoDB

---

