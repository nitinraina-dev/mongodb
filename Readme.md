# 🧠 MongoDB Concepts – Full Guide

---

## 📌 1. **Core MongoDB Concepts**

| Concept        | Description                                          |
| -------------- | ---------------------------------------------------- |
| **Database**   | A container for collections                          |
| **Collection** | A group of documents (like a table in SQL)           |
| **Document**   | A JSON-like object (BSON) that holds data            |
| **Field**      | A key-value pair inside a document                   |
| **\_id**       | A unique identifier auto-generated for each document |

---

## 🛠️ 2. **CRUD Operations**

### 📥 Create

```js
db.users.insertOne({ name: "Nitin", age: 25 });
db.users.insertMany([{ name: "A" }, { name: "B" }]);
```

### 📤 Read

```js
db.users.find();                        // Get all
db.users.find({ age: 25 });             // Filter
db.users.findOne({ name: "Nitin" });    // Get one
```

### ✏️ Update

```js
db.users.updateOne(
  { name: "Nitin" },
  { $set: { age: 26 } }
);

db.users.updateMany(
  { city: "Delhi" },
  { $set: { country: "India" } }
);
```

### ❌ Delete

```js
db.users.deleteOne({ name: "Nitin" });
db.users.deleteMany({ age: { $lt: 18 } });
```

---

## 🔍 3. **Query Operators**

### 🔹 Comparison

```js
$eq   // Equal
$ne   // Not equal
$gt   // Greater than
$lt   // Less than
$gte  // Greater than or equal
$lte  // Less than or equal
$in   // Matches values in an array
$nin  // Not in array
```

```js
db.users.find({ age: { $gte: 18, $lt: 30 } });
db.users.find({ name: { $in: ["A", "B"] } });
```

### 🔹 Logical

```js
$and
$or
$not
$nor
```

```js
db.users.find({ $or: [ { age: { $lt: 18 } }, { city: "Delhi" } ] });
```

### 🔹 Element

```js
$exists
$type
```

```js
db.users.find({ middleName: { $exists: false } });
```

### 🔹 Evaluation

```js
$regex     // Pattern match
$text      // Full-text search
```

---

## 📊 4. **Aggregation Functions**

Used for advanced data processing (like SQL GROUP BY + HAVING + JOIN)

### 🔧 Basic Structure

```js
db.collection.aggregate([
  { $match: { status: "active" } },
  { $group: { _id: "$city", total: { $sum: 1 } } },
  { $sort: { total: -1 } }
]);
```

### 🧱 Common Aggregation Stages

| Stage      | Purpose                   |
| ---------- | ------------------------- |
| `$match`   | Filter data (like `find`) |
| `$group`   | Group data by a field     |
| `$sort`    | Sort results              |
| `$project` | Include/exclude fields    |
| `$lookup`  | Join another collection   |
| `$limit`   | Limit results             |
| `$skip`    | Skip records              |
| `$count`   | Count documents           |
| `$unwind`  | Flatten arrays            |

### Example: Total Users by City

```js
db.users.aggregate([
  { $group: { _id: "$city", totalUsers: { $sum: 1 } } }
]);
```

### Example: Join Orders with Users

```js
db.orders.aggregate([
  {
    $lookup: {
      from: "users",
      localField: "userId",
      foreignField: "_id",
      as: "userInfo"
    }
  }
]);
```

---

## ⚖️ 5. **BSON vs JSON**

| Feature        | JSON                                 | BSON                              |
| -------------- | ------------------------------------ | --------------------------------- |
| Format         | Text-based                           | Binary-based                      |
| Readability    | Human-readable                       | Machine-readable                  |
| Data types     | Limited (no `Date`, `ObjectId`, etc) | Supports `Date`, `ObjectId`, etc. |
| Use in MongoDB | Used for input/output (queries)      | Used for storage internally       |

### 💡 BSON stands for:

**Binary JSON** – It extends JSON by adding more data types (like `ObjectId`, `Date`, `BinData`, etc.)

---

### 📜 Original:

**"BSON stands for Binary JavaScript Object Notation. It is a binary-encoded serialization of JSON documents."**

---

### 🧠 Simple Explanation:

**BSON** is a format used by MongoDB to **store data**.
It’s very similar to **JSON**, but:

1. It's saved in a **binary (0s and 1s)** format, not plain text.
2. It can store **extra data types** that JSON can’t (like `Date`, `ObjectId`, etc.).
3. It’s easier and faster for **computers to read and write** than JSON.

---

### 🔁 Rephrased:

> "BSON is like JSON, but saved in a special format that computers can process more quickly and that supports more kinds of data."

---



## 🧪 Tip: View Aggregated Result with Explain

```js
db.users.find({ age: 25 }).explain("executionStats");
```

---



# 📄 MongoDB Index Summary (Email Field Example)

## ✅ What is an Index?

An **index** in MongoDB is a special data structure that improves the speed of data retrieval for specific fields.

> Without an index → MongoDB scans every document (slow)
> With an index → MongoDB uses a fast B-tree structure (quick lookup)

---

## 📌 Index on `email` Field

### Example:

```js
db.users.createIndex({ email: 1 });
```

* Creates a **B-tree index** on the `email` field
* Used only inside the **`users` collection**
* Helps queries like:

  ```js
  db.users.find({ email: "nitin@gmail.com" });
  ```

---

## 📦 Behavior Breakdown

| Aspect            | Explanation                                                       |
| ----------------- | ----------------------------------------------------------------- |
| Scope             | The index only exists inside the specific collection (`users`)    |
| Field             | It only optimizes queries for the field it's created on (`email`) |
| Effect            | Speeds up filtering, sorting, and searching for that field        |
| Other collections | Not affected (e.g., `contacts` collection needs its own index)    |

---

## ❓ What If You Query Another Collection?

Suppose you have two collections:

### 1. `users`

```json
{ "name": "Nitin", "email": "nitin@gmail.com" }
```

### 2. `contacts`

```json
{ "person": "Nitin", "email": "nitin@gmail.com" }
```

You create:

```js
db.users.createIndex({ email: 1 });
```

| Query                                | Uses Index? | Reason                  |
| ------------------------------------ | ----------- | ----------------------- |
| `db.users.find({ email: "..." })`    | ✅ Yes       | Index exists in `users` |
| `db.contacts.find({ email: "..." })` | ❌ No        | No index in `contacts`  |

If you want fast queries in `contacts`, you also need:

```js
db.contacts.createIndex({ email: 1 });
```

---

## 🧠 Key Takeaways

* Indexes are **collection-specific**
* Indexes are **field-specific**
* Create separate indexes for **each field in each collection** that you query frequently

---

## 🧪 Tip to Check if Index Is Used

```js
db.users.find({ email: "..." }).explain("executionStats");
```

* Look for `"stage": "IXSCAN"` → Index used
* `"COLLSCAN"` → Full scan (no index used)

---
