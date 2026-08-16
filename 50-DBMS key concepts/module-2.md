# DBMS + SQL Interview Revision — MODULE 2 (Q26–50): Application + Interview

---

### Q26. What are isolation levels, and why do they exist?

**Answer:**
Isolation levels define how much one transaction's in-progress changes are visible to other concurrent transactions, controlling the trade-off between consistency and performance. The SQL standard defines four levels — Read Uncommitted, Read Committed, Repeatable Read, and Serializable — each preventing more concurrency anomalies than the last, at the cost of more locking/blocking. They exist because full isolation (Serializable) is expensive; most applications don't need it everywhere, so databases let you dial isolation up or down per use case.

**Mental Model:**
Isolation levels are like privacy settings on a shared document — the stricter the setting, the less you see of others' in-progress edits, but the more everyone has to wait their turn.

**Example:**
PostgreSQL defaults to Read Committed — a transaction sees data as it was at the start of each statement, not the start of the whole transaction.

**Interview Connection:**
This is one of the most commonly tested backend concepts because production bugs from wrong isolation levels are subtle and painful.

**Common Mistake:**
Assuming "Serializable" means transactions run one at a time — it actually means they run concurrently but produce a result *equivalent* to some serial order.

**Follow-up:**
What isolation level does PostgreSQL use by default, and what anomaly does it still allow?

---

### Q27. What are dirty reads, non-repeatable reads, and phantom reads?

**Answer:**
A dirty read happens when a transaction reads data written by another transaction that hasn't committed yet — if that other transaction rolls back, you've read data that never "really" existed. A non-repeatable read happens when a transaction reads the same row twice and gets different values because another transaction updated and committed that row in between. A phantom read happens when a transaction re-runs the same query and gets a different *set of rows* because another transaction inserted or deleted rows matching the query's condition in between.

**Mental Model:**
Dirty read = trusting a rumor before it's confirmed; non-repeatable read = the answer to your question changing when you ask twice; phantom read = new people walking into the room between two headcounts.

**Example:**
Transaction A reads `balance = 100`, Transaction B updates it to `50` and commits, Transaction A reads again and gets `50` — that's a non-repeatable read.

**Interview Connection:**
Interviewers use these three terms as a checklist to see if you can precisely map each isolation level to which anomalies it prevents.

**Common Mistake:**
Mixing up non-repeatable reads (same row, different value) with phantom reads (different set of rows) — they're solved by different isolation guarantees.

**Follow-up:**
Which isolation level is the first to fully prevent non-repeatable reads, and which is needed to prevent phantom reads too?

---

### Q28. What are locks, and what types exist in a relational database?

**Answer:**
Locks are the mechanism databases use to control concurrent access to the same data, preventing conflicting operations from corrupting it. The two fundamental types are shared (read) locks, which multiple transactions can hold simultaneously on the same row, and exclusive (write) locks, which only one transaction can hold at a time, blocking all other reads and writes on that row until released. Databases also apply locks at different granularities — row-level, page-level, or table-level — with row-level being the most common in modern RDBMSs since it allows the highest concurrency.

**Mental Model:**
A shared lock is like several people reading the same library book at once (fine); an exclusive lock is like someone editing the book — everyone else has to wait until they're done.

**Example:**
`SELECT * FROM Accounts WHERE id = 1 FOR UPDATE;` takes an exclusive lock on that row, blocking other transactions from updating it until the current transaction commits or rolls back.

**Interview Connection:**
Locking is the mechanism behind concurrency bugs and deadlocks, so interviewers expect you to explain it before discussing deadlocks or MVCC.

**Common Mistake:**
Assuming locks are always table-wide — most modern RDBMSs default to fine-grained row-level locking, which is far less blocking than people expect.

**Follow-up:**
Why might row-level locking still cause a whole table to feel "locked" under high contention?

---

### Q29. What is a deadlock, and how do databases handle it?

**Answer:**
A deadlock occurs when two (or more) transactions each hold a lock the other needs, and each is waiting for the other to release theirs — neither can proceed, and without intervention they'd wait forever. Databases handle this by running a deadlock detection algorithm that periodically checks for these circular wait patterns, then forcibly aborts (rolls back) one of the transactions — usually the "cheaper" one to undo — freeing its locks so the other can proceed. The aborted transaction's application code typically needs to catch that failure and retry.

**Mental Model:**
Classic deadlock: two people at a narrow doorway each say "after you," each stepping back — except in databases, no one steps back, so the system has to forcibly evict one.

**Example:**
Transaction A locks row 1 then tries to lock row 2; Transaction B locks row 2 then tries to lock row 1 — both wait on each other forever until the database detects and kills one.

**Interview Connection:**
This is a favorite scenario question because it tests both conceptual understanding and whether you write retry-safe application code.

**Common Mistake:**
Assuming deadlocks are rare edge cases — they're a routine, expected occurrence in high-concurrency systems, and production code should handle the resulting error gracefully.

**Follow-up:**
What coding pattern (e.g., consistent lock ordering) helps reduce the frequency of deadlocks in your application?

---

### Q30. What is MVCC (Multi-Version Concurrency Control)?

**Answer:**
MVCC lets a database serve consistent reads without blocking writers (and vice versa) by keeping multiple versions of a row — when a row is updated, the old version isn't immediately overwritten, it's kept around so transactions that started before the update can still see the version that existed when they began. This is how PostgreSQL achieves Read Committed and Repeatable Read isolation without readers and writers locking each other out constantly. Old row versions eventually get cleaned up by a background process (like PostgreSQL's `VACUUM`) once no active transaction needs them anymore.

**Mental Model:**
MVCC is like a document with tracked "as of" snapshots — instead of forcing everyone to wait while someone edits, each reader just sees the version of the document that existed when they opened it.

**Example:**
While Transaction A is reading a row, Transaction B updates and commits that row — A's ongoing transaction still sees its original snapshot version, not B's update, thanks to MVCC.

**Interview Connection:**
MVCC explains *why* PostgreSQL and similar databases achieve high concurrency without heavy locking — a strong signal you understand real engine internals, not just SQL syntax.

**Common Mistake:**
Assuming MVCC means "no locks are ever used" — writers still need locks to serialize conflicting writes; MVCC mainly frees up read concurrency.

**Follow-up:**
Why does PostgreSQL need a `VACUUM` process, and what happens if old row versions aren't cleaned up?

---

### Q31. What does it mean when two transactions modify the same row concurrently?

**Answer:**
When two transactions try to write to the same row concurrently, the database serializes the writes — the first to acquire the exclusive lock (or, in optimistic schemes, the first to commit) proceeds, and the second either waits, gets blocked, or fails depending on isolation level and locking strategy. In pessimistic locking, the second transaction blocks until the first commits or rolls back. In optimistic concurrency control, both proceed without locking upfront, but the second transaction's commit is rejected if it detects the row changed underneath it (a version mismatch), requiring a retry.

**Mental Model:**
Pessimistic locking is "ask permission first, then act"; optimistic locking is "act first, then check nobody else touched it before you save."

**Example:**
Two users editing the same shopping cart quantity: with pessimistic locking, the second edit waits; with optimistic locking (using a `version` column), the second edit fails at commit time and the UI asks the user to retry.

**Interview Connection:**
This is a classic scenario question testing whether you understand the practical difference between locking strategies, not just their names.

**Common Mistake:**
Assuming the database always silently picks a "winner" without any application-visible consequence — in reality, either a wait/block or an explicit failure occurs that your code must handle.

**Follow-up:**
How would you implement optimistic concurrency control using a `version` or `updated_at` column in SQL?

---

### Q32. An API endpoint's query suddenly becomes slow in production. How would you investigate?

**Answer:**
First, confirm scope — is it slow for all requests or specific ones (certain user IDs, date ranges, filter combinations)? Then run `EXPLAIN ANALYZE` on the actual query to see the execution plan: check whether it's doing a sequential scan where an index scan was expected, whether estimated row counts are wildly off from actual (stale statistics), or whether a JOIN order is inefficient. Also check for external factors — has table size grown significantly, is there lock contention from another long-running transaction, or has an index been dropped or become bloated. From there, the fix is usually adding/fixing an index, rewriting the query, or updating table statistics.

**Mental Model:**
Treat it like a doctor's diagnosis: don't guess-treat, run diagnostics (`EXPLAIN ANALYZE`) first to see exactly where time is being spent before touching anything.

**Example:**
`EXPLAIN ANALYZE SELECT * FROM Orders WHERE customer_id = 5;` showing "Seq Scan on Orders" instead of "Index Scan" tells you immediately that an index on `customer_id` is missing or not being used.

**Interview Connection:**
This is one of the most common real-world scenario questions because it directly tests hands-on debugging ability, not just definitions.

**Common Mistake:**
Jumping straight to "just add an index" without first running `EXPLAIN ANALYZE` to confirm that's actually the bottleneck.

**Follow-up:**
What would you check if `EXPLAIN ANALYZE` shows the right index is being used, but the query is still slow?

---

### Q33. When would you add an index to a column?

**Answer:**
Add an index when a column is frequently used in `WHERE` clauses, `JOIN` conditions, or `ORDER BY`, and the table is large enough that a full scan is measurably expensive. Good candidates are columns with high cardinality (many distinct values, like `user_id` or `email`) rather than low-cardinality columns (like a boolean `is_active`), since a low-cardinality index often doesn't help the query planner narrow results much. You should also weigh write frequency — a heavily-written table pays an ongoing cost for every index it has, since each insert/update/delete must also update the index.

**Mental Model:**
Index the columns you constantly "look someone up by" — not every column, just the ones acting as lookup keys or filters in your actual query patterns.

**Example:**
A `Users` table queried constantly with `WHERE email = ?` for login benefits hugely from an index on `email`; indexing `is_admin` (mostly `false`) rarely helps.

**Interview Connection:**
This tests practical judgment — knowing indexing rules isn't enough if you can't apply them to real query patterns.

**Common Mistake:**
Indexing a column just because it's used in `SELECT`, when indexes only help filtering/sorting/joining, not simply retrieving a column's value.

**Follow-up:**
Would you index a column used in `WHERE` with a `LIKE '%something%'` pattern? Why or why not?

---

### Q34. Why can too many indexes hurt performance?

**Answer:**
Every index must be updated whenever the underlying table's row is inserted, updated, or deleted — so more indexes mean more write overhead per operation, slowing down `INSERT`/`UPDATE`/`DELETE` statements. Indexes also consume disk space and memory (since active indexes are cached), which can push more useful data out of cache and hurt overall system performance. Additionally, having too many overlapping or redundant indexes can confuse the query planner into choosing a suboptimal plan, or simply waste maintenance effort during operations like `VACUUM` or index rebuilds.

**Mental Model:**
Each index is like a separate sorted copy of your book's index — handy for readers, but every time you edit the book, you now have to update every single copy too.

**Example:**
A table with 10 indexes on a high-write workload (like an events/logging table) can see insert throughput drop significantly compared to the same table with just 1–2 essential indexes.

**Interview Connection:**
This tests whether you see indexing as a trade-off rather than a free performance lever — a common junior-engineer misconception.

**Common Mistake:**
Adding an index for every column that appears anywhere in a query, without checking whether it's actually selective or frequently used.

**Follow-up:**
How would you identify and remove unused indexes in a production database?

---

### Q35. How would you design a database schema for a system with millions of users?

**Answer:**
Start with a normalized core schema (users, core entities, relationships) to avoid redundancy and anomalies, then selectively denormalize the specific read paths that are proven hot. Ensure every frequently-queried column has appropriate indexes, and design primary keys thoughtfully — surrogate integer/UUID keys scale better than natural keys. Plan early for growth: consider read replicas for read-heavy workloads, partitioning large tables (like an events or logs table) by time or another logical dimension, and caching frequently-accessed, rarely-changing data (like user profile lookups) in front of the database.

**Mental Model:**
Design like you're building a highway system: start with clean, well-marked roads (normalized schema), then add express lanes (indexes, caching, replicas) only where traffic (query load) is actually heavy.

**Example:**
A `Users` table with millions of rows keyed by an indexed `id`, a separate `UserSessions` table partitioned by month, and a Redis cache in front of `GET /users/:id` to avoid hitting the DB on every request.

**Interview Connection:**
This is a classic open-ended system design question used to see how you reason about trade-offs at scale, not just recite normalization rules.

**Common Mistake:**
Over-engineering for scale prematurely (sharding from day one) instead of starting simple and scaling incrementally based on actual bottlenecks.

**Follow-up:**
At what point would you consider sharding this database instead of just scaling vertically or adding read replicas?

---

### Q36. When would PostgreSQL be preferable to MongoDB, and vice versa?

**Answer:**
PostgreSQL is preferable when your data is naturally relational, you need strong consistency and complex multi-table queries/JOINs, and you value strict schema enforcement and mature ACID transaction guarantees — typical for financial systems, inventory, or anything with strict relationships between entities. MongoDB is preferable when your data is naturally document-shaped (nested, variable structure per record), your schema evolves frequently, and you prioritize horizontal write scalability over complex relational queries — typical for content management, catalogs with varying attributes, or event/log storage. In practice, PostgreSQL's JSONB support has blurred this line — it can handle many "document" use cases too.

**Mental Model:**
PostgreSQL is a well-organized filing cabinet with strict rules about what goes where; MongoDB is a set of flexible folders where each document can look a little different.

**Example:**
An e-commerce order system with strict relationships (users, orders, payments, inventory) fits PostgreSQL well; a product catalog where each product category has wildly different attributes fits MongoDB's flexible schema well.

**Interview Connection:**
This is a standard "SQL vs NoSQL" trade-off question used to see if you choose tools based on data shape and consistency needs, not just familiarity or hype.

**Common Mistake:**
Framing this as "MongoDB is for big scale, PostgreSQL isn't" — both can scale significantly; the real differentiator is data shape and consistency requirements, not raw scale.

**Follow-up:**
How does PostgreSQL's JSONB column type change this trade-off?

---

### Q37. How would you prevent SQL injection in an application?

**Answer:**
The primary defense is using parameterized queries (prepared statements) instead of string-concatenating user input directly into SQL — the database treats parameters strictly as data, never as executable SQL syntax, no matter what the input contains. Additional layers include using an ORM/query builder (which parameterizes by default), enforcing least-privilege database accounts (so even a successful injection can't do catastrophic damage), and validating/sanitizing input as a secondary defense, not a primary one. Never trust string concatenation for building queries with user-supplied values, even with escaping — parameterization is the reliable fix.

**Mental Model:**
Parameterized queries put user input in a locked box labeled "data only" — no matter what's inside, the database never reads it as instructions.

**Example:**
Vulnerable: `"SELECT * FROM Users WHERE email = '" + userInput + "'"`. Safe: `SELECT * FROM Users WHERE email = $1` with `userInput` passed as a bound parameter.

**Interview Connection:**
This is a standard security-awareness question in backend interviews, testing whether secure coding habits are automatic, not an afterthought.

**Common Mistake:**
Believing that "escaping quotes" manually is a sufficient defense — it's error-prone and easy to bypass; parameterized queries are the real fix.

**Follow-up:**
Why is input validation alone not sufficient to prevent SQL injection?

---

### Q38. How would you handle a database failure in a production backend system?

**Answer:**
Short-term, the application should fail gracefully — using connection retries with backoff, circuit breakers to stop hammering a downed database, and clear error responses rather than hanging requests, ideally backed by health checks and alerting so the team is notified immediately. Medium-term resilience comes from architecture: a replicated setup (primary + standby/read replicas) enables failover to a standby if the primary goes down, minimizing downtime. Long-term, regular backups (and tested restore procedures — untested backups are a false sense of security) protect against data loss, and monitoring/alerting catches degradation before it becomes a full outage.

**Mental Model:**
Think in layers: absorb the shock immediately (retries, circuit breakers), have a backup driver ready (replica failover), and keep an insurance policy in the drawer (tested backups).

**Example:**
A managed PostgreSQL setup with a synchronous standby replica can promote that replica to primary within seconds of a failure, while the application's connection pool retries and reconnects to the new primary.

**Interview Connection:**
This tests operational maturity — whether you think about databases as infrastructure with failure modes, not just a black box you query.

**Common Mistake:**
Treating "we have backups" as equivalent to "we have a failure plan" — backups address data loss, not availability during an outage.

**Follow-up:**
What's the difference between a hot standby and a cold backup, in terms of recovery time?

---

### Q39. What is database replication, and what problem does it solve?

**Answer:**
Replication is the process of copying data from a primary database to one or more replica databases, keeping them in sync (either synchronously or asynchronously) as changes happen. It solves two problems: read scalability (replicas can serve read-heavy traffic, offloading the primary) and availability/durability (if the primary fails, a replica can be promoted to take over, minimizing downtime and data loss). The trade-off with asynchronous replication is replication lag — replicas may briefly serve slightly stale data compared to the primary.

**Mental Model:**
Replication is like keeping several backup notebooks that copy everything written in the master notebook — useful for both handing out to readers and as a spare if the master notebook is destroyed.

**Example:**
A read-heavy analytics dashboard queries a read replica instead of the primary, so heavy reporting queries don't compete with the application's live write traffic.

**Interview Connection:**
Replication comes up in nearly every scaling discussion, and interviewers want to hear you connect it to concrete availability/performance benefits, not just define the word.

**Common Mistake:**
Assuming replicas are always perfectly up-to-date — with asynchronous replication, there's a lag window where a replica can serve stale reads.

**Follow-up:**
What problem could arise if your application writes to the primary and immediately reads from a replica?

---

### Q40. What is the difference between partitioning and sharding?

**Answer:**
Partitioning splits a single large table into smaller physical pieces (partitions) *within the same database instance*, based on a key like date range or region — the database still manages it as one logical table, transparently routing queries to the right partition. Sharding splits data *across multiple separate database instances/servers*, each holding a subset of the data — the application (or a routing layer) must know which shard holds which data, since there's no single database managing it all as one unit. Partitioning improves manageability and query performance on a single machine; sharding is what you reach for when a single machine's capacity is the actual bottleneck.

**Mental Model:**
Partitioning is organizing one giant warehouse into labeled sections; sharding is opening entirely separate warehouses in different cities, each holding only some of the inventory.

**Example:**
Partitioning: an `Events` table split into `events_2026_01`, `events_2026_02` partitions by month, all still queried as `Events`. Sharding: `Users` split across `shard_1` (user IDs 1–1M) and `shard_2` (user IDs 1M–2M), on entirely separate database servers.

**Interview Connection:**
This distinction is frequently tested because the two terms are often used loosely/interchangeably in casual conversation, and precision here signals real scaling experience.

**Common Mistake:**
Using "partitioning" and "sharding" interchangeably — partitioning is a single-instance optimization; sharding is a distributed-systems architecture decision with much higher complexity.

**Follow-up:**
What complexity does sharding introduce for queries that need to join data across shards?

---

### Q41. What is caching, and where would you introduce it in a backend system?

**Answer:**
Caching stores a copy of frequently-accessed or expensive-to-compute data in a faster-access layer (usually in-memory, like Redis or an in-process cache) so subsequent requests can skip hitting the database entirely. It's typically introduced in front of read-heavy, rarely-changing data — user profile lookups, product catalogs, computed aggregates — using a pattern like cache-aside (check cache first, fall back to DB and populate cache on a miss). The core trade-off is cache invalidation: stale cached data can be served if the underlying data changes and the cache isn't updated or expired appropriately.

**Mental Model:**
Caching is keeping a sticky note with the answer to a question you get asked constantly, instead of re-deriving the answer from scratch every single time.

**Example:**
Caching a user's profile data in Redis with a short TTL (say, 5 minutes) dramatically reduces database load for a `GET /users/:id` endpoint hit thousands of times per minute.

**Interview Connection:**
Caching is one of the most practical, universally-applicable performance topics, and interviewers use it to test whether you think about the full request path, not just the database.

**Common Mistake:**
Introducing caching without a clear invalidation strategy, leading to stale or inconsistent data being served long after the source changes.

**Follow-up:**
What's the difference between cache-aside, write-through, and write-behind caching strategies?

---

### Q42. What is connection pooling, and why does it matter?

**Answer:**
Connection pooling maintains a reusable set of open database connections that application requests borrow and return, instead of opening and closing a new TCP/database connection for every single request. Establishing a raw database connection is relatively expensive (TCP handshake, authentication, session setup), so pooling avoids paying that cost repeatedly under high request volume, and it also caps the total number of concurrent connections the database has to manage, protecting it from being overwhelmed. Most backend frameworks and ORMs include a connection pool by default, configurable by pool size, idle timeout, and max wait time.

**Mental Model:**
Connection pooling is like a taxi rank instead of building a brand-new car for every single passenger — cars (connections) wait ready to be reused, rather than being manufactured and scrapped per ride.

**Example:**
A backend service configured with a pool of 20 connections can serve many more than 20 concurrent requests, as long as each request's query finishes quickly and returns its connection to the pool.

**Interview Connection:**
This is a practical, often-overlooked topic that separates candidates who've actually run backend services in production from those who've only written queries locally.

**Common Mistake:**
Setting the pool size far higher than the database can actually handle, which can overwhelm the database rather than help it — pool size should be tuned to the database's capacity, not just the app's request volume.

**Follow-up:**
What happens to a request if all connections in the pool are currently in use?

---

### Q43. What is a query execution plan, and how do you read one?

**Answer:**
A query execution plan (viewed via `EXPLAIN` or `EXPLAIN ANALYZE`) shows exactly how the database intends to (or did) execute a query — which access method it uses per table (sequential scan vs. index scan), the join algorithm and order, and estimated vs. actual row counts and timing at each step. Reading it usually means scanning for red flags: sequential scans on large tables where an index scan was expected, large discrepancies between estimated and actual rows (suggesting stale statistics), or expensive operations like sorts and nested loop joins on large datasets. `EXPLAIN ANALYZE` actually runs the query and reports real timing, while plain `EXPLAIN` only estimates.

**Mental Model:**
An execution plan is the database showing its "work" like a math problem — you're checking whether it took the efficient shortcut or the long way around.

**Example:**
Seeing `Seq Scan on Orders (cost=0.00..18000.00 rows=1000000)` when you expected an index scan on a `customer_id` filter tells you the index either doesn't exist or isn't being used.

**Interview Connection:**
Being able to read an execution plan is one of the clearest practical signals of real hands-on database experience, beyond textbook SQL knowledge.

**Common Mistake:**
Only running `EXPLAIN` (estimates) instead of `EXPLAIN ANALYZE` (actual execution) when debugging a real performance issue, missing the true bottleneck.

**Follow-up:**
What does it mean if the planner's estimated row count is drastically different from the actual row count, and how would you fix it?

---

### Q44. How would you design tables for a many-to-many relationship, like students and courses?

**Answer:**
You create a junction (associative) table — commonly named something like `Enrollments` — that holds a foreign key to each side of the relationship (`student_id` referencing `Students.id`, and `course_id` referencing `Courses.id`). The junction table's primary key is typically the composite of both foreign keys (or a separate surrogate `id`, with a UNIQUE constraint on the pair to prevent duplicate enrollments). This design also naturally gives you a place to store relationship-specific attributes that don't belong to either entity alone, like `enrollment_date` or `grade`.

**Mental Model:**
The junction table is the "meeting point" — neither students nor courses can hold a list of each other cleanly, so you give the relationship itself its own table.

**Example:**
```sql
CREATE TABLE Enrollments (
  student_id INT REFERENCES Students(id),
  course_id INT REFERENCES Courses(id),
  enrollment_date DATE,
  PRIMARY KEY (student_id, course_id)
);
```

**Interview Connection:**
This is one of the most commonly asked hands-on schema design questions because it directly tests whether you can translate a relationship type into real DDL.

**Common Mistake:**
Trying to store a list of course IDs directly in a `Students` column (e.g., as a comma-separated string or array) instead of using a proper junction table — this breaks 1NF and referential integrity.

**Follow-up:**
How would you write a query to find all courses a given student is enrolled in, using this schema?

---

### Q45. How would you find and remove duplicate rows in a table using SQL?

**Answer:**
A common approach is to use a window function like `ROW_NUMBER()` partitioned by the columns that define a "duplicate," ordered by some tiebreaker (like `id`), then delete all rows where that row number is greater than 1 — keeping only the first occurrence per group. Alternatively, you can `GROUP BY` the duplicate-defining columns with `HAVING COUNT(*) > 1` to first *identify* which groups have duplicates, before deciding how to resolve them (which row to keep, e.g., most recent).

**Mental Model:**
Number every row within its duplicate group starting from 1, then throw away everyone after the first — the row numbering does the "who stays, who goes" decision for you.

**Example:**
```sql
DELETE FROM Users
WHERE id IN (
  SELECT id FROM (
    SELECT id, ROW_NUMBER() OVER (PARTITION BY email ORDER BY id) AS rn
    FROM Users
  ) t
  WHERE rn > 1
);
```

**Interview Connection:**
This is a very common hands-on SQL exercise because it combines window functions, subqueries, and careful thinking about "which duplicate to keep."

**Common Mistake:**
Deleting duplicates without first confirming which row should be considered "the original" (e.g., oldest vs newest) — this can unintentionally delete the wrong row.

**Follow-up:**
How would you find duplicates without deleting anything, just to report them first?

---

### Q46. What's the difference between a clustered and a non-clustered index?

**Answer:**
A clustered index determines the physical storage order of the table's rows — the table data itself is stored sorted by the indexed column(s), so there can only be one clustered index per table (often the primary key). A non-clustered index is a separate structure that stores the indexed column's values sorted, along with a pointer (or the primary key value) back to the actual row — a table can have many non-clustered indexes. Because clustered indexes dictate physical row order, range queries on the clustered key are especially fast, since matching rows are physically adjacent on disk.

**Mental Model:**
A clustered index is the book's pages themselves arranged in a specific order (e.g., alphabetically); a non-clustered index is a separate index card catalog pointing you to the right page number.

**Example:**
In SQL Server, if the primary key `id` is the clustered index, rows are physically stored sorted by `id`; a non-clustered index on `email` stores sorted email values with pointers back to the corresponding `id`.

**Interview Connection:**
This distinction matters more in SQL Server/MySQL (InnoDB) contexts and shows you understand storage internals, not just index syntax — though note PostgreSQL's indexing model differs (it doesn't have true clustered indexes by default).

**Common Mistake:**
Assuming every database implements clustered indexes the same way — PostgreSQL, for instance, doesn't maintain a persistently clustered table by default the way MySQL's InnoDB does.

**Follow-up:**
Why can a table have only one clustered index but many non-clustered indexes?

---

### Q47. How would you paginate a large result set efficiently in SQL?

**Answer:**
The naive approach — `LIMIT/OFFSET` — gets slower as the offset grows, because the database still has to scan and discard all the skipped rows before returning the requested page. A more scalable approach is keyset (cursor-based) pagination: instead of "skip N rows," you filter with `WHERE id > last_seen_id ORDER BY id LIMIT page_size`, which lets the database jump directly to the right starting point using an index, regardless of how deep into the result set you are. Keyset pagination requires a stable, indexed, sortable column (often the primary key or a timestamp) to anchor on.

**Mental Model:**
`OFFSET` pagination is like counting from the very beginning of a line every time to find your spot; keyset pagination is like remembering exactly where you left off and continuing from there.

**Example:**
Slow at scale: `SELECT * FROM Posts ORDER BY id LIMIT 20 OFFSET 100000;`. Faster: `SELECT * FROM Posts WHERE id > 100000 ORDER BY id LIMIT 20;`.

**Interview Connection:**
Pagination performance is a classic real-world backend gotcha, and this question separates candidates who've only used `LIMIT/OFFSET` from those who've hit its scaling wall in production.

**Common Mistake:**
Using `OFFSET` pagination for infinite-scroll or deep-paging features on large tables, without realizing performance degrades linearly with offset depth.

**Follow-up:**
What's a downside of keyset pagination compared to offset pagination (e.g., for "jump to page 50" UI features)?

---

### Q48. How would you model a schema so that it supports soft deletes instead of hard deletes?

**Answer:**
Instead of physically removing a row with `DELETE`, you add a column like `deleted_at` (nullable timestamp) or `is_deleted` (boolean), and "deleting" a row just means setting that column instead of removing the row. All normal queries then need a `WHERE deleted_at IS NULL` filter (often wrapped in a view or ORM default scope) to exclude soft-deleted rows from regular results, while still allowing recovery, auditing, or analytics on "deleted" data later. The trade-off is that unique constraints and foreign key relationships need extra care — a soft-deleted row's unique values (like an email) may still conflict with a new row unless the constraint accounts for `deleted_at`.

**Mental Model:**
Soft delete is like moving a file to Trash instead of shredding it — it's out of the way and hidden from normal browsing, but still recoverable if needed.

**Example:**
`UPDATE Users SET deleted_at = NOW() WHERE id = 5;` — the user "disappears" from the app's UI (via filtered queries) without losing their order history or audit trail.

**Interview Connection:**
This tests real-world schema design instincts around auditability and data recovery, which pure academic normalization questions don't cover.

**Common Mistake:**
Forgetting to add `deleted_at IS NULL` consistently across every query/join touching that table, leading to "deleted" data silently reappearing somewhere.

**Follow-up:**
How would you handle a UNIQUE constraint on `email` if soft-deleted users should be allowed to "free up" their email for reuse?

---

### Q49. Two services need to read the same frequently-updated counter (like a "likes" count) without hammering the database. How would you approach it?

**Answer:**
The most common approach is caching the counter in an in-memory store like Redis, using atomic increment operations (`INCR`) so concurrent updates don't race or lose increments, and periodically (or asynchronously) syncing that value back to the database rather than writing to the database on every single like. For read-heavy access, services read from the fast cache instead of the database entirely, with the database serving as the durable source of truth updated on a delay or via a write-behind queue. This trades a small window of potential inconsistency (cache vs. database) for a massive reduction in database write load.

**Mental Model:**
Instead of running to the vault (database) to update a number every single time someone likes a post, keep a running tally on a whiteboard (cache) and only formally record it in the vault periodically.

**Example:**
`INCR post:123:likes` in Redis handles the real-time counter; a background job flushes the current value to the `Posts.like_count` column in PostgreSQL every few seconds or on a batched schedule.

**Interview Connection:**
This is a favorite "real system" scenario question because it tests whether you can reach beyond "just query the database" and reason about caching, atomicity, and eventual consistency together.

**Common Mistake:**
Using a naive read-then-write pattern (`SELECT count`, then `UPDATE count = count + 1`) in the database under high concurrency, which causes lost updates without proper locking or atomic increments.

**Follow-up:**
What's the risk if the Redis cache crashes before syncing its latest counter values back to the database?

---

### Q50. In an interview, how would you explain the overall journey of a single SQL query from being written to returning results?

**Answer:**
First, the query is parsed and checked for syntax and semantic validity (do referenced tables/columns exist). Then the query planner/optimizer considers multiple possible execution strategies — which indexes to use, which join algorithm and order — and picks the one with the lowest estimated cost, based on table statistics. The chosen plan is executed: rows are fetched (via sequential scan or index scan), filtered, joined, grouped, and sorted as needed, often using locks or MVCC snapshots to ensure correct concurrent behavior. Finally, results are returned to the client, and if it's part of a transaction, changes aren't durable until that transaction commits.

**Mental Model:**
A query is like a travel request: parse it (understand the destination), plan the route (optimizer picks the fastest path), then actually make the trip (execution), respecting traffic rules along the way (locks/isolation).

**Example:**
`SELECT * FROM Orders WHERE customer_id = 5;` gets parsed, the optimizer decides between a sequential scan or an index scan on `customer_id` based on table statistics, then executes that chosen plan and streams rows back.

**Interview Connection:**
This "tie it all together" question is often asked near the end of an interview to see if you can synthesize everything — parsing, optimization, indexing, concurrency — into one coherent mental model.

**Common Mistake:**
Describing query execution as a single monolithic step, rather than distinct parse → plan → execute stages, which is what lets you reason about *where* a slow query is actually losing time.

**Follow-up:**
At which stage of this journey does the query planner decide whether to use an available index?

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

---

# FINAL REVISION

## 1. 20 Must-Remember Concepts

| Concept | One-line mental model |
|---|---|
| Primary key | The one unique, non-null ID every row must have |
| Foreign key | A pointer to another table's primary key, enforcing valid references |
| Normalization | Store each fact in exactly one place |
| 3NF | Every non-key column depends on the key, the whole key, and nothing but the key |
| Denormalization | Trade redundancy for read speed, deliberately |
| JOIN types | INNER = overlap only, LEFT/RIGHT = one full side + matches, FULL = everything |
| GROUP BY vs HAVING | WHERE filters rows before grouping, HAVING filters groups after |
| Window functions | Like GROUP BY, but keeps every row visible |
| Index | A sorted shortcut structure so the DB doesn't scan every row |
| B+Tree | Balanced tree that keeps index lookups at O(log n) even at scale |
| ACID | Atomic, Consistent, Isolated, Durable — the transaction safety contract |
| Isolation levels | Dial between consistency and concurrency; stricter = more blocking |
| Dirty read | Reading another transaction's uncommitted (possibly-rolled-back) data |
| Non-repeatable read | Same row, different value on re-read |
| Phantom read | Same query, different set of rows on re-read |
| Locks | Shared = many readers ok, Exclusive = one writer, everyone else waits |
| Deadlock | Circular wait between transactions; DB detects and kills one |
| MVCC | Readers see a consistent snapshot without blocking writers |
| Replication | Copy data to other servers for read scaling and failover |
| Sharding vs Partitioning | Sharding = across servers, Partitioning = within one table/instance |

## 2. 10 Concepts I Must Explain Without Notes

1. Difference between primary key, candidate key, and foreign key
2. 1NF, 2NF, 3NF with a concrete violating example for each
3. All four SQL JOIN types and what each returns
4. WHERE vs HAVING execution order
5. Why indexes speed up reads but slow down writes
6. ACID, defined precisely, letter by letter
7. Dirty read vs non-repeatable read vs phantom read
8. What a deadlock is and how databases resolve it
9. MVCC — why PostgreSQL doesn't block readers behind writers
10. Sharding vs partitioning vs replication — three different scaling tools

## 3. Top 10 Interview Questions

Referencing this guide's numbering:
- Q3 — What is a primary key?
- Q5 — What is a foreign key and what problem does it solve?
- Q14 — What is 3NF?
- Q18 — What are the different types of SQL JOINs?
- Q23 — What is an index, and why does it speed up queries?
- Q25 — What does ACID mean?
- Q27 — Dirty reads, non-repeatable reads, phantom reads
- Q29 — What is a deadlock?
- Q32 — An API query suddenly becomes slow — how do you investigate?
- Q37 — How would you prevent SQL injection?

## 4. One-Page DBMS Mental Map

```text
Database
│
├── Data Model
│   ├── Tables / Rows / Columns
│   ├── Keys (Primary, Foreign, Candidate)
│   └── Normalization (1NF → 2NF → 3NF)
│
├── SQL
│   ├── Queries (SELECT/WHERE/ORDER BY)
│   ├── Joins
│   ├── Aggregation (GROUP BY/HAVING)
│   └── Window Functions
│
├── Performance
│   ├── Indexes (B+Tree)
│   ├── Query Optimization (EXPLAIN ANALYZE)
│   └── Caching
│
├── Reliability
│   ├── Transactions
│   ├── ACID
│   ├── Isolation Levels (dirty/non-repeatable/phantom)
│   └── Locks / Deadlocks / MVCC
│
└── Scaling
    ├── Replication
    ├── Partitioning
    └── Sharding
```

## 5. Two-Day Plan

- **Day 1 → Q1–25:** Foundation — schema design, keys, normalization, core SQL, indexing basics, ACID.
- **Day 2 → Q26–50:** Application — isolation/concurrency, real debugging scenarios, scaling, security, design questions.

## 6. Active Recall Test

**Questions (attempt without looking back):**
1. What's the difference between a candidate key and a primary key?
2. Why does a table violate 2NF but not 3NF — give the distinguishing feature.
3. What does `HAVING` filter that `WHERE` cannot?
4. What's the difference between `RANK()` and `DENSE_RANK()`?
5. Why is a B+Tree preferred over a plain binary search tree for indexing?
6. Name the four ACID properties and define each in one phrase.
7. What's the difference between a non-repeatable read and a phantom read?
8. What causes a deadlock, and how does the database resolve it?
9. What's the core difference between sharding and partitioning?
10. Why is `OFFSET` pagination slow at scale, and what's the alternative?

**Answer Key (compact):**
1. Candidate key = any column that *could* uniquely identify a row; primary key = the one you chose to actually use.
2. 2NF fixes partial dependency (non-key depends on part of a composite key); 3NF fixes transitive dependency (non-key depends on another non-key).
3. `HAVING` filters on aggregated values *after* grouping; `WHERE` can't see aggregates because it runs before grouping.
4. `RANK()` leaves gaps after ties (1,1,3); `DENSE_RANK()` doesn't (1,1,2).
5. B+Trees are wide and shallow, minimizing disk reads; binary trees are narrow and deep, requiring far more disk accesses at scale.
6. Atomicity (all-or-nothing), Consistency (valid state to valid state), Isolation (concurrent transactions don't interfere), Durability (committed = permanent).
7. Non-repeatable read = same row, different value on re-read; phantom read = same query, different set of rows on re-read.
8. Two transactions each hold a lock the other needs (circular wait); the database detects it and aborts one transaction to break the cycle.
9. Sharding splits data across separate database servers; partitioning splits a table into pieces within a single database instance.
10. `OFFSET` forces the database to scan and discard all skipped rows first; keyset (cursor-based) pagination uses an indexed `WHERE id > last_id` filter instead, avoiding that scan.

---

## FINAL TAKEAWAY

- I understand how a relational schema is built: tables, keys, relationships, and normalization up through 3NF.
- I can write and reason about core SQL: filtering, grouping, joining, subqueries, CTEs, and window functions.
- I understand *why* indexes work (B+Trees) and the trade-off they impose on writes.
- I can define ACID precisely and connect it to real anomalies (dirty/non-repeatable/phantom reads).
- I understand locks, deadlocks, and MVCC well enough to explain concurrent-write scenarios in an interview.
- I can walk through a real "slow query" debugging scenario using `EXPLAIN ANALYZE`.
- I know when to reach for replication, partitioning, or sharding — and that they solve different problems.
- I should actively practice writing the JOIN, window function, and pagination queries by hand, not just read them.
- I can safely postpone deep internals (e.g., exact WAL/checkpoint mechanics, storage engine internals beyond B+Trees) — useful eventually, not interview-critical now.
- The goal isn't memorized definitions — it's being able to explain *why* each rule/mechanism exists, out loud, without notes.
