---
description: MongoDB practical guide - CRUD operations, data modeling, aggregation pipeline, indexing, replication, and cluster setup.
---

# MongoDB Learning Plan - Practical Guide

**Created:** 2026-03-09
**Author:** Documentation Team
**Goal:** Learn MongoDB fundamentals with practical examples + VM Cluster setup
**Time required:** 12-16 hours (at your own pace)

---

## Learning Structure

```
1. Basics (2 hours) → MongoDB installation, basic operations
2. CRUD & Models (3 hours) → Document management, data modeling
3. Aggregation Pipeline (2 hours) → Complex queries, grouping
4. Indexing (2 hours) → Performance optimization
5. Real-world examples (3 hours) → Blog, e-commerce, analytics
6. Kubernetes & VM Cluster (3 hours)→ MongoDB in K8s + VM Replica Set
7. Project (2+ hours) → Your own application
```

---

## What is MongoDB?

**MongoDB** = **M**odern **D**ocument **B**atabase

- **NoSQL** database (non-relational)
- **Document-oriented** - stores JSON-like data (in BSON format)
- **Schema-less** - flexible data structure
- **Horizontally scalable** - sharding support
- **Fast development** - no need for fixed tables and schemas

### MongoDB vs. Redis

| MongoDB | Redis |
|---------|-------|
| Primary database | Cache, session store |
| Documents (JSON-like) | Key-value pairs |
| Stores on disk (with fast SSD) | Stores in memory |
| Complex queries | Simple operations |
| Persistent data | Temporary/cache data |
| Aggregation pipeline | Pub/Sub, Sorted Sets |

**Often used together:**
- MongoDB - primary data storage
- Redis - cache layer, session, real-time calculations

---

## 1. Basics - MongoDB Setup and First Steps

### 1.1 Installation (10 minutes)

**With Docker (simplest):**
```bash
# Start MongoDB in Docker
docker run -d --name mongodb-learning \
 -p 27017:27017 \
 -e MONGO_INITDB_ROOT_USERNAME=admin \
 -e MONGO_INITDB_ROOT_PASSWORD=password123 \
 mongo:7

# Verification
docker ps | grep mongodb-learning

# Access MongoDB Shell
docker exec -it mongodb-learning mongosh -u admin -p password123
```

**Native on Fedora:**
```bash
# Install MongoDB Community Edition
sudo tee /etc/yum.repos.d/mongodb-org-7.0.repo <<EOF
[mongodb-org-7.0]
name=MongoDB Repository
baseurl=https://repo.mongodb.org/yum/redhat/9/mongodb-org/7.0/x86_64/
gpgcheck=1
enabled=1
gpgkey=https://pgp.mongodb.com/server-7.0.asc
EOF

sudo dnf install -y mongodb-org
sudo systemctl start mongod
sudo systemctl enable mongod

# Connect
mongosh
```

### 1.2 MongoDB Core Concepts (20 minutes)

**Terminology mapping from SQL:**

| SQL | MongoDB |
|-----|---------|
| Database | Database |
| Table | Collection |
| Row | Document |
| Column | Field |
| JOIN | Embedding / $lookup |
| Primary Key | _id field (auto) |

**Document example (BSON/JSON):**
```json
{
 "_id": ObjectId("507f1f77bcf86cd799439011"),
 "name": "Documentation Team",
 "email": "developer@company.com",
 "role": "SRE",
 "skills": ["Kubernetes", "Python", "Ansible"],
 "joined": ISODate("2020-01-15T00:00:00Z"),
 "address": {
 "city": "Budapest",
 "country": "Hungary"
 }
}
```

### 1.3 First MongoDB Commands (30 minutes)

**Practice: mongosh basics**

```javascript
// In MongoDB Shell (mongosh):

// Current database
db

// Switch/create database
use learning_db

// List all databases
show dbs

// List collections
show collections

// --- INSERT ---

// Insert one document
db.users.insertOne({
 name: "Alice",
 email: "alice@example.com",
 age: 28,
 skills: ["JavaScript", "React"]
})

// Insert multiple documents
db.users.insertMany([
 {
 name: "Bob",
 email: "bob@example.com",
 age: 32,
 skills: ["Python", "Django"]
 },
 {
 name: "Charlie",
 email: "charlie@example.com",
 age: 25,
 skills: ["Go", "Kubernetes"]
 }
])

// --- READ (Find) ---

// Find all
db.users.find()

// Find with pretty formatting
db.users.find().pretty()

// Find with filter
db.users.find({ name: "Alice" })

// Find with multiple conditions
db.users.find({ age: { $gt: 25 }, skills: "Python" })

// Find one
db.users.findOne({ name: "Bob" })

// Projection (select specific fields)
db.users.find({}, { name: 1, email: 1, _id: 0 })

// --- UPDATE ---

// Update one document
db.users.updateOne(
 { name: "Alice" },
 { $set: { age: 29, city: "New York" } }
)

// Update multiple documents
db.users.updateMany(
 { age: { $lt: 30 } },
 { $set: { status: "young" } }
)

// Replace entire document
db.users.replaceOne(
 { name: "Bob" },
 {
 name: "Bob Smith",
 email: "bob.smith@example.com",
 age: 33
 }
)

// --- DELETE ---

// Delete one document
db.users.deleteOne({ name: "Charlie" })

// Delete multiple documents
db.users.deleteMany({ age: { $gt: 30 } })

// --- COUNT ---

// Count documents
db.users.countDocuments()
db.users.countDocuments({ age: { $gte: 25 } })
```

**Exercise Result:**
```javascript
// After insertMany
db.users.find().pretty()
/*
{
 "_id": ObjectId("..."),
 "name": "Alice",
 "email": "alice@example.com",
 "age": 29,
 "skills": ["JavaScript", "React"],
 "city": "New York",
 "status": "young"
}
{
 "_id": ObjectId("..."),
 "name": "Bob Smith",
 "email": "bob.smith@example.com",
 "age": 33
}
*/
```

---

## 2. CRUD Operations & Data Modeling (3 hours)

### 2.1 Advanced Query Operators (45 minutes)

**Comparison operators:**

```javascript
// $eq (equal), $ne (not equal)
db.users.find({ age: { $eq: 28 } })
db.users.find({ age: { $ne: 28 } })

// $gt (greater than), $gte (greater than or equal)
db.users.find({ age: { $gt: 25 } })
db.users.find({ age: { $gte: 25 } })

// $lt (less than), $lte (less than or equal)
db.users.find({ age: { $lt: 30 } })
db.users.find({ age: { $lte: 30 } })

// $in (in array), $nin (not in array)
db.users.find({ name: { $in: ["Alice", "Bob"] } })
db.users.find({ name: { $nin: ["Charlie"] } })
```

**Logical operators:**

```javascript
// $and
db.users.find({
 $and: [
 { age: { $gte: 25 } },
 { skills: "Python" }
 ]
})

// $or
db.users.find({
 $or: [
 { age: { $lt: 25 } },
 { age: { $gt: 35 } }
 ]
})

// $not
db.users.find({
 age: { $not: { $gt: 30 } }
})

// $nor (not or)
db.users.find({
 $nor: [
 { age: { $lt: 25 } },
 { skills: "Java" }
 ]
})
```

**Element operators:**

```javascript
// $exists (field exists)
db.users.find({ city: { $exists: true } })
db.users.find({ city: { $exists: false } })

// $type (field type check)
db.users.find({ age: { $type: "number" } })
db.users.find({ age: { $type: "string" } })
```

**Array operators:**

```javascript
// $all (array contains all elements)
db.users.find({ skills: { $all: ["Python", "Django"] } })

// $elemMatch (array element matches conditions)
db.orders.find({
 items: { $elemMatch: { price: { $gt: 100 }, quantity: { $gte: 2 } } }
})

// $size (array size)
db.users.find({ skills: { $size: 2 } })
```

**String operators (regex):**

```javascript
// $regex
db.users.find({ email: { $regex: /gmail\.com$/ } })
db.users.find({ name: { $regex: /^A/, $options: "i" } }) // case-insensitive
```

### 2.2 Update Operators (45 minutes)

**Field update operators:**

```javascript
// $set (set field value)
db.users.updateOne(
 { name: "Alice" },
 { $set: { age: 30, city: "San Francisco" } }
)

// $unset (remove field)
db.users.updateOne(
 { name: "Alice" },
 { $unset: { city: "" } }
)

// $inc (increment/decrement)
db.users.updateOne(
 { name: "Alice" },
 { $inc: { age: 1 } } // age += 1
)

// $mul (multiply)
db.products.updateOne(
 { name: "Laptop" },
 { $mul: { price: 0.9 } } // 10% discount
)

// $rename (rename field)
db.users.updateOne(
 { name: "Alice" },
 { $rename: { "age": "years" } }
)

// $min (set to minimum), $max (set to maximum)
db.users.updateOne(
 { name: "Alice" },
 { $min: { age: 25 } } // only if current age > 25
)
```

**Array update operators:**

```javascript
// $push (add element to array)
db.users.updateOne(
 { name: "Alice" },
 { $push: { skills: "TypeScript" } }
)

// $push with $each (add multiple elements)
db.users.updateOne(
 { name: "Alice" },
 { $push: { skills: { $each: ["Node.js", "Express"] } } }
)

// $addToSet (add if not exists)
db.users.updateOne(
 { name: "Alice" },
 { $addToSet: { skills: "React" } } // no duplicates
)

// $pop (remove first/last element)
db.users.updateOne(
 { name: "Alice" },
 { $pop: { skills: 1 } } // remove last
)
db.users.updateOne(
 { name: "Alice" },
 { $pop: { skills: -1 } } // remove first
)

// $pull (remove matching elements)
db.users.updateOne(
 { name: "Alice" },
 { $pull: { skills: "JavaScript" } }
)

// $pullAll (remove multiple elements)
db.users.updateOne(
 { name: "Alice" },
 { $pullAll: { skills: ["React", "Vue"] } }
)
```

### 2.3 Data Modeling Patterns (90 minutes)

**Pattern 1: Embedding (Denormalization)**

Best for: One-to-one, one-to-few relationships

```javascript
// Bad: Separate collections (like SQL)
// users collection
{
 "_id": ObjectId("..."),
 "name": "Alice"
}
// addresses collection
{
 "_id": ObjectId("..."),
 "userId": ObjectId("..."),
 "street": "123 Main St",
 "city": "New York"
}

// Good: Embedded document
{
 "_id": ObjectId("..."),
 "name": "Alice",
 "address": {
 "street": "123 Main St",
 "city": "New York",
 "zip": "10001"
 }
}
```

**Pattern 2: Referencing (Normalization)**

Best for: One-to-many, many-to-many relationships

```javascript
// Users collection
{
 "_id": ObjectId("user1"),
 "name": "Alice"
}

// Posts collection (reference to user)
{
 "_id": ObjectId("post1"),
 "title": "My First Post",
 "content": "...",
 "authorId": ObjectId("user1"), // Reference
 "createdAt": ISODate("2026-01-01")
}

// Query with $lookup (like JOIN)
db.posts.aggregate([
 {
 $lookup: {
 from: "users",
 localField: "authorId",
 foreignField: "_id",
 as: "author"
 }
 }
])
```

**Pattern 3: Hybrid (Embedding + Referencing)**

Best for: Complex relationships with frequent access

```javascript
// Posts with embedded author info (denormalized for performance)
{
 "_id": ObjectId("post1"),
 "title": "My First Post",
 "content": "...",
 "author": {
 "id": ObjectId("user1"),
 "name": "Alice",
 "avatar": "https://..."
 },
 "comments": [
 {
 "userId": ObjectId("user2"),
 "userName": "Bob", // Denormalized
 "text": "Great post!",
 "createdAt": ISODate("2026-01-02")
 }
 ]
}
```

**Real-world Example: Blog System**

```javascript
// Users collection
db.users.insertOne({
 "_id": ObjectId("user1"),
 "name": "Alice Johnson",
 "email": "alice@example.com",
 "bio": "SRE at Company",
 "joinedAt": ISODate("2020-01-15")
})

// Posts collection (hybrid pattern)
db.posts.insertOne({
 "_id": ObjectId("post1"),
 "title": "Getting Started with MongoDB",
 "slug": "getting-started-mongodb",
 "content": "MongoDB is a NoSQL database...",
 
 // Embedded author info (frequently accessed)
 "author": {
 "id": ObjectId("user1"),
 "name": "Alice Johnson"
 },
 
 // Embedded comments (one-to-many, limited size)
 "comments": [
 {
 "id": ObjectId("comment1"),
 "userId": ObjectId("user2"),
 "userName": "Bob Smith",
 "text": "Great article!",
 "createdAt": ISODate("2026-01-16")
 }
 ],
 
 // Metadata
 "tags": ["mongodb", "nosql", "database"],
 "views": 1250,
 "likes": 42,
 "createdAt": ISODate("2026-01-15"),
 "updatedAt": ISODate("2026-01-15")
})

// Query: Get all posts with author name
db.posts.find(
 {},
 { title: 1, "author.name": 1, createdAt: 1, _id: 0 }
)

// Update: Add comment
db.posts.updateOne(
 { _id: ObjectId("post1") },
 {
 $push: {
 comments: {
 id: ObjectId(),
 userId: ObjectId("user3"),
 userName: "Charlie",
 text: "Thanks for sharing!",
 createdAt: new Date()
 }
 }
 }
)

// Update: Increment views
db.posts.updateOne(
 { _id: ObjectId("post1") },
 { $inc: { views: 1 } }
)
```

---

## 3. Aggregation Pipeline (2 hours)

### 3.1 Aggregation Basics (30 minutes)

**What is Aggregation?**
- Process documents in stages
- Transform and combine data
- Similar to SQL GROUP BY, JOIN, etc.

**Basic structure:**
```javascript
db.collection.aggregate([
 { $stage1: { ... } },
 { $stage2: { ... } },
 { $stage3: { ... } }
])
```

**Common stages:**
- `$match` - Filter documents (like WHERE)
- `$group` - Group by field (like GROUP BY)
- `$project` - Select/transform fields (like SELECT)
- `$sort` - Sort results (like ORDER BY)
- `$limit` - Limit results
- `$skip` - Skip results
- `$lookup` - Join collections (like JOIN)
- `$unwind` - Deconstruct array

### 3.2 Aggregation Examples (90 minutes)

**Example dataset: Sales**

```javascript
db.sales.insertMany([
 {
 _id: 1,
 product: "Laptop",
 category: "Electronics",
 price: 1200,
 quantity: 2,
 region: "North",
 date: ISODate("2026-01-15")
 },
 {
 _id: 2,
 product: "Mouse",
 category: "Electronics",
 price: 25,
 quantity: 5,
 region: "South",
 date: ISODate("2026-01-16")
 },
 {
 _id: 3,
 product: "Keyboard",
 category: "Electronics",
 price: 75,
 quantity: 3,
 region: "North",
 date: ISODate("2026-01-17")
 },
 {
 _id: 4,
 product: "Desk",
 category: "Furniture",
 price: 400,
 quantity: 1,
 region: "East",
 date: ISODate("2026-01-18")
 }
])
```

**Example 1: Total revenue by category**

```javascript
db.sales.aggregate([
 {
 $group: {
 _id: "$category",
 totalRevenue: {
 $sum: { $multiply: ["$price", "$quantity"] }
 },
 totalItems: { $sum: "$quantity" }
 }
 },
 {
 $sort: { totalRevenue: -1 }
 }
])

// Result:
[
 {
 _id: "Electronics",
 totalRevenue: 2750,
 totalItems: 10
 },
 {
 _id: "Furniture",
 totalRevenue: 400,
 totalItems: 1
 }
]
```

**Example 2: Average price by region**

```javascript
db.sales.aggregate([
 {
 $group: {
 _id: "$region",
 avgPrice: { $avg: "$price" },
 count: { $sum: 1 }
 }
 }
])

// Result:
[
 { _id: "North", avgPrice: 637.5, count: 2 },
 { _id: "South", avgPrice: 25, count: 1 },
 { _id: "East", avgPrice: 400, count: 1 }
]
```

**Example 3: Filter then group**

```javascript
db.sales.aggregate([
 {
 $match: {
 category: "Electronics",
 price: { $gte: 50 }
 }
 },
 {
 $group: {
 _id: "$region",
 totalRevenue: {
 $sum: { $multiply: ["$price", "$quantity"] }
 }
 }
 }
])

// Result:
[
 { _id: "North", totalRevenue: 2625 }
]
```

**Example 4: Project (select specific fields)**

```javascript
db.sales.aggregate([
 {
 $project: {
 _id: 0,
 product: 1,
 revenue: { $multiply: ["$price", "$quantity"] },
 month: { $month: "$date" }
 }
 }
])

// Result:
[
 { product: "Laptop", revenue: 2400, month: 1 },
 { product: "Mouse", revenue: 125, month: 1 },
 { product: "Keyboard", revenue: 225, month: 1 },
 { product: "Desk", revenue: 400, month: 1 }
]
```

**Example 5: $unwind (deconstruct array)**

```javascript
// Sample data with array
db.orders.insertOne({
 _id: 1,
 customer: "Alice",
 items: [
 { product: "Laptop", price: 1200 },
 { product: "Mouse", price: 25 }
 ]
})

// Unwind items array
db.orders.aggregate([
 { $unwind: "$items" },
 {
 $project: {
 customer: 1,
 product: "$items.product",
 price: "$items.price"
 }
 }
])

// Result:
[
 { _id: 1, customer: "Alice", product: "Laptop", price: 1200 },
 { _id: 1, customer: "Alice", product: "Mouse", price: 25 }
]
```

**Example 6: $lookup (JOIN)**

```javascript
// Products collection
db.products.insertMany([
 { _id: 1, name: "Laptop", categoryId: 10 },
 { _id: 2, name: "Mouse", categoryId: 10 }
])

// Categories collection
db.categories.insertMany([
 { _id: 10, name: "Electronics" },
 { _id: 20, name: "Furniture" }
])

// Join products with categories
db.products.aggregate([
 {
 $lookup: {
 from: "categories",
 localField: "categoryId",
 foreignField: "_id",
 as: "category"
 }
 },
 {
 $unwind: "$category"
 },
 {
 $project: {
 name: 1,
 categoryName: "$category.name"
 }
 }
])

// Result:
[
 { _id: 1, name: "Laptop", categoryName: "Electronics" },
 { _id: 2, name: "Mouse", categoryName: "Electronics" }
]
```

**Complex Example: Sales Dashboard**

```javascript
db.sales.aggregate([
 // 1. Filter by date range
 {
 $match: {
 date: {
 $gte: ISODate("2026-01-01"),
 $lte: ISODate("2026-01-31")
 }
 }
 },
 
 // 2. Calculate revenue per sale
 {
 $addFields: {
 revenue: { $multiply: ["$price", "$quantity"] }
 }
 },
 
 // 3. Group by category and region
 {
 $group: {
 _id: {
 category: "$category",
 region: "$region"
 },
 totalRevenue: { $sum: "$revenue" },
 totalQuantity: { $sum: "$quantity" },
 avgPrice: { $avg: "$price" },
 count: { $sum: 1 }
 }
 },
 
 // 4. Sort by revenue
 {
 $sort: { totalRevenue: -1 }
 },
 
 // 5. Format output
 {
 $project: {
 _id: 0,
 category: "$_id.category",
 region: "$_id.region",
 totalRevenue: 1,
 totalQuantity: 1,
 avgPrice: { $round: ["$avgPrice", 2] },
 salesCount: "$count"
 }
 }
])
```

---

## 4. Indexing & Performance (2 hours)

### 4.1 Index Basics (30 minutes)

**Why indexing?**
- Faster queries
- Without index: MongoDB scans ALL documents (COLLSCAN)
- With index: MongoDB uses index (IXSCAN)

**Index types:**
- Single field index
- Compound index (multiple fields)
- Multikey index (array fields)
- Text index (full-text search)
- Geospatial index

**Create index:**

```javascript
// Single field index
db.users.createIndex({ email: 1 }) // 1 = ascending, -1 = descending

// Compound index
db.users.createIndex({ age: 1, city: 1 })

// Unique index
db.users.createIndex({ email: 1 }, { unique: true })

// Text index (full-text search)
db.posts.createIndex({ title: "text", content: "text" })

// List indexes
db.users.getIndexes()

// Drop index
db.users.dropIndex("email_1")
```

### 4.2 Query Performance Analysis (45 minutes)

**Explain query:**

```javascript
// Without index
db.users.find({ email: "alice@example.com" }).explain("executionStats")

// Result (without index):
{
 "executionStats": {
 "executionSuccess": true,
 "nReturned": 1,
 "executionTimeMillis": 15,
 "totalKeysExamined": 0,
 "totalDocsExamined": 10000, // Scanned all documents!
 "executionStages": {
 "stage": "COLLSCAN" // Collection scan (bad!)
 }
 }
}

// Create index
db.users.createIndex({ email: 1 })

// With index
db.users.find({ email: "alice@example.com" }).explain("executionStats")

// Result (with index):
{
 "executionStats": {
 "executionSuccess": true,
 "nReturned": 1,
 "executionTimeMillis": 2, // Much faster!
 "totalKeysExamined": 1,
 "totalDocsExamined": 1, // Only scanned 1 document!
 "executionStages": {
 "stage": "IXSCAN", // Index scan (good!)
 "indexName": "email_1"
 }
 }
}
```

**Compound index usage:**

```javascript
// Create compound index
db.users.createIndex({ age: 1, city: 1 })

// Good: Uses index (leftmost prefix)
db.users.find({ age: 25 }) // Uses index
db.users.find({ age: 25, city: "New York" }) // Uses index

// Bad: Doesn't use index
db.users.find({ city: "New York" }) // No index (city is not leftmost)

// Solution: Create separate index for city
db.users.createIndex({ city: 1 })
```

### 4.3 Performance Best Practices (45 minutes)

**1. Index commonly queried fields**

```javascript
// Bad: No index
db.users.find({ email: "alice@example.com" }) // COLLSCAN

// Good: Index on email
db.users.createIndex({ email: 1 })
db.users.find({ email: "alice@example.com" }) // IXSCAN
```

**2. Use projection (select only needed fields)**

```javascript
// Bad: Returns entire document
db.users.find({ email: "alice@example.com" })

// Good: Returns only needed fields
db.users.find(
 { email: "alice@example.com" },
 { name: 1, email: 1, _id: 0 }
)
```

**3. Use limit for large result sets**

```javascript
// Bad: Returns all documents
db.users.find({ age: { $gt: 25 } })

// Good: Limit results
db.users.find({ age: { $gt: 25 } }).limit(10)
```

**4. Use covered queries (index-only queries)**

```javascript
// Create compound index
db.users.createIndex({ email: 1, name: 1 })

// Covered query (all fields in index)
db.users.find(
 { email: "alice@example.com" },
 { name: 1, email: 1, _id: 0 }
).explain("executionStats")

// Result: totalDocsExamined: 0 (didn't access collection!)
```

**5. Avoid $ne and $nin (can't use index efficiently)**

```javascript
// Bad: Doesn't use index well
db.users.find({ status: { $ne: "active" } })

// Good: Use positive condition
db.users.find({ status: "inactive" })
```

**6. Use aggregation for complex queries**

```javascript
// Bad: Multiple queries
const users = db.users.find({ age: { $gt: 25 } })
// Then process in application code

// Good: Aggregation pipeline
db.users.aggregate([
 { $match: { age: { $gt: 25 } } },
 { $group: { _id: "$city", count: { $sum: 1 } } },
 { $sort: { count: -1 } }
])
```

---

## 5. Real-world Examples (3 hours)

### 5.1 Example: Blog System

**Collections:**

```javascript
// Users
db.users.insertMany([
 {
 _id: ObjectId("user1"),
 name: "Alice Johnson",
 email: "alice@example.com",
 role: "author",
 bio: "SRE at Company",
 joinedAt: ISODate("2020-01-15")
 },
 {
 _id: ObjectId("user2"),
 name: "Bob Smith",
 email: "bob@example.com",
 role: "reader",
 joinedAt: ISODate("2021-03-20")
 }
])

// Posts (hybrid pattern)
db.posts.insertMany([
 {
 _id: ObjectId("post1"),
 title: "Getting Started with MongoDB",
 slug: "getting-started-mongodb",
 content: "MongoDB is a NoSQL database...",
 author: {
 id: ObjectId("user1"),
 name: "Alice Johnson"
 },
 tags: ["mongodb", "nosql", "database"],
 comments: [
 {
 id: ObjectId("comment1"),
 userId: ObjectId("user2"),
 userName: "Bob Smith",
 text: "Great article!",
 createdAt: ISODate("2026-01-16")
 }
 ],
 stats: {
 views: 1250,
 likes: 42,
 shares: 15
 },
 status: "published",
 createdAt: ISODate("2026-01-15"),
 updatedAt: ISODate("2026-01-15")
 }
])

// Indexes
db.posts.createIndex({ slug: 1 }, { unique: true })
db.posts.createIndex({ "author.id": 1 })
db.posts.createIndex({ tags: 1 })
db.posts.createIndex({ status: 1, createdAt: -1 })
db.posts.createIndex({ title: "text", content: "text" })
```

**Common queries:**

```javascript
// 1. Get post by slug
db.posts.findOne({ slug: "getting-started-mongodb" })

// 2. Get all published posts (paginated)
db.posts.find({ status: "published" })
 .sort({ createdAt: -1 })
 .limit(10)
 .skip(0)

// 3. Get posts by author
db.posts.find({ "author.id": ObjectId("user1") })
 .sort({ createdAt: -1 })

// 4. Get posts by tag
db.posts.find({ tags: "mongodb" })
 .sort({ "stats.views": -1 })

// 5. Full-text search
db.posts.find(
 { $text: { $search: "MongoDB NoSQL" } },
 { score: { $meta: "textScore" } }
).sort({ score: { $meta: "textScore" } })

// 6. Increment views
db.posts.updateOne(
 { _id: ObjectId("post1") },
 { $inc: { "stats.views": 1 } }
)

// 7. Add comment
db.posts.updateOne(
 { _id: ObjectId("post1") },
 {
 $push: {
 comments: {
 id: ObjectId(),
 userId: ObjectId("user2"),
 userName: "Bob Smith",
 text: "Thanks for sharing!",
 createdAt: new Date()
 }
 }
 }
)

// 8. Get popular posts (aggregation)
db.posts.aggregate([
 { $match: { status: "published" } },
 {
 $project: {
 title: 1,
 author: 1,
 totalEngagement: {
 $add: ["$stats.views", "$stats.likes", "$stats.shares"]
 }
 }
 },
 { $sort: { totalEngagement: -1 } },
 { $limit: 10 }
])
```

### 5.2 Example: E-commerce System

**Collections:**

```javascript
// Products
db.products.insertMany([
 {
 _id: ObjectId("prod1"),
 sku: "LAP-001",
 name: "ThinkPad X1 Carbon",
 category: "Laptops",
 brand: "Lenovo",
 price: 1299.99,
 stock: 15,
 specs: {
 cpu: "Intel i7",
 ram: "16GB",
 storage: "512GB SSD"
 },
 images: [
 "https://example.com/img1.jpg",
 "https://example.com/img2.jpg"
 ],
 reviews: [
 {
 userId: ObjectId("user1"),
 rating: 5,
 text: "Excellent laptop!",
 date: ISODate("2026-01-10")
 }
 ],
 avgRating: 4.8,
 reviewCount: 142,
 status: "active",
 createdAt: ISODate("2025-06-01")
 }
])

// Orders
db.orders.insertMany([
 {
 _id: ObjectId("order1"),
 orderNumber: "ORD-2026-0001",
 customerId: ObjectId("user2"),
 customerName: "Bob Smith",
 customerEmail: "bob@example.com",
 items: [
 {
 productId: ObjectId("prod1"),
 sku: "LAP-001",
 name: "ThinkPad X1 Carbon",
 price: 1299.99,
 quantity: 1
 }
 ],
 subtotal: 1299.99,
 tax: 129.99,
 shipping: 0,
 total: 1429.98,
 shippingAddress: {
 street: "123 Main St",
 city: "New York",
 state: "NY",
 zip: "10001",
 country: "USA"
 },
 status: "delivered",
 statusHistory: [
 { status: "placed", date: ISODate("2026-01-15T10:00:00Z") },
 { status: "processing", date: ISODate("2026-01-15T11:00:00Z") },
 { status: "shipped", date: ISODate("2026-01-16T09:00:00Z") },
 { status: "delivered", date: ISODate("2026-01-18T14:00:00Z") }
 ],
 createdAt: ISODate("2026-01-15T10:00:00Z")
 }
])

// Indexes
db.products.createIndex({ sku: 1 }, { unique: true })
db.products.createIndex({ category: 1, price: 1 })
db.products.createIndex({ name: "text" })
db.orders.createIndex({ orderNumber: 1 }, { unique: true })
db.orders.createIndex({ customerId: 1, createdAt: -1 })
db.orders.createIndex({ status: 1 })
```

**Common queries:**

```javascript
// 1. Get product by SKU
db.products.findOne({ sku: "LAP-001" })

// 2. Search products by category and price range
db.products.find({
 category: "Laptops",
 price: { $gte: 1000, $lte: 1500 },
 status: "active"
}).sort({ price: 1 })

// 3. Full-text search products
db.products.find({ $text: { $search: "ThinkPad laptop" } })

// 4. Get customer's order history
db.orders.find({ customerId: ObjectId("user2") })
 .sort({ createdAt: -1 })

// 5. Update product stock after purchase
db.products.updateOne(
 { _id: ObjectId("prod1") },
 { $inc: { stock: -1 } }
)

// 6. Add product review
db.products.updateOne(
 { _id: ObjectId("prod1") },
 {
 $push: {
 reviews: {
 userId: ObjectId("user2"),
 rating: 5,
 text: "Great product!",
 date: new Date()
 }
 },
 $inc: { reviewCount: 1 }
 }
)

// 7. Sales report by category (aggregation)
db.orders.aggregate([
 { $match: { status: "delivered" } },
 { $unwind: "$items" },
 {
 $lookup: {
 from: "products",
 localField: "items.productId",
 foreignField: "_id",
 as: "product"
 }
 },
 { $unwind: "$product" },
 {
 $group: {
 _id: "$product.category",
 totalRevenue: {
 $sum: { $multiply: ["$items.price", "$items.quantity"] }
 },
 totalOrders: { $sum: 1 },
 totalItems: { $sum: "$items.quantity" }
 }
 },
 { $sort: { totalRevenue: -1 } }
])
```

---

## 6. MongoDB in Kubernetes & VM Cluster (3 hours)

### 6.1 MongoDB Replica Set on VMs (90 minutes)

**Architecture:**
```
mongodb-1 192.168.122.20 Primary
mongodb-2 192.168.122.21 Secondary
mongodb-3 192.168.122.22 Secondary
```

**Step 1: Create 3 VMs**

```bash
# Using KVM/libvirt
for i in 1 2 3; do
 virt-install \
 --name mongodb-$i \
 --ram 2048 \
 --vcpus 2 \
 --disk path=/var/lib/libvirt/images/mongodb-$i.qcow2,size=20 \
 --network network=default \
 --os-variant rocky9 \
 --location /path/to/rocky9.iso \
 --extra-args "console=ttyS0"
done
```

**Step 2: Install MongoDB on all VMs**

```bash
# On each VM (mongodb-1, mongodb-2, mongodb-3)

# Set hostname
hostnamectl set-hostname mongodb-1 # Change number for each VM

# Add MongoDB repo
sudo tee /etc/yum.repos.d/mongodb-org-7.0.repo <<EOF
[mongodb-org-7.0]
name=MongoDB Repository
baseurl=https://repo.mongodb.org/yum/redhat/9/mongodb-org/7.0/x86_64/
gpgcheck=1
enabled=1
gpgkey=https://pgp.mongodb.com/server-7.0.asc
EOF

# Install MongoDB
sudo dnf install -y mongodb-org

# Configure /etc/hosts (on all VMs)
cat >> /etc/hosts << EOF
192.168.122.20 mongodb-1
192.168.122.21 mongodb-2
192.168.122.22 mongodb-3
EOF
```

**Step 3: Configure Replica Set**

```bash
# Edit /etc/mongod.conf on each VM

# ON EACH VM:
sudo vi /etc/mongod.conf

# Change these lines:
net:
 port: 27017
 bindIp: 0.0.0.0 # Listen on all interfaces

replication:
 replSetName: "rs0" # Replica set name

# Start MongoDB on all VMs
sudo systemctl start mongod
sudo systemctl enable mongod

# Firewall (if enabled)
sudo firewall-cmd --permanent --add-port=27017/tcp
sudo firewall-cmd --reload
```

**Step 4: Initialize Replica Set (on PRIMARY - mongodb-1)**

```bash
# Connect to mongodb-1
mongosh

# Initialize replica set
rs.initiate({
 _id: "rs0",
 members: [
 { _id: 0, host: "mongodb-1:27017", priority: 2 },
 { _id: 1, host: "mongodb-2:27017", priority: 1 },
 { _id: 2, host: "mongodb-3:27017", priority: 1 }
 ]
})

# Check status
rs.status()

// Expected output:
// mongodb-1: PRIMARY
// mongodb-2: SECONDARY
// mongodb-3: SECONDARY
```

**Step 5: Test Replication**

```bash
# On PRIMARY (mongodb-1)
mongosh

use testdb
db.users.insertOne({ name: "Alice", age: 28 })

# On SECONDARY (mongodb-2)
mongosh

// Enable reading from secondary
rs.secondaryOk()

use testdb
db.users.find()
// Should see the same data!
```

**Step 6: Connection String**

```javascript
// Application connection string (connects to replica set)
mongodb://mongodb-1:27017,mongodb-2:27017,mongodb-3:27017/mydb?replicaSet=rs0

// Node.js example
const { MongoClient } = require('mongodb');

const uri = "mongodb://mongodb-1:27017,mongodb-2:27017,mongodb-3:27017/mydb?replicaSet=rs0";
const client = new MongoClient(uri);

async function run() {
 await client.connect();
 const db = client.db("mydb");
 const users = db.collection("users");
 
 await users.insertOne({ name: "Bob", age: 32 });
 console.log("Inserted!");
 
 await client.close();
}
run();
```

### 6.2 MongoDB in Kubernetes (90 minutes)

**Using MongoDB Community Operator**

**Step 1: Install Operator**

```bash
# Add MongoDB Helm repo
helm repo add mongodb https://mongodb.github.io/helm-charts
helm repo update

# Install Community Operator
helm install community-operator mongodb/community-operator \
 --namespace mongodb --create-namespace

# Wait for operator
kubectl get pods -n mongodb
```

**Step 2: Deploy MongoDB Replica Set**

```yaml
# mongodb-replicaset.yaml
apiVersion: mongodbcommunity.mongodb.com/v1
kind: MongoDBCommunity
metadata:
 name: mongodb-rs
 namespace: mongodb
spec:
 members: 3
 type: ReplicaSet
 version: "7.0.0"
 
 security:
 authentication:
 modes: ["SCRAM"]
 
 users:
 - name: admin
 db: admin
 passwordSecretRef:
 name: mongodb-admin-password
 roles:
 - name: clusterAdmin
 db: admin
 - name: userAdminAnyDatabase
 db: admin
 scramCredentialsSecretName: mongodb-admin-scram
 
 additionalMongodConfig:
 storage.wiredTiger.engineConfig.journalCompressor: zlib
 
 statefulSet:
 spec:
 volumeClaimTemplates:
 - metadata:
 name: data-volume
 spec:
 accessModes: ["ReadWriteOnce"]
 resources:
 requests:
 storage: 10Gi
```

**Create admin password secret:**

```bash
kubectl create secret generic mongodb-admin-password \
 --from-literal="password=MySecurePassword123" \
 --namespace mongodb

# Deploy MongoDB
kubectl apply -f mongodb-replicaset.yaml

# Wait for pods
kubectl get pods -n mongodb -w

# Expected:
# mongodb-rs-0 Running
# mongodb-rs-1 Running
# mongodb-rs-2 Running
```

**Step 3: Connect to MongoDB**

```bash
# Get connection string
kubectl get secret mongodb-rs-admin-admin \
 -n mongodb \
 -o jsonpath='{.data.connectionString\.standardSrv}' | base64 -d

# Port-forward (for testing)
kubectl port-forward svc/mongodb-rs-svc 27017:27017 -n mongodb

# Connect from local machine
mongosh "mongodb://admin:MySecurePassword123@localhost:27017/?directConnection=true"
```

**Step 4: Deploy Application**

```yaml
# app-deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
 name: myapp
 namespace: mongodb
spec:
 replicas: 2
 selector:
 matchLabels:
 app: myapp
 template:
 metadata:
 labels:
 app: myapp
 spec:
 containers:
 - name: myapp
 image: my-app:latest
 env:
 - name: MONGODB_URI
 value: "mongodb://admin:MySecurePassword123@mongodb-rs-0.mongodb-rs-svc.mongodb.svc.cluster.local:27017,mongodb-rs-1.mongodb-rs-svc.mongodb.svc.cluster.local:27017,mongodb-rs-2.mongodb-rs-svc.mongodb.svc.cluster.local:27017/mydb?replicaSet=mongodb-rs"
```

---

## 7. Final Project: Build Your Own App (2+ hours)

**Project ideas:**

1. **Task Manager API**
 - Collections: users, tasks
 - Features: CRUD, filtering, sorting, aggregation
 - Bonus: Authentication, file uploads

2. **Blog Platform**
 - Collections: users, posts, comments
 - Features: Full-text search, pagination, likes
 - Bonus: Categories, tags, author profiles

3. **E-commerce Backend**
 - Collections: products, orders, users
 - Features: Product catalog, shopping cart, order tracking
 - Bonus: Inventory management, reviews, recommendations

**Example: Task Manager API (Node.js + Express)**

```javascript
// app.js
const express = require('express');
const { MongoClient, ObjectId } = require('mongodb');

const app = express();
app.use(express.json());

const uri = "mongodb://localhost:27017";
const client = new MongoClient(uri);

let tasksCollection;

// Connect to MongoDB
async function connectDB() {
 await client.connect();
 const db = client.db("taskmanager");
 tasksCollection = db.collection("tasks");
 
 // Create indexes
 await tasksCollection.createIndex({ userId: 1, createdAt: -1 });
 await tasksCollection.createIndex({ status: 1 });
 
 console.log("Connected to MongoDB");
}

// GET /tasks - Get all tasks
app.get('/tasks', async (req, res) => {
 const { status, userId } = req.query;
 
 const filter = {};
 if (status) filter.status = status;
 if (userId) filter.userId = ObjectId(userId);
 
 const tasks = await tasksCollection.find(filter)
 .sort({ createdAt: -1 })
 .toArray();
 
 res.json(tasks);
});

// POST /tasks - Create task
app.post('/tasks', async (req, res) => {
 const { title, description, userId } = req.body;
 
 const task = {
 title,
 description,
 userId: ObjectId(userId),
 status: "pending",
 createdAt: new Date(),
 updatedAt: new Date()
 };
 
 const result = await tasksCollection.insertOne(task);
 res.json({ id: result.insertedId, ...task });
});

// PUT /tasks/:id - Update task
app.put('/tasks/:id', async (req, res) => {
 const { id } = req.params;
 const { status, title, description } = req.body;
 
 const update = { $set: { updatedAt: new Date() } };
 if (status) update.$set.status = status;
 if (title) update.$set.title = title;
 if (description) update.$set.description = description;
 
 await tasksCollection.updateOne(
 { _id: ObjectId(id) },
 update
 );
 
 res.json({ message: "Task updated" });
});

// DELETE /tasks/:id - Delete task
app.delete('/tasks/:id', async (req, res) => {
 const { id } = req.params;
 
 await tasksCollection.deleteOne({ _id: ObjectId(id) });
 
 res.json({ message: "Task deleted" });
});

// GET /tasks/stats - Task statistics
app.get('/tasks/stats', async (req, res) => {
 const stats = await tasksCollection.aggregate([
 {
 $group: {
 _id: "$status",
 count: { $sum: 1 }
 }
 }
 ]).toArray();
 
 res.json(stats);
});

// Start server
connectDB().then(() => {
 app.listen(3000, () => {
 console.log("Server running on http://localhost:3000");
 });
});
```

---

## Additional Resources

**Official Documentation:**
- MongoDB Manual: https://docs.mongodb.com/manual/
- MongoDB University: https://university.mongodb.com/ (free courses)

**Drivers:**
- Node.js: https://www.mongodb.com/docs/drivers/node/
- Python: https://www.mongodb.com/docs/drivers/pymongo/
- Go: https://www.mongodb.com/docs/drivers/go/

**Tools:**
- MongoDB Compass: GUI for MongoDB
- Studio 3T: Advanced MongoDB IDE
- NoSQLBooster: MongoDB GUI with IntelliSense

**Community:**
- MongoDB Community Forums: https://www.mongodb.com/community/forums/
- Stack Overflow: [mongodb] tag

---

## Learning Checklist

**Basics:**
- [ ] Install MongoDB (Docker or native)
- [ ] Use mongosh (MongoDB Shell)
- [ ] Understand documents, collections, databases
- [ ] Insert, find, update, delete documents

**CRUD & Modeling:**
- [ ] Query operators ($eq, $gt, $in, $and, $or)
- [ ] Update operators ($set, $push, $inc)
- [ ] Embedding vs referencing patterns
- [ ] Design blog/e-commerce data model

**Aggregation:**
- [ ] $match, $group, $project stages
- [ ] Calculate totals, averages, counts
- [ ] $lookup (join collections)
- [ ] $unwind (deconstruct arrays)

**Performance:**
- [ ] Create indexes
- [ ] Use explain() to analyze queries
- [ ] Understand COLLSCAN vs IXSCAN
- [ ] Optimize slow queries

**Real-world:**
- [ ] Build blog system data model
- [ ] Implement e-commerce queries
- [ ] Use aggregation for reports

**Deployment:**
- [ ] Set up VM replica set (3 nodes)
- [ ] Deploy MongoDB in Kubernetes
- [ ] Connect application to replica set

**Project:**
- [ ] Build API with MongoDB
- [ ] Implement CRUD operations
- [ ] Add aggregation queries
- [ ] Deploy to production

---

**You're ready to build scalable applications with MongoDB!** 
