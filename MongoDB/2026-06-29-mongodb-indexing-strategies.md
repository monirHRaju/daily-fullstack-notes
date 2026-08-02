# MongoDB Indexing Strategies

## Overview
Indexes are essential for optimizing query performance in MongoDB. They allow the database to locate documents quickly without scanning entire collections. Understanding the different index types and when to use them is crucial for building scalable applications.

## Index Types

### Single Field Index
Indexes a single field, either in ascending or descending order.
```javascript
db.collection.createIndex({ age: 1 }) // ascending
db.collection.createIndex({ price: -1 }) // descending
```

### Compound Index
Indexes multiple fields together. The order of fields matters for query selectivity.
```javascript
db.collection.createIndex({ category: 1, price: -1 })
```

### Multikey Index
Automatically created when indexing a field that contains an array; MongoDB indexes each element of the array.

### Geospatial Index
Supports location-based queries using GeoJSON objects or legacy coordinate pairs.
```javascript
db.places.createIndex({ location: "2dsphere" })
```

### Text Index
Enables text search on string content.
```javascript
db.articles.createIndex({ content: "text" })
```

### Hashed Index
Useful for sharding based on a hashed field value to distribute data evenly.
```javascript
db.users.createIndex({ userId: "hashed" })
```

### TTL (Time-To-Live) Index
Automatically removes documents after a certain amount of time.
```javascript
db.sessions.createIndex({ createdAt: 1 }, { expireAfterSeconds: 3600 })
```

## Choosing the Right Index

- **Query Patterns**: Index fields that appear in query filters (`$eq`, `$range`, `$in`), sort, and projection.
- **Selectivity**: Index fields with high cardinality (many distinct values) for better selectivity.
- **Compound Queries**: Order fields in a compound index by equality matches first, then range sorts.
- **Covering Queries**: Design indexes that include all fields needed by a query so MongoDB can return results directly from the index without examining documents.

## Index Management Commands

```bash
# List all indexes on a collection
db.collection.getIndexes()

# Drop an index by name
db.collection.dropIndex("age_1")

# Drop all indexes except the default _id index
db.collection.dropIndexes()
```

## Performance Considerations

- Indexes improve read performance but add overhead to write operations (inserts, updates, deletes).
- Monitor index usage with the `$indexStats` aggregation stage.
- Use the `explain()` method to verify that queries are using the intended index.
- Balance the number of indexes: too many can degrade write performance; too few can slow reads.

## Example: Optimizing a Query

Suppose we have a collection `products` with fields `category`, `price`, and `stock`. A common query finds in-stock products in a given category sorted by price:

```javascript
db.products.find(
  { category: "electronics", stock: { $gt: 0 } },
  { name: 1, price: 1 }
).sort({ price: 1 })
```

An optimal compound index would be:
```javascript
db.products.createIndex({ category: 1, stock: 1, price: 1 })
```
This index supports the equality match on `category`, the range condition on `stock`, and the sort on `price`.

## Best Practices

- Use the `$hint` operator to force index selection during testing.
- Regularly review unused or duplicate indexes and remove them.
- Consider using partial indexes to index only a subset of documents, reducing index size.
- For frequently changing fields, avoid indexing if the selectivity is low.

## Resources

- [MongoDB Indexing Documentation](https://www.mongodb.com/docs/manual/indexes/)
- [Indexing Strategies Video Tutorial](https://www.mongodb.com/developer/products/mongodb/indexing-strategies/)
- [Performance Best Practices](https://www.mongodb.com/docs/manual/administration/production-checklist/)

---
*Daily Full-Stack Notes - MongoDB Indexing Strategies - 2026-06-29*