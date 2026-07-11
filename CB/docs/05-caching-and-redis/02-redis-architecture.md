# Day 20: Redis Architecture and Advanced Data Structures (MERN Stack Edition)  
*(Detailed, step-by-step, from first principles — with definitions, simple language, intuition, diagrams, and production Node.js examples)*

---

## SECTION 1 — Learning Objectives

**What will you learn?**
You will learn what Redis is, why it is vastly superior to basic Key-Value caches like Memcached, how its internal single-threaded event loop works, and how to use its rich data structures in a production MERN codebase.

**Why is this important?**
Redis is the most widely used in-memory database in the world. Whether you are at a Y-Combinator startup or at Meta, you will use Redis. Understanding it deeply separates junior developers (who just use it to cache JSON strings) from senior engineers (who use it for leaderboards, rate limiting, and geospatial queries).

**Where is it used?**
Session management, real-time leaderboards, pub/sub messaging, rate limiting, caching API responses, and fast queueing.

**Prerequisites:** Understanding of RAM vs. Disk latency (Day 19), basic data structures (Arrays, Hash Maps), and basic Node.js/Express.js.

---

## SECTION 2 — Motivation

**Why does this concept exist?**
Before Redis, if you wanted to cache data, you used Memcached. Memcached was great, but it was just a giant, dumb `String -> String` dictionary. If you cached a user's profile JSON and only wanted to update their "age", you had to fetch the entire JSON into your Node.js backend, parse it, update the age, re-serialize it, and send the whole string back to Memcached. This wasted massive network bandwidth and CPU.

**What problem were engineers trying to solve?**
Engineers needed a cache that was *smart*. They needed a cache that understood data structures natively, so you could tell the cache: "Hey, increment the age field in this user's hash map," without transferring the whole object over the network.

---

## SECTION 3 — Intuition

Imagine you have a super-fast assistant in your house.

How was Memcached? You give him a box labeled "Groceries" with 50 items inside. If you want to add "Milk", you have to take the whole box from him, open it, add Milk, and give the heavy box back.

**Redis is a smart assistant.**
Instead of a sealed box, you tell Redis: "Keep a List called Groceries." Later, you just shout: "Redis, push Milk to the Groceries list!" Redis does the operation *internally* and instantly. You never have to lift the heavy box.

Because Redis understands Lists, Sets, and Maps natively in RAM, it saves your Node.js backend from doing the heavy lifting.

---

## SECTION 4 — Formal Definitions

* **Redis (Remote Dictionary Server):** An open-source, in-memory, key-value data store that supports complex data structures.
* **Key-Value Store:** A database paradigm where every item is stored as a "Value" and retrieved via a unique "Key".
* **RDB (Redis Database Backup):** A persistence mechanism that takes point-in-time snapshots of your dataset at specified intervals.
* **AOF (Append Only File):** A persistence mechanism that logs every single write operation received by the server, which can be replayed to reconstruct the dataset.
* **Event Loop:** An infinite loop inside a program that waits for and dispatches events (like incoming network requests). Both Node.js and Redis use an event-driven, single-threaded model.

---

## SECTION 5 — History

**Who created it?**
Salvatore Sanfilippo (known online as *antirez*), an Italian developer, created Redis in 2009.

**Why?**
He was building a real-time web analytics startup called LLOOGG. MySQL was too slow to handle the massive influx of concurrent read/write operations required to track real-time website visitors. He needed something that lived in RAM but could handle lists and counters natively. He wrote the initial version of Redis in C, open-sourced it, and it changed the backend world forever.

---

## SECTION 6 — Deep Theory: Core & Advanced Data Structures

To build scalable MERN applications, you must understand both the basic and advanced data structures inside Redis, as well as its single-threaded nature.

### 1. Single-Threaded Architecture

Most databases spawn a new OS thread for every incoming connection. Redis does **not**.
Redis uses a **Single-Threaded Event Loop**. It processes exactly one command at a time.

**Why is this incredibly fast?**
Because accessing RAM takes nanoseconds. Context-switching between CPU threads takes microseconds (1000x slower). By avoiding thread locks, race conditions, and context switching, a single-threaded Redis instance can process upwards of 100,000+ requests per second.

### 2. Core Data Structures

* **Strings:** The most basic type. Can hold text, serialized JSON, or integers.
* **Lists:** Linked lists of strings. (Great for timelines or basic queues).
* **Sets:** Unordered collections of unique strings. (Great for tags, or tracking unique IP addresses).
* **Sorted Sets (ZSets):** Like Sets, but every item has a "score". Redis keeps the list perfectly sorted by this score in real-time. (The ultimate solution for Leaderboards).
* **Hashes:** Maps composed of fields associated with values. (Perfect for storing objects like a User Profile).

### 3. Advanced Data Structures

* **Streams:** An append-only log data structure. It represents a continuous stream of events.
  * **Why use it?** For Event-Driven systems (microservices). Streams allow **Consumer Groups**—multiple Node.js workers can read from the same stream, and Redis remembers which worker read which message.
* **Bitmaps:** A set of bit-level operations applied to a String.
  * **Why use it?** Tracking Daily Active Users (DAU) for 1 million users. Storing 1 million user IDs in an array takes megabytes. Flipping bits takes exactly **125 Kilobytes**.
* **HyperLogLog:** A probabilistic data structure used to estimate the number of unique elements.
  * **Why use it?** Counting unique IP addresses visiting your website. Uses a maximum of **12 KB of memory**, whether you count 1 thousand or 1 billion IPs, with 99.19% accuracy. (Simple Analogy: Like estimating there are 10,000 people by looking at a crowd).
* **Pub/Sub (Publish/Subscribe):** A messaging paradigm for real-time broadcasting.
  * **Internal Working:** It is **"Fire and Forget"**. If a Node.js server crashes and misses a message, it is lost forever. Used heavily with Socket.io for real-time chat.

---

## SECTION 7 — Internal Architecture & Memory

### 1. Persistence: RDB vs AOF

Since Redis is in RAM, what happens when the AWS EC2 instance restarts? To prevent complete data loss, Redis offers persistence.

**RDB (Redis Database Backup):**
* **How it works:** Takes a snapshot of your entire RAM and saves it to a `.rdb` file at specific intervals.
* **Internals (`fork()`):** Redis uses the Linux `fork()` system call to create a background clone. The clone writes data to disk while the main process serves Node.js requests without blocking.
* **Tradeoff:** Fast restarts, but you lose data written since the last snapshot.

**AOF (Append Only File):**
* **How it works:** Logs *every single write command* into an `.aof` file in real-time.
* **Internals:** On crash, Redis replays every command to rebuild the RAM state.
* **Tradeoff:** No data loss, but the file size grows massively, and server restart is slower.
* *Production Standard:* Use both. RDB for fast restarts, AOF for data safety.

> ✅ **[Principal Engineer Note]: The Fork() Memory Spike (OOM Crashes)**
> *When Redis creates an RDB snapshot, it uses the Linux `fork()` command, which uses "Copy-on-Write" memory. If your Redis is taking up 4GB of RAM out of an 8GB server, and you get a massive spike in write traffic while the snapshot is saving, the OS copies the modified memory pages. Suddenly, your Redis instance doubles its memory footprint to 8GB, hits the ceiling, and the Linux OOM Killer terminates Redis entirely. Never run Redis above 50-60% of your total system RAM!*

### 2. Memory Management & Eviction Policies

When your Node app writes 2.1GB into a 2GB Redis instance, Redis hits `maxmemory`. You must configure an **Eviction Policy**:

1. `noeviction`: (Default) Returns an error to your Node app. Your API crashes if not handled.
2. `allkeys-lru`: Deletes the Least Recently Used keys. (Best for standard caching).
3. `volatile-lru`: Deletes LRU keys, but *only* those that have a TTL (expiration) set.
4. `allkeys-lfu`: Deletes the Least *Frequently* Used keys.

### 3. Expiration (TTL Internals)

How does `redis.setex("key", 60, "value")` work?
1. **Passive Expiration:** When Node.js tries to `GET` the key, Redis checks the timestamp and deletes it on the spot if expired.
2. **Active Expiration:** 10 times a second, Redis randomly tests keys with TTLs and deletes the expired ones.

---

## SECTION 8 — Advanced Execution Flow

### 1. Transactions (MULTI / EXEC / WATCH)

In Redis, a transaction groups commands together so they execute sequentially without any other client interrupting.

* `MULTI`: Starts the transaction block.
* `EXEC`: Executes all commands in the block.
* **The Catch:** If one command fails (e.g., incrementing a non-integer), Redis *still executes the rest*. There is **no rollback**.

### 2. Lua Scripting

Every time Node.js sends a command to Redis and waits, you pay a "Network Latency" tax. If logic requires reading a value, doing math in Node, and writing it back, a Race Condition can occur.

* **Solution:** Write a script in the Lua language and send it to Redis. Redis executes the script **atomically**. Since Redis is single-threaded, no other command can run while the script executes.

### 3. Redis Modules

Redis can be extended using C-compiled modules:
* **RediSearch:** Full-text search engine capability.
* **RedisJSON:** Natively store and query nested JSON documents.

---

## SECTION 9 — Visual Diagrams

### Redis Single-Threaded Event Loop

```text
Concurrent Node.js Clients   OS Kernel (epoll)         Redis Process (Single Thread)
+----------+ 
| Node API | --(GET x)--> +-------------------+       +-------------------------+
+----------+              |                   |       |      Command Queue      |
                          | TCP Socket Array  | ----> | [GET x][SET y][INCR z]  |
+----------+              | (Multiplexer)     |       |           |             |
| Node API | --(SET y)--> |                   |       +-----------|-------------+
+----------+              +-------------------+                   | (One by one)
                                                                  v
+----------+                                          +-------------------------+
| Node API | --(INCR z)->                             |   RAM Execution Engine  |
+----------+                                          |  (Hash map data lookup) |
                                                      +-------------------------+
```

---

## SECTION 10 — Node.js / MERN Backend Implementation

Let's implement these advanced concepts using `ioredis` (the production-standard Redis client for Node.js).

**1. Setup (`npm install ioredis`)**

```javascript
const Redis = require('ioredis');

// Connect to Redis (Default is localhost:6379)
const redis = new Redis({
  host: '127.0.0.1',
  port: 6379,
  maxRetriesPerRequest: null // Required for advanced queueing features
});
```

**2. Bitmaps: Tracking Daily Active Users (DAU)**
Instead of storing user IDs in a Mongo Collection, we use a single bit per user.

```javascript
async function markUserActive(userId) {
  // Key format: dau:YYYY-MM-DD
  const today = new Date().toISOString().split('T')[0];
  const key = `dau:${today}`;
  
  // Set the bit at the offset corresponding to the userId to 1
  // If user ID is 5000, we flip the 5000th bit. Time complexity: O(1)
  await redis.setbit(key, userId, 1);
}

async function getDailyActiveUsers() {
  const today = new Date().toISOString().split('T')[0];
  const key = `dau:${today}`;
  
  // Count how many bits are set to 1 in this string
  const activeCount = await redis.bitcount(key);
  return activeCount;
}
```

**3. HyperLogLog: Counting Unique Page Views**

```javascript
async function recordPageView(pageId, userIp) {
  const key = `page_views:${pageId}`;
  
  // pfadd stands for "Probabilistic Feature Add". 
  // It hashes the IP and updates the internal 12KB data structure.
  await redis.pfadd(key, userIp);
}

async function getUniqueViewCount(pageId) {
  // pfcount returns the estimated unique count
  return await redis.pfcount(`page_views:${pageId}`);
}
```

**4. Transactions: Bank Transfer Example**
Ensuring that withdrawing from Account A and depositing to Account B happens without interruption.

```javascript
async function transferMoney(fromAccount, toAccount, amount) {
  try {
    // Start a pipeline transaction
    const pipeline = redis.multi();
    
    // Queue the commands
    pipeline.decrby(`balance:${fromAccount}`, amount);
    pipeline.incrby(`balance:${toAccount}`, amount);
    
    // Execute them sequentially inside Redis
    const results = await pipeline.exec();
    console.log("Transfer successful:", results);
  } catch (error) {
    console.error("Transfer failed", error);
  }
}
```

---

## SECTION 11 — Request Lifecycle

Trace an `incrementUserAge` request via a Redis Hash:

1. **Client:** POST `/users/123/birthday`
2. **Express.js Route:** Calls `redis.hincrby('user:123', 'age', 1)`.
3. **Network:** `ioredis` sends a TCP packet to the Redis Server.
4. **Redis (epoll):** The OS network layer adds it to the Event Loop Queue.
5. **Redis (Execution):** The single thread reads `HINCRBY`, finds `user:123` ($O(1)$), finds `age` ($O(1)$), increments, and writes to output.
6. **Response:** Redis sends the TCP packet back. Express returns `200 OK`.

---

## SECTION 12 — Performance

**Time Complexity of core commands:**
* `GET` / `SET` (Strings): $O(1)$
* `HGET` / `HSET` (Hashes): $O(1)$
* `ZADD` (Sorted Sets): $O(\log n)$ (Because it maintains a Skip List internally).

**Bottleneck:** Because Redis is single-threaded, **CPU speed matters more than CPU cores**. A 64-core processor won't make a single Redis instance faster. If you run a slow command (like `KEYS *`), **it blocks the single thread**, causing all Express APIs connected to it to timeout.

---

## SECTION 13 — Security

1. **Network Exposure:** Never expose Redis directly to the public internet (Port 6379). It should only be accessible within your private VPC.
2. **Authentication:** Configure `requirepass` in `redis.conf` to require a password.
3. **Command Renaming:** In production, dangerous commands like `KEYS`, `FLUSHALL`, and `CONFIG` should be renamed or disabled.

---

## SECTION 14 — Tradeoffs

**Advantages:**
* Unmatched speed (Sub-millisecond latency).
* Rich data structures reduce Node.js compute requirements.
* Built-in persistence allows it to act as a primary database for certain use cases.

**Disadvantages:**
* **Cost:** RAM is expensive. Storing massive data requires an expensive server cluster.
* **Single Point of Failure:** Without replication (Day 22), a server crash temporarily takes down caching.
* **Thread Blocking:** One bad $O(N)$ command halts the entire system.

---

## SECTION 15 — Common Mistakes (Node.js Specific)

1. **Not handling connection errors:** In Node.js, if Redis goes down, `ioredis` will buffer commands in memory waiting for it to come back. If it's down for too long, your Node API will run out of memory (OOM crash). Always add `redis.on('error', ...)` handlers.
2. **Using standard `redis.get()` for JSON in loops:** If you have an array of 50 User IDs, do not run `await redis.get()` inside a `for` loop. That is 50 network round trips. Use `await redis.mget(keysArray)` to fetch all 50 in a single network call.
3. **Blocking the Event Loop:** Node.js is single-threaded, and Redis is single-threaded. If you pull a massive 100MB list from Redis into Node.js, V8 will freeze while parsing that data, blocking all other API requests. Paginate your Redis lists using `LRANGE`.

> ✅ **[Principal Engineer Note]: Running KEYS * in Production**
> *The single deadliest command in Redis is `KEYS *`. Because Redis is single-threaded, if you have 10 million keys and run `KEYS *` (or even `KEYS user:*`), Redis will freeze for several seconds to scan memory. Every other API request across your entire cluster will queue up and time out, bringing down the whole architecture instantly. NEVER use `KEYS` in production. Always use the `SCAN` command, which iterates through keys incrementally via a cursor without blocking the thread.*

---

## SECTION 16 — Interview Preparation

**System Design Question:**

* *Company: Swiggy/Zomato*
* *Question:* "We need a system to limit a user to applying exactly 1 promo code per minute to prevent brute-force attacks on our API. How would you design this?"
* *Expected Answer:* I will use Redis. When the user requests `/apply-promo`, I will create a key `rate_limit:{userId}`. I will use the `INCR` command. If the result is 1, I use `EXPIRE key 60`. If the result is > 1, I block the request. Alternatively, I would write a Lua script to ensure `INCR` and `EXPIRE` are executed atomically to avoid race conditions.

---

## SECTION 17 — Revision Notes

* **Streams:** Event logs for microservices.
* **Bitmaps:** $O(1)$ space for boolean states (DAU tracking).
* **HyperLogLog:** 12KB memory for estimating unique items.
* **Pub/Sub:** Fire-and-forget real-time messaging.
* **RDB vs AOF:** RDB is a snapshot (smaller size, data loss risk). AOF is a command log (larger size, no data loss).
* **Lua Scripting:** Used to execute complex atomic logic inside the Redis server to avoid network latency and race conditions.

---

## SECTION 18 — Hands-On Assignment

**Conceptual:**
1. Why does HyperLogLog use a maximum of exactly 12KB of memory? (Research the math behind it briefly).
2. If you are building a chat application in Express.js and deploy it to 3 different servers (scaling horizontally), explain why saving WebSocket connections locally in memory fails, and how Redis Pub/Sub solves this.

**Coding Exercise:**
Write a Lua script as a string in Node.js that checks if a key exists; if it does, it deletes it and returns `1`; otherwise, it returns `0`. (Hint: use `redis.call('get', KEYS[1])`).

---

## SECTION 19 — Mini Project

**Mini Project: Real-Time View Counter**

* **Requirement:** Build an Express route that tracks how many times a specific article has been read.
* **Implementation:**  
  * Expose `GET /api/article/:id`.
  * Inside the controller, use `await redis.incr("article:views:" + id)`.
  * Return the new view count.
* **Interview consideration:** Why is this better than running `Article.updateOne({ _id: id }, { $inc: { views: 1 } })` in MongoDB? (Hint: Row-level/document locks in MongoDB under high concurrency cause write contention).

---

## SECTION 20 — Active Learning

To verify we are fully aligned on the complete Day 20 syllabus and the MERN stack context, please answer these two questions:

1. **Scenario:** You are building an analytics dashboard in Node.js that needs to show how many *unique* visitors a blog post had. The blog gets 5 million views a day. Will you use a Redis `Set` or a Redis `HyperLogLog`, and exactly why?
2. **Architecture:** In the context of Node.js and MongoDB, explain a scenario where you would intentionally configure your Redis Eviction Policy to `allkeys-lru` instead of the default `noeviction`. What happens to your Node API if you leave it as `noeviction` when RAM fills up?
