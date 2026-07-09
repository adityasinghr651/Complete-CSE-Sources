# Day 20: Redis Architecture and Data Structures  
*(Detailed, step-by-step, from first principles — with definitions, simple language, Hinglish intuition, diagrams, and production examples)*

---

## SECTION 1 — Learning Objectives

**What will you learn?**
You will learn what Redis is, why it is vastly superior to basic Key-Value caches like Memcached, how its internal single-threaded event loop works, and how to use its rich data structures in a production codebase.

**Why is this important?**
Redis is the most widely used in-memory database in the world. Whether you are at a Y-Combinator startup or at Meta, you will use Redis. Understanding it deeply separates junior developers (who just use it to cache JSON strings) from senior engineers (who use it for leaderboards, rate limiting, and geospatial queries).

**Where is it used?**
Session management, real-time leaderboards, pub/sub messaging, rate limiting, caching API responses, and fast queueing.

**Prerequisites:** Understanding of RAM vs. Disk latency (Day 19), basic data structures (Arrays, Hash Maps), and basic Java/Spring Boot.

---

## SECTION 2 — Motivation

**Why does this concept exist?**
Before Redis, if you wanted to cache data, you used Memcached. Memcached was great, but it was just a giant, dumb `String -> String` dictionary. If you cached a user's profile JSON and only wanted to update their "age", you had to fetch the entire JSON into your backend, parse it, update the age, re-serialize it, and send the whole string back to Memcached. This wasted massive network bandwidth and CPU.

**What problem were engineers trying to solve?**
Engineers needed a cache that was *smart*. They needed a cache that understood data structures natively, so you could tell the cache: "Hey, increment the age field in this user's hash map," without transferring the whole object over the network.

---

## SECTION 3 — Intuition

Imagine tumhare ghar mein ek super-fast assistant hai (Imagine you have a super-fast assistant in your house).

Memcached kaisa tha? (How was Memcached?) You give him a box labeled "Groceries" with 50 items inside. If you want to add "Milk", you have to take the whole box from him, open it, add Milk, and give the heavy box back.

**Redis is a smart assistant.**
Instead of a sealed box, you tell Redis: "Keep a List called Groceries." Later, you just shout: "Redis, push Milk to the Groceries list!" Redis does the operation *internally* and instantly. You never have to lift the heavy box.

Because Redis understands Lists, Sets, and Maps natively in RAM, it saves your backend servers from doing the heavy lifting.

---

## SECTION 4 — Formal Definitions

* **Redis (Remote Dictionary Server):** An open-source, in-memory, key-value data store that supports complex data structures, used as a database, cache, message broker, and streaming engine.
* **Key-Value Store:** A simple database paradigm where every item is stored as a "Value" and retrieved via a unique "Key".
* **RDB (Redis Database Backup):** A persistence mechanism that takes point-in-time snapshots of your dataset at specified intervals.
* **AOF (Append Only File):** A persistence mechanism that logs every single write operation received by the server, which can be replayed to reconstruct the dataset.
* **Event Loop:** An infinite loop inside a program that waits for and dispatches events or messages (like incoming network requests).

---

## SECTION 5 — History

**Who created it?**
Salvatore Sanfilippo (known online as *antirez*), an Italian developer, created Redis in 2009.

**Why?**
He was building a real-time web analytics startup called LLOOGG. MySQL was too slow to handle the massive influx of concurrent read/write operations required to track real-time website visitors. He needed something that lived in RAM but could handle lists and counters natively. He wrote the initial version of Redis in C, open-sourced it, and it changed the backend world forever.

---

## SECTION 6 — Deep Theory

To master Redis, you must understand two foundational pillars: **Data Structures** and **Single-Threaded Speed**.

### 1. The Data Structures

Unlike traditional databases that use Tables and Rows, Redis uses Keys that point to Data Structures.

* **Strings:** The most basic type. Can hold text, serialized JSON, or integers. (Great for caching API responses or simple counters).
* **Lists:** Linked lists of strings. (Great for timelines or basic queues).
* **Sets:** Unordered collections of unique strings. (Great for tags, or tracking unique IP addresses).
* **Sorted Sets (ZSets):** Like Sets, but every item has a "score". Redis keeps the list perfectly sorted by this score in real-time. (The ultimate solution for Leaderboards).
* **Hashes:** Maps composed of fields associated with values. (Perfect for storing objects like a User Profile).
* **Advanced:** Bitmaps (for DAU tracking), HyperLogLog (for estimating unique views using tiny memory), Geospatial indexes (for "find Uber drivers near me").

### 2. Single-Threaded Architecture

Most databases (like PostgreSQL) spawn a new OS thread for every incoming connection. Redis does **not**.
Redis uses a **Single-Threaded Event Loop**. It processes exactly one command at a time.

Why is this incredibly fast?
Because accessing RAM takes nanoseconds. Context-switching between CPU threads takes microseconds (1000x slower). By avoiding thread locks, race conditions, and context switching, a single-threaded Redis instance can process upwards of 100,000+ requests per second on a basic machine.

---

## SECTION 7 — Internal Working

What happens inside the server CPU when 10,000 users send a Redis command at exactly the same time?

1. **Network I/O Multiplexing:** Redis uses OS-level features (like `epoll` in Linux). The OS listens to all 10,000 TCP sockets.
2. **The Queue:** As commands arrive over the network, `epoll` places them into a sequential queue.
3. **The Event Loop:** The single Redis thread pulls the first command from the queue, executes it in RAM (e.g., $O(1)$ Hash lookup), and writes the response to the output buffer.
4. **Next:** It immediately grabs the next command.

Because memory is so fast, executing a command takes roughly 1 microsecond. It burns through the queue so fast that to the outside world, it *looks* parallel.

**Memory Management & Eviction:**
RAM is finite. When Redis reaches its `maxmemory` limit, it must evict (delete) old data to make room for new data. The most common policy is **LRU (Least Recently Used)**—Redis deletes the keys that haven't been accessed in the longest time.

---

## SECTION 8 — Visual Diagrams

### Redis Single-Threaded Event Loop Architecture

```text
Concurrent Clients           OS Kernel (epoll)         Redis Process (Single Thread)
+----------+ 
| Client 1 | --(GET x)--> +-------------------+       +-------------------------+
+----------+              |                   |       |      Command Queue      |
                          | TCP Socket Array  | ----> | [GET x][SET y][INCR z]  |
+----------+              | (Multiplexer)     |       |           |             |
| Client 2 | --(SET y)--> |                   |       +-----------|-------------+
+----------+              +-------------------+                   | (One by one)
                                                                  v
+----------+                                          +-------------------------+
| Client 3 | --(INCR z)->                             |   RAM Execution Engine  |
+----------+                                          |  (Hash map data lookup) |
                                                      +-------------------------+
```

---

## SECTION 9 — Production Examples

**Twitter (X) Home Timeline:**
When you open Twitter, querying the relational database for the latest tweets of the 1,000 people you follow would take seconds. Instead, Twitter uses **Redis Lists**. Every time someone you follow tweets, their tweet ID is `PUSH`ed into your specific Redis List. When you open the app, Twitter just reads your Redis List ($O(1)$ time complexity).

**Gaming Companies (Leaderboards):**
Games like PUBG or Call of Duty need real-time global leaderboards. Sorting 10 million rows in MySQL takes minutes. In Redis, they use **Sorted Sets** (`ZADD`). When a player gets a kill, Redis instantly adjusts their rank in $O(\log N)$ time. You can fetch the top 10 players instantly.

---

## SECTION 10 — Backend Implementation

Let's implement a real-world use case in Java/Spring Boot: **A User Session & Profile Cache using Redis Hashes**.

**1. Dependencies (`pom.xml`):**

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
```

**2. Redis Configuration:**
We configure a `RedisTemplate` to tell Spring how to serialize Java objects into Redis Strings/Hashes.

```java
@Configuration
public class RedisConfig {

    @Bean
    public RedisTemplate<String, Object> redisTemplate(RedisConnectionFactory connectionFactory) {
        RedisTemplate<String, Object> template = new RedisTemplate<>();
        template.setConnectionFactory(connectionFactory);
        
        // Use String serialization for Keys
        template.setKeySerializer(new StringRedisSerializer());
        // Use JSON serialization for Values
        template.setValueSerializer(new GenericJackson2JsonRedisSerializer());
        template.setHashKeySerializer(new StringRedisSerializer());
        template.setHashValueSerializer(new GenericJackson2JsonRedisSerializer());
        
        return template;
    }
}
```

**3. Service Implementation (Using Redis Hash):**
Instead of caching a massive JSON string, we use Redis Hashes so we can update specific fields (like `loginCount`) without pulling the whole object.

```java
@Service
public class UserService {

    private final RedisTemplate<String, Object> redisTemplate;
    private static final String USER_CACHE_PREFIX = "user:";

    public UserService(RedisTemplate<String, Object> redisTemplate) {
        this.redisTemplate = redisTemplate;
    }

    // Save user to Redis Hash
    public void cacheUser(String userId, String name, int age) {
        String key = USER_CACHE_PREFIX + userId;
        
        // HSET user:123 name "John" age 25
        redisTemplate.opsForHash().put(key, "name", name);
        redisTemplate.opsForHash().put(key, "age", age);
        
        // Set TTL of 1 hour so RAM doesn't fill up permanently
        redisTemplate.expire(key, Duration.ofHours(1));
    }

    // Update JUST the age, without fetching the rest of the profile!
    public void incrementUserAge(String userId) {
        String key = USER_CACHE_PREFIX + userId;
        
        // HINCRBY user:123 age 1 (Happens purely in Redis C code, insanely fast)
        redisTemplate.opsForHash().increment(key, "age", 1);
    }
    
    // Fetch a specific field
    public String getUserName(String userId) {
        String key = USER_CACHE_PREFIX + userId;
        return (String) redisTemplate.opsForHash().get(key, "name");
    }
}
```

*(Note for Node.js devs: The equivalent would be using the `ioredis` package, calling `redis.hset('user:123', 'name', 'John')` and `redis.hincrby('user:123', 'age', 1)`).*

---

## SECTION 11 — Request Lifecycle

Trace an `incrementUserAge` request:

1. **Client:** POST `/users/123/birthday`
2. **Spring Boot Controller:** Calls `UserService.incrementUserAge()`.
3. **Network:** Spring uses a connection pool (Lettuce library) to send a TCP packet with the command `HINCRBY user:123 age 1` to the Redis Server.
4. **Redis (epoll):** The OS network layer receives the packet and adds it to the Event Loop Queue.
5. **Redis (Execution):** The single thread reads `HINCRBY`, finds `user:123` in RAM ($O(1)$), finds the `age` field ($O(1)$), increments the integer, and writes `2` to the output buffer.
6. **Response:** Redis sends the TCP packet back. Spring Boot returns `200 OK`.
*Total time: ~1-2 milliseconds.*

---

## SECTION 12 — Performance

**Time Complexity of core commands:**
* `GET` / `SET` (Strings): $O(1)$
* `HGET` / `HSET` (Hashes): $O(1)$
* `LPUSH` / `RPOP` (Lists): $O(1)$
* `ZADD` (Sorted Sets): $O(\log n)$ (Because it maintains a Skip List + Hash Table internally).

**Bottleneck:** Because Redis is single-threaded, **CPU speed matters more than CPU cores**. A 64-core processor won't make a single Redis instance faster. Furthermore, if you run a slow command (like `KEYS *` which scans the entire database in $O(N)$ time), **it blocks the single thread**. Every other user in the world will have to wait until that scan finishes.

---

## SECTION 13 — Security

1. **Network Exposure:** Never expose Redis directly to the public internet (Port 6379). It should only be accessible within your private VPC (Virtual Private Cloud).
2. **Authentication:** Configure `requirepass` in `redis.conf` to require a password.
3. **Command Renaming:** In production, dangerous commands like `KEYS`, `FLUSHALL` (which deletes the entire database), and `CONFIG` should be renamed or disabled in `redis.conf` so developers don't accidentally run them and cause an outage.

---

## SECTION 14 — Tradeoffs

**Advantages:**
* Unmatched speed (Sub-millisecond latency).
* Rich data structures reduce application-side compute.
* Built-in persistence (RDB/AOF) means it can act as a primary database for certain use cases.

**Disadvantages:**
* **Cost:** RAM is expensive. Storing 1TB of data in PostgreSQL (Disk) is cheap. Storing 1TB in Redis (RAM) requires an expensive server cluster.
* **Single Point of Failure:** If not configured with replication (Day 22), a server crash loses the volatile memory state (or loses the few seconds since the last AOF write).
* **Thread Blocking:** One bad $O(N)$ command halts the entire system.

---

## SECTION 15 — Common Mistakes

1. **Using `KEYS *` in Production:** Beginners often use `KEYS pattern*` to search for keys. This is an $O(N)$ operation. If you have 10 million keys, it will freeze the Redis thread for seconds, taking down your entire backend. **Use `SCAN` instead**, which iterates without blocking.
2. **Storing Massive JSONs as Strings:** If you frequently update parts of a JSON, use Hashes. Don't pull 5MB strings over the network just to change a boolean flag.
3. **No Eviction Policy:** Forgetting to set a TTL (Time to Live) on cache keys and setting `maxmemory-policy noeviction`. When RAM fills up, Redis will crash or refuse new writes.

---

## SECTION 16 — Interview Preparation

**Conceptual Questions:**

* *Why is Redis single-threaded? Isn't multi-threading faster?*  
  **Answer:** CPU is rarely the bottleneck for an in-memory DB; memory and network I/O are. Single-threading avoids the massive overhead of locks, synchronization, and context switching, making it overall faster and deterministic for RAM operations.

* *What is the difference between RDB and AOF?*  
  **Answer:** RDB takes point-in-time snapshots (good for backups, faster restarts, but you might lose the last few minutes of data on crash). AOF logs every single command (better durability, but files get huge and restarts are slower). Production often uses both combined.

**System Design Question:**

* *Design a real-time global leaderboard for a mobile game with 5 million players.*  
  **Expected Answer:** Use a Redis Sorted Set (`ZSET`). Key: `leaderboard:global`. Member: `userId`. Score: `total_points`. Use `ZINCRBY` to update scores and `ZREVRANGE` to fetch the top 10 instantly.

---

## SECTION 17 — Revision Notes

* **Redis** is an in-memory, single-threaded, data-structure server.
* **Data Structures:** Strings, Lists, Sets, Sorted Sets, Hashes.
* **Event Loop:** Uses I/O multiplexing to handle thousands of connections efficiently without thread locks.
* **Persistence:** RDB (Snapshots) and AOF (Command Logs) ensure data isn't lost on restart.
* **Rule of Thumb:** Never block the Redis thread with $O(N)$ operations like `KEYS *`.

---

## SECTION 18 — Hands-On Assignment

**Conceptual Exercise:**

1. Look up the `SCAN` command in Redis documentation. Write a brief explanation of how it differs from `KEYS *`.
2. If you wanted to implement a "Rate Limiter" (e.g., max 5 API requests per minute per IP), which Redis data structure and commands would you intuitively use?

**Design Exercise:**
Draw a system flow of how an E-Commerce "Shopping Cart" could be implemented using a Redis Hash. What happens when a user adds an item? What happens when they log out?

---

## SECTION 19 — Mini Project

**Mini Project: Real-Time View Counter**

* **Requirement:** Build a Spring Boot API that tracks how many times a specific article has been read.
* **Implementation:**  
  * Expose `GET /article/{id}`.
  * Inside the controller, use `RedisTemplate.opsForValue().increment("article:views:" + id)`.
  * Return the new view count.

* **Interview consideration:** Why is this better than running `UPDATE articles SET views = views + 1 WHERE id = ?` in PostgreSQL? (Hint: Row-level locks in relational DBs under high concurrency cause massive contention and deadlocks).

---

## SECTION 20 — Active Learning

To ensure these concepts are locked in before we move to Caching Patterns (Day 21), please answer the following questions based on today's lesson:

1. Explain in your own words why Redis being single-threaded is actually a *feature* rather than a limitation.
2. If you had to build a queue system where users process tasks in a First-In-First-Out (FIFO) order, which Redis Data Structure would you use, and why?
3. Why is running the `KEYS *` command in a production environment considered a catastrophic mistake?
