 **Section 6.1 — Drivers Overview**, explaining how applications interact with MongoDB using official drivers.

This section includes:

* What MongoDB drivers are
* Node.js driver (most common)
* Python, Java, C#, Go (conceptual overview)
* Basic connection examples

---

# **📘 6. Working with MongoDB in Applications**

Applications interact with MongoDB using **drivers** provided by MongoDB.

MongoDB drivers allow your application code to:

✔ Connect to MongoDB
✔ Run queries (CRUD operations)
✔ Execute aggregations
✔ Use transactions
✔ Manage indexes

MongoDB provides **official drivers** for most languages.

---

# **📘 6.1 Drivers Overview**

MongoDB has drivers for:

* Node.js (JavaScript/TypeScript)
* Python
* Java
* C# / .NET
* Go
* C++
* PHP
* Ruby

We will cover the most popular ones.

---

# **1️⃣ Node.js Driver (Most Common for Web Apps)**

Node.js driver allows JavaScript/TypeScript applications to connect to MongoDB.

### ✔ Install

```bash
npm install mongodb
```

---

## ✔ Basic Connection Example

```js
const { MongoClient } = require("mongodb");

const uri = "mongodb://localhost:27017";
const client = new MongoClient(uri);

async function run() {
  try {
    await client.connect();

    const db = client.db("shop");
    const users = db.collection("users");

    const result = await users.findOne({ name: "Alice" });
    console.log(result);

  } finally {
    await client.close();
  }
}

run().catch(console.error);
```

---

## ✔ CRUD Example (Insert)

```js
await users.insertOne({ name: "Bob", age: 30 });
```

## ✔ Find

```js
const data = await users.find({ age: { $gt: 25 } }).toArray();
```

## ✔ Update

```js
await users.updateOne(
  { name: "Bob" },
  { $set: { age: 31 } }
);
```

## ✔ Delete

```js
await users.deleteOne({ name: "Bob" });
```

---

## ✔ Node.js Driver Features

| Feature                            | Supported |
| ---------------------------------- | --------- |
| CRUD Operations                    | ✔         |
| Aggregation Pipeline               | ✔         |
| Transactions                       | ✔         |
| Change Streams (real-time updates) | ✔         |
| Schema validation                  | ✔         |
| GridFS                             | ✔         |

---

# **2️⃣ Python Driver (PyMongo)**

Python applications use **PyMongo** to work with MongoDB.

### ✔ Install

```bash
pip install pymongo
```

---

## ✔ Basic Connection Example

```python
from pymongo import MongoClient

client = MongoClient("mongodb://localhost:27017")
db = client.shop

user = db.users.find_one({"name": "Alice"})
print(user)
```

---

## ✔ Insert Example

```python
db.users.insert_one({"name": "Bob", "age": 30})
```

---

## ✔ Find Example

```python
list(db.users.find({"age": {"$gt": 25}}))
```

---

## ✔ Python Driver Features

✔ Used in data science, ML pipelines
✔ Works with Pandas
✔ Good for automation scripts
✔ Supports transactions

---

# **3️⃣ Java Driver**

Java uses the **MongoDB Java Driver** or **Spring Data MongoDB**.

### ✔ Dependency (Maven)

```xml
<dependency>
  <groupId>org.mongodb</groupId>
  <artifactId>mongodb-driver-sync</artifactId>
  <version>4.11.0</version>
</dependency>
```

---

## ✔ Basic Example

```java
MongoClient client = MongoClients.create("mongodb://localhost:27017");
MongoDatabase db = client.getDatabase("shop");
MongoCollection<Document> users = db.getCollection("users");

Document doc = users.find(eq("name", "Alice")).first();
System.out.println(doc.toJson());
```

---

## ✔ Java Driver Features

✔ Type-safe
✔ Works with Spring Boot
✔ Enterprise applications
✔ Strongly typed models

---

# **4️⃣ C# / .NET Driver**

Used for Windows and enterprise applications.

### ✔ Install

```bash
dotnet add package MongoDB.Driver
```

---

## ✔ Basic Example

```csharp
var client = new MongoClient("mongodb://localhost:27017");
var db = client.GetDatabase("shop");
var users = db.GetCollection<BsonDocument>("users");

var result = users.Find(Builders<BsonDocument>.Filter.Eq("name", "Alice")).FirstOrDefault();
Console.WriteLine(result);
```

---

## ✔ Uses

✔ ASP.NET MVC
✔ Desktop apps
✔ Enterprise systems

---

# **5️⃣ Go Driver (MongoDB Go Driver)**

Popular for microservices.

### ✔ Install

```bash
go get go.mongodb.org/mongo-driver/mongo
```

---

## ✔ Basic Example

```go
client, _ := mongo.Connect(context.TODO(), options.Client().ApplyURI("mongodb://localhost:27017"))
collection := client.Database("shop").Collection("users")

var result bson.M
collection.FindOne(context.TODO(), bson.M{"name": "Alice"}).Decode(&result)
fmt.Println(result)
```

---

## ✔ Uses

✔ Cloud services
✔ Microservices architecture
✔ High-performance systems

---

# **6️⃣ Summary of Drivers**

| Language    | Driver                       | Usage                 |
| ----------- | ---------------------------- | --------------------- |
| **Node.js** | mongodb                      | Web apps, APIs        |
| **Python**  | PyMongo                      | Data science, scripts |
| **Java**    | mongodb-driver / Spring Data | Enterprise apps       |
| **C#**      | MongoDB.Driver               | .NET apps             |
| **Go**      | MongoDB Go Driver            | Microservices         |

---

# **7️⃣ What All Drivers Support**

| Feature        | Supported |
| -------------- | --------- |
| CRUD           | ✔         |
| Aggregation    | ✔         |
| Indexing       | ✔         |
| Transactions   | ✔         |
| Change Streams | ✔         |
| GridFS         | ✔         |

---

