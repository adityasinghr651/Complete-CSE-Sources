# FINAL MASTER REVISION

## 1. 30 MUST-KNOW SYSTEM DESIGN CONCEPTS

1. **System Design:** Architecting software to meet scale, reliability, and functionality.
2. **Horizontal Scaling:** Adding more machines (servers) to handle load.
3. **Vertical Scaling:** Adding more power (CPU/RAM) to a single machine.
4. **Load Balancer:** Distributes incoming traffic evenly across multiple servers.
5. **Reverse Proxy:** Shields backend servers from the internet, handles SSL/caching.
6. **API Gateway:** Centralized entry point for microservices (auth, routing, rate limiting).
7. **Monolith:** All code bundled in one deployable unit.
8. **Microservices:** Code split into small, independently deployable business units.
9. **Statelessness:** Servers don't store user session memory; any server can handle any request.
10. **Stateful:** Servers remember client data (bad for scaling).
11. **Latency:** Time taken for a single request (milliseconds).
12. **Throughput:** Number of requests handled over time (Requests Per Second).
13. **CAP Theorem:** In a network partition, you must choose Availability or Consistency.
14. **Strong Consistency:** All nodes see the exact same data instantly.
15. **Eventual Consistency:** Nodes sync up eventually; temporary staleness allowed.
16. **SQL (Relational):** Structured tables, ACID guarantees, hard to scale horizontally.
17. **NoSQL:** Flexible schema, easy horizontal scale, usually eventual consistency.
18. **Indexes:** B-Trees added to databases to make read lookups O(log N) fast.
19. **Normalization:** Removing redundant data in SQL (requires JOINs to read).
20. **Denormalization:** Adding redundant data to speed up reads (avoids JOINs).
21. **Read Replica:** A copy of the primary database used strictly to handle read traffic.
22. **Sharding:** Splitting a massive database across multiple physical servers.
23. **Cache (Redis):** In-memory storage for lightning-fast reads.
24. **Cache Stampede:** Massive traffic spike hitting the DB when a hot cache key expires.
25. **Message Queue (RabbitMQ/Kafka):** Async buffers decoupling microservices.
26. **Pub/Sub:** One event is broadcasted to multiple subscribed listeners (Fan-out).
27. **Object Storage (S3):** Infinitely scalable storage for media/files accessed via HTTP.
28. **CDN (Content Delivery Network):** Caches static assets globally near users.
29. **Idempotency:** Repeating a request safely without duplicating the side-effect (e.g., double-charging).
30. **Circuit Breaker:** Fails fast when a downstream service is broken, preventing cascading crashes.

---

## 2. 20 MUST-KNOW ARCHITECTURAL DECISIONS

1. **Cache** → Reduce repeated expensive database reads.
2. **Load Balancer** → Distribute traffic and remove Single Points of Failure.
3. **Message Queue** → Buffer massive traffic spikes and decouple microservices asynchronously.
4. **Read Replicas** → Scale read-heavy relational databases.
5. **Sharding** → Scale write-heavy databases (last resort).
6. **NoSQL** → Handle unstructured data or massive write throughput.
7. **SQL** → Ensure ACID compliance for financial/critical transactional data.
8. **CDN** → Reduce latency for static assets (images, video, JS) globally.
9. **Object Storage (S3)** → Store large files (never put files in a database).
10. **Pre-signed URLs** → Allow direct secure file uploads to S3, bypassing the API server.
11. **JWTs** → Achieve stateless authentication for horizontal scaling.
12. **WebSockets** → Enable real-time, low-latency bi-directional communication.
13. **API Gateway** → Centralize rate-limiting and auth in microservices.
14. **Idempotency Keys** → Prevent duplicate financial transactions on network retries.
15. **Redis Lock** → Prevent race conditions across distributed worker nodes.
16. **Dead Letter Queue (DLQ)** → Isolate broken queue messages for manual debugging.
17. **Vector Database** → Enable semantic search and RAG for AI applications.
18. **LLM Orchestration** → Route simple tasks to cheap AI and complex tasks to expensive AI.
19. **Server-Sent Events (SSE)** → Stream long-running AI responses to the UI so it doesn't freeze.
20. **Denormalization** → Speed up heavily-read feeds by duplicating data to avoid JOINs.

---

## 3. TOP 20 SYSTEM DESIGN QUESTIONS

1. Design a URL Shortener (Concept #51)
2. Design a Rate Limiter (Concept #53)
3. Design WhatsApp / Chat App (Concept #63)
4. Design Twitter / News Feed (Concept #64)
5. Design Uber / Ride Sharing (Concept #65)
6. Design an E-commerce Checkout (Concept #67)
7. Design a Ticket Booking System (Concept #68)
8. Design a Notification Service (Concept #54)
9. Design an Image Upload/Processing Pipeline (Concept #56)
10. Design a Leaderboard System (Concept #71)
11. Design a Distributed Job Queue (Concept #69)
12. Design Google Docs / Collaborative Editor (Concept #75)
13. Design a Search Autocomplete (Concept #66)
14. Design Netflix / Video Streaming (Concept #70)
15. Design Yelp / Proximity Search (Concept #72)
16. Design an Authentication Service (Concept #52)
17. Design an AI Customer Support Chatbot (Concept #84)
18. Design an AI Document Parser (Concept #93)
19. Design a Logging and Metrics System (Concept #55)
20. Design a Multi-Tenant SaaS DB (Concept #73)

---

## 4. ONE-PAGE SYSTEM DESIGN FRAMEWORK

Memorize this flow for every interview:

```text
1. Clarify Requirements
   (Functional: What does it do? Non-Functional: Latency? Availability?)
        ↓
2. Estimate Scale
   (DAU, Reads vs Writes, Storage needed. Keep math simple.)
        ↓
3. Define APIs
   (GET /tweets, POST /checkout)
        ↓
4. Design Data Model
   (SQL vs NoSQL. Schema. Denormalization?)
        ↓
5. Design Core Architecture
   (Draw the MVP: Client -> LB -> API -> DB)
        ↓
6. Add Cache
   (Where are reads slow? Add Redis/CDN)
        ↓
7. Add Async Processing
   (Where are writes slow? Add Kafka/Workers)
        ↓
8. Handle Failures
   (Remove SPOFs, add Replicas, Circuit Breakers)
        ↓
9. Secure the System
   (Rate limiting, Auth, Idempotency)
        ↓
10. Monitor
   (Metrics, Logging, Tracing)
        ↓
11. Discuss Trade-offs
   (Consistency vs Availability, Latency vs Cost)
```

---

## 5. ONE-PAGE ARCHITECTURE CHEAT SHEET

```text
       [ Client ] ────────> (Sends Request)
           │
           v
       [ DNS ] ───────────> (Resolves Domain to IP)
           │
           v
       [ CDN ] ───────────> (Serves Static Images/JS instantly)
           │ (Miss)
           v
   [ Load Balancer ] ─────> (Distributes traffic, terminates SSL)
           │
           v
   [ API Gateway ] ───────> (Auth, Rate Limiting, Routing)
       /       \
      v         v
 [ API 1 ]   [ API 2 ] ───> (Stateless Backend Servers)
      │         │
      │         ├──> [ Cache (Redis) ] (Absorbs read-heavy traffic)
      │         │
      │         ├──> [ Database (SQL/NoSQL) ] (Source of truth)
      │         │
      └─────────┴──> [ Queue (Kafka) ] ──> [ Workers ] (Async Tasks)
                                                │
                                                v
                                          [ Object Store (S3) ]
```

---

## 6. COMMON SYSTEM DESIGN PATTERNS

- **Cache-Aside:** API checks Cache. If miss, API reads DB, writes to Cache, returns. *Why? Fast reads.*
- **Queue + Worker (Asynchronous):** API drops task in Queue, returns 202 Accepted. Worker processes later. *Why? Prevents API blocking.*
- **Fan-out on Write:** User posts -> System explicitly writes the post ID into the pre-computed feed caches of all their followers. *Why? O(1) read time for feeds.*
- **Sharding:** Splitting a DB alphabetically (A-M, N-Z) across servers. *Why? Infinite write scaling.*
- **Rate Limiting (Token Bucket):** Redis tracks how many requests an IP made in the last minute. *Why? Protects against DDoS and scraping.*
- **Pre-signed Upload:** API gives client a temporary AWS URL. Client uploads massive video directly to AWS. *Why? Saves API server bandwidth/memory.*
- **Circuit Breaker:** Stop calling a broken downstream service immediately; return default data. *Why? Prevents cascading failure.*
- **Idempotency Key:** Client sends UUID. Server checks DB: "Have I seen this UUID?" If yes, ignore. *Why? Prevents double-charging.*
- **Retrieval-Augmented Generation (RAG):** User Prompt -> Search Vector DB -> Append Facts to Prompt -> Send to LLM. *Why? Prevents AI hallucinations.*

---

## 7. 10 SYSTEMS I MUST BE ABLE TO DESIGN WITHOUT NOTES

*Practice drawing these on a whiteboard or paper:*
1. URL Shortener
2. Rate Limiter
3. Chat App (WhatsApp)
4. Twitter Feed
5. Ticket Booking System
6. E-commerce Checkout
7. Search Autocomplete
8. Distributed Job Queue
9. Notification Service
10. AI Document QA Bot (RAG)

---

## 8. 10 DEBUGGING SCENARIOS (Problem Only)

*Solve these aloud to yourself:*
1. Your Node.js API CPU is at 5%, but requests take 5 seconds and eventually time out. What is wrong?
2. You have a Redis cluster, but one node is at 100% CPU and crashing. The rest are idle. What is it?
3. Users are complaining that they updated their profile picture, but they still see the old one.
4. Your application works locally, but when deployed behind an AWS Load Balancer, users get logged out randomly on every click.
5. Your event workers are crashing, restarting, pulling the same Kafka message, and crashing again endlessly.
6. A viral news article's cache key expires at exactly 12:00 PM. At 12:00:01 PM, your database crashes. Why?
7. Your AI support bot costs $10,000/month in OpenAI API fees just answering "How do I reset my password?"
8. Your system processes refunds via a queue. A user complains they got refunded 3 times for the same order.
9. Your database read performance dropped to a crawl after the `Users` table hit 10 million rows.
10. A user tries to upload a 5GB 4K video through your Express API, and the server crashes with an Out Of Memory error.

---

## 9. 10 FOLLOW-UP INTERVIEW QUESTIONS (Problem Only)

1. "We decided to use Microservices. How do you handle a transaction that updates both the Order database and the Billing database?"
2. "How do you generate unique short IDs for a URL shortener in a distributed system?"
3. "What happens if a network partition separates our two database nodes? (CAP Theorem)"
4. "Why is a relational database a poor choice for storing live GPS pings for Uber?"
5. "If we scale our web servers horizontally, where does the user's login session state live?"
6. "Explain why OFFSET pagination gets extremely slow on page 10,000."
7. "What is the danger of a health check endpoint pinging the database?"
8. "Why shouldn't we just index every single column in the database to make all reads fast?"
9. "How do you handle the 'Celebrity Problem' in a Twitter feed fan-out architecture?"
10. "If an LLM context window is limited, how do you build a chatbot with infinite memory?"

---

## 10. 2-DAY FOUNDATION PLAN

**Day 1: Fundamentals & Core Components (Module 1)**
- Read Concepts 1–50.
- **CRITICAL INSTRUCTION:** Spend *more time drawing* ASCII diagrams on paper and *explaining them out loud* than taking written notes.
- Focus heavily on caching patterns, database trade-offs (SQL/NoSQL), and load balancing.

**Day 2: Applications & AI (Module 2)**
- Read Concepts 51–100.
- For each Practical Design (e.g., URL Shortener), cover up the answer and try to draw the architecture first.
- Practice the "60-second verbal explanation" for the top 10 systems.
- Take the Active Recall Test below.

---

# ACTIVE RECALL TEST

*(Try to answer before scrolling to the Answer Key)*

### Part A — 10 Concept Questions
1. What does stateless mean in web architecture?
2. What are the 3 letters of CAP and what do they mean?
3. What is the difference between Latency and Throughput?
4. What does an API Gateway do?
5. What is the difference between Authentication and Authorization?
6. Why are JWTs considered stateless?
7. What is a Cache Stampede?
8. What is the difference between RabbitMQ and Kafka?
9. What is a Dead-Letter Queue?
10. What is RAG in AI engineering?

### Part B — 5 Architecture Problems
1. Draw/Explain the components of a URL Shortener.
2. Draw/Explain how a user uploads a 10GB video securely without crashing the server.
3. Draw/Explain a basic Event-Driven architecture for an E-commerce checkout.
4. Draw/Explain how WebSockets work in a Chat App.
5. Draw/Explain a semantic caching layer in front of an LLM.

### Part C — 5 Debugging Problems
1. DB CPU hits 100% when exactly one cache key expires. Solution?
2. Node API crashes processing a 5GB file upload. Solution?
3. User gets logged out randomly behind a Load Balancer. Solution?
4. Workers crash infinitely on a bad JSON queue message. Solution?
5. Justin Bieber tweets and crashes the Redis fan-out system. Solution?

### Part D — 5 Trade-off Questions
1. Monolith vs Microservices for a 3-person startup?
2. SQL vs NoSQL for an Uber live GPS tracking system?
3. Strong vs Eventual consistency for a banking ledger?
4. Polling vs Webhooks for a Stripe payment completion?
5. GPT-4o vs Llama-3-8B (local) for simple JSON extraction?

---
*(Scroll down for answers)*
.
.
.
.
.

### ANSWER KEY (Compact)

**Part A:**
1. Server holds no session data; any server can process the request.
2. Consistency, Availability, Partition Tolerance (choose 2).
3. Latency = speed of 1 request. Throughput = volume of requests per second.
4. Central routing, auth, and rate-limiting for microservices.
5. AuthN = Who are you? AuthZ = What can you do?
6. The user data is cryptographically signed inside the token itself; no DB lookup needed.
7. Cache expires -> massive concurrent traffic hits the DB instantly.
8. RabbitMQ = deletes after read (task queue). Kafka = append-only log (event streaming).
9. A queue for broken messages that failed processing 3+ times.
10. Retrieval-Augmented Generation: Search a Vector DB for facts, give facts to LLM to answer.

**Part B:**
1. Client -> LB -> API -> (Check Cache) -> DB. Use Base62 encoder.
2. Client -> API -> gets Pre-signed S3 URL -> Client uploads directly to S3.
3. API writes to DB -> emits "OrderPlaced" to Kafka -> Shipping/Email services consume.
4. Client holds persistent TCP connection to Server. Server uses Redis Pub/Sub to pass messages to other Servers holding the recipient's socket.
5. Prompt -> Embed -> Search Vector DB -> If High Similarity, return cache. Else call LLM.

**Part C:**
1. Add a Mutex lock so only 1 request queries the DB to refresh the cache. (Or pre-warm cache).
2. Use S3 Pre-signed URLs or chunked multipart uploads.
3. Remove in-memory sessions; move session state to Redis (stateless APIs).
4. Implement a Dead-Letter Queue (max retries = 3).
5. Use a Pull model for celebrities (merge their timeline at read-time) instead of Push/Fan-out.

**Part D:**
1. Monolith. Microservices add too much operational overhead for a tiny team.
2. NoSQL (Cassandra). High write-throughput required; relations not needed.
3. Strong consistency. Cannot double-spend money.
4. Webhooks. Polling wastes bandwidth.
5. Llama (local) or GPT-4o-mini. Cheaper, faster, sufficient for extraction.

---

# FINAL SYSTEM DESIGN MENTAL MODEL

*(Internalize these 15 bullets. They are your core interview armor).*

1. **Start with Requirements.** Never design a system without knowing the scale (DAU).
2. **Stateless APIs.** Always store state in a DB or Redis, never in the API server's RAM.
3. **Database first.** SQL for relations/ACID. NoSQL for massive scale/flexibility.
4. **Cache aggressively.** If reads are slow, add Redis. If static files are slow, add a CDN.
5. **Decouple with Queues.** If a task takes >1 second, push it to Kafka/RabbitMQ.
6. **Protect the Database.** Use connection pooling, rate limiting, and cache-aside.
7. **Expect Failure.** Networks will drop. Add retries, exponential backoff, and circuit breakers.
8. **Never trust input.** Use Idempotency keys for writes, parameterized queries for SQL.
9. **Offload heavy lifting.** Let S3 handle massive files (Pre-signed URLs).
10. **Scale Horizontally.** Add more small servers behind a Load Balancer, not one giant server.
11. **Eventual Consistency is okay.** Most systems (except finance) don't need perfect sync.
12. **Indexes fix read speed.** But they slow down write speed.
13. **RAG fixes AI hallucinations.** Don't fine-tune an LLM for facts; search a Vector DB first.
14. **Prompt routing saves money.** Send easy AI tasks to cheap models.
15. **Always discuss Trade-offs.** There is no perfect architecture, only optimal compromises.

```text
REAL-WORLD PROBLEM
        ↓
REQUIREMENTS
        ↓
SCALE
        ↓
API
        ↓
DATA
        ↓
COMPUTATION
        ↓
CACHE
        ↓
ASYNC / QUEUE
        ↓
SCALING
        ↓
FAILURE HANDLING
        ↓
SECURITY
        ↓
OBSERVABILITY
        ↓
TRADE-OFFS
```

### What can I safely postpone for now?
As an internship candidate, you do **NOT** need to master:
- Writing custom Paxos/Raft consensus algorithms.
- The deep internals of Kubernetes networking (CNI).
- Advanced B-Tree node splitting math.
- Complex Multi-Region Active-Active database replication setups.
- Custom LLM fine-tuning scripts in PyTorch.

Focus entirely on **connecting the building blocks (API, DB, Cache, Queue) logically and understanding their trade-offs.**

---
**End of 100 Concepts + Practical Applications Interview Master.** Good luck!
