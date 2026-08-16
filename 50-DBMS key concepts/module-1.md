# DBMS + SQL Interview Revision — MODULE 1 (Q1–25): Foundation

---

### Q1. What is the difference between DBMS and RDBMS?

**Answer:**
A DBMS is any software that lets you store, retrieve, and manage data — it doesn't require the data to be structured in tables or follow relationships. An RDBMS is a specific type of DBMS that organizes data into tables (relations) with rows and columns, and enforces rules like primary keys, foreign keys, and constraints to keep data consistent. Every RDBMS is a DBMS, but not every DBMS is an RDBMS — file systems and some NoSQL stores are DBMS-like but not relational. RDBMSs also support SQL and transactions with ACID guarantees, which plain DBMS software may not.

**Mental Model:**
DBMS is the broad category ("software that manages data"); RDBMS is a strict, well-organized member of that category that insists on tables and relationships, like a filing cabinet with labeled folders versus a pile of documents in a box.

**Example:**
MySQL and PostgreSQL are RDBMS. A flat-file system storing data in `.txt` or `.csv` files, or MongoDB (document-based), is a DBMS but not an RDBMS.

**Interview Connection:**
Interviewers ask this to check you understand the vocabulary before diving into relational concepts — it also sets up SQL vs NoSQL discussions later.

**Common Mistake:**
Saying "RDBMS is just a fancier DBMS" without mentioning the core differentiator: enforced relationships and constraints between structured tables.

**Follow-up:**
Can you give an example of a DBMS that is not relational?

---

### Q2. What are tables, rows, and columns in a relational database?

**Answer:**
A table (also called a relation) represents one entity type, like `Users` or `Orders`. Rows (also called tuples or records) are individual entries in that table — one row is one instance of the entity. Columns (also called attributes or fields) define the properties every row has, along with a fixed data type. Every row in a table has the same set of columns, which is what gives relational data its structured, predictable shape.

**Mental Model:**
Think of a table as a spreadsheet: each column is a labeled category (Name, Age, Email), and each row is one person's data filled into those categories.

**Example:**
A `Users` table might have columns `id`, `name`, `email`; a row would be `(1, "Aditya", "aditya@mail.com")`.

**Interview Connection:**
This is a warm-up question, but interviewers use your phrasing to gauge whether you think in relational terms or just "database = spreadsheet."

**Common Mistake:**
Confusing columns with rows when describing schema changes — e.g., saying "add a row" when you mean "add a column."

**Follow-up:**
What's the difference between a schema and an instance of a table?

---

### Q3. What is a primary key and why is it important?

**Answer:**
A primary key is a column (or set of columns) that uniquely identifies each row in a table — no two rows can share the same primary key value, and it cannot be NULL. It's the anchor other tables use to reference a specific row via foreign keys. A table can have only one primary key, though that key can be composite (made of multiple columns). Databases automatically create an index on the primary key, which also makes lookups by that key fast.

**Mental Model:**
The primary key is like a passport number — every person has exactly one, it never changes, and no two people share the same one.

**Example:**
In a `Users` table, `id` (auto-incrementing integer) is typically the primary key rather than `email`, even though email is also unique, because IDs are more stable and efficient.

**Interview Connection:**
Interviewers want to confirm you understand uniqueness + non-null + indexing implications, not just "it's the ID column."

**Common Mistake:**
Assuming any unique column can serve as primary key without considering NULL-ability or stability (e.g., using email when users can change emails).

**Follow-up:**
What is a composite primary key, and when would you use one?

---

### Q4. What is the difference between a candidate key and a primary key?

**Answer:**
A candidate key is any column or set of columns that *could* uniquely identify a row — a table can have multiple candidate keys. The primary key is the one candidate key you actually choose to be the official unique identifier for the table. The remaining candidate keys don't disappear; they're often still enforced with UNIQUE constraints, and are then called alternate keys. So "candidate key" describes potential, while "primary key" describes the selected implementation.

**Mental Model:**
Candidate keys are like a shortlist of interview candidates who could all do the job; the primary key is the one you hire.

**Example:**
In a `Users` table, both `id` and `email` are unique — both are candidate keys — but you choose `id` as the primary key and keep `email` as a UNIQUE alternate key.

**Interview Connection:**
This distinction tests whether you understand key selection isn't arbitrary — it's a design decision with trade-offs.

**Common Mistake:**
Treating "candidate key" and "primary key" as synonyms, or thinking a table can only have one unique-constrained column.

**Follow-up:**
Why might you prefer a surrogate key (like an auto-increment ID) over a natural candidate key (like email)?

---

### Q5. What is a foreign key and what problem does it solve?

**Answer:**
A foreign key is a column in one table that references the primary key of another table, creating a link between the two. It enforces referential integrity — the database won't let you insert a foreign key value that doesn't exist in the referenced table, and depending on configuration, it can restrict or cascade deletes/updates on the referenced row. This is how relational databases represent relationships between entities without duplicating data.

**Mental Model:**
A foreign key is like writing someone else's ID number on your form instead of copying their entire profile — it's a pointer, not a copy.

**Example:**
An `Orders` table has a `user_id` column that is a foreign key referencing `Users.id`. You can't insert an order with a `user_id` that doesn't exist in `Users`.

**Interview Connection:**
Foreign keys are central to almost every schema design and JOIN question, so interviewers check this early to build on it later.

**Common Mistake:**
Thinking foreign keys automatically prevent all bad data — they only enforce referential integrity, not business logic (e.g., they won't stop an order with a negative price).

**Follow-up:**
What happens if you try to delete a row that's referenced by a foreign key, and how do `ON DELETE CASCADE` / `RESTRICT` change that?

---

### Q6. What are database constraints and what are the common types?

**Answer:**
Constraints are rules enforced by the database to keep data valid and consistent, rejecting any operation that would violate them. Common types include `NOT NULL` (column can't be empty), `UNIQUE` (no duplicate values), `CHECK` (value must satisfy a condition, like `age >= 0`), `DEFAULT` (auto-fills a value if none is given), and `PRIMARY KEY`/`FOREIGN KEY` (identity and referential rules). Constraints push data validation into the database layer itself, so it's enforced regardless of which application or script writes to it.

**Mental Model:**
Constraints are like guardrails on a road — they don't tell you where to drive, but they stop you from driving somewhere illegal.

**Example:**
`CHECK (price > 0)` on a `Products` table prevents anyone from inserting a product with a negative or zero price.

**Interview Connection:**
Interviewers ask this to see if you rely on the database as a safety net, versus only trusting application-level validation (a common real-world bug source).

**Common Mistake:**
Relying solely on backend/application validation and skipping database constraints, which leaves the door open if another service writes to the same table.

**Follow-up:**
Why might you want a constraint enforced at the database level even if you already validate it in your application code?

---

### Q7. What are the types of relationships between tables?

**Answer:**
Relational databases model three core relationship types: one-to-one (one row in Table A relates to exactly one row in Table B), one-to-many (one row in Table A relates to many rows in Table B), and many-to-many (many rows in Table A relate to many rows in Table B). One-to-many is implemented with a foreign key on the "many" side. Many-to-many requires a separate junction (bridge) table holding foreign keys to both tables, since neither table alone can hold multiple references cleanly.

**Mental Model:**
One-to-many is like one teacher having many students; many-to-many is like students enrolling in multiple courses and courses having multiple students — you need a middleman table (`Enrollments`) to track who's in what.

**Example:**
`Users` to `Orders` is one-to-many (`Orders.user_id` is the FK). `Students` to `Courses` is many-to-many, resolved with an `Enrollments(student_id, course_id)` junction table.

**Interview Connection:**
Schema design questions almost always hinge on correctly identifying relationship type before writing any SQL.

**Common Mistake:**
Trying to model many-to-many with a single foreign key column instead of a junction table.

**Follow-up:**
How would you design the `Enrollments` junction table, including its own primary key?

---

### Q8. What is an ER (Entity-Relationship) diagram and why is it used?

**Answer:**
An ER diagram is a visual representation of a database's entities (things, which become tables), their attributes (which become columns), and the relationships between them, before any SQL is written. It's a planning tool used during database design to catch modeling issues — missing relationships, wrong cardinality, redundant data — early, when they're cheap to fix. ER diagrams typically use notation for entities (boxes), attributes (ovals or lists), and relationships (lines with cardinality markers like 1, N).

**Mental Model:**
An ER diagram is the architectural blueprint drawn before construction — you don't want to discover a missing wall after the building is up.

**Example:**
An ER diagram for an e-commerce system might show `Users`, `Orders`, and `Products` as entities, with lines showing `Users` —1:N— `Orders` and `Orders` —M:N— `Products` (via an `OrderItems` junction).

**Interview Connection:**
Asked in system/database design interviews to see if you plan schemas methodically rather than writing `CREATE TABLE` statements ad hoc.

**Common Mistake:**
Skipping the ER step entirely and jumping straight to tables, which often causes missed many-to-many relationships.

**Follow-up:**
How does an ER diagram translate into actual `CREATE TABLE` statements?

---

### Q9. What is the relational model, and what are its core principles?

**Answer:**
The relational model, introduced by E.F. Codd, represents all data as relations (tables) — sets of tuples (rows) with attributes (columns) — and manipulates that data using relational operations like selection, projection, and join, which SQL is built on top of. Its core principles are: data is organized into tables with no inherent order, each row is uniquely identifiable, and relationships are represented through shared key values rather than physical links or pointers. This gives strong data independence — how data is stored physically doesn't affect how you query it logically.

**Mental Model:**
The relational model treats data like sets in math class — you filter, combine, and project sets to get answers, and SQL is just the language for expressing those set operations.

**Example:**
A JOIN is a direct implementation of the relational "join" operation — combining rows from two relations based on matching attribute values.

**Interview Connection:**
This tests whether you understand *why* SQL looks the way it does, rather than treating it as arbitrary syntax to memorize.

**Common Mistake:**
Thinking rows in a table have a guaranteed order — the relational model treats tables as unordered sets; any ordering you see is from `ORDER BY`, not storage.

**Follow-up:**
What does "data independence" mean, and why does it matter for application development?

---

### Q10. What is a functional dependency?

**Answer:**
A functional dependency exists when the value of one column (or set of columns) determines the value of another column — written `A → B`, meaning "A determines B." If you know A, there's exactly one possible value of B for it. Functional dependencies are the theoretical foundation for normalization: normal forms are defined in terms of which dependencies are allowed to exist relative to the primary key.

**Mental Model:**
Functional dependency is like a vending machine: if you know the slot number (A), the snack that comes out (B) is completely determined — no ambiguity.

**Example:**
In an `Orders` table, `order_id → customer_id` (each order belongs to exactly one customer) and `order_id → order_date` are functional dependencies.

**Interview Connection:**
This is the concept interviewers use to test whether you can explain 2NF and 3NF with actual reasoning instead of memorized rules.

**Common Mistake:**
Confusing functional dependency with correlation — functional dependency is a strict, deterministic rule, not just "these values tend to relate."

**Follow-up:**
What is a "partial dependency," and why does it matter for 2NF?

---

### Q11. Why does normalization matter?

**Answer:**
Normalization is the process of structuring tables to reduce data redundancy and prevent update, insert, and delete anomalies — situations where the same fact is stored in multiple places and can become inconsistent. It works by decomposing tables based on functional dependencies so that each piece of data lives in exactly one place. The trade-off is that normalized schemas often require more JOINs to reconstruct a full picture of an entity, which can affect read performance.

**Mental Model:**
Normalization is like refusing to write the same phone number on ten different forms — you write it once, in one place, and every form just references it.

**Example:**
Without normalization, storing a customer's address on every order row means updating their address requires updating every single order — miss one, and now data is inconsistent.

**Interview Connection:**
Nearly every schema design interview question implicitly tests your normalization instincts, even if not asked directly.

**Common Mistake:**
Treating normalization as an all-or-nothing rule rather than a spectrum — real systems often normalize core data and selectively denormalize for performance.

**Follow-up:**
What's an example of an update anomaly caused by a non-normalized table?

---

### Q12. What is First Normal Form (1NF)?

**Answer:**
A table is in 1NF if every column holds atomic (indivisible) values, and each row/column combination holds a single value — no repeating groups, no comma-separated lists stuffed into one cell, no arrays. Every row must also be uniquely identifiable. 1NF is the baseline: it's what makes a table "relational" at all, since SQL columns are expected to hold single scalar values.

**Mental Model:**
1NF means "one cell, one fact" — if you find yourself splitting a cell's content with a comma to get the real values, it's not in 1NF.

**Example:**
A `Users` table with a `phone_numbers` column storing `"9876543210, 9123456789"` violates 1NF; fixing it means moving phone numbers into a separate `UserPhones` table with one row per number.

**Interview Connection:**
1NF violations are common in real messy data (CSVs, legacy systems), so interviewers check if you can spot and fix them practically.

**Common Mistake:**
Thinking 1NF just means "has a primary key" — atomicity of column values is the actual defining rule.

**Follow-up:**
How would you redesign a table that stores a JSON array of tags in a single column to satisfy 1NF?

---

### Q13. What is Second Normal Form (2NF)?

**Answer:**
A table is in 2NF if it's already in 1NF, and every non-key column depends on the *entire* primary key — not just part of it. This rule only matters when the primary key is composite (made of multiple columns); if a non-key column depends on only one part of that composite key, that's a partial dependency, and it violates 2NF. Fixing it means splitting the table so the partially dependent column moves to a table keyed by just that part.

**Mental Model:**
2NF says: every fact must need the *whole* key to make sense, not just a piece of it — if a fact only cares about half the key, it belongs in a different table.

**Example:**
An `OrderItems(order_id, product_id, product_name, quantity)` table with composite key `(order_id, product_id)` violates 2NF because `product_name` depends only on `product_id`, not the full key — it should live in a `Products` table instead.

**Interview Connection:**
2NF questions test whether you can identify composite keys and reason about partial dependency, a common real design flaw.

**Common Mistake:**
Applying 2NF reasoning to tables with single-column primary keys, where partial dependency can't exist by definition.

**Follow-up:**
Would a table with a single-column primary key ever violate 2NF? Why or why not?

---

### Q14. What is Third Normal Form (3NF)?

**Answer:**
A table is in 3NF if it's in 2NF, and no non-key column depends on another non-key column — only on the primary key. This eliminates transitive dependencies, where column A determines column B, and B determines column C, meaning C indirectly (transitively) depends on A rather than directly on the key. 3NF is the most commonly targeted normal form in practice — it strikes a good balance between eliminating redundancy and keeping queries reasonably simple.

**Mental Model:**
3NF says every non-key fact must depend "on the key, the whole key, and nothing but the key" — if column B can tell you column C's value without needing the primary key at all, that's a transitive dependency to remove.

**Example:**
An `Orders(order_id, customer_id, customer_city)` table violates 3NF because `customer_city` depends on `customer_id`, not on `order_id` directly — it should be moved into the `Customers` table.

**Interview Connection:**
3NF is the normal form most real-world schemas target, so interviewers expect you to both define it and spot violations in a sample schema.

**Common Mistake:**
Confusing a transitive dependency (non-key → non-key) with a partial dependency (part of composite key → non-key) — they're different issues fixed at different normal forms.

**Follow-up:**
Can you give a real schema example that satisfies 2NF but violates 3NF?

---

### Q15. What is denormalization, and when would you use it?

**Answer:**
Denormalization is the deliberate process of introducing some redundancy back into a normalized schema — usually by duplicating data or pre-joining tables — to improve read performance by reducing the number of JOINs needed. It's a trade-off: you gain query speed and simplicity at the cost of extra storage and the risk of data inconsistency, which now must be managed carefully (often via application logic or triggers). It's typically applied selectively, to specific high-traffic read paths, not the whole schema.

**Mental Model:**
Denormalization is like keeping a printed copy of a frequently-needed document instead of walking to the filing cabinet every time — faster access, but now you have two copies to keep in sync.

**Example:**
Storing `product_name` directly on an `OrderItems` row (even though it's redundant with `Products.name`) avoids a JOIN on every order history query, at the cost of needing an update if the product is renamed.

**Interview Connection:**
This tests whether you understand normalization isn't dogma — real backend systems denormalize strategically for performance, and interviewers want to see that judgment.

**Common Mistake:**
Denormalizing prematurely, before actually measuring that JOINs are a real performance bottleneck.

**Follow-up:**
How would you keep denormalized data consistent when the source of truth changes?

---

### Q16. How do SELECT, WHERE, and ORDER BY work together in a query?

**Answer:**
`SELECT` specifies which columns to return, `WHERE` filters which rows are included based on a condition, and `ORDER BY` sorts the final result set. Logically, the database evaluates `FROM`/`JOIN` first to gather rows, then `WHERE` to filter them, then `SELECT` to pick columns, and finally `ORDER BY` to sort — even though you write `SELECT` first syntactically. Understanding this execution order matters for writing correct, efficient queries and avoiding confusion about what's filterable where.

**Mental Model:**
Think of it as a pipeline: gather rows → filter rows → pick columns → sort — SQL's written order and its logical execution order are different, and knowing that avoids subtle bugs.

**Example:**
```sql
SELECT name, age FROM Users
WHERE age >= 18
ORDER BY age DESC;
```
This filters to adult users, then returns just `name` and `age`, sorted oldest first.

**Interview Connection:**
Nearly every SQL interview question builds on this basic query shape, so fluency here is assumed before moving to JOINs and aggregation.

**Common Mistake:**
Trying to reference a `SELECT`-aliased column inside `WHERE` — it fails in most databases because `WHERE` is evaluated before `SELECT` logically.

**Follow-up:**
Why can't you use a column alias defined in `SELECT` inside the `WHERE` clause of the same query?

---

### Q17. How do GROUP BY and HAVING work?

**Answer:**
`GROUP BY` collapses rows sharing the same value(s) in specified columns into groups, typically so you can apply aggregate functions (`COUNT`, `SUM`, `AVG`) per group. `HAVING` filters *after* grouping, based on conditions involving the aggregated values — unlike `WHERE`, which filters individual rows before grouping happens. Any non-aggregated column in `SELECT` must appear in `GROUP BY`, or the query is ambiguous about which row's value to show.

**Mental Model:**
`WHERE` filters people before sorting them into teams; `HAVING` filters entire teams after they're formed, based on team-level stats like team size.

**Example:**
```sql
SELECT customer_id, COUNT(*) AS order_count
FROM Orders
GROUP BY customer_id
HAVING COUNT(*) > 5;
```
This returns only customers who placed more than 5 orders.

**Interview Connection:**
`WHERE` vs `HAVING` confusion is one of the most commonly tested SQL basics, because it reveals whether you understand query execution order.

**Common Mistake:**
Using `WHERE` to filter on an aggregate value (like `WHERE COUNT(*) > 5`), which fails because aggregates don't exist yet at the `WHERE` stage.

**Follow-up:**
Could you rewrite a `HAVING` filter using a subquery and `WHERE` instead? Would it be as efficient?

---

### Q18. What are the different types of SQL JOINs?

**Answer:**
`INNER JOIN` returns only rows with matches in both tables. `LEFT JOIN` returns all rows from the left table plus matched rows from the right (unmatched right-side columns are NULL). `RIGHT JOIN` is the mirror of that. `FULL OUTER JOIN` returns all rows from both tables, matched where possible, NULL where not. There's also `CROSS JOIN`, which produces every combination of rows from both tables (a Cartesian product), used far less often and usually unintentionally when a join condition is missing.

**Mental Model:**
Picture two overlapping circles (a Venn diagram): INNER is the overlap only, LEFT is the whole left circle, RIGHT is the whole right circle, FULL is both circles entirely.

**Example:**
```sql
SELECT u.name, o.order_id
FROM Users u
LEFT JOIN Orders o ON u.id = o.user_id;
```
This lists every user, including ones with no orders (their `order_id` shows as NULL).

**Interview Connection:**
JOINs are the single most-tested SQL topic in interviews, because they reveal whether you can reason about which rows survive and which don't.

**Common Mistake:**
Forgetting the `ON` condition and accidentally producing a CROSS JOIN, which silently returns a huge, meaningless result set.

**Follow-up:**
What result would you get from a LEFT JOIN followed by `WHERE right_table.id IS NULL`, and when is that pattern useful?

---

### Q19. What is a subquery, and when would you use one?

**Answer:**
A subquery is a query nested inside another query, used to compute an intermediate result that the outer query then filters on, joins against, or selects from. Subqueries can appear in `WHERE` (to filter using a computed set), `FROM` (as a derived table), or `SELECT` (as a scalar value per row). They're useful when a calculation logically needs to happen "first" before the outer query can make sense of it, though many subqueries can be rewritten as JOINs or CTEs for clarity or performance.

**Mental Model:**
A subquery is a self-contained mini-question you answer first, so the outer query can use that answer as an ingredient.

**Example:**
```sql
SELECT name FROM Users
WHERE id IN (SELECT user_id FROM Orders WHERE total > 1000);
```
This finds users who have placed at least one order over 1000.

**Interview Connection:**
Interviewers use subqueries to test whether you can decompose a complex requirement into a step-by-step query, and whether you know when a JOIN would be better.

**Common Mistake:**
Writing a correlated subquery (one that re-runs per outer row) when an uncorrelated subquery or JOIN would perform much better.

**Follow-up:**
What is a correlated subquery, and why can it be slower than a JOIN?

---

### Q20. What is a CTE (Common Table Expression), and why use one?

**Answer:**
A CTE, defined with `WITH name AS (...)`, is a named, temporary result set you can reference within the main query, as if it were a table. CTEs primarily improve readability by letting you break a complex query into logical, named steps instead of nesting nested subqueries. Some databases also support recursive CTEs, which let a CTE reference itself — useful for hierarchical data like org charts or category trees.

**Mental Model:**
A CTE is like defining a variable before using it in a formula — you compute a piece once, name it, and reference that name cleanly instead of repeating the calculation inline.

**Example:**
```sql
WITH big_spenders AS (
  SELECT user_id FROM Orders WHERE total > 1000
)
SELECT name FROM Users WHERE id IN (SELECT user_id FROM big_spenders);
```

**Interview Connection:**
CTEs signal that you write maintainable SQL rather than deeply nested, hard-to-read subqueries — a practical skill interviewers value.

**Common Mistake:**
Assuming a CTE is always materialized (computed once and cached) — in many databases it may be inlined and re-evaluated, so it's not automatically a performance optimization.

**Follow-up:**
How would you write a recursive CTE to find all subordinates of a manager in an `Employees(id, manager_id)` table?

---

### Q21. What are window functions, and how are they different from GROUP BY?

**Answer:**
Window functions perform calculations across a set of rows related to the current row — like `GROUP BY` — but without collapsing those rows into a single output row; each original row stays visible alongside its computed value. They're written with an `OVER()` clause, which can define partitioning (`PARTITION BY`, like a per-group `GROUP BY`) and ordering (`ORDER BY`) within that window. This makes them ideal for rankings, running totals, and comparisons against group aggregates while still showing row-level detail.

**Mental Model:**
`GROUP BY` shrinks your rows down into one row per group; a window function keeps every row but lets each one "peek" at its group's aggregate value.

**Example:**
```sql
SELECT name, department, salary,
  RANK() OVER (PARTITION BY department ORDER BY salary DESC) AS dept_rank
FROM Employees;
```
This ranks each employee's salary within their department, without collapsing rows.

**Interview Connection:**
Window functions are a strong signal of intermediate-to-advanced SQL fluency and come up frequently in analytics-style interview questions (top-N per group, running totals).

**Common Mistake:**
Confusing `RANK()`, `DENSE_RANK()`, and `ROW_NUMBER()` — they handle ties differently, and picking the wrong one gives subtly wrong results.

**Follow-up:**
What's the difference between `RANK()`, `DENSE_RANK()`, and `ROW_NUMBER()` when there are ties?

---

### Q22. What are aggregate functions, and what do you need to be careful about when using them?

**Answer:**
Aggregate functions — `COUNT`, `SUM`, `AVG`, `MIN`, `MAX` — compute a single summary value from a set of rows, typically used with `GROUP BY`. A key subtlety: most aggregate functions ignore NULL values by default (except `COUNT(*)`, which counts rows regardless of NULLs), which can silently skew results like `AVG` if you're not careful. Another common trap is mixing aggregated and non-aggregated columns in `SELECT` without grouping by the non-aggregated ones, which most databases will reject or handle unpredictably.

**Mental Model:**
Aggregate functions boil a group of rows down to one number — like squeezing a group of oranges into a single glass of juice — but NULLs are "not counted" oranges, so they vanish rather than becoming zero.

**Example:**
`AVG(discount)` on rows where some `discount` values are NULL only averages the non-NULL rows, not treating NULL as 0 — which can make the average look higher than expected.

**Interview Connection:**
This is a classic "gotcha" question interviewers use to check attention to detail, since NULL-handling bugs are common in production reporting queries.

**Common Mistake:**
Assuming `COUNT(column)` and `COUNT(*)` behave the same — `COUNT(column)` skips NULLs in that column, `COUNT(*)` counts every row.

**Follow-up:**
What would `AVG(discount)` return if you actually wanted NULLs treated as zero, and how would you write that?

---

### Q23. What is an index, and why does it speed up queries?

**Answer:**
An index is a separate data structure that stores a sorted (or otherwise organized) reference to column values alongside pointers to the actual rows, letting the database find matching rows without scanning the entire table. Without an index, a query filtering on a column requires a full table scan, checking every row one by one. Indexes trade extra storage and slower writes (since the index must be updated on every insert/update/delete) for dramatically faster reads on the indexed column.

**Mental Model:**
An index is like the index at the back of a textbook — instead of reading every page to find "mitochondria," you jump straight to the page number listed under M.

**Example:**
`CREATE INDEX idx_users_email ON Users(email);` makes `WHERE email = 'x@mail.com'` fast, turning an O(n) scan into a fast lookup.

**Interview Connection:**
Indexing is one of the highest-value topics in backend interviews because it directly maps to real production performance problems.

**Common Mistake:**
Assuming adding an index is always a free performance win — every index adds write overhead and storage cost, so indexes should be added deliberately, not everywhere.

**Follow-up:**
Why might a query still not use an available index even when filtering on the indexed column?

---

### Q24. What is the intuition behind B-Tree / B+Tree indexes?

**Answer:**
Most relational database indexes use a B+Tree structure: a balanced, sorted tree where every leaf node is at the same depth, guaranteeing predictable O(log n) lookup time regardless of table size. Internal nodes store only "signpost" keys used for navigation, while the actual data (or pointers to rows) lives in the leaf nodes, which are also linked together — making range queries (`BETWEEN`, `>`, `ORDER BY`) efficient by letting the database walk sequentially across leaves once it finds the start. The "balanced" property is what keeps lookups fast even as the table grows to millions of rows.

**Mental Model:**
A B+Tree is like a well-organized library's catalog system: instead of walking every aisle, you follow signposts that narrow you down floor by floor until you reach the exact shelf, and shelves are connected so you can browse nearby books easily too.

**Example:**
A `WHERE age BETWEEN 20 AND 30` query using a B+Tree index on `age` jumps to the leaf node for 20 and reads sequentially along the linked leaves until it passes 30 — no full scan needed.

**Interview Connection:**
Interviewers use this to see if you understand *why* indexes are fast, not just that "indexes make things fast" as a black box.

**Common Mistake:**
Confusing B-Tree with binary search tree — B-Trees are wider (many children per node) and shallower, specifically designed to minimize disk reads, unlike in-memory binary trees.

**Follow-up:**
Why are B+Trees preferred over plain binary search trees for database indexes, given both are logarithmic?

---

### Q25. What does ACID mean, and why does it matter for transactions?

**Answer:**
ACID stands for Atomicity (a transaction's operations all succeed or all fail together — no partial updates), Consistency (a transaction moves the database from one valid state to another, respecting all constraints), Isolation (concurrent transactions don't interfere with each other's intermediate states), and Durability (once committed, changes survive crashes or power loss). Together, these guarantees let you treat a group of operations as a single, safe unit of work, which is essential whenever multiple related changes must succeed or fail as one — like transferring money between two accounts.

**Mental Model:**
ACID is a promise: "either the whole transaction happens cleanly, or none of it does, and once it's done, it's really done" — no matter what crashes, races, or errors happen mid-way.

**Example:**
A bank transfer debits Account A and credits Account B inside one transaction — atomicity guarantees that if the credit fails, the debit is rolled back too, so money never vanishes.

**Interview Connection:**
ACID is foundational vocabulary for any transactions/concurrency discussion, and interviewers expect you to define all four letters precisely, not just recite the acronym.

**Common Mistake:**
Confusing Consistency (constraint/business-rule validity) with Isolation (concurrent-transaction independence) — they sound similar but address different problems.

**Follow-up:**
Can a database be atomic but not isolated? What would that look like in practice?

---

## Best Practices in SQL & DBMS

### 1. Naming Conventions
Always use a consistent naming convention for tables and columns (e.g., `snake_case`).

```sql
-- Bad Practice
CREATE TABLE userdata (
  UserID INT,
  firstName VARCHAR(50)
);

-- Good Practice
CREATE TABLE users (
  user_id INT PRIMARY KEY,
  first_name VARCHAR(50)
);
```

### 2. Using Indexes Properly
Add indexes to foreign keys and columns frequently used in `WHERE`, `JOIN`, or `ORDER BY` clauses. Don't over-index as it slows down writes.

```sql
-- Creating an index on a frequently searched column
CREATE INDEX idx_users_email ON users(email);
```

### 3. Avoiding SELECT *
Never use `SELECT *` in production queries. Always specify the exact columns you need. This saves memory, network bandwidth, and prevents issues when schema changes.

```sql
-- Bad Practice
SELECT * FROM users WHERE status = 'active';

-- Good Practice
SELECT id, name, email FROM users WHERE status = 'active';
```

### 4. Using Prepared Statements (Application Level)
To prevent SQL Injection, always use parameterized queries or prepared statements instead of string concatenation.

```javascript
-- Bad Practice (Node.js/JS example)
db.query(`SELECT * FROM users WHERE email = '${userEmail}'`); 

-- Good Practice
db.query('SELECT id, name FROM users WHERE email = $1', [userEmail]);
```

### 5. Proper Constraints
Enforce data integrity using constraints rather than relying solely on the application code.

```sql
-- Good Practice
CREATE TABLE orders (
  order_id INT PRIMARY KEY,
  amount DECIMAL(10, 2) NOT NULL CHECK (amount > 0),
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```
