# DBMS + SQL Interview Revision — MODULE 2: Advanced Concepts

This module builds on the foundational concepts from Module 1 and covers advanced database topics such as isolation levels, locks, concurrency, scaling, and real backend interview scenarios.

## 1. Transactions & Isolation Levels
(Content to be added...)

## 2. Locking Mechanisms
(Content to be added...)

## 3. Concurrency Control
(Content to be added...)

## 4. Scaling Databases (Sharding, Partitioning, Replication)
(Content to be added...)

---

## Best Practices in Advanced DBMS

### 1. Managing Transactions
Always keep transactions as short as possible to minimize lock duration and reduce the chances of deadlocks.

```sql
-- Bad Practice: Doing non-database work (like API calls) inside a transaction
BEGIN;
UPDATE accounts SET balance = balance - 100 WHERE id = 1;
-- <Wait 5 seconds for a third-party API call to finish>
UPDATE accounts SET balance = balance + 100 WHERE id = 2;
COMMIT;

-- Good Practice: Only wrap the necessary DB operations
BEGIN;
UPDATE accounts SET balance = balance - 100 WHERE id = 1;
UPDATE accounts SET balance = balance + 100 WHERE id = 2;
COMMIT;
```

### 2. Handling Deadlocks
In your application logic, implement retry mechanisms for transactions that might fail due to deadlocks. Use an appropriate isolation level (`READ COMMITTED` vs `SERIALIZABLE`) based on your business requirements to prevent reading uncommitted data or phantom reads.

### 3. Connection Pooling
Never open and close a new database connection for every query. Use connection pooling (like `pg-pool` for PostgreSQL or `HikariCP` for Java) to reuse connections efficiently.

```javascript
// Node.js example using pg
const { Pool } = require('pg');
const pool = new Pool({
  max: 20, // Max number of connections in the pool
  idleTimeoutMillis: 30000,
});

async function queryDB() {
  const client = await pool.connect();
  try {
    const res = await client.query('SELECT NOW()');
    console.log(res.rows[0]);
  } finally {
    client.release();
  }
}
```

### 4. Database Migrations
Always use version-controlled database migrations (e.g., Flyway, Liquibase, Prisma Migrate, or knex migrations) instead of running ad-hoc SQL scripts directly on the production database.

```sql
-- Example migration file: V1__Create_users_table.sql
CREATE TABLE users (
    id SERIAL PRIMARY KEY,
    username VARCHAR(50) UNIQUE NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

### 5. Proper Indexing for Scale
When dealing with large volumes of data, ensure that your foreign keys are indexed. Also, consider creating composite indexes if you frequently query multiple columns together.

```sql
-- Creating a composite index on frequently queried pairs
CREATE INDEX idx_orders_user_status ON orders(user_id, status);
```
