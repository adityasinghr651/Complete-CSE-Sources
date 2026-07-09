# Day 14: Advanced Database Concepts

*(Detailed explanation + proper definitions, from first principles — with simple language, intuition, diagrams, and production examples)*

---

## SECTION 1: INTUITION (Why Advanced Concepts Matter)

Think of a **giant library** storing millions of books:

1. **Basic design**:
   - You have shelves (tables).
   - Each shelf has books (rows).
   - Each book has pages (columns).

2. As the library grows:
   - You need better organization (**normalization**).
   - You need faster search (**indexing**).
   - You need to optimize how you fetch books (**query optimization**).
   - You need multiple copies of shelves for safety (**replication**).
   - You need to split huge shelves into parts (**partitioning**).

> **Simple Analogy:**  
> - **Normalization** = Organizing books properly and removing duplicates.  
> - **Indexing** = Creating a catalog for fast searching.  
> - **Query optimization** = Fetching data quickly while using fewer resources.  
> - **Replication** = Keeping multiple copies of the same shelf (for safety and speed).  
> - **Partitioning** = Breaking a huge shelf into smaller sections.

These are **advanced database concepts** used in production systems.

---

## SECTION 2: CORE DEFINITIONS & TOPICS

We’ll cover:
1. Normalization (1NF, 2NF, 3NF, BCNF)
2. Indexing Strategies
3. Query Optimization
4. Replication
5. Partitioning

---

## SECTION 3: NORMALIZATION

### 3.1 What is Normalization? (Definition)

**Normalization** = the process of organizing data in a database to:
- Reduce **redundancy** (duplicate data).
- Improve **data integrity** (consistency).
- Make schema easier to maintain.

Goal:  
Store each fact **once**, in the **right place**.

Key idea:  
Avoid:
- Repeating data.
- Inconsistent updates.
- Data anomalies (insert, update, delete anomalies).

---

### 3.2 Why Do We Need Normalization?

Without normalization:
- You store same data many times.
- If you update one copy, others remain old → inconsistency.
- Inserting or deleting data can cause errors.

Example:

Bad table (unnormalized):
```sql
orders (
  order_id,
  customer_name,
  customer_email,
  product_name,
  product_price,
  quantity
)
```

Problems:
- If customer name changes, you must update many rows.
- If product price changes, you update many rows.
- Duplicate customer/product data everywhere.

---

### 3.3 Normal Forms (1NF, 2NF, 3NF, BCNF)

Normalization is done in steps: **normal forms**.

#### 3.3.1 First Normal Form (1NF)

**Definition**:  
A table is in **1NF** if:
- Each column contains **atomic** (single) values.
- No repeating groups (e.g., multiple values in one column).
- Each row is unique.

Example:

Unnormalized:
```sql
students (
  id,
  name,
  courses  -- "Math, Science" in one column
)
```

1NF:
```sql
students (
  id,
  name,
  course
)
```
Each row has one course.

Rules for 1NF:
- No arrays/lists in a column.
- Each column has one value.
- Primary key exists.

---

#### 3.3.2 Second Normal Form (2NF)

**Definition**:  
A table is in **2NF** if:
- It is in 1NF.
- **No partial dependency**:
  - Every non-key column depends on **the whole primary key**, not just part of it.
  - This applies when primary key has **multiple columns**.

Example:
```sql
order_items (
  order_id,
  product_id,
  product_name,
  quantity,
  customer_name
)
```
Primary key: `(order_id, product_id)`.

Problems:
- `product_name` depends only on `product_id`, not full key.
- `customer_name` depends on `order_id`, not full key.

2NF solution:
Split into:
```sql
orders (
  order_id,
  customer_name
)

products (
  product_id,
  product_name
)

order_items (
  order_id,
  product_id,
  quantity
)
```
Now:
- `product_name` depends fully on `product_id`.
- `customer_name` depends fully on `order_id`.

---

#### 3.3.3 Third Normal Form (3NF)

**Definition**:  
A table is in **3NF** if:
- It is in 2NF.
- **No transitive dependency**:
  - Non-key columns should not depend on other non-key columns.
  - Only depend on primary key.

Example:
```sql
employees (
  employee_id,
  name,
  department_id,
  department_name,
  department_location
)
```
- `department_name` depends on `department_id`.
- `department_location` depends on `department_id`.
- But `department_id` is not primary key → transitive dependency.

3NF solution:
```sql
employees (
  employee_id,
  name,
  department_id
)

departments (
  department_id,
  department_name,
  department_location
)
```
Now:
- `department_name` and `department_location` depend directly on `department_id` (primary key of departments).

---

#### 3.3.4 BCNF (Boyce-Codd Normal Form)

**Definition**:  
A table is in **BCNF** if:
- It is in 3NF.
- Every **determinant** is a **candidate key**.
  - If `A → B` (A determines B), then A must be a candidate key.

BCNF is used for complex cases with multiple overlapping keys.
Usually, 3NF is enough for most apps. BCNF is for advanced schemas.

---

### 3.4 Practical Normalization for Apps

For web apps:
- Aim for **3NF**.
- Don’t over-normalize to BCNF unless needed.
- Sometimes you intentionally **denormalize** for performance:
  - Store extra data to avoid joins.
  - Example: store `user_name` in `posts` table to avoid joining `users` every time.

Tradeoff:
- Normalization: good for data integrity.
- Denormalization: good for performance.

---

## SECTION 4: INDEXING STRATEGIES

### 4.1 What is an Index? (Definition)

**Index** = a data structure that speeds up data retrieval.
- Like a book’s index:
  - Book index tells you: “Chapter 3 starts at page 50”.
- DB index tells you:
  - “Row with email = raj@example.com is at disk page 1024”.

Without index:
- DB searches all rows (slow).

With index:
- DB jumps directly to relevant rows (fast).

---

### 4.2 How Indexes Work (Conceptual)

Common index type: **B-tree**.
- Sorted structure.
- fast for:
  - Equality search (`WHERE email = ...`).
  - Range search (`WHERE age > 20`).
  - Sorting (`ORDER BY`).

Example:
```sql
CREATE INDEX idx_users_email ON users(email);
```
Now:
```sql
SELECT * FROM users WHERE email = 'raj@example.com';
```
Faster because of index.

---

### 4.3 Types of Indexes

#### 1. Single-Column Index
```sql
CREATE INDEX idx_orders_user_id ON orders(user_id);
```
For one column.

#### 2. Multi-Column Index
```sql
CREATE INDEX idx_orders_user_status ON orders(user_id, status);
```
For queries like:
```sql
SELECT * FROM orders
WHERE user_id = 42 AND status = 'pending';
```
Order matters:
- `(user_id, status)` is different from `(status, user_id)`.

#### 3. Unique Index
```sql
CREATE UNIQUE INDEX idx_users_email ON users(email);
```
Ensures:
- No duplicate emails.
- Also speeds up search.

#### 4. Composite Index
Same as multi-column index.

#### 5. Partial Index
Index only some rows.
Example:
```sql
CREATE INDEX idx_orders_pending ON orders(user_id)
WHERE status = 'pending';
```
Only indexes pending orders. Smaller index → faster.

#### 6. Full-Text Index
For text search.
Example (PostgreSQL):
```sql
CREATE INDEX idx_posts_title_ft ON posts USING gin(title);
```
For:
```sql
SELECT * FROM posts
WHERE title ILIKE '%search%';
```

---

### 4.4 When to Use Indexes

Use indexes on:
- Columns used in:
  - `WHERE` clauses.
  - `JOIN` conditions.
  - `ORDER BY`.
  - `GROUP BY`.

Don’t over-index:
- Indexes take space.
- Indexes slow down writes (INSERT, UPDATE, DELETE) because index must be updated.

Rule:
- Index columns that are **frequently queried**.
- Avoid indexes on columns rarely used.

---

## SECTION 5: QUERY OPTIMIZATION

### 5.1 What is Query Optimization? (Definition)

**Query Optimization** = the process of improving SQL queries so they:
- Run faster.
- Use less CPU, memory, I/O.
- Cause less lock contention.

Goal:  
Get the **same result** with **less work**.

---

### 5.2 How Query Optimization Works

Database has an **query optimizer**:
- Takes your SQL.
- Generates multiple possible execution plans.
- Chooses the best plan based on:
  - Indexes.
  - Table sizes.
  - Statistics.

Example:
```sql
SELECT * FROM users WHERE email = 'raj@example.com';
```
Optimizer:
- Checks if index exists on `email`.
- If yes → use index.
- If no → scan all rows.

---

### 5.3 Common Optimization Techniques

#### 1. Use Indexes
Already covered.

#### 2. Avoid `SELECT *`
Instead:
```sql
SELECT id, email, name FROM users WHERE email = 'raj@example.com';
```
- Only fetch needed columns.
- Less I/O.

#### 3. Use `WHERE` Filters Early
Filter before joining.
Example:
```sql
SELECT u.name, COUNT(o.id) AS order_count
FROM users u
LEFT JOIN orders o ON o.user_id = u.id AND o.status = 'pending'
WHERE u.created_at > '2026-01-01'
GROUP BY u.id;
```
Filtering early reduces data.

#### 4. Avoid Nested/Subqueries When Possible
Subqueries can be slow.
Example:

Bad:
```sql
SELECT * FROM users
WHERE id IN (
  SELECT user_id FROM orders WHERE status = 'pending'
);
```

Better:
```sql
SELECT u.* FROM users u
JOIN orders o ON o.user_id = u.id
WHERE o.status = 'pending';
```

#### 5. Use `EXPLAIN` to Analyze Queries
```sql
EXPLAIN SELECT * FROM users WHERE email = 'raj@example.com';
```
Shows:
- How DB executes query.
- Which indexes used.
- Estimated cost.

Use this to debug slow queries.

#### 6. Avoid Functions on Indexed Columns in `WHERE`
Example:
```sql
SELECT * FROM users
WHERE LOWER(email) = 'raj@example.com';
```
If index is on `email`, `LOWER(email)` may not use it.

Better:
- Store lowercase emails.
- Or use index on `LOWER(email)` if supported.

---

## SECTION 6: REPLICATION

### 6.1 What is Replication? (Definition)

**Replication** = copying data from one database (primary) to other databases (replicas) automatically.

Goals:
- **Durability**: multiple copies → data safer.
- **Performance**: read from replicas, not only primary.
- **Availability**: if primary fails, replicas can serve.

---

### 6.2 Types of Replication

#### 1. Master-Slave (Primary-Replica)
- One **primary** (master).
- Multiple **replicas** (slaves).
- Primary handles writes.
- Replicas handle reads.

Example:
```text
Primary (writes)
  |
  | Stream changes
  v
Replica 1 (reads)
Replica 2 (reads)
```
Use for: Read-heavy apps.

#### 2. Master-Master (Multi-Master)
- Multiple primaries.
- All can handle writes.
- More complex.

Used in: Distributed systems, global apps.

#### 3. Synchronous vs Asynchronous
- **Synchronous**:
  - Write confirmed on primary + replica together.
  - Safe, but slower.
- **Asynchronous**:
  - Primary confirms write, then replicates later.
  - Faster, but small risk of lost data if primary fails before replication.

---

### 6.3 Replication in Practice

Example: PostgreSQL replication:
- Primary node.
- Replica nodes.
- Data streamed from primary to replicas.

Use cases:
- Read-heavy APIs.
- Backups from replicas.
- Failover if primary fails.

---

## SECTION 7: PARTITIONING

### 7.1 What is Partitioning? (Definition)

**Partitioning** = splitting a large table into smaller, more manageable parts.

Goals:
- Improve performance.
- Reduce I/O.
- Make maintenance easier.

Example:
Orders table with 100 million rows.
Partition by year:
```sql
orders_2024
orders_2025
orders_2026
```
Each partition is a smaller table.

---

### 7.2 Types of Partitioning

#### 1. Horizontal Partitioning
Split rows into groups.
- Based on:
  - Range (e.g., by date).
  - List (e.g., by region).

Example:
```sql
orders_2024: all orders with year = 2024
orders_2025: all orders with year = 2025
```

#### 2. Vertical Partitioning
Split columns into groups.
- Example:
  - Table 1: `id`, `email`, `name`.
  - Table 2: `id`, `large_text_field`, `binary_data`.

Use when:
- Some columns are large and rarely accessed.

---

### 7.3 Partitioning in Practice

Example (PostgreSQL):
```sql
CREATE TABLE orders (
  id BIGINT,
  user_id BIGINT,
  created_at TIMESTAMP
) PARTITION BY RANGE (created_at);

CREATE TABLE orders_2024 PARTITION OF orders
FOR VALUES FROM ('2024-01-01') TO ('2025-01-01');

CREATE TABLE orders_2025 PARTITION OF orders
FOR VALUES FROM ('2025-01-01') TO ('2026-01-01');
```

Query:
```sql
SELECT * FROM orders
WHERE created_at >= '2024-01-01' AND created_at < '2025-01-01';
```
DB only scans `orders_2024`.

---

## SECTION 8: COMMON MISTAKES

1. **No normalization**  
   - Duplicate data, inconsistencies. Aim for 3NF.
2. **Over-normalization**  
   - Too many joins, slow queries. Sometimes denormalize for performance.
3. **No indexes**  
   - Slow queries. Index frequently queried columns.
4. **Too many indexes**  
   - Slow writes, wasted space. Index only needed columns.
5. **Not using EXPLAIN**  
   - Can’t debug slow queries. Always use `EXPLAIN`.
6. **No replication**  
   - Single point of failure. Use primary-replica for safety + read scaling.
7. **No partitioning for huge tables**  
   - Slow queries, hard maintenance. Partition large tables.

---

## SECTION 9: INTERVIEW-STYLE QUESTIONS

1. What is normalization? Why do we need it?
2. Explain 1NF, 2NF, 3NF with examples.
3. What is a completely normalized table (3NF)?
4. What is an index? Why do we use indexes?
5. What are different types of indexes?
6. When should you use a multi-column index?
7. What is query optimization? Give 5 techniques.
8. How do you analyze a slow query?
9. What is replication? What are its benefits?
10. What is partitioning? When do you use it?

---

## SECTION 10: REVISION NOTES (CHEAT SHEET)

- **Normalization**:
  - 1NF: atomic values, no repeating groups.
  - 2NF: no partial dependency (on full key).
  - 3NF: no transitive dependency.
  - Goal: reduce redundancy, improve integrity.
- **Indexing**:
  - B-tree, single/multi, unique, partial, full-text.
  - Index `WHERE`, `JOIN`, `ORDER BY`, `GROUP BY` columns.
- **Query Optimization**:
  - Use indexes.
  - Avoid `SELECT *`.
  - Avoid subqueries.
  - Use `EXPLAIN`.
- **Replication**:
  - Primary-replica, multi-master.
  - Benefits: durability, performance, availability.
- **Partitioning**:
  - Horizontal (range, list), vertical.
  - Benefits: performance, maintenance.

---

## SECTION 11: HANDS-ON ASSIGNMENT

Design and optimize a database for an **e-commerce app**:

### Requirements
- Users: id, email, name, created_at.
- Orders: id, user_id, total, status, created_at.
- Order items: id, order_id, product_id, quantity, price.
- Products: id, name, price, category, created_at.

### Tasks
1. Draw schema in **3NF**.
2. Define relationships & constraints.
3. Add indexes on:
   - `users.email`.
   - `orders.user_id`.
   - `orders.created_at`.
   - `order_items.order_id`.
   - `products.category`.
4. Write optimized query:
   - “Get all pending orders for user 42 with order items.”
5. Use `EXPLAIN` on that query (if you can run it).
6. Design partitioning for `orders` table by year.

---

## SECTION 12: MINI PROJECT

Build an **optimized e-commerce DB**:

- Use PostgreSQL or MySQL.
- Create normalized schema (3NF).
- Add indexes.
- Write queries:
  - Get user orders.
  - Get order with items.
  - Get products by category.
- Use `EXPLAIN` to optimize slow queries.
- Partition `orders` by year.

---

## ACTIVE LEARNING – YOUR TURN

Answer these in your own words:

1. What is **normalization**? Why do we need it?
2. Explain **1NF, 2NF, 3NF** with examples.
3. What is an **index**? Why do we use indexes?
4. What are different types of indexes? Give examples.
5. What is **query optimization**? Give 3–5 techniques.
6. How do you analyze a slow query?
7. What is **replication**? What are its benefits?
8. What is **partitioning**? When do you use it?
9. For an e-commerce app:
   - Design schema in 3NF for users, orders, order_items, products.
   - Define relationships & constraints.
   - Add indexes on key columns.
