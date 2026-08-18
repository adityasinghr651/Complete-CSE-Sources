# MODULE 1 — CONCEPTS 1–50 (PART 3: 26-38)

# C. DATABASE DESIGN

## #26. SQL vs NoSQL [Type D — Trade-off Scenario]

### What is it?
- **SQL (Relational):** Data is stored in structured tables with strict columns, rows, and predefined relationships (Foreign Keys). e.g., PostgreSQL, MySQL.
- **NoSQL (Non-Relational):** Data is stored flexibly as JSON-like documents, key-value pairs, wide columns, or graphs. No strict schema required. e.g., MongoDB, DynamoDB, Cassandra.

### Mental Model
SQL = A strict Excel spreadsheet. Every row must have the exact same columns.
NoSQL = A filing cabinet of word documents. One document can have 5 paragraphs, another can have 20, and they don't have to match.

### Why does it exist?
SQL was designed in the 1970s for highly structured data where storage was expensive. NoSQL was born in the 2000s (by companies like Amazon and Google) to handle massive horizontal scale, unstructured data, and agile development where schemas change constantly.

### Real-World Example
**Uber:** Uses PostgreSQL for core relational data (user accounts, billing) because ACID guarantees are critical. Uses NoSQL (Cassandra) for high-velocity, massive-scale data like real-time GPS locations where strict relations aren't needed but extreme write-throughput is.

### Architecture / Raw Diagram
```text
SQL (Structured/Relations)        NoSQL (Document)
┌────┬──────┬──────┐              {
│ ID │ Name │RoleID│                "id": 1,
├────┼──────┼──────┤                "name": "Aditya",
│ 1  │ Aditya│  9   │                "skills": ["JS", "Go"]
└────┴──────┴──────┘              }
```

### Data Flow
N/A

### When Would I Use It?
- **SQL:** E-commerce, banking, inventory management, or any system where relationships (Joins) and data integrity (ACID) are the highest priority.
- **NoSQL:** Real-time analytics, user profiles, IoT sensor data, or rapid prototyping where the data schema is unknown or highly variable.

### When Would I NOT Use It?
- Don't use NoSQL if your application heavily relies on complex multi-table JOIN operations.
- Don't use SQL if you need to ingest 1 million unstructured logs per second.

### Trade-offs
- **SQL:** Strong consistency and relational power. BUT scaling horizontally (sharding) is notoriously difficult.
- **NoSQL:** Easy horizontal scaling and flexible schema. BUT sacrifices ACID guarantees (usually favors eventual consistency) and complex JOINs.

### Implementation Idea
If building a robust MVP, default to **PostgreSQL**. Modern Postgres supports `JSONB` columns, essentially giving you NoSQL flexibility inside a relational database.

### Interview Question
"You are designing a high-velocity event logging system. Would you choose PostgreSQL or Cassandra? Why?"

### Follow-up
"How does a NoSQL database handle a lack of Foreign Keys if you need to associate a User with their Posts?" (Answer: Denormalization/embedding the data, or doing 'application-level joins').

### Common Mistake
Saying "NoSQL is for big data, SQL is for small data." SQL databases can scale to petabytes. The choice is about the *shape* of the data and the *query patterns*, not just scale.

---

## #27. Relational Schema & Normalization [Type A — Concept]

### What is it?
**Normalization** is the process of structuring a relational database to reduce data redundancy and improve data integrity. It splits large tables into smaller tables linked by relationships.

### Mental Model
Instead of writing your home address on every single medical form you fill out at the doctor's office, you write it once on a master sheet, and all other forms just reference your "Patient ID."

### Why does it exist?
If you store a user's address directly on 50 different Order records, and the user moves, you have to update 50 rows. If you miss one, your data is corrupted (Update Anomaly). Normalization fixes this.

### Real-World Example
**Amazon Orders:** An order record does not contain the text "Aditya, 123 Main St." It contains `user_id = 5` and `address_id = 9`. The actual text lives in exactly one place.

### Architecture / Raw Diagram
```text
UNNORMALIZED:
Order | User   | Address
101   | Aditya | 123 Main
102   | Aditya | 123 Main

NORMALIZED (1-to-Many):
[ Users ]      [ Addresses ]      [ Orders ]
ID | Name      ID | UserID | Loc  ID | AddrID
1  | Aditya    9  | 1      | 123  101| 9
                                  102| 9
```

### Data Flow
N/A

### When Would I Use It?
- Always, as the starting point for any relational database design. (Typically aiming for 3rd Normal Form - 3NF).

### When Would I NOT Use It?
- In analytical databases (Data Warehouses) or extremely high-read scenarios where joining tables is too slow (see Denormalization).

### Trade-offs
- **What do I gain?** Data consistency. A fact is stored in exactly one place.
- **What do I sacrifice?** Query performance. To read a complete record, the database must perform expensive JOIN operations across multiple tables.

### Implementation Idea
In PostgreSQL, create distinct tables for `Users`, `Products`, and `Orders`, linking them with `FOREIGN KEY` constraints.

### Interview Question
"Design the database schema for a library management system."

### Follow-up
"What is a many-to-many relationship, and how do you represent it in SQL?" (Answer: Using a junction/join table).

### Common Mistake
Over-normalizing to the point of absurdity (e.g., creating a separate table for "First Name" and "Last Name"), which grinds database read performance to a halt due to excessive JOINs.

---

## #28. Denormalization [Type A — Concept]

### What is it?
The deliberate process of adding redundant data to a normalized database to reduce the number of JOINs required to read data, trading write speed/complexity for faster read performance.

### Mental Model
Keeping a printed cheat-sheet of formulas on your desk (redundant data) instead of walking to the library to look them up in the master textbook every time (a JOIN).

### Why does it exist?
JOIN operations are computationally expensive. At massive scale (millions of reads per second), performing 5 JOINs to render a user's profile page is too slow.

### Real-World Example
**Twitter/X Timeline:** A tweet object often contains the author's `username` and `profile_pic_url` embedded directly on the tweet record. Even though this violates normalization (if the user changes their pic, millions of tweets need updating), it allows the timeline to load instantly without joining the `Users` table for every tweet.

### Architecture / Raw Diagram
```text
NORMALIZED (Slow Read, 2 Tables):
SELECT * FROM Tweets JOIN Users ON Tweets.user_id = Users.id

DENORMALIZED (Fast Read, 1 Table):
[ Tweets Table ]
ID | Text      | UserID | Username (Redundant) | ProfilePic (Redundant)
1  | Hello!    | 5      | @aditya              | url.com/pic
```

### Data Flow
N/A

### When Would I Use It?
- In read-heavy systems where database CPU is bottlenecking on JOIN operations.
- In NoSQL document databases (where denormalization is the default approach).

### When Would I NOT Use It?
- In write-heavy systems (like a financial ledger) where data consistency is critical. Denormalization makes writes slow and dangerous.

### Trade-offs
- **What do I gain?** Blazing fast read queries.
- **What do I sacrifice?** Storage space, and complex write operations (updating a username now means finding and updating a million tweet rows to keep data consistent).

### Implementation Idea
If rendering an E-commerce order history is too slow, store the `product_name` and `price_at_purchase` directly on the `Order_Items` table, rather than joining the `Products` table dynamically.

### Interview Question
"Your database reads are extremely slow because of complex joins on the timeline. How do you optimize this at the database level?"

### Follow-up
"If you denormalize the author's username onto the post, how do you handle the user changing their username?"

### Common Mistake
Denormalizing prematurely. Start normalized. Only denormalize when metrics prove that JOINs are your actual bottleneck.

---

## #29. Read-Heavy vs Write-Heavy Systems [Type B — Practical Design]

### What is it?
Categorizing a system by its traffic ratio.
- **Read-Heavy:** 100 reads for every 1 write (e.g., YouTube, Twitter).
- **Write-Heavy:** 100 writes for every 1 read (e.g., IoT sensors, Logging systems).

### Mental Model
Read-Heavy = A museum. One person sets up the exhibit (write), millions come to look at it (read).
Write-Heavy = A security camera. It records continuously 24/7 (write), but is only looked at if a crime happens (read).

### Why does it exist?
The ratio dictates entirely different caching, database, and scaling strategies.

### Real-World Example
**YouTube (Read-Heavy):** Uses aggressive CDN caching, Redis caches, and read replicas because videos are uploaded once but watched millions of times.
**Datadog/Logging (Write-Heavy):** Uses append-only databases (like Cassandra or Time-Series DBs) and message queues to buffer massive incoming data streams without caching.

### Architecture / Raw Diagram
```text
READ-HEAVY ARCHITECTURE:
Writes ──> [ Primary DB ] ──(Sync)──> [ Replica 1 ] [ Replica 2 ]
                                         ^             ^
Reads ───────────────────────────────────┘─────────────┘

WRITE-HEAVY ARCHITECTURE:
Writes ──> [ Kafka Queue ] ──> [ Fast Append-Only DB (Cassandra) ]
Reads  <── (Rarely happen) ────┘
```

### Data Flow
N/A

### When Would I Use It?
- Early in the interview during scale estimation. "Is this system read-heavy or write-heavy?"

### When Would I NOT Use It?
- N/A

### Trade-offs
- **Read-Heavy Optimization:** Add caches and indexes. (Indexes speed up reads, but slow down writes).
- **Write-Heavy Optimization:** Remove indexes, add message queues to buffer writes, and use NoSQL.

### Implementation Idea
For a Read-Heavy blog: Add a Redis Cache in front of the PostgreSQL database.
For a Write-Heavy metrics app: Use an in-memory buffer or Kafka to batch writes before inserting them into a Time-Series database like InfluxDB.

### Interview Question
"Design a system to collect temperature data from 10 million IoT devices every second."

### Follow-up
"Why is a standard PostgreSQL setup a bad choice for a write-heavy IoT system?"

### Common Mistake
Adding Redis to a Write-Heavy system. If data is written constantly and rarely read, caching it in Redis is a massive waste of memory and adds unnecessary invalidation overhead.

---

## #30. Database Indexes [Type A — Concept]

### What is it?
A separate data structure (usually a B-Tree) created by the database to quickly locate rows based on a specific column, without having to scan the entire table.

### Mental Model
An index is the Glossary at the back of a textbook. Instead of reading all 500 pages to find the word "System Design," you check the glossary, which tells you it's exactly on page 412.

### Why does it exist?
Without an index, the database must perform a "Sequential Scan" (reading every single row on the hard drive). In a table with 10 million rows, a sequential scan takes seconds. An index lookup takes milliseconds.

### Real-World Example
In a `Users` table, looking up a user by `email` for login is slow. Adding `CREATE INDEX idx_email ON users(email);` makes the login query nearly instant.

### Architecture / Raw Diagram
```text
NO INDEX (O(N) Scan):
DB checks Row 1, Row 2, Row 3... Row 10,000,000.

B-TREE INDEX (O(log N) Search):
             [ 50 ]
           /        \
       [ 25 ]      [ 75 ]
      /      \    /      \
    [10]    [30][60]    [90] --> Pointer to actual disk row
```

### Data Flow
N/A

### When Would I Use It?
- Columns frequently used in `WHERE`, `JOIN`, or `ORDER BY` clauses.
- Columns with high cardinality (many unique values, like User ID or Email).

### When Would I NOT Use It?
- Columns with low cardinality (e.g., a boolean `is_active` column).
- Tables that are extremely small.
- Tables that are extremely write-heavy (like a log table).

### Trade-offs
- **What do I gain?** Massive read performance speedups.
- **What do I sacrifice?** Storage space (indexes take up disk space) and write performance (every `INSERT`/`UPDATE`/`DELETE` must now also update the index tree).

### Implementation Idea
Check your slow queries using `EXPLAIN ANALYZE` in PostgreSQL. If you see `Seq Scan`, add an index.
```sql
CREATE INDEX idx_users_created_at ON users(created_at);
```

### Interview Question
"An API endpoint querying user orders by date is timing out. How do you fix it at the database level?"

### Follow-up
"Why shouldn't you just add an index to every single column in the table?"

### Common Mistake
Thinking indexes are a free performance boost. Candidates often suggest indexing everything, ignoring the severe write-amplification penalty it causes.

---

## #31. ACID Transactions [Type A — Concept]

### What is it?
A set of properties ensuring database transactions are processed reliably.
- **Atomicity:** All or nothing. (No partial saves).
- **Consistency:** Data moves from one valid state to another.
- **Isolation:** Concurrent transactions don't mess with each other.
- **Durability:** Once saved, it survives a power crash.

### Mental Model
Transferring $100 from Alice to Bob.
Atomicity: You can't deduct from Alice and fail to add to Bob. Both happen, or neither happens.
Durability: If someone pulls the server's power plug right after the transfer, the money is still there when it reboots.

### Why does it exist?
To protect data integrity. Without ACID, a server crash or a race condition between two users could literally destroy money in a banking system.

### Real-World Example
**Stripe:** Heavily relies on PostgreSQL's ACID guarantees to ensure that billing cycles, payments, and ledger balances are mathematically perfect, regardless of network crashes.

### Architecture / Raw Diagram
```text
BEGIN TRANSACTION;
  UPDATE accounts SET bal = bal - 100 WHERE name = 'Alice';
  -- (If Server crashes here, DB rolls back. No money lost)
  UPDATE accounts SET bal = bal + 100 WHERE name = 'Bob';
COMMIT;
```

### Data Flow
N/A

### When Would I Use It?
- Financial systems, inventory management, or anything where partial data writes are unacceptable.

### When Would I NOT Use It?
- Real-time analytics, social media likes, or log streams where losing a single record doesn't matter, but extreme speed does.

### Trade-offs
- **What do I gain?** Perfect data safety and developer peace of mind.
- **What do I sacrifice?** Concurrency speed. Strict ACID (Serializable isolation) requires heavy locking, which slows down high-throughput systems.

### Implementation Idea
In Node.js with a SQL ORM (like Sequelize or Prisma):
```javascript
await db.transaction(async (t) => {
  await User.decrement('balance', { by: 100, transaction: t });
  await Seller.increment('balance', { by: 100, transaction: t });
}); // If anything throws an error, the whole block rolls back.
```

### Interview Question
"What does ACID stand for, and why is it difficult to achieve in a NoSQL database?"

### Follow-up
"What is the difference between Atomicity and Isolation?"

### Common Mistake
Confusing Consistency in ACID (database rules/constraints) with Consistency in the CAP theorem (all nodes seeing the same data). They are different concepts sharing the same name.

---

## #32. Replication & Read Replicas [Type B — Practical Design]

### What is it?
Replication is copying data from one database (Primary) to one or more databases (Replicas). A **Read Replica** is a read-only copy of the primary database used to serve read traffic.

### Mental Model
The Primary database is the author writing a book. The Read Replicas are the printed copies given to readers. Readers don't bother the author; they just read the copies.

### Why does it exist?
To scale read-heavy applications and provide high availability. A single database can only handle so many queries. Replicas distribute the read load.

### Real-World Example
**WordPress Blogs:** 99% of traffic is reading articles. By having 1 Primary database (for the admin creating posts) and 3 Read Replicas (for users reading posts), the system scales beautifully.

### Architecture / Raw Diagram
```text
      Writes (POST/PUT)           Reads (GET)
             │                        │
             v                        v
      ┌─────────────┐        ┌─────────────────┐
      │ Primary DB  │───────>│ Load Balancer   │
      └──────┬──────┘(Sync)  └────┬───────┬────┘
             │                    │       │
       ┌─────┴─────┐              v       v
       v           v           ┌────┐   ┌────┐
    ┌────┐       ┌────┐        │Rep1│   │Rep2│
    │Rep1│       │Rep2│        └────┘   └────┘
    └────┘       └────┘
```

### Data Flow
1. User creates a post -> hits Primary DB.
2. Primary DB writes to disk.
3. Primary DB asynchronously streams the update to Replicas.
4. User reads a post -> hits Load Balancer -> routes to Replica 1.

### When Would I Use It?
- Any read-heavy relational database system (which is most systems).
- To provide a hot-standby (if Primary dies, promote a Replica).

### When Would I NOT Use It?
- Write-heavy systems. Replicas don't help you scale writes (all writes must still go to the single Primary).

### Trade-offs
- **What do I gain?** Massive read scalability and failover redundancy.
- **What do I sacrifice?** Replication Lag. Because replication is usually asynchronous, a user might write data to the Primary, instantly refresh the page, read from a Replica, and not see their data yet (Eventual Consistency).

### Implementation Idea
In AWS RDS, clicking "Add Read Replica" provisions this automatically. In the app code, configure the ORM with two connection strings: one for writes, one for reads.

### Interview Question
"Your single PostgreSQL database is at 95% CPU, mostly from `SELECT` queries. How do you scale it?"

### Follow-up
"If you use asynchronous read replicas, how do you handle the problem of 'Read-after-Write' inconsistency (a user updating their profile and not seeing the changes immediately)?" (Answer: Route the specific user's reads to the Primary DB for a few seconds after they make a write).

### Common Mistake
Thinking Read Replicas solve write bottlenecks. They do not. If your writes are maxing out the database, you need Sharding or a NoSQL solution.

---

## #33. Sharding vs Partitioning [Type D — Trade-off Scenario]

### What is it?
Techniques for splitting massive tables.
- **Partitioning:** Splitting a large table into smaller pieces *within the same database server* (e.g., partitioning logs by month).
- **Sharding:** Splitting a large database *across multiple separate physical servers* (e.g., User IDs 1-1M on Server A, 1M-2M on Server B).

### Mental Model
Partitioning = Organizing a massive filing cabinet into separate drawers (A-M, N-Z) in the *same room*.
Sharding = Moving the A-M drawers to a building in New York, and the N-Z drawers to a building in London.

### Why does it exist?
When a dataset becomes so large (terabytes) that it exceeds the CPU, RAM, or Disk limits of a single machine, or when write-throughput exceeds what one machine can handle.

### Real-World Example
**Discord:** Stores billions of messages. A single database can't hold them or process the writes. They shard their Cassandra database by `channel_id`, so all messages for one channel live on a specific server, spreading the massive write load across a cluster.

### Architecture / Raw Diagram
```text
SHARDING ARCHITECTURE:
                  Client
                     │
            [ Routing Layer ] (Checks Shard Key)
             /               \
       Shard A               Shard B
    (Users A-M)           (Users N-Z)
    [Database 1]          [Database 2]
```

### Data Flow
1. Client requests data for User "Bob".
2. Application hashes "Bob" or uses a lookup table to find the shard.
3. Application routes SQL query specifically to Database 1.

### When Would I Use It?
- **Partitioning:** When queries are getting slow on a massive table, but you still have room on the server.
- **Sharding:** As a last resort for extreme scale (billions of rows, massive write throughput) when Vertical Scaling and Replicas have failed.

### When Would I NOT Use It?
- Avoid Sharding relational databases unless absolutely necessary. It introduces catastrophic complexity.

### Trade-offs
- **What do I gain?** Infinite horizontal scale for writes and storage.
- **What do I sacrifice?** JOINs across shards are nearly impossible. Schema changes are a nightmare. High operational complexity (handling a shard failure, rebalancing data).

### Implementation Idea
Instead of building custom application-level sharding for PostgreSQL, use a managed NoSQL database like **DynamoDB** or **MongoDB**, which handles sharding (partitioning) under the hood transparently.

### Interview Question
"What is the difference between sharding and partitioning, and why is sharding considered a last resort in relational databases?"

### Follow-up
"What is a 'Celebrity Problem' or 'Hot Spot' in sharding?" (Answer: If you shard by user, and one user is Justin Bieber with millions of followers, his specific shard will crash under the traffic, defeating the purpose of load distribution).

### Common Mistake
Recommending sharding in the first 5 minutes of an interview for a system that will only have 1 million users. A single PostgreSQL instance can easily handle millions of rows.

---

## #34. Connection Pooling [Type C — Debugging Scenario]

### What is it?
A cache of active database connections maintained in application memory so they can be reused for future requests, rather than opening and closing a new connection to the database for every single API call.

### Mental Model
Like an Uber taxi rank. Instead of building a brand new car and hiring a driver every time someone requests a ride (expensive), you keep 10 cars running in a queue ready to take the next passenger.

### Why does it exist?
Establishing a TCP connection and authenticating with a database (the handshake) takes significant time and CPU. Doing this 1,000 times a second will crash the database purely from connection overhead.

### Real-World Example
Any standard Node.js, Java, or Python web backend. If you don't configure a connection pool, your app will crash under load with "Too many connections" errors from PostgreSQL.

### Architecture / Raw Diagram
```text
Without Pool:
Req 1 ─> Open TCP ─> Query ─> Close TCP
Req 2 ─> Open TCP ─> Query ─> Close TCP (Slow, exhausts DB)

With Pool:
[ Pool holds 10 open connections ]
Req 1 ─> Borrows Conn 1 ─> Query ─> Returns Conn 1 to Pool
Req 2 ─> Borrows Conn 2 ─> Query ─> Returns Conn 2 to Pool (Fast)
```

### Data Flow
N/A

### When Would I Use It?
- In *every* backend application that connects to a database.

### When Would I NOT Use It?
- Serverless functions (like AWS Lambda). Since Lambdas spin up and die rapidly, they can't maintain a persistent pool. (You must use an external proxy like AWS RDS Proxy).

### Trade-offs
- **What do I gain?** Massive reduction in latency and database CPU load.
- **What do I sacrifice?** A small amount of application memory to hold idle connections.

### Implementation Idea
In Node.js using `pg` (PostgreSQL):
```javascript
const { Pool } = require('pg');
const pool = new Pool({ max: 20 }); // Maintains 20 connections

app.get('/users', async (req, res) => {
  const result = await pool.query('SELECT * FROM users');
  res.json(result.rows);
});
```

### Interview Question
"Your new API works perfectly locally, but under a load test of 500 concurrent users, the database rejects connections and the API crashes. What is likely missing?"

### Follow-up
"How does connection pooling fail in a Serverless (AWS Lambda) environment?"

### Common Mistake
Setting the pool size too high (e.g., 1000). Databases are optimized for a small number of active connections (e.g., 20-50 per node). A pool size of 1000 will overwhelm the DB's scheduler.

---

# D. CACHING

## #35. Cache-Aside Pattern [Type E — Implementation Scenario]

### What is it?
The most common caching pattern. The application code dictates the logic: it checks the cache first. If it's a miss, it reads from the database, saves the result to the cache, and returns it.

### Mental Model
Looking for a word definition. Check your personal notebook first (Cache). If it’s not there (Miss), look it up in the heavy dictionary (DB), write it down in your notebook for next time (Save), and use it.

### Why does it exist?
To protect databases from redundant read queries. Databases are slow (fetching from disk). Caches (like Redis) are in-memory and lightning fast.

### Real-World Example
**User Profiles:** When a user visits `/profile/aditya`, the backend checks Redis for `user:aditya`. On a cache miss, it hits PostgreSQL, formats the JSON, and stores it in Redis for 1 hour. Subsequent visits load instantly.

### Architecture / Raw Diagram
```text
                 (1) Check Cache
               ┌─────────────────> [ Redis ]
               │                       │ (2) Hit? Return data.
[ API Server ] ┴                       v
               ┬ (3) Miss?         (Client)
               │     Read DB
               └─────────────────> [ Database ]
                 (4) Save to Cache
```

### Data Flow
1. API receives request.
2. API queries Redis.
3. If Hit: Return data immediately.
4. If Miss: Query Database -> Save result to Redis -> Return data.

### When Would I Use It?
- Read-heavy data that doesn't change every millisecond (e.g., user profiles, product catalogs, article content).

### When Would I NOT Use It?
- Real-time financial balances or live GPS tracking, where reading slightly stale data is disastrous.

### Trade-offs
- **What do I gain?** Massive read scaling and sub-millisecond latency.
- **What do I sacrifice?** Data consistency (stale data) and increased application code complexity.

### Implementation Idea
```javascript
async function getUser(id) {
  // 1. Check cache
  const cached = await redis.get(`user:${id}`);
  if (cached) return JSON.parse(cached);

  // 2. Cache miss, check DB
  const user = await db.query('SELECT * FROM users WHERE id = ?', [id]);
  
  // 3. Save to cache with TTL (1 hour)
  await redis.setex(`user:${id}`, 3600, JSON.stringify(user));
  return user;
}
```

### Interview Question
"Explain the cache-aside pattern. What happens when the data in the database is updated?"

### Follow-up
"What is a cache penalty?" (Answer: The added latency of checking the cache, missing, querying the DB, and writing to the cache—making a miss slightly slower than just querying the DB directly).

### Common Mistake
Caching raw database rows instead of the final aggregated JSON response. Cache the data in the exact format the client needs to save CPU cycles on serialization.

---

## #36. Write-Through & Write-Back Caching [Type A — Concept]

### What is it?
Alternative caching patterns focusing on how writes are handled.
- **Write-Through:** The application writes data to the cache and the database simultaneously.
- **Write-Back (Write-Behind):** The application writes data *only* to the cache, and a background process asynchronously syncs it to the database later.

### Mental Model
Write-Through: Putting a copy of a receipt in your wallet AND the filing cabinet immediately.
Write-Back: Throwing receipts in a basket on your desk, and emptying the basket into the filing cabinet once a week.

### Why does it exist?
Cache-aside (previous concept) can lead to stale data if the DB updates but the cache isn't cleared. Write-Through guarantees absolute consistency between cache and DB. Write-Back guarantees extreme write performance.

### Real-World Example
**DynamoDB Accelerator (DAX):** Operates as a write-through cache. You write to DAX, and DAX handles writing to DynamoDB, ensuring they are always in sync.
**High-Speed Loggers:** Often use write-back, writing logs to a memory buffer (fast) which syncs to disk every few seconds.

### Architecture / Raw Diagram
```text
WRITE-THROUGH:
App ──> Cache ──(Sync Wait)──> Database
(Safe, but slow writes)

WRITE-BACK:
App ──> Cache ──(Async Batch)──> Database
(Fast writes, but risk of data loss if Cache crashes)
```

### Data Flow
N/A

### When Would I Use It?
- **Write-Through:** Systems requiring absolute consistency where you cannot tolerate stale cache data.
- **Write-Back:** Write-heavy systems (like a "likes" counter on a viral video) where writing to the DB directly would crash it.

### When Would I NOT Use It?
- Don't use Write-Back for critical financial transactions. If the cache node loses power before syncing to the DB, the money vanishes.

### Trade-offs
- **Write-Through:** Perfect consistency. BUT higher write latency (must write to two places).
- **Write-Back:** Incredible write throughput. BUT complex to implement and risk of data loss on crash.

### Implementation Idea
For a viral tweet's "Like" counter (Write-Back):
When a user clicks like, do `redis.incr('tweet:123:likes')`. Have a cron job run every 10 seconds that reads the Redis value and executes `UPDATE tweets SET likes = X WHERE id = 123`.

### Interview Question
"How would you design a system to handle a viral video getting 100,000 'likes' per second without crashing the database?"

### Follow-up
"If you use a Write-Back cache for likes, what happens if the Redis server restarts unexpectedly?"

### Common Mistake
Over-engineering Cache-aside systems into Write-Through systems when a simple TTL (Time To Live) invalidation would have been sufficient.

---

## #37. Cache Invalidation & TTL [Type C — Debugging Scenario]

### What is it?
- **Cache Invalidation:** The process of explicitly deleting or updating cached data when the source-of-truth database is modified.
- **TTL (Time To Live):** Setting an expiration timer on a cache key so it deletes itself automatically after a set period.

### Mental Model
TTL: A carton of milk with an expiration date.
Invalidation: Throwing out the milk early because you accidentally left it in the sun.

### Why does it exist?
"There are only two hard things in Computer Science: cache invalidation and naming things." If a user updates their username, but the profile page cache isn't invalidated, they will see their old username, leading to bug reports.

### Real-World Example
**News Websites:** Front-page articles might have a TTL of 5 minutes. But if an editor makes a critical typo correction, the CMS fires an event that explicitly invalidates the cache key for that article to force an immediate refresh.

### Architecture / Raw Diagram
```text
User updates profile ──> [ API Server ]
                              │
                              ├──> UPDATE users DB
                              └──> DELETE redis_key: user:123 (Invalidation)
```

### Data Flow
1. Client sends `PUT /users/123`.
2. Backend updates PostgreSQL.
3. Backend calls `redis.del('user:123')`.
4. Next `GET` request will miss the cache, fetch fresh from DB, and cache it.

### When Would I Use It?
- TTL: Every single piece of cached data should have a TTL as a failsafe.
- Invalidation: On any `PUT`, `POST`, or `DELETE` endpoint that modifies cached resources.

### When Would I NOT Use It?
- N/A. An infinite cache without a TTL is a memory leak waiting to happen.

### Trade-offs
- **What do I gain?** Data consistency and memory management (Redis evicts expired keys to save RAM).
- **What do I sacrifice?** Code complexity. You must remember to invalidate keys across all microservices that might modify that data.

### Implementation Idea
```javascript
// On Update:
await db.query('UPDATE users SET name = ? WHERE id = ?', [name, id]);
await redis.del(`user:${id}`); // Explicit Invalidation
```

### Interview Question
"You added Redis to your system, but users complain that when they update their profile pictures, the old picture still shows up. What went wrong?"

### Follow-up
"If you use a microservices architecture where the 'Billing Service' updates a user's premium status, how does the 'Profile Service' know to invalidate its cache?" (Answer: Event-driven architecture. Billing emits an event to a Message Queue, Profile listens and deletes the key).

### Common Mistake
Setting TTLs to "Never" or setting them too long (e.g., 30 days) without explicit invalidation logic, resulting in hopelessly stale data.

---

## #38. Cache Stampede (Thundering Herd) [Type C — Debugging Scenario]

### What is it?
A failure mode where a highly popular cache key expires (or is deleted), and suddenly thousands of concurrent requests miss the cache at the exact same millisecond. All thousands of requests instantly query the database, crushing it.

### Mental Model
A popular exhibit at a museum closes for 5 minutes for cleaning (Cache expiration). A massive crowd forms outside the door. When it opens, everyone rushes in at once and tramples the staff (Database).

### Why does it exist?
Because high-traffic systems rely heavily on caches. If a key accessed 10,000 times a second expires, the database isn't provisioned to handle that traffic spike.

### Real-World Example
**E-commerce Flash Sales:** The cache key for "Black Friday Deal Price" expires at exactly midnight. Millions of clients hit the backend, miss the cache, and hammer the database to calculate the deal price, causing a total outage.

### Architecture / Raw Diagram
```text
NORMAL:
10,000 Req/sec ──> [ Redis Cache ] (Hits)

STAMPEDE:
Cache Key Expires!
10,000 Req/sec ──> [ Redis Cache ] (MISS)
                     │
                     └──> 10,000 simultaneous queries hammer [ DB ] 💥
```

### Data Flow
N/A

### When Would I Use It?
- A critical failure scenario to discuss when designing highly concurrent systems (like ticket sales or viral feeds).

### When Would I NOT Use It?
- Low-traffic internal tools don't experience stampedes.

### Trade-offs
- N/A

### Implementation Idea (Solutions)
1. **Debouncing / Mutex Locks:** When the cache misses, the first request acquires a Redis lock (`SETNX`), queries the DB, and sets the cache. The other 9,999 requests wait for the lock to release or poll the cache again.
2. **Probabilistic Early Expiration:** Re-compute the cache slightly *before* it actually expires.

```javascript
// Simple Lock concept
let cached = await redis.get('viral_post');
if (!cached) {
  const lock = await redis.set('lock:viral_post', '1', 'NX', 'EX', 5);
  if (lock) {
    // Only ONE request gets here
    const data = await db.query(...);
    await redis.setex('viral_post', 3600, data);
  } else {
    // Others wait and retry
    await sleep(100); 
    return getPost(); 
  }
}
```

### Interview Question
"Your system relies heavily on Redis to cache the homepage. The cache key has a 1-hour TTL. Exactly every hour, your database CPU spikes to 100% and crashes. What is happening?"

### Follow-up
"How would you use a background worker to prevent a cache stampede entirely?" (Answer: Have a cron job update the cache key every 55 minutes, so it never actually expires and users never trigger a cache miss).

### Common Mistake
Assuming caching solves all database load problems without realizing that a cache miss on a hot key essentially removes the cache entirely for that instant.

---
*(End of Part 3)*
