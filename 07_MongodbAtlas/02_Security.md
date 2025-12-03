 **Section 7.2 — MongoDB Atlas Security**, covering:

* Users & Roles
* IP Access Control
* Database User Privileges

This section explains how Atlas protects your data and how you can properly secure your cluster.

---

# **📘 7.2 Security**

MongoDB Atlas includes strong, enterprise-grade security features by default.
A secure Atlas deployment protects against:

✔ Unauthorized access
✔ Network attacks
✔ Misconfigured privileges
✔ Compromised credentials

Let's break down the major components.

---

# **1️⃣ Users & Roles (Authentication)**

MongoDB uses **role-based access control (RBAC)**.
This means each user gets **roles** that define what they can do.

---

## **A. Creating Database Users in Atlas**

Atlas database users are created with:

* Username
* Password
* Role(s)
* Authentication database (default: `admin`)

### ✔ Example roles:

* `read`
* `readWrite`
* `dbAdmin`
* `clusterAdmin`
* `atlasAdmin`

### Example connection string (Atlas):

```
mongodb+srv://myUser:myPassword@cluster.mongodb.net/myDatabase
```

#### ❗ Note:

Application users ≠ Atlas account users

* Database users → used by your app
* Atlas account users → manage Atlas platform

---

## **B. Built-in Roles**

| Role             | Permissions                             |
| ---------------- | --------------------------------------- |
| **read**         | Only read data                          |
| **readWrite**    | Read + write                            |
| **dbAdmin**      | Manage indexes, validation, collections |
| **userAdmin**    | Create/manage users                     |
| **clusterAdmin** | Manage cluster-wide settings            |
| **atlasAdmin**   | Full control over the project           |

---

## **C. Custom Roles**

Atlas allows custom roles for:

* Fine-grained access
* Least-privilege principle
* Specific collections or actions

Example custom role:

* User can read `products`
* User can only write to `orders`

This prevents accidental or malicious writes.

---

# **2️⃣ IP Access Control (Network Security)**

IP access control decides:

✔ Which IP addresses *can connect* to your cluster
❌ All others are blocked

---

# **A. Allow Specific IPs Only**

You can whitelist:

* Your laptop IP
* Backend server IP
* VPC peering
* Private networks

Example:

```
Allowed IPs:
  103.5.12.88
  52.78.11.120
  44.197.21.9
```

---

## **B. Add IP in Atlas**

Go to:
**Network Access → Add IP Address**

You can add:

✔ Specific IP
✔ IP range (CIDR)
✔ AWS/Azure/GCP private IPs
✔ Allow Access from Anywhere (0.0.0.0/0) — NOT recommended

---

## **C. IP Access Best Practices**

### ✔ DO:

* Allow only known IPs
* Use private IP VPN for server-to-Atlas access
* Use environment variables for connection strings

### ❌ DON'T:

* Use `0.0.0.0/0` in production (anyone can access your DB)
* Open cluster to the public internet

---

# **3️⃣ Database User Privileges**

Privileges determine:

✔ What collections a user can access
✔ What operations they can perform
✔ Whether they can modify indexes
✔ Whether they can run admin commands

MongoDB uses the **principle of least privilege**.

---

# **A. Common Privileges**

| Privilege            | Description            |
| -------------------- | ---------------------- |
| **find**             | Read documents         |
| **insert**           | Insert documents       |
| **update**           | Modify documents       |
| **remove**           | Delete documents       |
| **createCollection** | Create new collections |
| **createIndex**      | Manage indexes         |
| **listDatabases**    | Show database list     |
| **dropDatabase**     | Delete database        |

---

# **B. Example: Limited-Privilege User for Application**

A user that can only read/write in one database:

```json
{
  "user": "appUser",
  "roles": [
    {
      "role": "readWrite",
      "db": "shop"
    }
  ]
}
```

---

# **C. Example: Admin User**

```json
{
  "user": "dbAdmin",
  "roles": [
    {
      "role": "dbAdmin",
      "db": "shop"
    },
    {
      "role": "userAdmin",
      "db": "shop"
    }
  ]
}
```

---

# **D. Custom Role Example**

Allow user to **read products** but **write only orders**:

```json
{
  "role": "customRole",
  "privileges": [
    {
      "resource": { "db": "shop", "collection": "products" },
      "actions": ["find"]
    },
    {
      "resource": { "db": "shop", "collection": "orders" },
      "actions": ["insert", "update"]
    }
  ],
  "roles": []
}
```

---

# **4️⃣ Authentication Types**

Atlas supports:

✔ Username + Password
✔ X.509 Certificates
✔ AWS IAM Authentication
✔ SCRAM-SHA-1 / SCRAM-SHA-256

For production:

* Prefer **SCRAM-SHA-256** (more secure)
* For enterprise, use **x.509 certificates**

---

# **5️⃣ Additional Atlas Security Features**

Atlas provides even more layers of security:

### ✔ Encryption at Rest

Data stored on disk is encrypted by default.

### ✔ Encryption in Transit

TLS/SSL enabled automatically.

### ✔ Network Peering

Connect Atlas to AWS/Azure/GCP private networks.

### ✔ Private Endpoints

Removes public access completely.

### ✔ Audit Logs

Track who accessed your database.

### ✔ Role-based access to Atlas UI

Control which teammates can manage deployments.

---

# **6️⃣ Security Best Practices (HIGHLY RECOMMENDED)**

| Best Practice             | Why                                    |
| ------------------------- | -------------------------------------- |
| Use environment variables | Protect passwords                      |
| Avoid 0.0.0.0/0           | Prevent open access                    |
| Use least-privilege roles | Limit damage from compromised accounts |
| Rotate DB passwords       | Prevent breaches                       |
| Enable IP whitelisting    | Block unknown access                   |
| Enable TLS encryption     | Protect data in transit                |
| Use private peering       | No internet exposure                   |
| Enable auditing           | Track suspicious activity              |

---

# **🎯 Summary: Atlas Security**

| Topic                 | Summary                            |
| --------------------- | ---------------------------------- |
| **Users & Roles**     | Control what users can do          |
| **IP Access Control** | Control who can connect            |
| **Privileges**        | Fine-grained access to operations  |
| **Encryption**        | Data secured at rest & in transit  |
| **Network Security**  | Peering, private endpoints         |
| **Best Practices**    | Least privilege + no public access |

---


Just tell me the next topic!
