# MongoDB Fundamentals

## Overview
MongoDB is a popular NoSQL document database that stores data in flexible, JSON-like documents. It is designed for scalability, high availability, and performance, making it a popular choice for modern web applications.

## Key Concepts

- **Documents**: The basic unit of data in MongoDB, similar to JSON objects.
- **Collections**: Groups of documents, analogous to tables in relational databases.
- **Schema Flexibility**: Documents in a collection can have different fields and structures.
- **Indexes**: Special data structures that improve the speed of data retrieval.
- **Aggregation Pipeline**: A framework for data aggregation modeled on the concept of data processing pipelines.

## Basic Commands (Mongo Shell)

```bash
# Show all databases
show dbs

# Use or create a database
use mydatabase

# Insert a document
db.users.insertOne({ name: "Alice", age: 30, email: "alice@example.com" })

# Find documents
db.users.find({ age: { $gt: 25 } })

# Update a document
db.users.updateOne({ name: "Alice" }, { $set: { age: 31 } })

# Delete a document
db.users.deleteOne({ name: "Alice" })
```

## Indexing Example

Creating an index on the `email` field for faster lookups:

```javascript
db.users.createIndex({ email: 1 }) // 1 for ascending, -1 for descending
```

## Aggregation Pipeline Example

Calculate the average age of users per country:

```javascript
db.users.aggregate([
  { $group: { _id: "$country", avgAge: { $avg: "$age" } } },
  { $sort: { avgAge: -1 } }
]);
```

## Best Practices

- **Design your schema** according to your application's access patterns (embedding vs. referencing).
- **Use indexes** to support frequent queries and sort operations.
- **Validate data** using schema validation rules to maintain data quality.
- **Monitor performance** using MongoDB Atlas or MongoDB Cloud Manager.
- **Backup regularly** using `mongodump` or cloud-based snapshots.

## Resources

- [MongoDB Official Documentation](https://www.mongodb.com/docs/)
- [MongoDB University](https://university.mongodb.com/)
- [The Little MongoDB Book](https://openmymind.net/MongoDB.pdf)

---
*Daily Full-Stack Notes - MongoDB Fundamentals - 2026-06-29*