**Section 7.3 — Serverless & Triggers in MongoDB Atlas**, covering:

* Atlas **Serverless Instances**
* **Database Triggers**
* **Function Automation** (Atlas Functions)

This section explains how Atlas enables event-driven, auto-scaling, serverless data workflows.

---

# **📘 7.3 Serverless & Triggers**

MongoDB Atlas provides **serverless instances** and **database triggers** that let you build scalable, event-driven applications **without managing servers**.

---

# **1️⃣ Atlas Serverless Instances**

Serverless clusters allow you to run MongoDB **without provisioning or managing servers**.

---

## ✔ What Is a Serverless Instance?

A **serverless** Atlas instance automatically scales:

* Storage
* Compute
* Connections

It charges you **only for the operations you perform**, not for running servers 24/7.

---

## ✔ Best for:

* Apps with unpredictable or bursty traffic
* Development / testing
* Low-traffic apps
* Event-driven applications
* Scripts, cron jobs, background tasks

---

## ✔ Features of Serverless Instances

| Feature                 | Description                                      |
| ----------------------- | ------------------------------------------------ |
| **On-demand scaling**   | No need to select instance size (e.g., M10, M20) |
| **Usage-based billing** | Pay per read/write/compute                       |
| **Automatic backups**   | Enabled depending on plan                        |
| **TLS encryption**      | Always enabled                                   |
| **Global availability** | Hosted on AWS regions                            |
| **No idle cost**        | You pay **only** when used                       |

---

## ✔ When *NOT* to use serverless

Serverless is **not ideal** for:

❌ High-volume production workloads
❌ Constant heavy writes
❌ Long-running aggregation workloads
❌ Low-latency, millisecond-critical apps

In such cases → use **Dedicated clusters**.

---

# **2️⃣ Database Triggers (Atlas Triggers)**

Atlas Triggers allow you to **run server-side code automatically when events happen**.

You can trigger a function on:

* A document insert
* A document update
* A document delete
* On a timed schedule (cron-like)

Think of them like:

✔ Webhooks
✔ Event listeners
✔ Cron jobs
✔ "Lambda" functions inside MongoDB

---

# **Types of Atlas Triggers**

There are **three** types:

---

## **A. Database Triggers (Change Stream Triggers)**

Fire when a database event occurs.

### Supported events:

* `insert`
* `update`
* `replace`
* `delete`
* `invalidate`

### Example use cases:

✔ Send a welcome email when a user creates an account
✔ Update inventory when an order is created
✔ Log changes for auditing
✔ Real-time analytics updates
✔ Trigger workflows (Slack, Discord messages, etc.)

---

### ✔ Example: Trigger on new user registration

Event: Document inserted into `users` collection
Trigger function:

```js
exports = async (changeEvent) => {
  const fullDocument = changeEvent.fullDocument;

  console.log("New user created:", fullDocument.email);

  // Example: Write to "audit_logs" collection
  const logs = context.services.get("mongodb-atlas")
                    .db("shop").collection("audit_logs");

  await logs.insertOne({
    message: "New user registered",
    user: fullDocument.email,
    timestamp: new Date()
  });
};
```

This runs automatically.

---

## **B. Scheduled Triggers (Cron Jobs)**

Run code at scheduled times, like:

* Every hour
* Daily
* Every Monday
* Every 10 minutes

### Example uses:

✔ Clearing expired sessions
✔ Sending daily summary emails
✔ Syncing external APIs
✔ Auto-cleaning logs
✔ Auto-billing tasks

---

### ✔ Example schedule trigger (every midnight)

```js
exports = async () => {
  const logs = context.services.get("mongodb-atlas")
                    .db("shop").collection("logs");

  await logs.deleteMany({
    createdAt: { $lt: new Date(Date.now() - 7*24*60*60*1000) }
  });

  console.log("Old logs cleaned");
};
```

---

## **C. Authentication Triggers**

Fire when authentication events occur:

* User created
* User deleted
* Login events

Useful for:

✔ Syncing external identity providers
✔ Logging login activity
✔ Sending welcome messages

---

# **3️⃣ Function Automation (Atlas Functions)**

Atlas Functions are **serverless JavaScript functions** that run in the cloud.

You can call them from:

✔ Triggers
✔ HTTP endpoints (Webhooks)
✔ Mobile/Frontend apps via App Services SDK
✔ Scheduled tasks
✔ External APIs

---

## ✔ What Atlas Functions Support

Atlas Functions run in a serverless environment and support:

| Feature                         | Details                               |
| ------------------------------- | ------------------------------------- |
| **JavaScript/Node environment** | ES6 syntax                            |
| **Async/await**                 | Yes                                   |
| **Database access**             | context.services.get("mongodb-atlas") |
| **HTTP requests**               | Built-in HTTP client                  |
| **Secrets storage**             | Secure values                         |
| **Third-party API calls**       | Yes                                   |

---

## ✔ Example Function: Send email after purchase

```js
exports = async (order) => {
  const http = context.http;

  await http.post({
    url: "https://api.email.com/send",
    body: {
      to: order.email,
      subject: "Order Confirmation",
      message: `Thanks for your order ${order.id}`
    },
    encodeBodyAsJSON: true
  });

  console.log("Confirmation email sent");
};
```

You can call this:

* From a trigger
* From frontend
* From webhook

---

# **4️⃣ Real-World Use Cases**

Here are realistic use cases for Serverless + Triggers:

---

## ✔ E-commerce

* Send email on new order
* Update real-time inventory
* Auto-calculate daily revenue
* Cleanup abandoned carts via schedule

---

## ✔ Social Media

* Notify followers on new post
* Auto-delete spam using triggers
* Log login attempts

---

## ✔ IoT / Real-time Monitoring

* Process sensor data live
* Enforce thresholds
* Trigger alerts

---

## ✔ Backend Automation

* Auto-archive logs
* Sync external APIs
* Data cleanups
* Scheduled analytics

---

# **5️⃣ Summary: Serverless & Triggers**

| Feature                     | Purpose                                              |
| --------------------------- | ---------------------------------------------------- |
| **Serverless Instances**    | Automatic, usage-based database with no provisioning |
| **Database Triggers**       | Run functions when DB events occur                   |
| **Scheduled Triggers**      | Cron-like automation                                 |
| **Function Automation**     | Serverless functions with DB access                  |
| **Authentication Triggers** | When users sign up/login                             |

MongoDB Atlas makes your app **event-driven**, scalable, and automated.

---

