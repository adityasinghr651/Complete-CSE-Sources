# Day 13: Database Engineering Fundamentals

*(Detailed, step-by-step, from first principles — with proper definitions, simple language, intuition, diagrams, and production examples)*

---

## SECTION 1: INTUITION (What is Database Engineering?)

Think of **database engineering** as the **architecture + construction + maintenance** of a building that stores data.

A normal building:
- You design the layout (rooms, floors).
- You build it (walls, doors, pipes).
- You maintain it (fix leaks, clean, paint).

A **database** is similar:
- You design the **schema** (tables, columns, relationships).
- You build the **database system** (create tables, indexes, constraints).
- You **maintain** it (performance tuning, backups, scaling, security).

> **Simple Analogy:**  
> - **Database engineering** = Building and maintaining the entire system to store, organize, and retrieve data correctly.  
> - "Building" = database.  
> - "Layout" = schema (tables, columns, relationships).  
> - "Construction" = create tables, indexes, constraints.  
> - "Maintenance" = performance tuning, backups, scaling, security.

From definitions:
- **Database engineer**: professional who creates and manages databases for web apps, analytics, e-commerce, enterprise systems.
- Responsible for:
  - Designing database schema (structure, relationships, constraints).
  - Developing database logic (how data is stored, retrieved, manipulated).
  - Ensuring performance, security, reliability, scalability.

---

## SECTION 2: CORE DEFINITIONS

### 2.1 What is a Database?

**Database** = organized collection of data, stored and accessed electronically.

- Types:
  - **Relational (SQL)**: tables with rows/columns (MySQL, PostgreSQL, SQL Server).
  - **Non-relational (NoSQL)**: documents, key-value, graphs (MongoDB, Redis, Cassandra).

Database stores:
- Users, orders, posts, messages, logs, etc.

---

### 2.2 What is a Database Schema?

**Database Schema** = the blueprint of your database.

It defines:
- **Tables**: e.g., `users`, `orders`, `posts`.
- **Columns**: e.g., `id`, `email`, `name`, `created_at`.
- **Data types**: e.g., `INTEGER`, `VARCHAR`, `BOOLEAN`, `TIMESTAMP`.
- **Relationships**: e.g., `orders.user_id` → `users.id`.
- **Constraints**: e.g., `PRIMARY KEY`, `UNIQUE`, `NOT NULL`, `FOREIGN KEY`.

**Example schema for a blog:**

```sql
users (
  id, email, name, password_hash, created_at
)

posts (
  id, user_id, title, content, created_at
)
```

Relationship:
- `posts.user_id` → `users.id` (one user → many posts).

---

### 2.3 What is a Database Engine?

**Database Engine** = the core software that:
- Stores data.
- Processes queries.
- Manages transactions.
- Handles indexing, caching, locking.

Examples:
- PostgreSQL engine.
- MySQL InnoDB engine.
- MongoDB storage engine.

Engine is what makes the database work under the hood.

---

### 2.4 What is Database Engineering?

**Database Engineering** = a specialized discipline within computer science that focuses on:
- **Designing** database systems.
- **Developing** database architecture.
- **Implementing** databases.
- **Troubleshooting** and **managing** secure database systems.
- **Optimizing** performance.

Primary goal:  
**Efficiently store, organize, and retrieve data** in a secure, reliable, scalable way.

Key aspects:
1. Database Design & Development.
2. Programming database algorithms.
3. Setting data storage rules.
4. Allocating computing resources.
5. Performance tuning, security, backups.

---

## SECTION 3: DATABASE ENGINEERING vs RELATED ROLES

### 3.1 Database Engineer vs DBA (Database Administrator)

**Database Engineer**:
- Designs & implements databases.
- Builds database architecture.
- Writes code, queries, data pipelines.
- Focus: design, implementation, optimization.

**DBA (Database Administrator)**:
- Manages existing databases.
- Handles:
  - Monitoring.
  - Backups.
  - Security.
  - Performance.
- Focus: operations, maintenance.

*Often in small teams, one person does both.*

---

### 3.2 Database Engineer vs Data Engineer

**Database Engineer**:
- Focus: design, implementation, maintenance, optimization of **database systems**.
- Tools: SQL, PostgreSQL, MySQL, MongoDB.
- Goal: robust database for storing/retrieving structured data.

**Data Engineer**:
- Broader scope: **data pipelines**, big data, warehouses.
- Tools: Spark, Kafka, Airflow, Hive, cloud data platforms.
- Focus: gathering, transforming, loading large volumes of data for analytics.

**Simple analogy:**
- Database engineer: builds the **database building**.
- Data engineer: builds the **pipelines** that bring data from many sources into warehouses.

> ✅ **[Principal Engineer Note]: OLTP vs OLAP**
> *To put it in enterprise terms: Database Engineers focus on **OLTP** (Online Transaction Processing). Your job is to make sure when a user buys a coffee, the transaction is recorded in 50 milliseconds without crashing. Data Engineers focus on **OLAP** (Online Analytical Processing). Their job is to run a massive query at midnight to figure out how many coffees were sold globally in the last year. OLTP = Speed & Accuracy. OLAP = Volume & Aggregation.*

---

## SECTION 4: CORE RESPONSIBILITIES OF DATABASE ENGINEERING

### 4.1 Designing Database Architecture

- Create database diagrams.
- Define tables, columns, relationships.
- Define business rules:
  - “An order must have a user.”
  - “A post must have a title.”

Example:

```sql
users (id, email, name, password_hash, created_at)
orders (id, user_id, total, created_at)
```
Relationship:
- `orders.user_id` → `users.id`.

---

### 4.2 Programming Database Algorithms

- Write queries (SQL).
- Write stored procedures, functions.
- Write triggers.
- Optimize queries.

Example:

```sql
SELECT u.name, COUNT(o.id) AS order_count
FROM users u
LEFT JOIN orders o ON o.user_id = u.id
GROUP BY u.id;
```

---

### 4.3 Setting Data Storage Rules

- Define:
  - Data types.
  - Constraints (PRIMARY KEY, UNIQUE, NOT NULL, FOREIGN KEY).
  - Indexes.
- Decide:
  - Where data is stored.
  - How it’s partitioned.
  - How data is accessed.

---

### 4.4 Allocating Computing Resources

- Choose hardware:
  - CPU, RAM, storage.
- Choose:
  - Cloud vs on-prem.
  - Single server vs replicated.
- Allocate resources:
  - More RAM for caching.
  - More CPU for heavy queries.

---

### 4.5 Performance Tuning & Query Optimization

- Identify bottlenecks.
- Add indexes.
- Optimize queries.
- Use caching.
- Partition tables.

Example:

```sql
-- Add index on user_id
CREATE INDEX idx_orders_user_id ON orders(user_id);
```

---

### 4.6 Security

- Authentication: who can access DB.
- Authorization: what they can do.
- Encryption:
  - At rest.
  - In transit.
- Backups & recovery.

---

### 4.7 Backups & Recovery

- Regular backups.
- Test recovery.
- Point-in-time recovery.

---

## SECTION 5: VISUAL DIAGRAMS (TEXT)

### Diagram 1: Database Engineering Lifecycle

```text
[Requirements]
  |
  | Understand app needs
  v
[Design]
  |
  | Define schema: tables, columns, relationships
  v
[Implementation]
  |
  | Create tables, indexes, constraints
  v
[Testing]
  |
  | Write queries, test performance
  v
[Deployment]
  |
  | Deploy to production
  v
[Maintenance]
  |
  | Monitor, tune, backup, secure, scale
```

---

### Diagram 2: Schema Example (Blog App)

```text
users
  id: INTEGER (PK)
  email: VARCHAR (UNIQUE)
  name: VARCHAR
  password_hash: VARCHAR
  created_at: TIMESTAMP

posts
  id: INTEGER (PK)
  user_id: INTEGER (FK → users.id)
  title: VARCHAR
  content: TEXT
  created_at: TIMESTAMP

Relationship:
  One user → Many posts
```

---

## SECTION 6: KEY DATABASE ENGINEERING CONCEPTS

### 6.1 Relational Databases (SQL)
- Data in **tables**.
- Tables have **rows** and **columns**.
- Use **SQL** (Structured Query Language).
- Examples: PostgreSQL, MySQL, SQL Server.
- Characteristics:
  - Strong relationships.
  - Constraints (PRIMARY KEY, FOREIGN KEY).
  - ACID transactions.

### 6.2 Non-Relational Databases (NoSQL)
Data not in tables:
- **Document**: MongoDB (JSON-like documents).
- **Key-value**: Redis.
- **Column-family**: Cassandra.
- **Graph**: Neo4j.
- Use when:
  - Data is unstructured/semi-structured.
  - High scalability needed.
  - Flexible schema.

> ✅ **[Principal Engineer Note]: The NoSQL Trap**
> *In the 2010s, there was a massive trend where startups used MongoDB just because "writing schemas is annoying." This is a catastrophic mistake. If your data is relational (e.g., Users have Posts, Posts have Comments), use a Relational SQL database. Only use NoSQL when you actually have unstructured document data or you need insane horizontal write-scaling (like logging 10,000 IoT sensor events per second).*

### 6.3 ACID Properties
For relational databases:
- **Atomicity**: transaction completes fully or not at all.
- **Consistency**: database stays valid.
- **Isolation**: transactions don’t interfere.
- **Durability**: committed data is permanent.

### 6.4 Indexes
**Index** = like a book’s index, speeds up data retrieval.
Example:
```sql
CREATE INDEX idx_users_email ON users(email);
```
Now:
```sql
SELECT * FROM users WHERE email = 'raj@example.com';
```
Faster because of index.

### 6.5 Constraints
Common constraints:
- `PRIMARY KEY`: unique identifier.
- `UNIQUE`: value must be unique.
- `NOT NULL`: value must exist.
- `FOREIGN KEY`: reference another table.

Example:
```sql
CREATE TABLE users (
  id INTEGER PRIMARY KEY,
  email VARCHAR(255) UNIQUE NOT NULL,
  name VARCHAR(255) NOT NULL
);
```

### 6.6 Transactions
A **transaction** = group of queries that must all succeed or all fail.
Example:
```sql
BEGIN;

UPDATE accounts SET balance = balance - 100 WHERE id = 1;
UPDATE accounts SET balance = balance + 100 WHERE id = 2;

COMMIT;
```
If any query fails → `ROLLBACK`.

---

## SECTION 7: COMMON MISTAKES

1. **No schema design**  
   - Random tables, no relationships.  
   - Design schema first.
2. **No indexes**  
   - Slow queries.  
   - Add indexes on frequently queried columns.
3. **No constraints**  
   - Invalid data.  
   - Use PRIMARY KEY, UNIQUE, NOT NULL, FOREIGN KEY.
4. **Not using transactions**  
   - Inconsistent data.  
   - Use transactions for critical operations.
5. **No backups**  
   - Data loss.  
   - Regular backups + test recovery.
6. **No performance tuning**  
   - Slow app.  
   - Monitor, tune queries, add indexes.

---

## SECTION 8: INTERVIEW-STYLE QUESTIONS

1. What is database engineering? Define it clearly.
2. What is a database schema? What does it define?
3. What is a database engine? What does it do?
4. How is database engineering different from DBA and data engineering?
5. What are the core responsibilities of a database engineer?
6. What is a relational database? What is NoSQL?
7. What are ACID properties? Why are they important?
8. What is an index? Why do we use indexes?
9. What are constraints? Give examples.
10. What is a transaction? Why do we use transactions?

---

## SECTION 9: REVISION NOTES (CHEAT SHEET)

- **Database engineering**:
  - Design, develop, implement, manage, optimize database systems.
  - Goal: efficient, secure, scalable data storage & retrieval.
- **Database**: Organized data (SQL / NoSQL).
- **Schema**: Blueprint: tables, columns, relationships, constraints.
- **Engine**: Core software that stores, processes, manages data.
- **Responsibilities**: Design architecture, program algorithms, set storage rules, allocate resources, performance tuning, security, backups & recovery.
- **Key concepts**: SQL vs NoSQL, ACID, Indexes, Constraints, Transactions.

---

## SECTION 10: HANDS-ON ASSIGNMENT

Design a **database schema** for a simple blog app:

### Requirements
- Users: Email (unique), Name, Password hash, created_at.
- Posts: Title, Content, user_id (references users), created_at.

### Tasks
1. Draw the schema (tables, columns, types).
2. Define relationships.
3. Define constraints (PRIMARY KEY, UNIQUE, NOT NULL, FOREIGN KEY).
4. Write SQL to create tables.
5. Add indexes on:
   - `users.email`.
   - `posts.user_id`.

---

## SECTION 11: MINI PROJECT

Build a **simple blog app with database**:

- Use PostgreSQL or MySQL.
- Create tables: `users`, `posts`.
- Add indexes.
- Add constraints.
- Write queries:
  - Create user.
  - Create post.
  - Get posts by user.
  - Get all posts with user info.

---

## ACTIVE LEARNING – YOUR TURN

Answer these in your own words:

1. What is **database engineering**? Define it clearly.
2. What is a **database schema**? What does it define?
3. What is a **database engine**? What does it do?
4. What are the **core responsibilities** of a database engineer? (List 5–6).
5. What is the difference between:
   - Database engineer vs DBA.
   - Database engineer vs data engineer.
6. What are **ACID properties**? Why are they important?
7. What is an **index**? Why do we use indexes?
8. For a blog app:
   - Design schema for `users` and `posts`.
   - Define relationships.
   - Define constraints.
