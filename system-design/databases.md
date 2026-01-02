# Database Design

## Types of Databases

### 1. Relational Databases (SQL)
Examples: PostgreSQL, MySQL, Oracle, SQL Server

**Characteristics:**
- Structured data with schema
- ACID transactions
- SQL query language
- Strong consistency
- Relationships via foreign keys

**Use cases:**
- Financial systems
- E-commerce
- CRM systems
- Any structured data

**Pros:**
- Data integrity
- ACID guarantees
- Mature ecosystem
- Complex queries
- Standardized

**Cons:**
- Harder to scale horizontally
- Schema changes difficult
- Can be slow for large datasets
- Fixed schema

### 2. NoSQL Databases

#### Key-Value Stores
Examples: Redis, Memcached, DynamoDB

**Structure:**
```
key1 → value1
key2 → value2
```

**Use cases:**
- Caching
- Session storage
- Shopping carts
- User preferences

**Pros:**
- Very fast reads/writes
- Simple model
- Easy to scale

**Cons:**
- Limited query capabilities
- No complex relationships
- No transactions (usually)

#### Document Databases
Examples: MongoDB, CouchDB, Firestore

**Structure:**
```json
{
  "_id": "123",
  "name": "John Doe",
  "email": "john@example.com",
  "orders": [
    {"id": "order1", "total": 99.99},
    {"id": "order2", "total": 149.99}
  ]
}
```

**Use cases:**
- Content management
- Catalogs
- User profiles
- Real-time analytics

**Pros:**
- Flexible schema
- Easy to scale horizontally
- Natural data representation
- Good for hierarchical data

**Cons:**
- No joins
- Eventual consistency
- Data duplication

#### Column-Family Stores
Examples: Cassandra, HBase, ScyllaDB

**Structure:**
```
RowKey1:
  Column Family 1:
    Column1: value
    Column2: value
  Column Family 2:
    Column3: value
```

**Use cases:**
- Time-series data
- IoT data
- Event logging
- Large-scale analytics

**Pros:**
- Excellent write performance
- Horizontal scalability
- Good for sparse data

**Cons:**
- Complex data modeling
- Limited query flexibility

#### Graph Databases
Examples: Neo4j, Amazon Neptune, ArangoDB

**Structure:**
```
(User1)-[FOLLOWS]->(User2)
(User1)-[LIKES]->(Post1)
(Post1)-[TAGGED]->(Tag1)
```

**Use cases:**
- Social networks
- Recommendation engines
- Fraud detection
- Knowledge graphs

**Pros:**
- Efficient relationship queries
- Intuitive for connected data
- Fast traversals

**Cons:**
- Limited scalability
- Specialized use cases
- Smaller ecosystem

### 3. Search Engines
Examples: Elasticsearch, Solr, Algolia

**Use cases:**
- Full-text search
- Log analytics
- Product search
- Auto-complete

**Pros:**
- Fast text search
- Rich query DSL
- Relevance scoring
- Analytics capabilities

**Cons:**
- Not a primary database
- Resource intensive
- Eventual consistency

## Database Design Principles

### 1. Normalization (SQL)

#### First Normal Form (1NF)
- Atomic values (no lists/arrays)
- Each column has unique name
- Order doesn't matter

**Before:**
```sql
Users:
  id | name     | phones
  1  | John     | 123-456, 789-012
```

**After:**
```sql
Users:
  id | name
  1  | John

Phones:
  id | user_id | phone
  1  | 1       | 123-456
  2  | 1       | 789-012
```

#### Second Normal Form (2NF)
- Must be in 1NF
- No partial dependencies

#### Third Normal Form (3NF)
- Must be in 2NF
- No transitive dependencies

### 2. Denormalization
Trade redundancy for performance.

**Example:**
```sql
-- Normalized: requires join
Orders:
  order_id | user_id | total
  
Users:
  user_id | username

-- Denormalized: no join needed
Orders:
  order_id | user_id | username | total
```

**When to denormalize:**
- Read-heavy workloads
- Performance critical
- Complex joins are slow
- Caching isn't sufficient

### 3. Indexing

#### Types of Indexes

**1. Primary Index**
```sql
CREATE TABLE users (
  id INT PRIMARY KEY,  -- Automatically indexed
  username VARCHAR(50)
);
```

**2. Secondary Index**
```sql
CREATE INDEX idx_username ON users(username);
CREATE INDEX idx_email ON users(email);
```

**3. Composite Index**
```sql
CREATE INDEX idx_name_age ON users(last_name, first_name, age);
```

**4. Unique Index**
```sql
CREATE UNIQUE INDEX idx_email ON users(email);
```

**5. Full-Text Index**
```sql
CREATE FULLTEXT INDEX idx_content ON posts(title, body);
```

#### Index Best Practices
- Index columns used in WHERE, JOIN, ORDER BY
- Consider composite indexes for multi-column queries
- Don't over-index (slows writes)
- Monitor index usage
- Use covering indexes when possible

### 4. Partitioning

Split table into smaller physical pieces.

#### Horizontal Partitioning (Sharding)
```sql
-- Partition by range
CREATE TABLE orders (
  order_id INT,
  order_date DATE,
  ...
) PARTITION BY RANGE (YEAR(order_date)) (
  PARTITION p2022 VALUES LESS THAN (2023),
  PARTITION p2023 VALUES LESS THAN (2024),
  PARTITION p2024 VALUES LESS THAN (2025)
);

-- Partition by hash
CREATE TABLE users (
  user_id INT,
  ...
) PARTITION BY HASH(user_id) PARTITIONS 4;
```

#### Vertical Partitioning
Split columns into separate tables.

```sql
-- Before
Users: id, name, email, bio, preferences, settings

-- After
Users: id, name, email
UserProfiles: id, bio, preferences
UserSettings: id, settings
```

## Replication

### Master-Slave Replication
```
Master (Read/Write) → Slave 1 (Read Only)
                    → Slave 2 (Read Only)
                    → Slave 3 (Read Only)
```

**Pros:**
- Scale reads
- Backup/failover
- Analytics on replica

**Cons:**
- Replication lag
- Write bottleneck
- Failover complexity

### Master-Master Replication
```
Master 1 (Read/Write) ←→ Master 2 (Read/Write)
```

**Pros:**
- No single point of failure
- Better write performance
- Geographic distribution

**Cons:**
- Conflict resolution
- Complexity
- Consistency challenges

## Transactions and Consistency

### ACID Properties
```sql
START TRANSACTION;

UPDATE accounts SET balance = balance - 100 WHERE id = 1;
UPDATE accounts SET balance = balance + 100 WHERE id = 2;

COMMIT;  -- Both succeed or both fail
```

### Isolation Levels

1. **Read Uncommitted**: Dirty reads possible
2. **Read Committed**: No dirty reads
3. **Repeatable Read**: Consistent reads within transaction
4. **Serializable**: Full isolation, slowest

### CAP Theorem Trade-offs

**Choose Consistency + Partition Tolerance (CP):**
- Examples: MongoDB (with strong consistency), HBase
- Use: Financial transactions, inventory

**Choose Availability + Partition Tolerance (AP):**
- Examples: Cassandra, DynamoDB
- Use: Social media, analytics, logging

## Data Modeling Patterns

### 1. Embedding (Document DB)
```json
{
  "user_id": "123",
  "name": "John",
  "addresses": [
    {"type": "home", "street": "123 Main St"},
    {"type": "work", "street": "456 Office Blvd"}
  ]
}
```

**When to embed:**
- 1-to-few relationships
- Data accessed together
- Bounded array size

### 2. Referencing (Document DB)
```json
// User document
{"_id": "user123", "name": "John"}

// Order documents
{"_id": "order1", "user_id": "user123", "total": 99.99}
{"_id": "order2", "user_id": "user123", "total": 149.99}
```

**When to reference:**
- 1-to-many relationships
- Large unbounded arrays
- Data updated independently

### 3. Bucketing (Time-Series)
```json
{
  "sensor_id": "sensor1",
  "date": "2024-01-01",
  "readings": [
    {"time": "00:00", "value": 23.5},
    {"time": "00:01", "value": 23.7},
    ...
  ]
}
```

## Query Optimization

### 1. Use EXPLAIN
```sql
EXPLAIN SELECT * FROM users WHERE email = 'john@example.com';
```

### 2. Avoid SELECT *
```sql
-- Bad
SELECT * FROM users WHERE id = 1;

-- Good
SELECT id, name, email FROM users WHERE id = 1;
```

### 3. Use Proper Joins
```sql
-- Use appropriate join type
INNER JOIN, LEFT JOIN, RIGHT JOIN

-- Add indexes on join columns
CREATE INDEX idx_order_user ON orders(user_id);
```

### 4. Limit Results
```sql
SELECT * FROM posts ORDER BY created_at DESC LIMIT 20;
```

### 5. Use Pagination
```sql
-- Offset-based (can be slow)
SELECT * FROM posts ORDER BY id LIMIT 20 OFFSET 40;

-- Cursor-based (better performance)
SELECT * FROM posts WHERE id > last_seen_id ORDER BY id LIMIT 20;
```

## Common Patterns

### 1. Soft Delete
```sql
ALTER TABLE users ADD COLUMN deleted_at TIMESTAMP NULL;

-- Delete
UPDATE users SET deleted_at = NOW() WHERE id = 1;

-- Query active users
SELECT * FROM users WHERE deleted_at IS NULL;
```

### 2. Audit Trail
```sql
CREATE TABLE audit_log (
  id INT PRIMARY KEY,
  table_name VARCHAR(50),
  record_id INT,
  action VARCHAR(20),
  old_values JSON,
  new_values JSON,
  user_id INT,
  timestamp TIMESTAMP
);
```

### 3. Optimistic Locking
```sql
ALTER TABLE products ADD COLUMN version INT DEFAULT 0;

-- Update with version check
UPDATE products 
SET quantity = quantity - 1, version = version + 1
WHERE id = 1 AND version = 5;
```

## Tips

- Choose database based on use case
- Understand your query patterns
- Index wisely
- Monitor slow queries
- Plan for growth
- Backup regularly
- Test failover procedures
- Consider read/write ratio
- Use connection pooling
- Implement caching layer
