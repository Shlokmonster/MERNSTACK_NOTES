# Complete MongoDB Guide - Zero to Advanced

## Table of Contents
1. [Introduction](#introduction)
2. [Installation and Setup](#installation-and-setup)
3. [Basic Concepts](#basic-concepts)
4. [CRUD Operations](#crud-operations)
5. [Data Types](#data-types)
6. [Querying Data](#querying-data)
7. [Indexing](#indexing)
8. [Aggregation Framework](#aggregation-framework)
9. [Data Modeling](#data-modeling)
10. [Schema Design Patterns](#schema-design-patterns)
11. [Transactions](#transactions)
12. [Replication](#replication)
13. [Sharding](#sharding)
14. [Security](#security)
15. [Performance Optimization](#performance-optimization)
16. [MongoDB Compass](#mongodb-compass)
17. [MongoDB Atlas](#mongodb-atlas)
18. [Backup and Restore](#backup-and-restore)
19. [Monitoring and Debugging](#monitoring-and-debugging)
20. [Advanced Features](#advanced-features)

---

## Introduction

MongoDB is a NoSQL, document-oriented database that stores data in flexible, JSON-like documents called BSON (Binary JSON). Unlike traditional relational databases, MongoDB doesn't require a predefined schema, making it highly scalable and flexible for modern applications.

### Key Features
- **Document-based**: Stores data in documents rather than tables
- **Schema-less**: No fixed schema required
- **Scalable**: Horizontal scaling through sharding
- **High Performance**: Optimized for read/write operations
- **Rich Query Language**: Powerful querying capabilities
- **Replication**: Built-in data redundancy and high availability

---

## Installation and Setup

### Windows Installation
1. Download MongoDB Community Server from official website
2. Run the installer and follow setup wizard
3. Choose "Complete" installation
4. Install MongoDB as a service
5. Add MongoDB bin directory to PATH environment variable

### macOS Installation
```bash
# Using Homebrew
brew tap mongodb/brew
brew install mongodb-community

# Start MongoDB
brew services start mongodb-community
```

### Linux Installation (Ubuntu)
```bash
# Import public key
wget -qO - https://www.mongodb.org/static/pgp/server-6.0.asc | sudo apt-key add -

# Create list file
echo "deb [ arch=amd64,arm64 ] https://repo.mongodb.org/apt/ubuntu focal/mongodb-org/6.0 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-6.0.list

# Update package database
sudo apt-get update

# Install MongoDB
sudo apt-get install -y mongodb-org

# Start MongoDB
sudo systemctl start mongod
sudo systemctl enable mongod
```

### Starting MongoDB
```bash
# Start MongoDB server
mongod

# Connect to MongoDB shell
mongo
# or for newer versions
mongosh
```

---

## Basic Concepts

### Document
A document is a basic unit of data in MongoDB, similar to a row in relational databases. Documents are stored in BSON format.

```javascript
{
  "_id": ObjectId("..."),
  "name": "John Doe",
  "age": 30,
  "email": "john@example.com",
  "address": {
    "street": "123 Main St",
    "city": "New York",
    "zipcode": "10001"
  },
  "hobbies": ["reading", "swimming", "coding"]
}
```

### Collection
A collection is a group of documents, similar to a table in relational databases. Collections don't enforce a schema.

### Database
A database contains multiple collections. MongoDB can have multiple databases.

### Key Terminology Comparison
| MongoDB | Relational Database |
|---------|-------------------|
| Database | Database |
| Collection | Table |
| Document | Row |
| Field | Column |
| Index | Index |
| Embedded Document | Join |

---

## CRUD Operations

### Create Operations

#### Insert One Document
```javascript
db.users.insertOne({
  name: "Alice Smith",
  age: 25,
  email: "alice@example.com",
  createdAt: new Date()
});
```

#### Insert Multiple Documents
```javascript
db.users.insertMany([
  {
    name: "Bob Johnson",
    age: 35,
    email: "bob@example.com"
  },
  {
    name: "Carol Williams",
    age: 28,
    email: "carol@example.com"
  }
]);
```

### Read Operations

#### Find All Documents
```javascript
db.users.find();
```

#### Find with Conditions
```javascript
// Find users older than 30
db.users.find({ age: { $gt: 30 } });

// Find user by name
db.users.find({ name: "Alice Smith" });

// Find with multiple conditions
db.users.find({ 
  age: { $gte: 25, $lte: 35 },
  email: { $exists: true }
});
```

#### Find One Document
```javascript
db.users.findOne({ name: "Alice Smith" });
```

#### Projection (Select specific fields)
```javascript
// Include only name and email
db.users.find({}, { name: 1, email: 1 });

// Exclude _id field
db.users.find({}, { name: 1, email: 1, _id: 0 });
```

### Update Operations

#### Update One Document
```javascript
db.users.updateOne(
  { name: "Alice Smith" },
  { $set: { age: 26, lastUpdated: new Date() } }
);
```

#### Update Multiple Documents
```javascript
db.users.updateMany(
  { age: { $lt: 30 } },
  { $set: { category: "young" } }
);
```

#### Replace Document
```javascript
db.users.replaceOne(
  { name: "Bob Johnson" },
  {
    name: "Bob Johnson",
    age: 36,
    email: "bob.johnson@example.com",
    status: "active"
  }
);
```

#### Upsert (Update or Insert)
```javascript
db.users.updateOne(
  { name: "David Brown" },
  { $set: { age: 40, email: "david@example.com" } },
  { upsert: true }
);
```

### Delete Operations

#### Delete One Document
```javascript
db.users.deleteOne({ name: "Alice Smith" });
```

#### Delete Multiple Documents
```javascript
db.users.deleteMany({ age: { $lt: 18 } });
```

#### Delete All Documents
```javascript
db.users.deleteMany({});
```

---

## Data Types

MongoDB supports various data types:

### Basic Data Types
```javascript
{
  // String
  name: "John Doe",
  
  // Number (Integer/Double)
  age: 30,
  salary: 50000.50,
  
  // Boolean
  isActive: true,
  
  // Date
  createdAt: new Date(),
  birthDate: ISODate("1990-01-01"),
  
  // Array
  hobbies: ["reading", "swimming"],
  scores: [85, 92, 78],
  
  // Object/Embedded Document
  address: {
    street: "123 Main St",
    city: "New York"
  },
  
  // Null
  middleName: null,
  
  // ObjectId
  _id: ObjectId("..."),
  
  // Binary Data
  profilePicture: BinData(0, "base64data..."),
  
  // Regular Expression
  pattern: /^[a-z]+$/i,
  
  // JavaScript Code
  script: function() { return this.name; }
}
```

### Special Data Types
```javascript
{
  // Decimal128 (for precise decimal calculations)
  price: NumberDecimal("19.99"),
  
  // Long (64-bit integer)
  bigNumber: NumberLong("9223372036854775807"),
  
  // Timestamp (internal MongoDB use)
  timestamp: Timestamp(1234567890, 1),
  
  // MinKey/MaxKey (for indexing)
  minValue: MinKey(),
  maxValue: MaxKey()
}
```

---

## Querying Data

### Comparison Operators
```javascript
// Equal
db.users.find({ age: 30 });

// Not equal
db.users.find({ age: { $ne: 30 } });

// Greater than
db.users.find({ age: { $gt: 25 } });

// Greater than or equal
db.users.find({ age: { $gte: 25 } });

// Less than
db.users.find({ age: { $lt: 35 } });

// Less than or equal
db.users.find({ age: { $lte: 35 } });

// In array
db.users.find({ age: { $in: [25, 30, 35] } });

// Not in array
db.users.find({ age: { $nin: [25, 30, 35] } });
```

### Logical Operators
```javascript
// AND (implicit)
db.users.find({ age: { $gte: 25 }, status: "active" });

// AND (explicit)
db.users.find({ 
  $and: [
    { age: { $gte: 25 } },
    { status: "active" }
  ]
});

// OR
db.users.find({ 
  $or: [
    { age: { $lt: 25 } },
    { status: "inactive" }
  ]
});

// NOT
db.users.find({ age: { $not: { $gte: 30 } } });

// NOR
db.users.find({ 
  $nor: [
    { age: { $lt: 25 } },
    { status: "inactive" }
  ]
});
```

### Element Operators
```javascript
// Field exists
db.users.find({ email: { $exists: true } });

// Field type
db.users.find({ age: { $type: "number" } });
db.users.find({ age: { $type: 16 } }); // 16 = 32-bit integer
```

### Array Operators
```javascript
// All elements match
db.users.find({ hobbies: { $all: ["reading", "swimming"] } });

// Array size
db.users.find({ hobbies: { $size: 3 } });

// Element match
db.users.find({ 
  "scores": { 
    $elemMatch: { $gte: 80, $lt: 90 } 
  }
});
```

### String Operators
```javascript
// Regular expression
db.users.find({ name: { $regex: /^john/i } });
db.users.find({ name: { $regex: "^john", $options: "i" } });

// Text search (requires text index)
db.users.find({ $text: { $search: "john doe" } });
```

### Geospatial Queries
```javascript
// Near
db.places.find({
  location: {
    $near: {
      $geometry: { type: "Point", coordinates: [-73.9667, 40.78] },
      $maxDistance: 1000
    }
  }
});

// Within
db.places.find({
  location: {
    $geoWithin: {
      $geometry: {
        type: "Polygon",
        coordinates: [[
          [-73.9857, 40.7484],
          [-73.9857, 40.7894],
          [-73.9397, 40.7894],
          [-73.9397, 40.7484],
          [-73.9857, 40.7484]
        ]]
      }
    }
  }
});
```

### Cursor Methods
```javascript
// Sort
db.users.find().sort({ age: 1 }); // Ascending
db.users.find().sort({ age: -1 }); // Descending

// Limit
db.users.find().limit(5);

// Skip
db.users.find().skip(10);

// Count
db.users.find({ age: { $gte: 25 } }).count();

// Pagination
db.users.find().skip(20).limit(10);
```

---

## Indexing

Indexes improve query performance by creating efficient data structures for fast lookups.

### Single Field Index
```javascript
// Create index on single field
db.users.createIndex({ name: 1 }); // Ascending
db.users.createIndex({ age: -1 }); // Descending
```

### Compound Index
```javascript
// Create compound index
db.users.createIndex({ name: 1, age: -1 });

// Query that uses compound index efficiently
db.users.find({ name: "John", age: { $gte: 25 } });
```

### Index Types

#### Text Index
```javascript
// Create text index
db.articles.createIndex({ title: "text", content: "text" });

// Text search
db.articles.find({ $text: { $search: "mongodb database" } });
```

#### Geospatial Index
```javascript
// 2dsphere index for GeoJSON objects
db.places.createIndex({ location: "2dsphere" });

// 2d index for legacy coordinate pairs
db.places.createIndex({ location: "2d" });
```

#### Partial Index
```javascript
// Index only documents that match filter
db.users.createIndex(
  { email: 1 },
  { partialFilterExpression: { age: { $gte: 18 } } }
);
```

#### TTL Index (Time To Live)
```javascript
// Documents expire after 3600 seconds
db.sessions.createIndex(
  { createdAt: 1 },
  { expireAfterSeconds: 3600 }
);
```

#### Unique Index
```javascript
// Ensure uniqueness
db.users.createIndex({ email: 1 }, { unique: true });
```

#### Sparse Index
```javascript
// Only index documents that have the field
db.users.createIndex({ phone: 1 }, { sparse: true });
```

### Index Management
```javascript
// List all indexes
db.users.getIndexes();

// Drop specific index
db.users.dropIndex({ name: 1 });
db.users.dropIndex("name_1");

// Drop all indexes (except _id)
db.users.dropIndexes();

// Rebuild indexes
db.users.reIndex();
```

### Index Performance
```javascript
// Explain query execution
db.users.find({ name: "John" }).explain("executionStats");

// Check index usage
db.users.find({ name: "John" }).hint({ name: 1 });
```

---

## Aggregation Framework

The aggregation framework provides powerful data processing and transformation capabilities.

### Basic Aggregation Pipeline
```javascript
db.orders.aggregate([
  { $match: { status: "completed" } },
  { $group: { _id: "$customerId", total: { $sum: "$amount" } } },
  { $sort: { total: -1 } },
  { $limit: 10 }
]);
```

### Aggregation Stages

#### $match - Filter documents
```javascript
db.orders.aggregate([
  { $match: { 
    orderDate: { $gte: ISODate("2023-01-01") },
    status: "completed"
  }}
]);
```

#### $project - Reshape documents
```javascript
db.users.aggregate([
  { $project: {
    name: 1,
    email: 1,
    fullName: { $concat: ["$firstName", " ", "$lastName"] },
    age: { $subtract: [{ $year: new Date() }, { $year: "$birthDate" }] }
  }}
]);
```

#### $group - Group documents
```javascript
db.orders.aggregate([
  { $group: {
    _id: "$category",
    totalSales: { $sum: "$amount" },
    avgOrder: { $avg: "$amount" },
    orderCount: { $sum: 1 },
    maxOrder: { $max: "$amount" },
    minOrder: { $min: "$amount" }
  }}
]);
```

#### $sort - Sort documents
```javascript
db.orders.aggregate([
  { $sort: { orderDate: -1, amount: 1 } }
]);
```

#### $limit and $skip - Pagination
```javascript
db.orders.aggregate([
  { $sort: { orderDate: -1 } },
  { $skip: 20 },
  { $limit: 10 }
]);
```

#### $unwind - Deconstruct arrays
```javascript
db.orders.aggregate([
  { $unwind: "$items" },
  { $group: {
    _id: "$items.productId",
    totalQuantity: { $sum: "$items.quantity" }
  }}
]);
```

#### $lookup - Join collections
```javascript
db.orders.aggregate([
  { $lookup: {
    from: "customers",
    localField: "customerId",
    foreignField: "_id",
    as: "customer"
  }},
  { $unwind: "$customer" }
]);
```

#### $addFields - Add new fields
```javascript
db.orders.aggregate([
  { $addFields: {
    totalWithTax: { $multiply: ["$total", 1.1] },
    orderYear: { $year: "$orderDate" }
  }}
]);
```

#### $replaceRoot - Replace document root
```javascript
db.orders.aggregate([
  { $replaceRoot: { newRoot: "$customer" } }
]);
```

### Complex Aggregation Examples

#### Sales Report
```javascript
db.sales.aggregate([
  { $match: { 
    date: { $gte: ISODate("2023-01-01"), $lt: ISODate("2024-01-01") }
  }},
  { $group: {
    _id: {
      year: { $year: "$date" },
      month: { $month: "$date" },
      product: "$productId"
    },
    totalSales: { $sum: "$amount" },
    totalQuantity: { $sum: "$quantity" }
  }},
  { $sort: { "_id.year": 1, "_id.month": 1, totalSales: -1 } },
  { $group: {
    _id: { year: "$_id.year", month: "$_id.month" },
    products: { $push: {
      product: "$_id.product",
      sales: "$totalSales",
      quantity: "$totalQuantity"
    }},
    monthlyTotal: { $sum: "$totalSales" }
  }}
]);
```

#### User Activity Analysis
```javascript
db.userActivity.aggregate([
  { $match: { timestamp: { $gte: ISODate("2023-01-01") } } },
  { $group: {
    _id: "$userId",
    sessions: { $sum: 1 },
    totalTime: { $sum: "$duration" },
    avgSessionTime: { $avg: "$duration" },
    actions: { $push: "$action" }
  }},
  { $addFields: {
    uniqueActions: { $size: { $setUnion: ["$actions", []] } }
  }},
  { $sort: { totalTime: -1 } }
]);
```

---

## Data Modeling

### Embedded Documents vs References

#### Embedded Documents (Denormalization)
```javascript
// User with embedded address
{
  _id: ObjectId("..."),
  name: "John Doe",
  email: "john@example.com",
  address: {
    street: "123 Main St",
    city: "New York",
    state: "NY",
    zipcode: "10001"
  },
  orders: [
    {
      orderId: ObjectId("..."),
      date: ISODate("2023-01-15"),
      total: 99.99,
      items: [
        { product: "Laptop", quantity: 1, price: 99.99 }
      ]
    }
  ]
}
```

#### References (Normalization)
```javascript
// User document
{
  _id: ObjectId("user1"),
  name: "John Doe",
  email: "john@example.com",
  addressId: ObjectId("addr1")
}

// Address document
{
  _id: ObjectId("addr1"),
  street: "123 Main St",
  city: "New York",
  state: "NY",
  zipcode: "10001"
}

// Order document
{
  _id: ObjectId("order1"),
  userId: ObjectId("user1"),
  date: ISODate("2023-01-15"),
  total: 99.99,
  items: [ObjectId("item1"), ObjectId("item2")]
}
```

### When to Embed vs Reference

#### Embed When:
- Data is accessed together
- One-to-one or one-to-few relationships
- Data doesn't change frequently
- Document size remains manageable (<16MB)

#### Reference When:
- Many-to-many relationships
- Data is accessed independently
- Data changes frequently
- Large amounts of data
- Need to query referenced data independently

### Schema Design Best Practices

1. **Design for your application's query patterns**
2. **Optimize for common use cases**
3. **Consider read-to-write ratio**
4. **Keep document size manageable**
5. **Use appropriate indexes**
6. **Plan for data growth**

---

## Schema Design Patterns

### 1. Polymorphic Pattern
```javascript
// Different types of vehicles in same collection
[
  {
    _id: ObjectId("..."),
    type: "car",
    make: "Toyota",
    model: "Camry",
    doors: 4,
    engine: "V6"
  },
  {
    _id: ObjectId("..."),
    type: "motorcycle",
    make: "Honda",
    model: "CBR600",
    engine: "600cc"
  }
]
```

### 2. Attribute Pattern
```javascript
// Flexible product attributes
{
  _id: ObjectId("..."),
  name: "Laptop",
  category: "Electronics",
  attributes: [
    { key: "brand", value: "Dell" },
    { key: "processor", value: "Intel i7" },
    { key: "ram", value: "16GB" },
    { key: "storage", value: "512GB SSD" }
  ]
}

// Create index for flexible querying
db.products.createIndex({ "attributes.key": 1, "attributes.value": 1 });
```

### 3. Bucket Pattern
```javascript
// Time-series data bucketing
{
  _id: ObjectId("..."),
  sensor_id: "sensor_001",
  timestamp: ISODate("2023-01-01T00:00:00Z"),
  measurements: [
    { time: ISODate("2023-01-01T00:00:00Z"), temperature: 23.5, humidity: 65 },
    { time: ISODate("2023-01-01T00:01:00Z"), temperature: 23.7, humidity: 64 },
    { time: ISODate("2023-01-01T00:02:00Z"), temperature: 23.9, humidity: 63 }
  ],
  count: 3
}
```

### 4. Outlier Pattern
```javascript
// Handle outliers separately
{
  _id: ObjectId("..."),
  book_id: "book_001",
  title: "MongoDB Guide",
  reviews: [
    { user: "user1", rating: 5, comment: "Great book!" },
    { user: "user2", rating: 4, comment: "Very helpful" }
  ],
  overflow: false
}

// When reviews exceed limit
{
  _id: ObjectId("..."),
  book_id: "book_001",
  title: "MongoDB Guide",
  reviews: [
    { user: "user1", rating: 5, comment: "Great book!" }
  ],
  overflow: true
}

// Overflow collection
{
  _id: ObjectId("..."),
  book_id: "book_001",
  reviews: [
    { user: "user100", rating: 3, comment: "Good book" },
    { user: "user101", rating: 4, comment: "Informative" }
  ]
}
```

### 5. Computed Pattern
```javascript
// Pre-computed values for performance
{
  _id: ObjectId("..."),
  movie_id: "movie_001",
  title: "The Matrix",
  ratings: [
    { user: "user1", rating: 5 },
    { user: "user2", rating: 4 },
    { user: "user3", rating: 5 }
  ],
  // Computed fields
  total_ratings: 3,
  average_rating: 4.67,
  rating_distribution: {
    "5": 2,
    "4": 1,
    "3": 0,
    "2": 0,
    "1": 0
  }
}
```

---

## Transactions

MongoDB supports multi-document ACID transactions (MongoDB 4.0+).

### Single Document Transactions
```javascript
// Single document operations are always atomic
db.accounts.updateOne(
  { _id: "account1" },
  { $inc: { balance: -100 } }
);
```

### Multi-Document Transactions
```javascript
// Start session
const session = db.getMongo().startSession();

try {
  // Start transaction
  session.startTransaction();
  
  // Operations within transaction
  db.accounts.updateOne(
    { _id: "account1" },
    { $inc: { balance: -100 } },
    { session: session }
  );
  
  db.accounts.updateOne(
    { _id: "account2" },
    { $inc: { balance: 100 } },
    { session: session }
  );
  
  // Commit transaction
  session.commitTransaction();
} catch (error) {
  // Rollback on error
  session.abortTransaction();
  throw error;
} finally {
  session.endSession();
}
```

### Transaction with Retry Logic
```javascript
function transferMoney(fromAccount, toAccount, amount) {
  const session = db.getMongo().startSession();
  
  try {
    session.startTransaction({
      readConcern: { level: "snapshot" },
      writeConcern: { w: "majority" }
    });
    
    // Check if sender has sufficient balance
    const sender = db.accounts.findOne(
      { _id: fromAccount },
      { session: session }
    );
    
    if (sender.balance < amount) {
      throw new Error("Insufficient funds");
    }
    
    // Transfer money
    db.accounts.updateOne(
      { _id: fromAccount },
      { $inc: { balance: -amount } },
      { session: session }
    );
    
    db.accounts.updateOne(
      { _id: toAccount },
      { $inc: { balance: amount } },
      { session: session }
    );
    
    // Log transaction
    db.transactions.insertOne({
      from: fromAccount,
      to: toAccount,
      amount: amount,
      timestamp: new Date()
    }, { session: session });
    
    session.commitTransaction();
    return { success: true, message: "Transfer completed" };
    
  } catch (error) {
    session.abortTransaction();
    return { success: false, error: error.message };
  } finally {
    session.endSession();
  }
}
```

---

## Replication

Replication provides data redundancy and high availability.

### Replica Set Setup
```javascript
// Initialize replica set
rs.initiate({
  _id: "myReplicaSet",
  members: [
    { _id: 0, host: "mongodb1:27017" },
    { _id: 1, host: "mongodb2:27017" },
    { _id: 2, host: "mongodb3:27017" }
  ]
});

// Check replica set status
rs.status();

// Add member
rs.add("mongodb4:27017");

// Remove member
rs.remove("mongodb4:27017");
```

### Read Preferences
```javascript
// Read from primary (default)
db.collection.find().readPref("primary");

// Read from secondary
db.collection.find().readPref("secondary");

// Read from primary preferred
db.collection.find().readPref("primaryPreferred");

// Read from secondary preferred
db.collection.find().readPref("secondaryPreferred");

// Read from nearest
db.collection.find().readPref("nearest");
```

### Write Concerns
```javascript
// Write to majority of nodes
db.collection.insertOne(
  { name: "John" },
  { writeConcern: { w: "majority", j: true, wtimeout: 5000 } }
);

// Write to specific number of nodes
db.collection.insertOne(
  { name: "John" },
  { writeConcern: { w: 2, j: true } }
);
```

---

## Sharding

Sharding distributes data across multiple machines for horizontal scaling.

### Shard Key Selection
```javascript
// Good shard keys have:
// - High cardinality
// - Even distribution
// - Non-monotonic values

// Examples of good shard keys:
// User ID (hashed)
db.users.createIndex({ _id: "hashed" });
sh.shardCollection("mydb.users", { _id: "hashed" });

// Compound shard key
db.orders.createIndex({ customerId: 1, orderDate: 1 });
sh.shardCollection("mydb.orders", { customerId: 1, orderDate: 1 });
```

### Sharding Setup
```bash
# Start config servers
mongod --configsvr --replSet configReplSet --port 27019

# Start mongos router
mongos --configdb configReplSet/config1:27019,config2:27019,config3:27019

# Start shard servers
mongod --shardsvr --replSet shard1 --port 27018
```

### Shard Management
```javascript
// Add shard
sh.addShard("shard1/shard1-server1:27018,shard1-server2:27018");

// Enable sharding for database
sh.enableSharding("mydb");

// Shard collection
sh.shardCollection("mydb.users", { _id: "hashed" });

// Check sharding status
sh.status();

// Move chunk
sh.moveChunk("mydb.users", { _id: ObjectId("...") }, "shard2");
```

---

## Security

### Authentication
```javascript
// Create admin user
use admin
db.createUser({
  user: "admin",
  pwd: "securePassword",
  roles: [
    { role: "userAdminAnyDatabase", db: "admin" },
    { role: "readWriteAnyDatabase", db: "admin" }
  ]
});

// Create database-specific user
use myapp
db.createUser({
  user: "appUser",
  pwd: "appPassword",
  roles: [
    { role: "readWrite", db: "myapp" }
  ]
});
```

### Role-Based Access Control
```javascript
// Built-in roles
// - read
// - readWrite
// - dbAdmin
// - userAdmin
// - clusterAdmin
// - root

// Custom role
db.createRole({
  role: "customRole",
  privileges: [
    {
      resource: { db: "myapp", collection: "users" },
      actions: ["find", "insert", "update"]
    }
  ],
  roles: []
});
```

### SSL/TLS Configuration
```bash
# Start MongoDB with SSL
mongod --sslMode requireSSL --sslPEMKeyFile /path/to/mongodb.pem

# Connect with SSL
mongo --ssl --sslCAFile /path/to/ca.pem --host hostname
```

### Field-Level Encryption
```javascript
// Client-side field level encryption
const clientEncryption = new ClientEncryption(keyVault, kmsProviders);

// Create data key
const dataKey = clientEncryption.createDataKey("local", {
  keyAltNames: ["sensitiveDataKey"]
});

// Encrypt field
const encryptedField = clientEncryption.encrypt(
  "sensitive data",
  {
    keyId: dataKey,
    algorithm: "AEAD_AES_256_CBC_HMAC_SHA_512-Deterministic"
  }
);

// Insert encrypted document
db.users.insertOne({
  name: "John Doe",
  ssn: encryptedField
});
```

### Auditing
```javascript
// Enable auditing in mongod.conf
auditLog:
  destination: file
  format: JSON
  path: /var/log/mongodb/audit.json
  filter: '{
    "atype": "authenticate",
    "param.mechanism": "SCRAM-SHA-1"
  }'
```

---

## Performance Optimization

### Query Optimization

#### Use Indexes Effectively
```javascript
// Create compound index for query patterns
db.users.createIndex({ status: 1, age: 1, name: 1 });

// Query matches index prefix
db.users.find({ status: "active", age: { $gte: 25 } });

// Check query execution plan
db.users.find({ status: "active" }).explain("executionStats");
```

#### Optimize Aggregation Pipelines
```javascript
// Move $match early in pipeline
db.orders.aggregate([
  { $match: { status: "completed" } }, // Filter early
  { $lookup: { from: "customers", localField: "customerId", foreignField: "_id", as: "customer" } },
  { $unwind: "$customer" },
  { $group: { _id: "$customer.region", total: { $sum: "$amount" } } }
]);

// Use $project to reduce document size
db.orders.aggregate([
  { $match: { status: "completed" } },
  { $project: { customerId: 1, amount: 1, _id: 0 } }, // Only needed fields
  { $group: { _id: "$customerId", total: { $sum: "$amount" } } }
]);
```

### Memory Management
```javascript
// Monitor memory usage
db.serverStatus().mem;

// Set cache size (in mongod.conf)
storage:
  wiredTiger:
    engineConfig:
      cacheSizeGB: 4
```

### Connection Pooling
```javascript
// Configure connection pool
const client = new MongoClient(uri, {
  maxPoolSize: 50,
  minPoolSize: 5,
  maxIdleTimeMS: 30000,
  serverSelectionTimeoutMS: 5000
});
```

### Profiling
```javascript
// Enable profiling for slow operations
db.setProfilingLevel(1, { slowms: 100 });

// View profiling data
db.system.profile.find().limit(5).sort({ ts: -1 });

// Profile specific operations
db.setProfilingLevel(2); // Profile all operations
```

---

## MongoDB Compass

MongoDB Compass is the official GUI for MongoDB.

### Key Features
- **Visual Query Builder**: Build queries with drag-and-drop interface
- **Schema Analysis**: Visualize document structure and field types
- **Index Management**: Create and manage indexes visually
- **Performance Monitoring**: Real-time performance metrics
- **Aggregation Pipeline Builder**: Visual pipeline construction

### Using Compass
1. **Connection**: Connect using connection string
2. **Browse Collections**: Navigate databases and collections
3. **Query Documents**: Use visual query builder or raw MongoDB syntax
4. **Analyze Schema**: View field types and value distributions
5. **Manage Indexes**: Create, modify, and drop indexes
6. **Build Aggregations**: Create complex pipelines visually

---

## MongoDB Atlas

MongoDB Atlas is the cloud-hosted MongoDB service.

### Getting Started
1. Create account at mongodb.com/cloud/atlas
2. Create cluster (M0 free tier available)
3. Configure network access and database users
4. Connect using connection string

### Atlas Features
- **Automated Scaling**: Automatic cluster scaling
- **Backup and Restore**: Point-in-time recovery
- **Performance Advisor**: Query optimization recommendations
- **Real-time Monitoring**: Comprehensive metrics and alerts
- **Global Clusters**: Multi-region deployments
- **Data Lake**: Query data across Atlas and AWS S3

### Atlas CLI
```bash
# Install Atlas CLI
curl -fsSL https://pgp.mongodb.com/server-6.0.asc | sudo apt-key add -

# Login
atlas auth login

# Create cluster
atlas clusters create myCluster --provider AWS --region US_EAST_1 --tier M10

# List clusters
atlas clusters list
```

---

## Backup and Restore

### mongodump and mongorestore
```bash
# Backup entire database
mongodump --host localhost:27017 --db myapp --out /backup/

# Backup specific collection
mongodump --host localhost:27017 --db myapp --collection users --out /backup/

# Backup with authentication
mongodump --host localhost:27017 --db myapp --username admin --password --authenticationDatabase admin --out /backup/

# Restore database
mongorestore --host localhost:27017 --db myapp /backup/myapp/

# Restore specific collection
mongorestore --host localhost:27017 --db myapp --collection users /backup/myapp/users.bson
```

### Replica Set Backup
```bash
# Backup from secondary to avoid impact on primary
mongodump --host secondary:27017 --db myapp --out /backup/

# Point-in-time backup using oplog
mongodump --host localhost:27017 --oplog --out /backup/

# Restore with oplog replay
mongorestore --host localhost:27017 --oplogReplay /backup/
```

### File System Snapshots
```bash
# Create consistent snapshot
db.fsyncLock();
# Create filesystem snapshot
# Unlock after snapshot
db.fsyncUnlock();
```

---

## Monitoring and Debugging

### Database Statistics
```javascript
// Database stats
db.stats();

// Collection stats
db.users.stats();

// Server status
db.serverStatus();

// Current operations
db.currentOp();

// Kill operation
db.killOp(opid);
```

### Performance Monitoring
```javascript
// Monitor slow queries
db.setProfilingLevel(1, { slowms: 100 });
db.system.profile.find().limit(5).sort({ ts: -1 });

// Index usage statistics
db.users.aggregate([{ $indexStats: {} }]);

// Collection scan ratio
db.serverStatus().metrics.queryExecutor;
```

### Debugging Tools
```javascript
// Explain query execution
db.users.find({ age: { $gte: 25 } }).explain("executionStats");

// Validate collection
db.users.validate();

// Check index consistency
db.users.validate({ full: true });

// Compact collection (reclaim space)
db.runCommand({ compact: "users" });
```

### Monitoring with mongostat and mongotop
```bash
# Real-time statistics
mongostat --host localhost:27017

# Top collections by activity
mongotop --host localhost:27017
```

---

## Advanced Features

### GridFS (Large File Storage)
```javascript
// Store file in GridFS
const fs = require('fs');
const { GridFSBucket } = require('mongodb');

const bucket = new GridFSBucket(db, { bucketName: 'uploads' });

// Upload file
fs.createReadStream('large-file.pdf')
  .pipe(bucket.openUploadStream('large-file.pdf', {
    metadata: { userId: 'user123', type: 'document' }
  }));

// Download file
bucket.openDownloadStreamByName('large-file.pdf')
  .pipe(fs.createWriteStream('downloaded-file.pdf'));

// Find files
db.uploads.files.find({ 'metadata.userId': 'user123' });
```

### Change Streams
```javascript
// Watch for changes
const changeStream = db.users.watch();

changeStream.on('change', (change) => {
  console.log('Change detected:', change);
});

// Watch specific operations
const changeStream = db.users.watch([
  { $match: { 'operationType': 'insert' } }
]);

// Watch with resume token
const changeStream = db.users.watch([], { resumeAfter: resumeToken });
```

### Capped Collections
```javascript
// Create capped collection
db.createCollection('logs', {
  capped: true,
  size: 1048576, // 1MB
  max: 1000 // Maximum 1000 documents
});

// Insert into capped collection
db.logs.insertOne({
  timestamp: new Date(),
  level: 'info',
  message: 'Application started'
});

// Tailable cursor for capped collection
const cursor = db.logs.find().tailable();
```

### Time Series Collections (MongoDB 5.0+)
```javascript
// Create time series collection
db.createCollection('weather', {
  timeseries: {
    timeField: 'timestamp',
    metaField: 'metadata',
    granularity: 'hours'
  }
});

// Insert time series data
db.weather.insertMany([
  {
    timestamp: ISODate("2023-01-01T00:00:00Z"),
    metadata: { sensorId: "sensor1", location: "NYC" },
    temperature: 20.5,
    humidity: 65
  },
  {
    timestamp: ISODate("2023-01-01T01:00:00Z"),
    metadata: { sensorId: "sensor1", location: "NYC" },
    temperature: 21.0,
    humidity: 64
  }
]);
```

### MongoDB Transactions with Causal Consistency
```javascript
// Causal consistency ensures read-your-writes
const session = client.startSession({ causalConsistency: true });

try {
  // Write operation
  await db.collection('users').insertOne(
    { name: 'John', email: 'john@example.com' },
    { session }
  );
  
  // Read operation will see the write
  const user = await db.collection('users').findOne(
    { name: 'John' },
    { session }
  );
  
} finally {
  await session.endSession();
}
```

### Partial Indexes for Conditional Indexing
```javascript
// Index only active users
db.users.createIndex(
  { email: 1 },
  { 
    partialFilterExpression: { status: "active" },
    unique: true 
  }
);

// Index only documents with specific field
db.products.createIndex(
  { category: 1, price: 1 },
  { partialFilterExpression: { price: { $exists: true } } }
);
```

### Wildcard Indexes
```javascript
// Index all fields in subdocuments
db.products.createIndex({ "attributes.$**": 1 });

// Query any attribute
db.products.find({ "attributes.brand": "Apple" });
db.products.find({ "attributes.color": "Red" });
```

### Collation for String Comparison
```javascript
// Create collection with collation
db.createCollection('names', {
  collation: {
    locale: 'en',
    strength: 1, // Case insensitive
    numericOrdering: true
  }
});

// Query with collation
db.names.find({ name: 'john' }).collation({ locale: 'en', strength: 1 });
```

---

## Best Practices Summary

### Schema Design
1. **Model for your queries** - Design documents based on how you'll query them
2. **Embed vs Reference** - Embed for one-to-few, reference for one-to-many
3. **Avoid large documents** - Keep documents under 16MB
4. **Use appropriate data types** - Choose the right type for each field

### Performance
1. **Create proper indexes** - Index fields used in queries
2. **Avoid large skip() operations** - Use cursor-based pagination
3. **Use projection** - Only retrieve fields you need
4. **Monitor slow queries** - Use profiling to identify bottlenecks

### Security
1. **Enable authentication** - Never run MongoDB without auth in production
2. **Use role-based access** - Grant minimal required permissions
3. **Enable SSL/TLS** - Encrypt data in transit
4. **Regular security updates** - Keep MongoDB version current

### Operations
1. **Use replica sets** - Always run with replication for production
2. **Plan for backup** - Regular backups with tested restore procedures
3. **Monitor performance** - Use proper monitoring tools
4. **Capacity planning** - Plan for data growth and scaling needs

---

## Common Patterns and Anti-Patterns

### Good Patterns
- **Atomic operations** - Use MongoDB's atomic operations when possible
- **Proper indexing** - Create indexes that support your query patterns
- **Appropriate embedding** - Embed related data that's accessed together
- **Connection pooling** - Reuse connections efficiently

### Anti-Patterns
- **Massive documents** - Avoid documents that grow without bounds
- **Inefficient queries** - Don't query without proper indexes
- **Unnecessary embedding** - Don't embed data that's rarely accessed
- **Poor shard key choice** - Avoid monotonic or low-cardinality shard keys

This comprehensive guide covers MongoDB from basic concepts to advanced features. Each section can be expanded based on specific needs and use cases. Remember to always test configurations and queries in a development environment before applying them to production systems.
