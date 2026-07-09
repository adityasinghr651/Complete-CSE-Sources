# Day 16: Indexes (B-Tree, Hash Index, Composite Index)  
*(Detailed, step-by-step, from first principles — with definitions, simple language, intuition, diagrams, and production examples)*

---

## SECTION 1: INTUITION (Why Indexes Matter)

Think of a **giant book without page numbers**:

- You search for "Chapter 5".
- You must read every page one by one.
- Very slow.

Now add **page numbers + index at the back**:

- Index says: "Chapter 5 → page 50".
- You jump directly to page 50.
- Very fast.

**Database index** is like that page index:

- Without index: DB searches all rows (slow).
- With index: DB jumps directly to relevant rows (fast).

> **Simple Analogy:**  
> - **Index** = A "catalog" for the database to perform fast searches.  
> - It says: "Email = raj@example.com → row 1024".  
> - The DB doesn't have to scan the entire table.

---

## SECTION 2: CORE DEFINITIONS

### 2.1 What is an Index? (Definition)

**Index** = a data structure that speeds up data retrieval.

- Like a book's index:
  - Book index tells you: "Chapter 3 starts at page 50".
- DB index tells you:
  - "Row with email = raj@example.com is at disk page 1024".

Without index:
- DB searches all rows (**full table scan**).

With index:
- DB jumps directly to relevant rows (**index scan**).

---

### 2.2 How Indexes Work (Conceptual)

Common index type: **B-tree**.
- Sorted structure.
- Fast for:
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

## SECTION 3: B-TREE INDEX

### 3.1 Definition

**B-Tree Index** = a balanced tree data structure where:
- Each node has multiple keys.
- Tree is balanced (all leaves at same depth).
- Fast for equality, range, and sorting.

B-Tree is the **most common** index type in databases (PostgreSQL, MySQL).

---

### 3.2 How B-Tree Works

Imagine sorting numbers:

```text
Root: 50
  Left: 10, 20, 30, 40
  Right: 60, 70, 80, 90
```

Search for 30:
- Start at root (50).
- 30 < 50 → go left.
- Find 30 in left node.

Very fast (O(log n)).

---

### 3.3 When to Use B-Tree Index

Use B-Tree for:
- Equality: `WHERE email = '...'`.
- Range: `WHERE age > 20`.
- Sorting: `ORDER BY created_at`.
- Prefix search: `WHERE name LIKE 'Raj%'`.

---

### 3.4 Example

```sql
CREATE INDEX idx_orders_created_at ON orders(created_at);
```

Query:

```sql
SELECT * FROM orders
WHERE created_at > '2026-01-01'
ORDER BY created_at;
```

Fast because of B-Tree index.

---

## SECTION 4: HASH INDEX

### 4.1 Definition

**Hash Index** = an index that uses a **hash table**:
- Key → hash value → row location.
- Fast for **equality** search.
- NOT fast for range or sorting.

---

### 4.2 How Hash Index Works

Example:

```text
email → hash("raj@example.com") → row 1024
```

Search for "raj@example.com":
- Hash the email.
- Jump directly to row 1024.

Very fast (O(1)).

---

### 4.3 When to Use Hash Index

Use Hash for:
- Equality only: `WHERE email = '...'`.

Don't use for:
- Range: `WHERE age > 20` (Hash doesn't support).
- Sorting: `ORDER BY created_at` (Hash doesn't support).

---

### 4.4 Example

```sql
CREATE INDEX idx_users_email_hash ON users(email) USING HASH;
```

Query:

```sql
SELECT * FROM users WHERE email = 'raj@example.com';
```

Fast for equality.

---

## SECTION 5: COMPOSITE INDEX

### 5.1 Definition

**Composite Index** = an index on **multiple columns**.

Example:

```sql
CREATE INDEX idx_orders_user_status ON orders(user_id, status);
```

---

### 5.2 When to Use Composite Index

Use for queries like:

```sql
SELECT * FROM orders
WHERE user_id = 42 AND status = 'pending';
```

Index `(user_id, status)` helps.

---

### 5.3 Order Matters

Composite index `(user_id, status)` is **different** from `(status, user_id)`.

Rule:
- Leftmost column is most important.
- Query should use leftmost column first.

Example:
Index: `(user_id, status)`

Good query:
```sql
WHERE user_id = 42 AND status = 'pending'
```

Less effective:
```sql
WHERE status = 'pending'
```
(because `user_id` is not used).

---

### 5.4 Example

```sql
CREATE INDEX idx_orders_user_status ON orders(user_id, status);
```

Query:

```sql
SELECT * FROM orders
WHERE user_id = 42 AND status = 'pending';
```

Fast because of composite index.

---

## SECTION 6: WHEN TO USE INDEXES

Use indexes on:
- Columns used in:
  - `WHERE` clauses.
  - `JOIN` conditions.
  - `ORDER BY`.
  - `GROUP BY`.

Don't over-index:
- Indexes take space.
- Indexes slow down writes (INSERT, UPDATE, DELETE) because index must be updated.

Rule:
- Index columns that are **frequently queried**.
- Avoid indexes on columns rarely used.

---

## SECTION 7: COMMON MISTAKES

1. **No indexes on foreign keys**  
   - Slow JOINs. Index foreign key columns.
2. **Too many indexes**  
   - Slow writes, wasted space. Index only needed columns.
3. **Wrong index type**  
   - Use B-Tree for range/sorting. Use Hash for equality only.
4. **Ignoring composite index order**  
   - Order matters. Use leftmost column first.

---

## SECTION 8: INTERVIEW-STYLE QUESTIONS

1. What is an index? Why do we use indexes?  
2. What is a B-Tree index? When do you use it?  
3. What is a Hash index? When do you use it?  
4. What is a composite index?  
5. Why does order matter in composite index?  
6. When should you NOT use an index?  
7. How do indexes affect INSERT, UPDATE, DELETE?  
8. What index should you create on foreign key columns?  
9. Compare B-Tree vs Hash index.  
10. Design indexes for a blog app (users, posts, comments).

---

## SECTION 9: REVISION NOTES (CHEAT SHEET)

- **Index**: Speeds up data retrieval.
- **B-Tree**: Equality, range, sorting. Most common.
- **Hash**: Equality only. Fast (O(1)).
- **Composite**: Multiple columns. Order matters.
- **Rule**: Index `WHERE`, `JOIN`, `ORDER BY`, `GROUP BY` columns.

---

## SECTION 10: HANDS-ON ASSIGNMENT

Design indexes for a **blog app**:

### Tables
- `users`: `id`, `email`, `name`, `created_at`.
- `posts`: `id`, `user_id`, `title`, `content`, `created_at`.
- `comments`: `id`, `post_id`, `user_id`, `content`, `created_at`.

### Tasks
1. Identify frequently queried columns.
2. Create indexes:
   - On `users.email`.
   - On `posts.user_id`.
   - On `posts.created_at`.
   - On `comments.post_id`.
   - On `comments.user_id`.
3. Create composite index for:
   - `posts(user_id, created_at)`.
4. Write queries:
   - Get posts by user.
   - Get posts by date range.
   - Get comments by post.
